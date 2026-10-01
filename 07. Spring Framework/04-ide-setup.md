# Setting Up an IDE for Spring

## What You Need

To write and run Spring applications, you need:
1. **JDK** installed (Java Development Kit)
2. An **IDE** (Integrated Development Environment) to write your code

## Checking Your Java Version

Open your terminal (Command Prompt on Windows, or Terminal on Mac/Linux) and run:

```bash
java -version
```

**Important:** Spring 6 requires **Java 17 or above**. If you have Java 21 (a newer version), that also works fine.

## Which IDE Should You Use?

You have several options, and no single one is "required":

- **Eclipse**
- **IntelliJ IDEA**
- **VS Code**
- **NetBeans**

> **Good to know:** Since Spring projects use **Maven**, the project structure will be the same no matter which IDE you choose. Maven creates a standard structure, so switching IDEs later won't break your project or change your code.

## IntelliJ IDEA: Community vs Ultimate

IntelliJ IDEA comes in two versions:

| Version | Cost | Spring Support |
|---|---|---|
| Community | Free | ❌ No built-in Spring project support |
| Ultimate | Paid | ✅ Built-in Spring support |

Many companies provide the Ultimate version license to their developers, but if you don't have it, the **free Community version still works** — you'll just set up Spring projects slightly differently (explained in the next tutorial using `start.spring.io`).

## Setting Up Eclipse for Spring

By default, Eclipse does **not** come with Spring support. You need to add an extension called **Spring Tools 4**:

1. Go to **Help → Eclipse Marketplace**
2. Search for **"Spring Tool 4"**
3. Click **Install**
4. Accept the default settings and confirm
5. Restart Eclipse when prompted

After this, Eclipse will show a **Spring Boot / Spring Starter Project** option when creating a new project.

## Setting Up VS Code for Spring

VS Code now supports Java and Spring well. To enable Spring support:

1. Open the **Extensions** panel
2. Search for **Spring**
3. Install the extension by **VMware** (this is the main Spring Boot extension)
4. This usually also installs **Spring Initializr** support automatically
5. Optionally install **Spring Boot Dashboard** for extra features

## Summary Diagram

```
Choose an IDE
   ├── Eclipse       → Install "Spring Tool 4" plugin
   ├── VS Code        → Install "Spring Boot Extension Pack" (by VMware)
   └── IntelliJ IDEA
         ├── Community → No built-in Spring support (use start.spring.io instead)
         └── Ultimate   → Built-in Spring support
```

> **Tip:** It's completely normal for developers to switch between IDEs over time. Pick one that feels comfortable — the Spring code you write will be the same everywhere.
