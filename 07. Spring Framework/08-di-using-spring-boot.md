# Dependency Injection Using Spring Boot

Now let's actually implement **Dependency Injection** using Spring Boot.

## Understanding the Main Method

Every Spring Boot project has a main class like this:

```java
@SpringBootApplication
public class SpringBootDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootDemoApplication.class, args);
    }
}
```

The line `SpringApplication.run(...)`:
- Activates the Spring Framework
- Starts the **IoC container**, where Spring will create and manage your objects (called **Beans**)

> **What is a Bean?** A Bean is simply a normal Java object — but one that is **created and managed by Spring**. It's not a special type of object; it's just a regular object with a different name because Spring controls its lifecycle.

## Step 1: Create a Simple Class

Let's create a class called `Alien`:

```java
public class Alien {
    public void code() {
        System.out.println("Coding");
    }
}
```

## Step 2: The Old Way (Without Spring)

Normally, to use this class, you'd create the object yourself:

```java
Alien obj = new Alien();
obj.code();
```

This works and prints `Coding`. But remember — we **don't want to create the object ourselves**. We want **Spring** to create and give us this object.

## Step 3: Accessing Spring's Container — ApplicationContext

To ask Spring for an object, we need access to the **IoC container**. We get this using **`ApplicationContext`**.

Here's the key insight: `SpringApplication.run(...)` actually **returns** an object of type `ConfigurableApplicationContext` (which extends `ApplicationContext`). So we can capture it like this:

```java
ApplicationContext context = SpringApplication.run(SpringBootDemoApplication.class, args);
```

Now `context` gives us a way to communicate with the Spring container.

## Step 4: Asking Spring for the Object

To get an object (Bean) from the container, use `getBean()`:

```java
Alien obj = context.getBean(Alien.class);
obj.code();
```

If you run this right now, **it will fail** with an error like:

```
No qualifying bean of type 'com.telusko.app.Alien' available
```

### Why does it fail?

Spring does **not** automatically create objects for every class in your project. Imagine a project with 100 classes — Spring creating objects for all of them, even ones you don't need, would be wasteful.

So by default, Spring says:
> "I will not create any object unless you explicitly tell me to."

## Step 5: Telling Spring to Manage the Class — `@Component`

To tell Spring "please create and manage this object," add the **`@Component`** annotation on top of the class:

```java
import org.springframework.stereotype.Component;

@Component
public class Alien {
    public void code() {
        System.out.println("Coding");
    }
}
```

Now, run the code again:

```java
Alien obj = context.getBean(Alien.class);
obj.code();
```

**Output:**
```
Coding
```

It works! Here's what happened:
- When Spring Boot starts, it scans your classes.
- It sees `@Component` on top of `Alien`.
- It knows: "I am responsible for creating and managing this object."
- Spring creates the object and stores it inside the **container**.
- When you call `getBean(Alien.class)`, Spring hands you that managed object.

> **Important Rule:** Only classes marked with `@Component` (or similar annotations) get created and managed by Spring. If you try `getBean()` on a class without `@Component`, it will fail the same way.

## Step 6: Getting the Same Bean Multiple Times

What if you call `getBean()` more than once?

```java
Alien obj1 = context.getBean(Alien.class);
obj1.code();
```

This also prints `Coding` successfully. But an interesting question comes up:

> Are `obj` and `obj1` the **same** object, or **different** objects?

This is related to something called **Bean Scope**, and it will make more sense once we study the Spring Framework core concepts in later tutorials.

## Summary

| Step | What Happens |
|---|---|
| Class has no `@Component` | `getBean()` fails — Spring doesn't know about it |
| Class has `@Component` | Spring creates and manages the object automatically |
| `ApplicationContext` | Your access point to Spring's IoC container |
| `context.getBean(ClassName.class)` | Fetches a Spring-managed object (Bean) |

```
Application Starts
        │
        ▼
SpringApplication.run() → returns ApplicationContext
        │
        ▼
Spring scans classes for @Component
        │
        ▼
Creates Bean(s) and stores in IoC Container
        │
        ▼
context.getBean(Alien.class) → returns the managed object
```
