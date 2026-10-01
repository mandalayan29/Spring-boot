# Spring Framework Core — XML Configuration, Bean Scopes, and Dependency Injection

## 1. Creating Your First Plain Spring Project (Without Spring Boot)

Until now, we used **Spring Boot**, which makes everything easy with annotations like `@Component` and `@Autowired`. But to understand what really happens **behind the scenes**, we need to build a project using **plain Spring Framework** — without Spring Boot.

### Creating the Project

1. In your IDE (IntelliJ or Eclipse — both work the same since we use Maven), choose **File → New → Project**.
2. This time, select a plain **Maven** project — **not** a Spring Boot project (we are not using `start.spring.io` here).
3. Choose the **Quick Start** archetype (a basic Maven project template).
4. Fill in the project details:

| Field | Example Value |
|---|---|
| Group ID | com.telusko |
| Artifact ID | Spring1 |
| Version | 1.0 |
| JDK | 17 or above (needed for Spring 6) |

5. Click **Create**.

Maven will build a basic project structure for you. The first file that opens is `pom.xml` — this is where Maven dependencies are defined. By default, it only contains a testing dependency (JUnit).

### Testing the Basic Setup

Before making changes, always test that the default project runs correctly. The generated `App.java` file usually prints "Hello World" — run it first to confirm your setup works.

### Writing the Alien Class

Create a simple class:

```java
public class Alien {
    public void code() {
        System.out.println("Coding");
    }
}
```

Test it the normal Java way first:

```java
Alien obj = new Alien();
obj.code();
```

This works, but remember — we don't want to create the object ourselves. We want **Spring** to create it for us.

### Getting Access to Spring's Container

To ask Spring for objects, we need a **container**. There are two options in Spring:

| Option | Status |
|---|---|
| `BeanFactory` | Old, mostly deprecated, removed in many places in Spring 6 |
| `ApplicationContext` | Newer, includes everything `BeanFactory` has, plus more features |

We will always use **`ApplicationContext`**.

### Adding the Spring Dependency

`ApplicationContext` is not part of core Java — it belongs to the Spring Framework. So we must add the Spring dependency to our project.

1. Go to **Maven Repository** (a website that lists Maven dependencies).
2. Search for **"Spring Context"**.
3. Pick a stable version (a good practice is to choose the **second-to-last** released version, since the very latest version may still have unresolved issues).
4. Copy the dependency snippet and paste it inside the `<dependencies>` section of your `pom.xml`.
5. **Reload Maven changes** (in IntelliJ, click the refresh/reload icon that appears; in Eclipse, this usually happens automatically).

After reloading, Spring's classes (including `ApplicationContext`) will be available in your project.

### Writing the Container Code

```java
ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
Object obj = context.getBean("alien");
Alien alien = (Alien) obj;
```

Explanation:
- `ClassPathXmlApplicationContext` is a class that implements `ApplicationContext`, using an **XML file** for configuration.
- `getBean("alien")` asks the container for an object named `"alien"`.
- `getBean()` always returns type `Object` by default, so we **typecast** it to `Alien`.

If you run this right now, you'll get an error like:

```
BeanFactory not initialized or already closed
```

This happens because we haven't yet told Spring **what** `"alien"` refers to. That's covered next.

---

## 2. Configuring Beans Using XML

### Creating the XML Configuration File

Spring (using `ClassPathXmlApplicationContext`) looks for the XML file in the **classpath**. To set this up:

1. Inside `src/main`, create a new folder named **`resources`**.
2. Inside `resources`, create an XML file — commonly named **`spring.xml`** (the name doesn't matter, but it must match what you pass into `ClassPathXmlApplicationContext("spring.xml")`).

> **Important:** The file must be inside the `resources` folder, or Spring won't find it.

### Writing the Bean Definition

Inside `spring.xml`, we tell Spring which classes it should manage using a `<bean>` tag:

```xml
<bean id="alien" class="com.telusko.Alien"></bean>
```

- **`id`** — the name used to look up this bean later (matches what you pass into `getBean("alien")`).
- **`class`** — the **fully qualified class name** (package + class name).

### Adding the Required XML Schema

Simply writing `<bean>` isn't enough — Spring needs to know what this tag *means*. You must include a proper XML schema definition at the top of the file. Rather than memorizing this, search the official Spring documentation for **"Spring bean configuration XML"** and copy the standard definition block. This defines the `beans` root element that wraps all your `<bean>` tags.

A basic structure looks like:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="alien" class="com.telusko.Alien"></bean>

</beans>
```

Once this is correctly set up, running the project works and prints:

```
Coding
```

### Adding More Beans

If you have more classes, just add more `<bean>` tags:

```xml
<bean id="alien" class="com.telusko.Alien"></bean>
<bean id="lap" class="com.telusko.Laptop"></bean>
```

The configuration is written once — after that, you can freely use `getBean()` for any class registered this way.

---

## 3. When Does Spring Actually Create the Object?

A common question: does Spring create the bean when the **container loads**, or only when you call `getBean()`?

### Testing With a Constructor

Add a constructor to `Alien` that prints a message:

```java
public Alien() {
    System.out.println("Object Created");
}
```

If you comment out the `getBean()` line and keep only the container creation line:

```java
ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
```

...and run it, `"Object Created"` **still prints**.

**Conclusion:** The object is created as soon as the container is loaded (i.e., when `ClassPathXmlApplicationContext` runs), **not** when `getBean()` is called. `getBean()` only **fetches** the already-created object from the container; it doesn't create a new one.

### How Many Objects Get Created?

- If you define **one `<bean>`** tag for a class, Spring creates **one object** for it, at container load time.
- If you define the **same class twice with two different IDs**, Spring creates **two separate objects** — one for each bean definition.
- The **`id`** attribute is technically optional, but without it, you cannot refer to that bean by name later.

```
spring.xml has:
   <bean id="alien1" class="Alien">
   <bean id="alien2" class="Alien">

Result: 2 separate Alien objects are created
```

---

## 4. Bean Scopes: Singleton and Prototype

### The Experiment

If you call `getBean("alien1")` **twice** using two different references:

```java
Alien obj1 = (Alien) context.getBean("alien1");
Alien obj2 = (Alien) context.getBean("alien1");
```

You might expect two different objects. But testing shows **both references point to the same object**. If you change a property (like `age`) on `obj1`, `obj2` shows the same change.

### Why? — The Default Scope is Singleton

Every Spring bean has a **scope**, which decides how many objects Spring creates for that bean definition.

| Scope | Behavior | Where Used |
|---|---|---|
| **Singleton** (default) | Only **one** object is created for the entire application, no matter how many times you call `getBean()` | Spring Core (default) |
| **Prototype** | A **new object is created every time** you call `getBean()` | Spring Core |
| Request, Session | Create a new object per web request/session | Web applications only (not covered here) |

### Setting the Scope

```xml
<bean id="alien1" class="com.telusko.Alien" scope="prototype"></bean>
```

With `scope="prototype"`, every `getBean()` call returns a **brand new object**. You can confirm this because the constructor (`"Object Created"`) prints once for every `getBean()` call.

### Important Timing Difference

- **Singleton** beans are created **immediately when the container loads** — even before you call `getBean()`.
- **Prototype** beans are created **only when you call `getBean()`** — not when the container loads.

```
Singleton:  Container Loads → Object Created immediately
Prototype:  Container Loads → (nothing yet) → getBean() called → Object Created
```

---

## 5. Setter Injection

**Injection** means Spring assigning values to an object's properties for you, instead of you assigning them manually in code.

### The Problem

Suppose `Alien` has a private field:

```java
public class Alien {
    private int age;
    // getters and setters generated
}
```

Normally, you'd assign a value like this in Java code:

```java
obj.setAge(21);
```

But Spring encourages **injecting** values through configuration instead of hardcoding them.

### Using the `<property>` Tag

In `spring.xml`:

```xml
<bean id="alien1" class="com.telusko.Alien">
    <property name="age" value="21"/>
</bean>
```

- **`name`** — must match the **property name** (the variable name), not the getter/setter method name.
- **`value`** — used only for **primitive types** (int, String, boolean, etc.).

### How It Works Internally

This is called **setter injection** because Spring internally calls the **setter method** (`setAge(21)`) to assign the value — it does not directly assign it to the variable. You can verify this by adding a print statement inside `setAge()`; it will print when the object is created.

```
Spring creates the object
        │
        ▼
Spring calls setAge(21) automatically
        │
        ▼
Object is ready to use
```

---

## 6. Injecting Object References — The `ref` Attribute

The `<property value="...">` syntax works only for **primitive values**. What if a property is a **reference to another object** (not a primitive)?

### Example Scenario

`Alien` depends on `Laptop`:

```java
public class Alien {
    private Laptop lap;
    // getters and setters
}
```

Using `new Laptop()` directly inside `Alien` defeats the purpose of Spring — we want Spring to **inject** the `Laptop` object instead.

### Using `ref` Instead of `value`

First, define a bean for `Laptop`:

```xml
<bean id="lap1" class="com.telusko.Laptop"></bean>
```

Then, reference it inside `Alien`'s bean definition:

```xml
<bean id="alien1" class="com.telusko.Alien">
    <property name="age" value="21"/>
    <property name="lap" ref="lap1"/>
</bean>
```

- **`value`** → for primitive types.
- **`ref`** → for object references, pointing to another bean's `id`.

This connects (**wires**) the `Alien` bean to the `Laptop` bean. You can create multiple `Laptop` beans with different IDs (e.g., `lap1`, `lap2`) and reference whichever one you need.

---

## 7. Constructor Injection

Setter injection assigns values **after** the object is created. If you want values assigned **as soon as the object is created**, use a **constructor** instead.

### Step 1: Create a Parameterized Constructor

```java
public class Alien {
    private int age;
    private Laptop lap;

    public Alien(int age, Laptop lap) {
        this.age = age;
        this.lap = lap;
    }
}
```

### Step 2: Use `<constructor-arg>` in XML

```xml
<bean id="alien1" class="com.telusko.Alien">
    <constructor-arg value="21"/>
    <constructor-arg ref="lap1"/>
</bean>
```

Like `<property>`, `<constructor-arg>` also uses `value` for primitives and `ref` for object references.

### The Problem: Matching Arguments Correctly

By default, Spring matches constructor arguments **by sequence** (the order they appear), not by type. If the order in your XML doesn't match your constructor's parameter order, Spring throws an error or assigns values incorrectly.

There are **three ways to fix ambiguous matching**:

| Method | How It Works | Example |
|---|---|---|
| **`type`** | Explicitly state the data type of each argument (works only if all types are different) | `<constructor-arg type="int" value="21"/>` |
| **`index`** | Explicitly state the position (0, 1, 2...) of each argument — most reliable | `<constructor-arg index="0" value="21"/>` |
| **`name`** | Match by parameter name (requires debug mode info, or `@ConstructorProperties` annotation on the constructor) | `<constructor-arg name="age" value="21"/>` |

> **Recommended approach:** Use **`index`**, since it works reliably in all cases, including when you have multiple parameters of the same type (e.g., two `int` fields like `age` and `salary`).

```xml
<bean id="alien1" class="com.telusko.Alien">
    <constructor-arg index="0" value="21"/>
    <constructor-arg index="1" ref="lap1"/>
</bean>
```

### Setter Injection vs Constructor Injection

| | Setter Injection | Constructor Injection |
|---|---|---|
| When values are assigned | After object creation | At the moment of object creation |
| Best used for | Optional properties | Compulsory/required properties |

---

## 8. Using Interfaces for Loose Coupling

### The Problem

So far, `Alien` directly depends on the `Laptop` class. But in real life, you might code using a laptop, a desktop, or some other device — not just a laptop specifically.

### The Solution: Extract an Interface

Create a `Computer` interface with one method:

```java
public interface Computer {
    void compile();
}
```

Make `Laptop` implement it:

```java
public class Laptop implements Computer {
    public void compile() {
        System.out.println("Compiling using Laptop");
    }
}
```

Create another implementation, `Desktop`:

```java
public class Desktop implements Computer {
    public void compile() {
        System.out.println("Compiling using Desktop");
    }
}
```

Now update `Alien` to depend on the **interface**, not a specific class:

```java
public class Alien {
    private Computer com;
    // getters and setters
}
```

### Why This Matters

`Alien` no longer cares whether it gets a `Laptop` or a `Desktop` — any class implementing `Computer` will work. This is a core Object-Oriented principle: **coding to an interface, not an implementation**. It also sets up the next concept — **autowiring**.

```
        Computer (interface)
         /            \
    Laptop          Desktop
  (implements)    (implements)
```

---

## 9. Autowiring in XML Configuration

Now that `Alien` depends on the `Computer` interface, we need to tell Spring which implementation to inject. We could keep using `ref` manually, but Spring can also do this **automatically** — this is called **autowiring**.

### Autowire by Name

```xml
<bean id="alien1" class="com.telusko.Alien" autowire="byName">
    <property name="age" value="21"/>
</bean>

<bean id="com" class="com.telusko.Laptop"></bean>
```

With `autowire="byName"`, Spring looks at the property name in `Alien` (`com`) and searches for a bean with the **same ID** (`com`). If found, it automatically injects it — no need for an explicit `ref`.

> **Note:** If you explicitly specify a `<property name="com" ref="...">` as well, that explicit reference takes priority over autowiring.

### Autowire by Type

```xml
<bean id="alien1" class="com.telusko.Alien" autowire="byType">
</bean>

<bean id="com1" class="com.telusko.Laptop"></bean>
```

With `autowire="byType"`, Spring ignores the bean's `id`/name and instead looks for a bean whose **type matches** the property's type (`Computer` in this case). It finds `Laptop` because `Laptop implements Computer`.

### The Problem With `byType` and Multiple Implementations

If you have **two beans of the same type** (e.g., both `Laptop` and `Desktop`, both implementing `Computer`), `autowire="byType"` will throw an error:

```
Expected single matching bean but found 2
```

Spring cannot decide which one to inject. This is solved using the **`primary`** attribute (next section) or by switching to `autowire="byName"`.

---

## 10. Primary Bean

When there are multiple beans of the same type and Spring can't decide which one to autowire (using `byType`), you can mark one bean as the **default choice** using `primary="true"`.

```xml
<bean id="com1" class="com.telusko.Laptop" primary="true"></bean>
<bean id="com2" class="com.telusko.Desktop"></bean>
```

### How It Works

| Situation | Result |
|---|---|
| Confusion between multiple beans of the same type, using `autowire="byType"` | Spring picks the bean marked `primary="true"` |
| An explicit `ref` is mentioned for a specific bean | The explicit reference is used, **overriding** the primary setting |
| Using `autowire="byName"` | `primary` has no effect — Spring always matches names directly |

`primary` is essentially a **tie-breaker** — it only matters when there's ambiguity.

---

## 11. Lazy Initialization

By default, Spring creates all **singleton** beans as soon as the container loads — even beans you might not use immediately. This is called **eager initialization**.

### The Problem

If your application has hundreds of beans, creating all of them upfront — even unused ones — wastes memory and slows down startup.

### The Solution: `lazy-init`

```xml
<bean id="com2" class="com.telusko.Desktop" lazy-init="true"></bean>
```

With `lazy-init="true"`, the `Desktop` object is **not created** when the container loads. It is created **only the first time** it is requested (via `getBean()` or through autowiring/dependency).

### Behavior Summary

| Bean Type | When Object is Created |
|---|---|
| Singleton (default, eager) | Immediately, when container loads |
| Singleton + `lazy-init="true"` | Only when first requested |
| Prototype | Every time `getBean()` is called (never at container load) |

### Important Exception

If a **non-lazy (eager)** bean **depends on** a **lazy** bean, the lazy bean **still gets created immediately** — because the eager bean needs it right away during its own initialization.

```
Eager Bean (Alien) 
      │
      └── depends on ──▶ Lazy Bean (Desktop)

Result: Desktop is still created immediately,
because Alien (eager) needs it during startup.
```

---

## 12. Getting Beans by Type (Avoiding Typecasting)

Normally, `getBean()` returns type `Object`, forcing you to typecast:

```java
Alien obj = (Alien) context.getBean("alien1");
```

### Cleaner Way — Pass the Class Type

```java
Alien obj = context.getBean("alien1", Alien.class);
```

By passing `Alien.class` as a second argument, Spring returns the object **already cast** to the correct type — no manual typecasting needed.

### Getting a Bean Without Specifying the ID

You can also fetch a bean using **only the class type**, without mentioning its `id`:

```java
Desktop obj = context.getBean(Desktop.class);
```

This works by searching for a bean of matching type. However, if **multiple beans share the same type** (like both `Laptop` and `Desktop` implementing `Computer`), this will fail with:

```
No qualifying bean of type 'Computer' available — expected single matching bean but found 2
```

> **Best Practice:** When multiple beans of the same type exist, it's safer to fetch by **name** (`id`) to avoid ambiguity, rather than relying purely on type.

---

## 13. Inner Beans

Normally, a bean defined in `spring.xml` is available to the **entire application** — any other bean can reference it using `ref`.

### The Problem

Sometimes, you want a bean to be used by **only one specific bean**, and not be accessible from anywhere else.

### The Solution: Inner Bean

Instead of defining `Laptop` as a separate, top-level bean and linking it with `ref`, you can define it **directly inside** the `<property>` tag of `Alien`:

```xml
<bean id="alien1" class="com.telusko.Alien">
    <property name="age" value="21"/>
    <property name="com">
        <bean class="com.telusko.Laptop"></bean>
    </property>
</bean>
```

Here, the `Laptop` bean has **no `id`**, and it exists only inside the `com` property of `Alien`. This is called an **inner bean**.

### Outer Bean vs Inner Bean

| | Outer Bean | Inner Bean |
|---|---|---|
| Defined | At the top level of `spring.xml` | Nested inside another bean's property |
| Visibility | Available to the whole application via `ref` | Usable only by the bean that contains it |
| Use case | Shared dependency needed by multiple beans | Dependency specific to just one bean |

---

## Quick Reference Summary

| Concept | XML Syntax |
|---|---|
| Define a bean | `<bean id="..." class="..."></bean>` |
| Set primitive value (setter injection) | `<property name="..." value="..."/>` |
| Set object reference (setter injection) | `<property name="..." ref="..."/>` |
| Set primitive value (constructor injection) | `<constructor-arg value="..."/>` |
| Set object reference (constructor injection) | `<constructor-arg ref="..."/>` |
| Fix constructor-arg ambiguity | `type="..."`, `index="..."`, or `name="..."` |
| Set scope | `scope="singleton"` or `scope="prototype"` |
| Enable autowiring by name | `autowire="byName"` |
| Enable autowiring by type | `autowire="byType"` |
| Mark default bean for ambiguous type match | `primary="true"` |
| Delay bean creation until first use | `lazy-init="true"` |
| Nest a bean only for one property | `<property name="..."><bean class="..."></bean></property>` |