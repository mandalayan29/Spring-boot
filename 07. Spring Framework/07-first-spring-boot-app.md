# Creating Your First Spring Boot Application

There are two ways to set up a new Spring Boot project:

1. Directly from your **IDE** (if it has Spring support, like Eclipse with the Spring Tools plugin).
2. Using the website **start.spring.io** (works with any IDE, including IntelliJ Community edition).

## Option 1: Creating a Project in Eclipse

Since Eclipse has the Spring Tools plugin installed, you get a **"Spring Starter Project"** option directly.

Steps:
1. Click **Create Project** → choose **Spring Starter Project**
2. You'll notice it uses a URL behind the scenes: **start.spring.io**
3. Fill in the project details:

| Field | Example Value |
|---|---|
| Name | SpringBootFirst |
| Build Type | Maven |
| Packaging | Jar |
| Java Version | 17 |
| Group | com.telusko |
| Artifact | (your project name) |
| Package | com.telusko.app |

4. Click **Next** — this is where you can add extra features/dependencies (like Web, JPA, WebSocket). Since Spring is **modular**, it only adds what you choose.
5. For your **first simple project, don't add any dependency**. Just click **Next**, then **Finish**.

Eclipse will now generate your project using files downloaded from `start.spring.io`.

## Option 2: Creating a Project via start.spring.io (For Any IDE)

If your IDE (like IntelliJ Community edition) doesn't have built-in Spring project support, use the website directly:

1. Go to **start.spring.io**
2. Fill in the same kind of details:
   - **Project:** Maven
   - **Language:** Java
   - **Spring Boot version:** choose a stable version (avoid "snapshot" versions)
   - **Group:** com.telusko
   - **Artifact:** SpringBootDemo
   - **Package name:** com.telusko.app
   - **Java version:** 17
3. Skip adding dependencies for now.
4. Click **Generate** — this downloads a **`.zip` file**.
5. **Unzip** the downloaded file.
6. Open your IDE (e.g., IntelliJ) → **Open** → select the unzipped folder.

Your project will load with the standard Spring Boot structure.

## Understanding the Generated Project

Once opened, check the **`pom.xml`** file (Maven's configuration file). You will notice:

- A **Spring Boot Starter** dependency was added automatically.
- If you expand the project's external libraries, you'll also see **Spring Framework** dependencies (version 6) — confirming that **Spring Boot 3 runs on Spring Framework 6**.

## Running "Hello World"

Every generated Spring Boot project comes with a main application file, something like:

```java
@SpringBootApplication
public class SpringBootDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootDemoApplication.class, args);
        System.out.println("Hello World");
    }
}
```

Run this file. You should see:
- A stylized "Spring" startup banner in the console (a signature Spring Boot feature)
- The Spring Boot version and Java version being used
- Your printed output: `Hello World`

> **What's next?** Printing "Hello World" is just a basic check that your setup works. The real purpose — **Dependency Injection** — hasn't been used yet. That comes next.
