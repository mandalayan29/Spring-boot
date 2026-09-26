# IoC (Inversion of Control) and DI (Dependency Injection)

Before writing any Spring code, you must understand two core concepts:

- **IoC** — Inversion of Control
- **DI** — Dependency Injection

These two ideas are related but **not the same thing**.

## The Problem: Too Much Responsibility

Normally in Java, as a programmer, you do everything yourself:
- You **create** objects (using the `new` keyword)
- You **manage** objects
- You **destroy** objects
- You **control the flow** of the application

For example:

```java
Laptop obj = new Laptop();
```

This is easy to write, but it means **you** are responsible for the object's entire lifecycle. The problem is: this takes your focus away from what actually matters — the **business logic** (the real purpose of your application).

Every application is different because of its business logic. So as a programmer, your focus should be on solving business problems — not on the repetitive work of creating and managing objects.

## What is Inversion of Control (IoC)?

**Inversion of Control** means: instead of *you* controlling object creation, you **hand over that control to someone else** — in this case, the **Spring Framework**.

> Earlier: **You** had control over creating objects.
> Now: **Spring** takes control over creating objects.

This is why it's called "inversion" — the control is inverted (reversed) from you to the framework.

## The IoC Container

To make this work, Spring provides something called an **IoC Container**.

Think of the IoC container as a **box** that:
- Creates objects for you
- Stores those objects
- Manages their lifecycle

You, as a programmer, no longer write `new` for these objects. Spring creates them and keeps them inside this container.

```
        ┌─────────────────────────┐
        │      IoC Container       │
        │  (managed by Spring)     │
        │                          │
        │   [Object A]             │
        │   [Object B]             │
        │   [Object C]             │
        └─────────────────────────┘
```

## What is Dependency Injection (DI)?

IoC is a **principle** (a concept/idea). But *how* do we actually make it work in real code? That's where **Dependency Injection** comes in.

DI is a **design pattern** used to implement the IoC principle.

### Example Scenario

Imagine two classes:
- `Laptop`
- `CPU`

A `Laptop` **depends on** a `CPU` — you can't really use a laptop without one. Both objects live inside the Spring container. But how does the `CPU` object get placed inside the `Laptop` object?

That connection — **injecting one object into another** — is called **Dependency Injection**.

## IoC vs DI — Key Difference

| | IoC | DI |
|---|---|---|
| **What it is** | A principle / concept | A design pattern |
| **What it does** | Says "let someone else control object creation" | Provides the actual mechanism to inject dependencies |
| **Relationship** | The goal | The technique used to achieve the goal |

> **Note:** In everyday conversation, developers often use "IoC" and "DI" interchangeably. That's generally fine in practice, but technically, IoC is the idea, and DI is how Spring implements it.

## Summary

- Spring manages object creation for you (this is **Inversion of Control**).
- Objects are stored in the **IoC Container**.
- When one object needs another object, Spring **injects** it — this is **Dependency Injection**.
- Dependency Injection is one of the core features of the Spring Framework, and we'll see how to actually implement it in the next tutorials.
