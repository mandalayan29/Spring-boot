# Introduction to Spring Framework

## What is Spring?

When you want to build a big, enterprise-level application in Java, you need a **framework**. A framework gives you ready-made tools and structure so you don't build everything from scratch.

In Java, **Spring** is one of the best frameworks available for building applications.

Before Spring became popular, developers used separate tools for separate jobs:

| Need | Old Tool Used |
|---|---|
| Enterprise components | EJB |
| Web applications | Struts |
| Database access (ORM) | Hibernate |

The problem: you had to learn and combine many different tools. Spring solves this by giving you **everything in one framework**.

## Spring is Lightweight

Spring is called a **lightweight framework** because it works with **POJOs** (Plain Old Java Objects) — normal, simple Java objects. You don't need special classes or complex setups. A normal object can do a lot when Spring manages it.

## What Spring Can Do

According to Spring's official website (spring.io), Spring helps make Java:
- **Productive**
- **Reactive** (supports reactive programming)
- **Simple and modern**

Using Spring, you can build:
- Microservices
- Reactive applications
- Cloud applications
- Web applications
- Serverless applications
- Event-driven applications
- Batch applications

## Spring is an Ecosystem, Not Just One Thing

When Spring was first launched, it was mainly about **one module: Dependency Injection**. Over time, Spring grew into many projects. So today, Spring is not a single tool — it's a whole **ecosystem** of projects.

Some well-known Spring projects:
- Spring Boot
- Spring Framework (the core)
- Spring Data
- Spring Cloud
- Spring Security
- Spring for GraphQL
- Spring Batch

New projects keep getting added over time (for example, support for Android, Scala, and Kotlin).

## Spring Boot vs Spring Framework (Quick Note)

You will often hear "Spring" and "Spring Boot" together. They are related but different:

- **Spring Framework** — the core framework.
- **Spring Boot** — built **on top of** Spring Framework. It makes Spring much easier to use.

> **Note:** At this early stage, we only focus on understanding Spring Framework basics. Spring Boot will be covered once the foundation is clear.

## What This Course Will Cover

```
Spring Framework
      │
      ▼
  Spring Boot
      │
      ▼
  Spring ORM
      │
      ▼
  Spring AOP
      │
      ▼
 Spring Security
      │
      ▼
  Microservices
```

By the end, you will understand why Spring is so widely used for building enterprise applications.
