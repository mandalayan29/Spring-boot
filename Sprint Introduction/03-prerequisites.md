# Prerequisites Before Learning Spring

Before starting Spring Framework, you should already be comfortable with a few Java basics. Spring is built on top of Java, so weak fundamentals will make Spring harder to understand.

## Required Knowledge

| Topic | Why It's Needed |
|---|---|
| Core Java syntax | Spring code is Java code — you need to read and write it comfortably |
| OOP concepts | Spring is heavily based on objects, interfaces, and design patterns |
| Exception handling | Needed to understand error handling in Spring applications |
| Collection API | Used often while working with data inside Spring applications |
| JDBC (Java Database Connectivity) | Enterprise apps need databases; JDBC is how plain Java talks to a database |
| Maven (or Gradle) | Spring projects need a **build tool** to manage dependencies and project structure. This course uses **Maven** |
| Hibernate / ORM basics | Later, we use **Spring ORM**, which is built on ORM concepts like Hibernate |
| Servlets | Needed to understand how **Spring MVC** (web applications) works under the hood |

> **Note on Threads:** Basic awareness of threads is useful, but it is not critical for starting with Spring.

## A Note About Servlets

You may have heard that **Servlets are outdated** — and that's true. Nobody builds new applications directly using raw Servlets anymore; instead, we use **Spring MVC**.

However, Spring MVC applications still run inside a **Servlet container** (like **Tomcat**) behind the scenes. So, having basic Servlet knowledge helps you understand what's really happening when Spring MVC runs your web application.

## Summary

If you already know:
- Java syntax + OOP + exceptions + collections
- Basic JDBC
- Maven
- Basic ORM/Hibernate idea
- Basic Servlet idea

...then you are ready to start learning Spring Framework.
