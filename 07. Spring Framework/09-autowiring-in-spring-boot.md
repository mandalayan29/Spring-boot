# Autowiring in Spring Boot

In the previous tutorial, we saw how Spring creates and gives us an object using `@Component` and `getBean()`. Now let's see what happens when **one object depends on another object**.

## The Scenario

Think about it in real life: to write code, an `Alien` (our programmer) needs a `Laptop`. So the `Alien` class **depends on** a `Laptop` object.

## Step 1: Create the Laptop Class

```java
public class Laptop {
    public void compile() {
        System.out.println("Compiling");
    }
}
```

## Step 2: Use Laptop Inside Alien

Update the `Alien` class to use a `Laptop` object:

```java
@Component
public class Alien {

    Laptop laptop;

    public void code() {
        laptop.compile();
    }
}
```

If you run this now, you'll get an error like:

```
Cannot invoke "Laptop.compile()" because "this.laptop" is null
```

### Why is `laptop` null?

Even though `Alien` has `@Component`, the `laptop` field inside it was never assigned any value — so it defaults to `null`. Just adding `@Component` on `Laptop` alone is **not enough** to automatically connect it to the `Alien` object.

## Step 3: Confirming the Laptop Bean Exists

To prove that Spring *can* create a `Laptop` bean, we can fetch it directly using `getBean()`, just like we did for `Alien`:

```java
@Component
public class Laptop {
    public void compile() {
        System.out.println("Compiling");
    }
}
```

```java
Laptop lap = context.getBean(Laptop.class);
lap.compile();  // Works! Prints "Compiling"
```

This confirms: Spring **can** create the `Laptop` bean — the container has it. The problem is only that it's **not automatically placed inside** the `Alien` object.

## Step 4: Connecting the Two — `@Autowired`

To tell Spring "please inject the `Laptop` bean into this field," we use the **`@Autowired`** annotation:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Alien {

    @Autowired
    Laptop laptop;

    public void code() {
        laptop.compile();
    }
}
```

Now when you run:

```java
Alien obj = context.getBean(Alien.class);
obj.code();
```

**Output:**
```
Compiling
```

It works! This is **wiring** — connecting one Spring-managed object to another. Since it happens automatically through the annotation, it's called **Autowiring**.

## How Autowiring Works (Concept)

When Spring sees `@Autowired` on the `laptop` field inside `Alien`, it understands:

> "It is my responsibility to find a `Laptop` object inside the container and place it into this field."

```
IoC Container
   ├── Alien bean   (needs a Laptop → @Autowired)
   └── Laptop bean
             │
             ▼
   Spring automatically injects
   Laptop bean into Alien's field
```

## Important Rule: `@Component` + `@Autowired`

For this pattern to work at all levels **except the main method**, remember:

| Where | What You Need |
|---|---|
| Inside `main()` method | Direct access via `context.getBean(...)` (because you have direct access to the container) |
| Inside any other class that needs another Spring-managed object | `@Component` on the dependency class, and `@Autowired` on the field that needs it |

## Extending Further

You can build multiple layers of dependency. For example:
- `Laptop` could depend on a `CPU` object.
- You would create a `CPU` class, mark it `@Component`, add a field of type `CPU` in `Laptop`, and mark that field `@Autowired`.

This is exactly how larger real-world Spring applications are structured — many small, connected components, all wired together automatically by Spring.

## Different Ways to Configure Spring

There isn't just one way to tell Spring how to wire your application. The three common approaches are:

1. **XML-based configuration** (the traditional, older way)
2. **Java-based configuration**
3. **Annotation-based configuration** (what we just used: `@Component` + `@Autowired`)

> **Note:** The annotation approach we used here is more specific to Spring Boot's simplified style. We will explore what happens "behind the scenes" using plain Spring Framework in upcoming tutorials.

## Summary

- `@Component` tells Spring: "manage this class as a Bean."
- `@Autowired` tells Spring: "inject a matching Bean into this field automatically."
- Together, these two annotations let you build applications made of multiple connected, Spring-managed objects — without ever writing `new` yourself.
