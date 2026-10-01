# Spring vs Spring Boot

## Why Do We Need Spring Boot?

When Spring Framework was first introduced, it was genuinely useful for the industry. But there was one big problem: **even to print "Hello World," you had to do a lot of configuration.**

To create a basic Spring project, you had to:
- Set up the project manually
- Configure XML files
- Manually define Beans

This required a lot of time and effort just to get started.

## Spring Boot: The Solution

**Spring Boot** was created to solve this problem. It is called an **opinionated framework**, which means it makes sensible default choices for you so that:

- You get a working project structure immediately.
- You don't need heavy manual configuration.
- The project runs correctly on the very first try.

You can still customize things in Spring Boot if you need to — but you don't **have** to configure everything manually like in plain Spring.

## Spring Boot is Built on Spring

This is the most important point to remember:

> **Spring Boot works on top of Spring Framework. It does not replace it.**

Even when you build a project using Spring Boot, you are still using Spring Framework underneath. Spring Boot just removes the repetitive setup work.

```
        ┌───────────────────┐
        │    Spring Boot     │   ← Makes things easy (auto-configuration)
        ├───────────────────┤
        │  Spring Framework  │   ← The actual core framework
        └───────────────────┘
```

## Version Note

- **Spring Framework** version used in this course: **Spring 6**
- **Spring Boot** version used in this course: **Spring Boot 3**

Spring Boot 3 is built on top of Spring Framework 6.

## Learning Approach for This Course

To understand Dependency Injection practically:
1. We will **first write code using Spring Boot** — since it's simpler and quicker.
2. Then, we will look at **what happens behind the scenes using plain Spring Framework** — to understand the real mechanics.

This way, you get a complete understanding of both worlds.
