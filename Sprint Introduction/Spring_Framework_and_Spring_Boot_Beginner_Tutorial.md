# Spring Framework and Spring Boot — Beginner Tutorial

## 1. Introduction to Spring Framework

### What is Spring?

Spring is a Java framework used to build applications, especially large or enterprise-level applications.

A framework provides ready-made features and structure so that developers do not have to build everything from scratch.

For example, before Spring became popular, Java developers commonly used different technologies for different jobs:

- **EJB** — used for enterprise applications.
- **Struts** — commonly used for web applications.
- **Hibernate** — commonly used for working with databases and ORM.

Spring brought many application-development features together into one ecosystem.

Spring is also called a **lightweight framework**. One reason is that Spring can work with normal Java objects called **POJOs**.

### What is a POJO?

POJO means:

> **Plain Old Java Object**

It is simply a normal Java class/object.

For example:

```java
public class Laptop {

    public void start() {
        System.out.println("Laptop started");
    }
}
```

This is a normal Java class. Spring can manage objects created from classes like this.

---

## 2. What Can Spring Be Used For?

Spring is not only one feature. It is a large ecosystem containing many projects.

Spring can be used to build:

- Web applications
- Microservices
- Cloud applications
- Reactive applications
- Serverless applications
- Event-driven applications
- Batch applications

The Spring ecosystem contains projects such as:

- Spring Framework
- Spring Boot
- Spring Data
- Spring Cloud
- Spring Security
- Spring for GraphQL
- Spring Batch

There are also integrations for technologies and languages such as Kotlin.

### Spring is an ecosystem

It is better to think of Spring as a collection of related projects rather than a single library.

A simple view is:

```text
Spring Ecosystem
│
├── Spring Framework
├── Spring Boot
├── Spring Data
├── Spring Cloud
├── Spring Security
├── Spring Batch
└── Other Spring Projects
```

The main concepts in this tutorial are:

- Spring Framework
- Spring Boot
- Dependency Injection
- IoC
- Spring ORM
- Spring AOP
- Spring Security
- Microservices

---

# 3. Why Is Spring Popular?

A framework becomes useful when it has good features, a strong community, and good documentation.

Spring has all three.

### 1. Features

Spring provides many features needed for application development.

### 2. Community

Spring has a large Java developer community.

A large community means:

- Many developers use it.
- Many problems have already been solved.
- Many learning resources are available.

### 3. Documentation

Spring provides official documentation for its projects and features.

When learning Spring, the official documentation is an important reference.

You should learn to use documentation instead of depending only on tutorials.

For example, when you need to understand Spring AOP, you can directly read the Spring documentation for AOP.

---

# 4. Prerequisites for Learning Spring

Spring uses many Java concepts. Therefore, you should have a good understanding of core Java first.

## Core Java

You should know:

### Java syntax

You should understand basic Java syntax such as:

```java
class Student {
    String name;
}
```

### OOP

You should understand:

- Class
- Object
- Inheritance
- Polymorphism
- Encapsulation
- Abstraction
- Interfaces

### Exception handling

You should know:

```java
try {
    // code
} catch (Exception e) {
    // handle exception
}
```

You should understand checked and unchecked exceptions and how exceptions are handled.

### Threads

You do not need advanced thread knowledge at the beginning, but basic thread concepts are useful.

### Collections

You should understand the Java Collection API, especially:

- List
- Set
- Map
- ArrayList
- HashSet
- HashMap

These are commonly used in Spring applications.

---

# 5. JDBC

You should have basic knowledge of **JDBC**.

JDBC means:

> **Java Database Connectivity**

JDBC allows a Java application to communicate with a database.

The basic idea is:

```text
Java Application
       │
       ▼
      JDBC
       │
       ▼
    Database
```

Spring applications frequently work with databases, so understanding the basic database connection process is useful.

---

# 6. Maven or Gradle

A Spring project needs a build tool.

Common Java build tools are:

- Maven
- Gradle

The tutorial uses **Maven**.

Maven helps with:

- Project structure
- Dependency management
- Building the application
- Running build tasks

For example, Spring dependencies can be declared in `pom.xml`.

---

# 7. ORM and Hibernate

When building enterprise applications, you will normally work with databases.

Spring also works with ORM technologies.

You should have at least a basic understanding of:

- ORM
- Hibernate

ORM means **Object-Relational Mapping**.

It allows Java objects to be mapped to database tables.

---

# 8. Servlets

You should also have a basic understanding of Java Servlets.

You may hear that Servlets are old technology. You normally do not build modern Spring applications directly using Servlets.

However, understanding Servlets is useful because Spring MVC runs on servlet-based servers such as **Tomcat**.

Tomcat is a **Servlet container**.

A simplified view is:

```text
Spring MVC Application
        │
        ▼
      Tomcat
        │
        ▼
   Servlet APIs
```

You do not need advanced Servlet knowledge before starting Spring. Basic knowledge is enough.

---

# 9. Tools Required for Spring

To develop Spring applications, you need a JDK and an IDE.

## JDK

You need the Java Development Kit installed.

For **Spring Framework 6**, Java 17 or newer is required.

For example:

```bash
java -version
```

You should see a Java version such as:

```text
17
```

or a newer version such as:

```text
21
```

---

# 10. Choosing an IDE

You can use different IDEs for Spring development.

Common choices include:

- IntelliJ IDEA
- Eclipse
- Visual Studio Code
- NetBeans

The Java code itself does not depend on the IDE.

Maven also provides a standard project structure, so the project structure remains mostly the same across IDEs.

---

## IntelliJ IDEA

IntelliJ IDEA has two main editions:

- Community Edition
- Ultimate Edition

The Ultimate Edition provides additional Spring support.

The Community Edition can still be used to work with Spring projects, especially by generating the project externally using Spring Initializr.

---

## Eclipse

Eclipse can be extended with Spring Tools.

Spring Tools provides Spring-specific development support.

After installing Spring Tools, Eclipse can provide features for creating and working with Spring projects.

---

## Visual Studio Code

VS Code can also be used for Spring development.

You can install Spring-related extensions, including Spring Tools.

---

# 11. IoC and Dependency Injection

Before writing Spring code, two important concepts must be understood:

1. **IoC — Inversion of Control**
2. **DI — Dependency Injection**

They are related, but they are not exactly the same thing.

---

# 12. What Is IoC?

IoC means:

> **Inversion of Control**

Normally, in Java, the programmer creates and controls objects.

For example:

```java
Laptop laptop = new Laptop();
```

Here, the programmer is responsible for creating the object.

The programmer also decides when and how objects are created and used.

The problem is that in a large application, there may be hundreds or thousands of objects.

Managing all of these objects manually can make the code more difficult.

The main purpose of the application should be the **business logic**, not manually managing every object.

So we give object creation and management to another system.

In Spring, that system is the **Spring IoC container**.

---

# 13. Spring IoC Container

The Spring IoC container is responsible for creating and managing Spring objects.

A simple mental model is:

```text
                Spring Framework
                       │
                       ▼
                 IoC Container
              ┌─────────────────┐
              │                 │
              │  Alien object   │
              │  Laptop object  │
              │  CPU object     │
              │  Other objects  │
              │                 │
              └─────────────────┘
```

Instead of writing:

```java
Alien alien = new Alien();
```

you can ask Spring for the object.

Spring creates and manages the object for you.

---

# 14. What Is a Spring Bean?

An object that is created and managed by Spring is called a **Spring Bean**.

There is nothing special about the object itself.

For example:

```java
Alien alien = new Alien();
```

The object is still a normal Java object.

If Spring creates and manages it, we call it a **bean**.

So:

```text
Normal Java Object
       │
       │ Spring manages it
       ▼
   Spring Bean
```

---

# 15. IoC Is a Principle

IoC is a principle.

It means that control over something, such as object creation, is moved from your application code to another system.

Spring implements this principle using its IoC container.

But how are objects connected to each other?

That is where **Dependency Injection** comes in.

---

# 16. What Is Dependency Injection?

A class often needs another object to perform its work.

That required object is called a **dependency**.

For example, imagine:

```text
Laptop
  │
  └── needs
       │
       ▼
      CPU
```

A laptop depends on a CPU.

Therefore:

- `Laptop` is one object.
- `CPU` is another object.
- `Laptop` has a dependency on `CPU`.

Dependency Injection means that the required dependency is provided to the object instead of the object creating the dependency itself.

---

# 17. IoC vs Dependency Injection

A simple way to remember the difference:

### IoC

IoC is the **principle**.

> Give control of object creation and management to another system.

### Dependency Injection

DI is a **design pattern/technique** used to implement IoC.

> Provide an object's dependencies from outside instead of making the object create them itself.

So:

```text
IoC
 │
 │ principle
 ▼
Dependency Injection
 │
 │ technique used to implement IoC
 ▼
Spring Container manages objects
```

People often use the terms IoC and DI interchangeably in everyday Spring discussions, but conceptually they are different.

---

# 18. Spring vs Spring Boot

Spring and Spring Boot are closely related.

They are not completely separate technologies.

## Spring Framework

Spring Framework is the core framework.

It provides features such as:

- Dependency Injection
- IoC
- Web development
- Data access
- Security
- AOP
- Transaction management

## Spring Boot

Spring Boot is built on top of Spring Framework.

Its main goal is to make Spring applications easier and faster to create.

---

# 19. Why Was Spring Boot Created?

Originally, creating a Spring application required a lot of configuration.

Even a simple application could require configuration such as:

- Creating the project structure
- Configuring XML files
- Defining beans
- Configuring different Spring components

This could take significant time.

Spring Boot reduces much of this configuration.

It provides sensible defaults and creates a project structure that can usually run with very little setup.

Spring Boot is often described as **opinionated** because it makes many sensible choices for you.

You can still change the configuration when required.

---

# 20. Spring and Spring Boot Relationship

A simple way to remember it:

```text
Spring Framework
       │
       ▼
  Spring Boot
       │
       ▼
Makes Spring applications
easier to create and run
```

Using Spring Boot does **not** mean you are no longer using Spring.

Spring Boot uses Spring Framework underneath.

The transcript uses:

- Spring Framework 6
- Spring Boot 3

Spring Boot 3 is based on Spring Framework 6.

---

# 21. Creating Your First Spring Boot Application

One easy way to create a Spring Boot project is using **Spring Initializr**.

The website is:

```text
https://start.spring.io
```

Spring Initializr generates the project structure for you.

---

## Basic Project Settings

For a basic Java Spring Boot application, you can select:

- Project: Maven
- Language: Java
- Packaging: Jar
- Java: 17 or newer

You also provide values such as:

- Group
- Artifact
- Package name

For example:

```text
Group: com.example
Artifact: spring-demo
Package: com.example.app
```

You can add dependencies depending on what your application needs.

For a simple dependency-injection example, no additional dependency is required beyond the basic Spring Boot setup.

---

# 22. Maven Project Structure

A generated Spring Boot project normally has a structure similar to:

```text
spring-demo
│
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com.example.app
│   │   │       └── SpringDemoApplication.java
│   │   │
│   │   └── resources
│   │
│   └── test
│
├── pom.xml
└── ...
```

The exact package name depends on what you selected while creating the project.

---

# 23. `pom.xml`

Maven projects use `pom.xml`.

This file contains project information and dependencies.

A Spring Boot project contains Spring Boot dependencies.

For example, the project has a Spring Boot starter dependency.

Spring Boot starters make dependency management easier.

Spring Framework dependencies are also brought into the application.

So even though you are creating a Spring Boot application, Spring Framework is still being used underneath.

---

# 24. Running the First Spring Boot Application

A Spring Boot application normally has a main class similar to:

```java
@SpringBootApplication
public class SpringBootDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootDemoApplication.class, args);
    }
}
```

For the first example, the important line is:

```java
SpringApplication.run(SpringBootDemoApplication.class, args);
```

This starts the Spring application.

It also starts the Spring container.

A simple view is:

```text
main()
  │
  ▼
SpringApplication.run()
  │
  ▼
Spring starts
  │
  ▼
Spring IoC Container starts
```

---

# 25. Simple Hello World Test

You can first test whether the application starts correctly.

For example:

```java
public static void main(String[] args) {

    SpringApplication.run(SpringBootDemoApplication.class, args);

    System.out.println("Hello World");
}
```

When you run the application, Spring starts and the message is printed.

This does not demonstrate Dependency Injection yet.

It only confirms that the Spring Boot application can start.

---

# 26. Dependency Injection Using Spring Boot

Now we can create a simple example.

Suppose we have an `Alien` class.

```java
public class Alien {

    public void code() {
        System.out.println("Coding");
    }
}
```

The class has one method:

```java
code()
```

It prints:

```text
Coding
```

---

## Creating the Object Normally

Without Spring, we create the object ourselves:

```java
Alien alien = new Alien();
alien.code();
```

The important part is:

```java
new Alien();
```

The programmer creates the object.

But we want Spring to create and manage the object.

---

# 27. Making a Class a Spring Component

To tell Spring that it should manage the `Alien` object, use the `@Component` annotation.

```java
import org.springframework.stereotype.Component;

@Component
public class Alien {

    public void code() {
        System.out.println("Coding");
    }
}
```

Now Spring knows that `Alien` is a component that it should manage.

Conceptually:

```text
@Component
     │
     ▼
Spring detects Alien
     │
     ▼
Creates Alien object
     │
     ▼
Stores it in IoC Container
     │
     ▼
Alien becomes a Spring Bean
```

---

# 28. Getting a Bean From the Container

`SpringApplication.run()` returns an application context.

The context gives us access to the Spring container.

For example:

```java
ApplicationContext context =
        SpringApplication.run(SpringBootDemoApplication.class, args);
```

The `ApplicationContext` is an important Spring interface used to access beans and other application information.

Now we can ask Spring for the `Alien` bean.

```java
Alien alien = context.getBean(Alien.class);
```

Then:

```java
alien.code();
```

Complete example:

```java
@SpringBootApplication
public class SpringBootDemoApplication {

    public static void main(String[] args) {

        ApplicationContext context =
                SpringApplication.run(
                        SpringBootDemoApplication.class,
                        args
                );

        Alien alien = context.getBean(Alien.class);

        alien.code();
    }
}
```

Output:

```text
Coding
```

---

# 29. What Happened Here?

We did **not** write:

```java
Alien alien = new Alien();
```

Instead, we wrote:

```java
Alien alien = context.getBean(Alien.class);
```

Spring created and managed the object.

The process is:

```text
Application starts
       │
       ▼
Spring container starts
       │
       ▼
Spring finds @Component
       │
       ▼
Spring creates Alien object
       │
       ▼
Alien is stored as a Bean
       │
       ▼
context.getBean(Alien.class)
       │
       ▼
Application receives Alien object
```

This is the basic idea behind Spring's IoC and Dependency Injection system.

---

# 30. Why Does `@Component` Matter?

Spring does not automatically create objects for every class in your application.

Imagine an application contains 100 classes.

You may not want Spring to create and manage all 100 objects.

Therefore, you tell Spring which classes it should manage.

One simple way is:

```java
@Component
```

For example:

```java
@Component
public class Alien {
}
```

Now Spring knows that this class should be managed.

Without `@Component`, calling:

```java
context.getBean(Alien.class);
```

will fail if there is no other configuration telling Spring to create that bean.

---

# 31. Multiple Objects and Beans

You can call:

```java
Alien alien1 = context.getBean(Alien.class);
Alien alien2 = context.getBean(Alien.class);
```

Both calls ask Spring for the `Alien` bean.

For example:

```java
alien1.code();
alien2.code();
```

The method will run for both references.

An important question is:

> Are `alien1` and `alien2` the same object or different objects?

The transcript introduces this question but does not explain the answer in this section.

The important point here is that both objects are requested from the Spring container.

---

# 32. Dependency Between Two Classes

Now consider a more realistic example.

An `Alien` needs a `Laptop` to work.

```text
Alien
  │
  └── depends on
          │
          ▼
        Laptop
```

For example:

```java
public class Laptop {

    public void compile() {
        System.out.println("Compiling");
    }
}
```

And:

```java
public class Alien {

    private Laptop laptop;

    public void code() {
        laptop.compile();
    }
}
```

Here, `Alien` depends on `Laptop`.

The `Laptop` object is a dependency of `Alien`.

---

# 33. The Problem With Manual Object Creation

Without Spring, you could write:

```java
public class Alien {

    private Laptop laptop = new Laptop();

    public void code() {
        laptop.compile();
    }
}
```

This works, but `Alien` is now responsible for creating its own `Laptop`.

The goal of Dependency Injection is to let Spring provide the dependency.

So instead of:

```java
new Laptop();
```

we want Spring to provide the `Laptop` object.

---

# 34. Making Both Classes Spring Components

First, tell Spring to manage both classes.

### Laptop

```java
@Component
public class Laptop {

    public void compile() {
        System.out.println("Compiling");
    }
}
```

### Alien

```java
@Component
public class Alien {

    private Laptop laptop;

    public void code() {
        laptop.compile();
    }
}
```

The `Laptop` object can now be created by Spring.

However, the `laptop` variable inside `Alien` is still not automatically connected to that object.

We need to tell Spring to wire the dependency.

---

# 35. Autowiring

Spring provides `@Autowired` for this purpose.

```java
@Component
public class Alien {

    @Autowired
    private Laptop laptop;

    public void code() {
        laptop.compile();
    }
}
```

Now Spring knows:

> `Alien` needs a `Laptop`.

Spring searches the container for the required `Laptop` bean and connects it to `Alien`.

This process is called **autowiring**.

Conceptually:

```text
Spring Container
┌─────────────────────┐
│                     │
│   Alien Bean        │
│      │              │
│      │ needs        │
│      ▼              │
│   Laptop Bean       │
│                     │
└─────────────────────┘
```

---

# 36. How the Complete Example Works

### Laptop

```java
@Component
public class Laptop {

    public void compile() {
        System.out.println("Compiling");
    }
}
```

### Alien

```java
@Component
public class Alien {

    @Autowired
    private Laptop laptop;

    public void code() {
        laptop.compile();
    }
}
```

### Main Application

```java
@SpringBootApplication
public class SpringBootDemoApplication {

    public static void main(String[] args) {

        ApplicationContext context =
                SpringApplication.run(
                        SpringBootDemoApplication.class,
                        args
                );

        Alien alien = context.getBean(Alien.class);

        alien.code();
    }
}
```

Output:

```text
Compiling
```

---

# 37. What Is Happening Behind the Scenes?

There are several steps.

### Step 1 — Application starts

```java
SpringApplication.run(...)
```

starts Spring.

### Step 2 — Spring creates the container

The IoC container is started.

### Step 3 — Spring finds components

Spring finds classes such as:

```java
@Component
public class Alien {
}
```

and:

```java
@Component
public class Laptop {
}
```

### Step 4 — Spring creates beans

Spring creates:

```text
Alien Bean
Laptop Bean
```

### Step 5 — Spring sees the dependency

Spring sees:

```java
@Autowired
private Laptop laptop;
```

### Step 6 — Spring connects the dependency

Spring injects the `Laptop` bean into the `Alien` bean.

### Step 7 — Application gets Alien

The main method asks:

```java
context.getBean(Alien.class);
```

### Step 8 — Alien uses Laptop

When:

```java
alien.code();
```

runs, the method calls:

```java
laptop.compile();
```

and the output is:

```text
Compiling
```

---

# 38. Multiple Levels of Dependencies

Dependencies can have multiple levels.

For example:

```text
Main Application
       │
       ▼
     Alien
       │
       ▼
     Laptop
       │
       ▼
      CPU
```

Here:

- Main application needs `Alien`.
- `Alien` needs `Laptop`.
- `Laptop` needs `CPU`.

Spring can manage these relationships.

For example, you could create:

```java
@Component
public class CPU {

    public void process() {
        System.out.println("Processing");
    }
}
```

Then inject it into `Laptop`.

The same basic dependency-management idea continues at each level.

---

# 39. Important Annotations Introduced

## `@Component`

Marks a class as a Spring-managed component.

Example:

```java
@Component
public class Laptop {
}
```

Spring can create and manage an object of this class.

---

## `@Autowired`

Tells Spring to inject a required dependency.

Example:

```java
@Autowired
private Laptop laptop;
```

Spring looks for a suitable `Laptop` bean and injects it.

---

## `@SpringBootApplication`

This is used on the main Spring Boot application class.

Example:

```java
@SpringBootApplication
public class SpringBootDemoApplication {
}
```

It is used to configure and start the Spring Boot application.

---

# 40. Important Classes and Interfaces

## `SpringApplication`

Used to start a Spring Boot application.

Example:

```java
SpringApplication.run(SpringBootDemoApplication.class, args);
```

---

## `ApplicationContext`

Provides access to the Spring application context and its beans.

Example:

```java
ApplicationContext context =
        SpringApplication.run(
                SpringBootDemoApplication.class,
                args
        );
```

Then:

```java
Alien alien = context.getBean(Alien.class);
```

---

# 41. Three Common Ways to Configure Spring

Spring applications can be configured in different ways.

The transcript introduces three approaches:

1. XML configuration
2. Java-based configuration
3. Annotation-based configuration

The examples in this section use annotations such as:

```java
@Component
```

and:

```java
@Autowired
```

Spring Boot makes annotation-based configuration especially convenient.

---

# 42. Spring Boot vs Spring Framework — Quick Comparison

| Spring Framework | Spring Boot |
|---|---|
| Core Spring framework | Built on top of Spring Framework |
| Can require more configuration | Reduces configuration |
| Provides IoC and DI | Makes using Spring easier |
| Provides many core features | Provides convenient project setup and defaults |
| More manual setup can be required | Ready-to-run project structure |

Remember:

> **Spring Boot does not replace Spring Framework. Spring Boot makes working with Spring easier.**

---

# 43. Complete Beginner Flow

The main concepts from this tutorial can be connected like this:

```text
Java Application
       │
       ▼
Spring Framework
       │
       ├── IoC
       │
       └── Dependency Injection
               │
               ▼
        Spring IoC Container
               │
               ├── Alien Bean
               ├── Laptop Bean
               └── CPU Bean
               │
               ▼
        Dependencies are wired
               │
               ▼
       Application uses beans
```

Spring Boot makes starting this application easier:

```text
Spring Boot
    │
    ├── Creates project structure
    ├── Provides defaults
    ├── Reduces configuration
    └── Starts Spring Framework
              │
              ▼
        Spring IoC Container
              │
              ▼
       Beans + Dependency Injection
```

---

# 44. Key Points to Remember

1. **Spring is a Java framework and ecosystem.**
2. Spring can be used for web applications, microservices, cloud applications, reactive applications, event-driven applications, and more.
3. Spring works with normal Java objects, including POJOs.
4. Spring has many projects such as Spring Boot, Spring Data, Spring Cloud, Spring Security, and Spring Batch.
5. Good core Java knowledge is important before learning Spring.
6. JDBC, Maven, ORM/Hibernate, and basic Servlet knowledge are useful prerequisites.
7. Spring Framework 6 requires Java 17 or newer.
8. **IoC means Inversion of Control.**
9. IoC means giving control of object creation and management to another system.
10. Spring uses an **IoC container** to create and manage objects.
11. A Spring-managed object is called a **Spring Bean**.
12. **Dependency Injection** is a technique used to implement IoC.
13. A dependency is an object that another object needs.
14. `@Component` tells Spring to manage a class.
15. `ApplicationContext` provides access to Spring-managed beans.
16. `getBean()` can be used to retrieve a bean from the container.
17. `@Autowired` can be used to connect a dependency to another bean.
18. Spring Boot is built on top of Spring Framework.
19. Spring Boot reduces configuration and makes Spring applications easier to create.
20. Spring Initializr can generate a Spring Boot project structure.
21. Maven is a common build tool for Spring projects.
22. Spring Boot 3 works with Spring Framework 6.
