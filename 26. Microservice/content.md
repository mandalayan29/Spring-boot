# Spring Boot Microservices Tutorial
### From a Monolithic Quiz App to the Question Microservice

---

## Table of Contents

1. [What are Microservices?](#1-what-are-microservices)
2. [Cloud Native and the 12-Factor App](#2-cloud-native-and-the-12-factor-app)
3. [Build a Monolithic Quiz Application](#3-build-a-monolithic-quiz-application)
4. [Why We Must Split the Monolith](#4-why-we-must-split-the-monolith)
5. [Create the Question Microservice](#5-create-the-question-microservice)
6. [Final Summary](#6-final-summary)

---

## 1. What are Microservices?

### 1.1 Applications are made of services

Think about a shopping website like Amazon. It has many features (we call them **services**):

- Search for a product
- Add a product to the cart
- Pay online
- Create an account
- Sell your own product (marketplace)

All these features together make one big application.

### 1.2 Monolithic Architecture

In a **monolithic** application, all services are built and packaged **together as one unit**.

- In Java, this is usually one `.war` or `.jar` file.
- You deploy this one file on one server or cloud.

```
+------------------------------------+
|        ONE BIG APPLICATION         |
|  +--------+ +--------+ +--------+  |
|  | Search | | Cart   | | Payment|  |
|  +--------+ +--------+ +--------+  |
|  +--------+ +--------+             |
|  | Account| | Seller |             |
|  +--------+ +--------+             |
+------------------------------------+
        Deployed as ONE package
```

**Advantage**

- Everything is in one place. It is easy to group and deploy.

**Drawbacks (problems)**

| Problem | Explanation |
|---|---|
| **Team dependency** | If 10 teams work on one project, they must wait for each other. Everyone must agree on the release date. |
| **Scaling is wasteful** | During a big sale, only *search* and *payment* need more power. But in a monolith you must scale the **whole** application. |
| **One technology only** | If the app is in Java, every team must use Java. A team that wants Node.js cannot use it. |
| **One bug can crash everything** | A small mistake in one module can bring the full application down. |

### 1.3 Microservices Architecture

In **microservices**, each service is a **small, separate application**.

```
   +--------+   +--------+   +---------+   +---------+
   | Search |   |  Cart  |   | Payment |   | Account |
   |Service |   |Service |   | Service |   | Service |
   | (Java) |   | (Node) |   | (Java)  |   | (Java)  |
   +--------+   +--------+   +---------+   +---------+
     Each one is deployed and scaled on its own
```

Rules for a microservice:

- It is **self-contained** (it does not depend on other services to run).
- It can be **deployed separately**.
- It can be **scaled separately**.

**Benefits**

- **Different technologies:** One service in Java, another in Node.js.
- **Scale only what you need:** For example, run 10 copies of the payment service and 5 copies of the search service.
- **Failure is limited:** If one service goes down, the rest of the application still works.
- **Independent teams:** Each team owns one or more services.

### 1.4 Challenges of Microservices

Microservices look great, but they bring new problems:

1. **Communication:** Services must talk to each other (usually with HTTP request/response through endpoints). This needs configuration.
2. **Service Discovery:** How does one service find the address of another?
3. **API Gateway:** One entry door in front of all services.
4. **Resilience:** What happens if a service fails? You need a fallback plan.
5. **Design:** You must design the architecture *before* writing code. A bad design gives you a system worse than a monolith.
6. **Security:** When a request comes in, which services should it reach? Does this user have permission?

> Big companies use microservices at a very large scale, even with these challenges.

---

## 2. Cloud Native and the 12-Factor App

### 2.1 Cloud-Ready vs Cloud-Native

Today most applications run on the **cloud** (Google Docs, Gmail, Dropbox, and many company apps).

| Term | Meaning |
|---|---|
| **Cloud-ready** | An **old** application (built for your own server) that you changed a little so it can run on the cloud. Example: moving settings to environment variables. |
| **Cloud-native** | An application **built from the start for the cloud**, so it uses all cloud benefits (cost, scaling, fewer issues). |

To build a cloud-native application, we follow a set of rules created by Heroku called the **12-Factor App**.

### 2.2 The 12 Factors (simple explanation)

**1. Codebase**
- One codebase per application, stored in version control (like Git/GitHub/GitLab).
- Many deployments (dev, staging, production) come from that **same** codebase.
- Not many codebases for one app, and not many apps in one codebase.

**2. Dependencies**
- Do not copy library files into the project folder.
- Declare dependencies in a manifest file (in Java: `pom.xml` with Maven) with names and versions.
- Other people download them using that file. This avoids version problems (2.5 vs 2.6).

**3. Configuration**
- Do **not** hard-code database URL, username, password, or port in code.
- Keep them in the environment (environment variables or config files).
- You can change servers without changing source code.

**4. Backing Services**
- Treat database or third-party services as **attached resources**.
- Keep them loosely coupled. Switching from MySQL to PostgreSQL should be easy.

**5. Build, Release, Run**
- Keep these three stages separate:
  - **Build:** create the package (for example with Maven).
  - **Release:** combine the package with configuration, and give it a version (5.4, 5.5...).
  - **Run:** run the release in the environment.
- Never change code in the running environment. If something fails, go back to the old version.

**6. Processes (Stateless)**
- Do not store user data inside the application process (no sticky sessions).
- Keep data in a database or other storage.
- Then you can remove or add process copies at any time without losing data.

**7. Port Binding**
- Each service is exposed through its own **port number**.
- This is why every microservice has a different port.

**8. Concurrency**
- Do not depend only on vertical scaling (bigger machine).
- Use **horizontal scaling**: run more copies (instances) of the same service.

**9. Disposability**
- Start fast and shut down gracefully.
- Close connections properly and do not lose data, even in a crash.

**10. Dev/Prod Parity**
- Keep development, staging, and production as similar as possible.
- Use DevOps ideas, CI/CD, and containers like Docker so the app behaves the same everywhere.

**11. Logs**
- Do not depend on `System.out.println`.
- Each service writes logs, and a logging service collects them in one place.

**12. Admin Processes**
- You should be able to manage the application from outside (admin tasks through an exposed port or service).

> **Tip:** Think "cloud first". Do not build only for your development machine.

---

## 3. Build a Monolithic Quiz Application

We first build a **monolithic** quiz app. Later we will break it into microservices.
The focus is on learning the tools, so the example is kept simple.

### 3.1 What the application does

- A **question** part: create, read, update, delete (CRUD) questions.
- A **quiz** part: create a quiz with random questions of one topic, show the quiz to a user, and calculate the score.

### 3.2 Create the project

Go to **start.spring.io** and choose:

| Setting | Value |
|---|---|
| Project | Maven |
| Language | Java |
| Spring Boot | 3.1.x (any stable version) |
| Group | `com.telusko` |
| Artifact | `quizapp` |
| Packaging | Jar |
| Java | 17 |

**Dependencies:**

- **Spring Web** → to build REST APIs
- **PostgreSQL Driver** → to connect to the PostgreSQL database
- **Spring Data JPA** → to talk to the database without writing JDBC code
- **Lombok** → to reduce boilerplate code (getters, setters, etc.). This one is optional.

Generate, unzip, and open the project in your IDE.

### 3.3 The database

We use **PostgreSQL**. Database name: `questiondb`. It has one table: `question`.

| Column | Meaning |
|---|---|
| `id` | Primary key |
| `category` | Topic (Java, Python...) |
| `difficultylevel` | easy / medium / difficult |
| `option1` ... `option4` | The four answer options |
| `question_title` | The question text |
| `right_answer` | The correct answer |

### 3.4 Database configuration

Without these settings, Spring Boot fails to start (because we added JPA). Put them in `application.properties`:

```properties
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/questiondb
spring.datasource.username=postgres
spring.datasource.password=YOUR_PASSWORD

# create/update tables automatically
spring.jpa.hibernate.ddl-auto=update

spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
```

- `5432` is the default PostgreSQL port.
- `ddl-auto=update` creates the table if it does not exist.
- Never use a simple password like this in real projects.

### 3.5 Your first REST controller

```java
@RestController
@RequestMapping("question")
public class QuestionController {

    @GetMapping("allQuestions")
    public String getAllQuestions() {
        return "hi, these are your questions";
    }
}
```

- `@RestController` → this class handles web requests and returns data.
- `@RequestMapping("question")` → every URL in this class starts with `/question`.
- `@GetMapping("allQuestions")` → handles `GET /question/allQuestions`.

Run it and open `http://localhost:8080/question/allQuestions` in the browser. (8080 is the default port.)

### 3.6 The layered architecture

A web application is split into layers. Each layer has **one job**.

```
Client (browser / mobile / Postman)
        |
        v
+----------------+
|   Controller   |  -> accepts the request, sends the response
+----------------+
        |
        v
+----------------+
|    Service     |  -> business logic (calculations, rules)
+----------------+
        |
        v
+----------------+
|      DAO       |  -> talks to the database
+----------------+
        |
        v
    Database
```

Put each type of class in its own package: `controller`, `service`, `dao`, `model`.

### 3.7 The Model (Entity) class

A class that matches a table is called an **entity** (or model).

- Class name ↔ table name
- Fields ↔ columns
- One object ↔ one row

This is called **ORM** (Object Relational Mapping).

```java
@Data       // Lombok: creates getters, setters, toString
@Entity     // maps this class to the "question" table
public class Question {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    private String category;
    private String difficultylevel;
    private String option1;
    private String option2;
    private String option3;
    private String option4;
    private String questionTitle;   // maps to column question_title
    private String rightAnswer;     // maps to column right_answer
}
```

Important points:

- `@Id` → this is the primary key.
- `@GeneratedValue(strategy = GenerationType.IDENTITY)` → the database creates the id automatically.
  - In the video, `SEQUENCE` gave a "duplicate key" error. `IDENTITY` worked because the table column was created as auto-increment. Use the strategy that matches your table.
- Java uses `camelCase` (`questionTitle`), SQL uses `snake_case` (`question_title`). JPA converts between them automatically.
- Without Lombok you would have to write getters, setters, and `toString()` by hand.

### 3.8 DAO layer with Spring Data JPA

Normally, with JDBC, you must write many steps: connect, write SQL, loop through results, convert to objects. Spring Data JPA does all this for you.

You only create an **interface** and extend `JpaRepository`:

```java
@Repository
public interface QuestionDao extends JpaRepository<Question, Integer> {
}
```

`JpaRepository<Question, Integer>` needs two things:

1. The entity class (`Question`)
2. The type of the primary key (`Integer`)

You immediately get methods like `findAll()`, `findById()`, `save()`, `deleteById()`, and more. No code needed.

### 3.9 Service layer

```java
@Service
public class QuestionService {

    @Autowired
    QuestionDao questionDao;

    public List<Question> getAllQuestions() {
        return questionDao.findAll();
    }
}
```

- `@Service` tells Spring to create and manage this object.
- `@Autowired` asks Spring to give you the object (so you do not write `new`).
- (The IDE warns that field injection is not recommended. Constructor injection is better, but field injection is fine for learning.)

Now the controller calls the service:

```java
@RestController
@RequestMapping("question")
public class QuestionController {

    @Autowired
    QuestionService questionService;

    @GetMapping("allQuestions")
    public List<Question> getAllQuestions() {
        return questionService.getAllQuestions();
    }
}
```

Open the URL again and you will see all questions from the database as JSON.

### 3.10 Get questions by category

URL example: `/question/category/Java`

```java
// Controller
@GetMapping("category/{category}")
public List<Question> getQuestionsByCategory(@PathVariable String category) {
    return questionService.getQuestionsByCategory(category);
}
```

- `{category}` is a **variable part** of the URL.
- `@PathVariable` copies the URL value into the method parameter.
- If the names are different (`{cat}` and `String category`) you write `@PathVariable("cat")`.

```java
// Service
public List<Question> getQuestionsByCategory(String category) {
    return questionDao.findByCategory(category);
}
```

```java
// DAO
List<Question> findByCategory(String category);
```

**Magic of JPA:** You only write the method name `findByCategory`. Because `category` is a field of `Question`, JPA creates the SQL query for you. This is called a **derived query method**.

For very custom queries you need `@Query` with SQL, HQL, or JPQL (shown later).

If you ask for a category that has no data (e.g. `Kotlin`), you get an empty list.

### 3.11 Add a question (POST request)

```java
// Controller
@PostMapping("add")
public String addQuestion(@RequestBody Question question) {
    return questionService.addQuestion(question);
}
```

```java
// Service
public String addQuestion(Question question) {
    questionDao.save(question);
    return "success";
}
```

- `@PostMapping` is used when the client **sends** data to the server (GET is used to **fetch** data).
- `@RequestBody` tells Spring: "The request body contains JSON. Convert it into a `Question` object."
- `save()` inserts the data into the table.

**Delete and Update (practice):**

- Use `@DeleteMapping` and `deleteById()` for delete.
- Use `@PutMapping` and `save()` for update. (`save()` works for both insert and update. If the id exists, it updates.)

### 3.12 Test with Postman

The browser address bar can only send GET requests. To test POST, use **Postman** (or any API tool).

1. Select method **POST**.
2. URL: `http://localhost:8080/question/add`
3. Open **Body → raw → JSON** and send:

```json
{
  "category": "Java",
  "difficultylevel": "easy",
  "option1": "100",
  "option2": "127",
  "option3": "255",
  "option4": "999",
  "questionTitle": "What is the maximum value of byte in Java?",
  "rightAnswer": "127"
}
```

Do not send the `id`. The database creates it.

Common errors you may see:

- **405 Method Not Allowed:** You used GET on a POST URL.
- **400 Bad Request:** You did not send a body.

### 3.13 HTTP status codes and `ResponseEntity`

A good API returns data **and** a status code.

| Range | Meaning | Examples |
|---|---|---|
| 100–199 | Informational | |
| 200–299 | Success | `200 OK`, `201 Created` |
| 300–399 | Redirection | |
| 400–499 | **Client** error | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `405 Method Not Allowed` |
| 500–599 | **Server** error | `500 Internal Server Error`, `502 Bad Gateway` |

To send both data and a status, return `ResponseEntity<T>`:

```java
// Service
public ResponseEntity<List<Question>> getAllQuestions() {
    try {
        return new ResponseEntity<>(questionDao.findAll(), HttpStatus.OK);
    } catch (Exception e) {
        e.printStackTrace();
        return new ResponseEntity<>(new ArrayList<>(), HttpStatus.BAD_REQUEST);
    }
}
```

```java
// Controller
@GetMapping("allQuestions")
public ResponseEntity<List<Question>> getAllQuestions() {
    return questionService.getAllQuestions();
}
```

Explanation:

- `new ResponseEntity<>(data, status)` takes **two** things: the body and the status code.
- `try/catch` handles exceptions. If something fails, we return an empty list with `BAD_REQUEST`.
- Use `HttpStatus.OK` for reading data.
- Use `HttpStatus.CREATED` (201) when you create something:

```java
public ResponseEntity<String> addQuestion(Question question) {
    questionDao.save(question);
    return new ResponseEntity<>("success", HttpStatus.CREATED);
}
```

The client (web or mobile app) uses the status code to show a proper message to the user.

### 3.14 Create a Quiz — the data design

A quiz has a title and many questions. The same question can appear in more than one quiz. So the relationship is **Many-to-Many**.

| Relationship | Meaning | Fits our case? |
|---|---|---|
| One-to-One | 1 quiz ↔ 1 question | No |
| One-to-Many | 1 quiz ↔ many questions (a question belongs to only one quiz) | No |
| **Many-to-Many** | Many quizzes ↔ many questions | **Yes** |

For many-to-many, JPA creates an **extra mapping table** (here `quiz_questions`) automatically.

```java
@Data
@Entity
public class Quiz {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    private String title;

    @ManyToMany
    private List<Question> questions;
}
```

Create the DAO:

```java
@Repository
public interface QuizDao extends JpaRepository<Quiz, Integer> {
}
```

### 3.15 Get random questions with a native query

JPA cannot create "random N questions of a category" from a method name. So we write the query ourselves with `@Query`.

```java
@Repository
public interface QuestionDao extends JpaRepository<Question, Integer> {

    List<Question> findByCategory(String category);

    @Query(value = "SELECT * FROM question q WHERE q.category = :category ORDER BY RANDOM() LIMIT :numQ",
           nativeQuery = true)
    List<Question> findRandomQuestionsByCategory(@Param("category") String category,
                                                 @Param("numQ") int numQ);
}
```

- `nativeQuery = true` → this is real SQL for the database.
- `:category` and `:numQ` → the method parameters are placed here (colon + name).
- `ORDER BY RANDOM()` → shuffles the rows. `LIMIT` → takes only the number we want.

### 3.16 Quiz creation (Controller + Service)

```java
@RestController
@RequestMapping("quiz")
public class QuizController {

    @Autowired
    QuizService quizService;

    @PostMapping("create")
    public ResponseEntity<String> createQuiz(@RequestParam String category,
                                             @RequestParam int numQ,
                                             @RequestParam String title) {
        return quizService.createQuiz(category, numQ, title);
    }
}
```

- `@RequestParam` reads values from the URL query string:
  `POST /quiz/create?category=Java&numQ=5&title=JQuiz`

```java
@Service
public class QuizService {

    @Autowired
    QuizDao quizDao;

    @Autowired
    QuestionDao questionDao;

    public ResponseEntity<String> createQuiz(String category, int numQ, String title) {
        List<Question> questions = questionDao.findRandomQuestionsByCategory(category, numQ);

        Quiz quiz = new Quiz();
        quiz.setTitle(title);
        quiz.setQuestions(questions);
        quizDao.save(quiz);

        return new ResponseEntity<>("Success", HttpStatus.CREATED);
    }
}
```

Steps:

1. Get random questions from `QuestionDao`.
2. Create a `Quiz` object and set title and questions.
3. Save it with `QuizDao`.

After this, two new tables appear in the database: `quiz` (id, title) and `quiz_questions` (quiz id, question id).

### 3.17 Show the quiz to the user — `QuestionWrapper`

A `Question` has the **right answer**. We must **not** send the answer to the client (it would be a security problem).

So we create a **wrapper class** with only the safe fields:

```java
@Data
@AllArgsConstructor     // Lombok: constructor with all fields
public class QuestionWrapper {
    private Integer id;
    private String questionTitle;
    private String option1;
    private String option2;
    private String option3;
    private String option4;
}
```

Controller:

```java
@GetMapping("get/{id}")
public ResponseEntity<List<QuestionWrapper>> getQuizQuestions(@PathVariable Integer id) {
    return quizService.getQuizQuestions(id);
}
```

Service:

```java
public ResponseEntity<List<QuestionWrapper>> getQuizQuestions(Integer id) {
    Optional<Quiz> quiz = quizDao.findById(id);
    List<Question> questionsFromDB = quiz.get().getQuestions();

    List<QuestionWrapper> questionsForUser = new ArrayList<>();
    for (Question q : questionsFromDB) {
        QuestionWrapper qw = new QuestionWrapper(
                q.getId(), q.getQuestionTitle(),
                q.getOption1(), q.getOption2(), q.getOption3(), q.getOption4());
        questionsForUser.add(qw);
    }
    return new ResponseEntity<>(questionsForUser, HttpStatus.OK);
}
```

Key ideas:

- `findById()` returns an `Optional` because the id may not exist. `Optional` helps avoid null errors.
- `quiz.get()` gets the real object. (Better: check `quiz.isPresent()` first.)
- We loop through each `Question` and copy the safe fields into a `QuestionWrapper`.

### 3.18 Submit the quiz and calculate the score

The client sends only the **question id** and the **user's response**.

**Response class:**

```java
@Data
public class Response {
    private Integer id;
    private String response;
}
```

> Tip: Lombok has its own `Response`-like names in some IDEs. Make sure you import **your own** `model.Response`.

**Request example:**

`POST /quiz/submit/1` with body:

```json
[
  { "id": 18, "response": "3" },
  { "id": 8,  "response": "..." }
]
```

Controller:

```java
@PostMapping("submit/{id}")
public ResponseEntity<Integer> submitQuiz(@PathVariable Integer id,
                                          @RequestBody List<Response> responses) {
    return quizService.calculateResult(id, responses);
}
```

Service:

```java
public ResponseEntity<Integer> calculateResult(Integer id, List<Response> responses) {
    Quiz quiz = quizDao.findById(id).get();
    List<Question> questions = quiz.getQuestions();

    int right = 0;
    int i = 0;
    for (Response response : responses) {
        if (response.getResponse().equals(questions.get(i).getRightAnswer())) {
            right++;
        }
        i++;
    }
    return new ResponseEntity<>(right, HttpStatus.OK);
}
```

How it works:

1. Load the quiz and its questions.
2. Go through each response. Compare it with the question's `rightAnswer`.
3. Count the correct ones and return the score.

> We compare the **answer text**, not "A/B/C/D". This is safer if you shuffle options later.

At this point we have a complete **monolithic** application.

---

## 4. Why We Must Split the Monolith

In our monolith, the quiz part **directly uses** `QuestionDao`. They are tightly coupled.

We want two microservices:

```
Client
  |
  v
+-------------+   asks for questions   +------------------+
| Quiz Service| ---------------------> | Question Service |
|  (quiz DB)  |                        |  (question DB)   |
+-------------+                        +------------------+
```

### 4.1 What changes?

- Each service has its **own database**.
  - `questiondb` for the question service.
  - A separate quiz database for the quiz service.
- The quiz service **cannot** use `QuestionDao` anymore. It must call the question service **over the network**.
- Each service can be **scaled separately**. Example: 10 quiz instances, 2 question instances.

### 4.2 New problems and the tools that solve them

| Problem | Tool / Concept |
|---|---|
| Many instances have different IP addresses. How does a service find another? | **Service Registry** (Service Discovery) |
| Which instance should receive the call? | **Load Balancer** |
| Client should not remember every service URL | **API Gateway** (one door for all services) |
| What if the question service is down? Quiz service should not wait forever | **Circuit Breaker** (fail fast + fallback) |
| Services must call each other easily | **OpenFeign** (HTTP client) |

> Building microservices is easy. **Connecting them well** is the real work. We will use these tools step by step.

---

## 5. Create the Question Microservice

### 5.1 Create a new project

Every microservice is its **own separate project**. Create a new project on start.spring.io:

| Setting | Value |
|---|---|
| Group | `com.telusko` |
| Artifact | `question-service` |
| Spring Boot | 3.1.x (stable) |

**Dependencies:** Spring Web, PostgreSQL Driver, Lombok, Spring Data JPA, **OpenFeign**, **Eureka Client**.

> OpenFeign and Eureka Client are needed **later**. After downloading, **comment them out** in `pom.xml` for now, so you can simply uncomment them when needed. After any change in `pom.xml`, reload Maven.

### 5.2 Copy only what this service needs

Copy the code from the monolith and then **delete what does not belong** to the question service.

| Keep | Delete |
|---|---|
| `QuestionController` | `QuizController` |
| `QuestionService` | `QuizService` |
| `QuestionDao` | `QuizDao` |
| `Question`, `QuestionWrapper`, `Response` (models) | `Quiz` (model) |

Also copy `application.properties` (database settings for `questiondb`). Fix the imports. Run the app and test `GET /question/allQuestions` to be sure everything works.

### 5.3 What the question service must offer

In the monolith, the quiz service picked questions and calculated the score by itself. Now the question service owns the question data, so it must provide **three APIs** for the quiz service:

| # | API | Job |
|---|---|---|
| 1 | `generate` | Return **only question ids** for a category and number of questions |
| 2 | `getQuestions` | Return questions (**without answers**) for a list of question ids |
| 3 | `getScore` | Return the score for a list of responses |

Flow:

```
Quiz Service                             Question Service
    |                                           |
    |--- generate (category, numQuestions) ---> |  picks random question ids
    | <------------- [18, 8, 17, 6, 19] ------- |
    |                                           |
    |--- getQuestions ([18, 8, 17, 6, 19]) ---> |  finds each question
    | <---- list of QuestionWrapper ----------- |  (no right answers)
    |                                           |
    |--- getScore (list of responses) --------> |  compares with right answers
    | <------------------ 4 ------------------- |
```

Why return only ids from `generate`? The quiz service only needs to **remember which questions belong to the quiz**. It does not need the full questions.

### 5.4 API 1 — Generate question ids

**DAO** — change the query to return only `id` (not `*`):

```java
@Query(value = "SELECT q.id FROM question q WHERE q.category = :category ORDER BY RANDOM() LIMIT :numQuestions",
       nativeQuery = true)
List<Integer> findRandomQuestionsByCategory(@Param("category") String category,
                                            @Param("numQuestions") int numQuestions);
```

The return type changed from `List<Question>` to `List<Integer>`.

**Service:**

```java
public ResponseEntity<List<Integer>> getQuestionsForQuiz(String categoryName, Integer numQuestions) {
    List<Integer> questions = questionDao.findRandomQuestionsByCategory(categoryName, numQuestions);
    return new ResponseEntity<>(questions, HttpStatus.OK);
}
```

**Controller:**

```java
@GetMapping("generate")
public ResponseEntity<List<Integer>> getQuestionsForQuiz(
        @RequestParam String categoryName,
        @RequestParam Integer numQuestions) {
    return questionService.getQuestionsForQuiz(categoryName, numQuestions);
}
```

Example call: `GET /question/generate?categoryName=Java&numQuestions=5`

### 5.5 API 2 — Get questions from ids

The quiz service sends a list of ids. We return `QuestionWrapper` objects (no answers).

**Controller:**

```java
@PostMapping("getQuestions")
public ResponseEntity<List<QuestionWrapper>> getQuestionsFromId(@RequestBody List<Integer> questionIds) {
    return questionService.getQuestionsFromId(questionIds);
}
```

- We use `POST` and `@RequestBody` because we are sending a **list** of ids in the body.

**Service:**

```java
public ResponseEntity<List<QuestionWrapper>> getQuestionsFromId(List<Integer> questionIds) {
    List<QuestionWrapper> wrappers = new ArrayList<>();
    List<Question> questions = new ArrayList<>();

    // 1. get each question from the database
    for (Integer id : questionIds) {
        questions.add(questionDao.findById(id).get());
    }

    // 2. copy safe fields into wrappers (no right answer)
    for (Question question : questions) {
        QuestionWrapper wrapper = new QuestionWrapper();
        wrapper.setId(question.getId());
        wrapper.setQuestionTitle(question.getQuestionTitle());
        wrapper.setOption1(question.getOption1());
        wrapper.setOption2(question.getOption2());
        wrapper.setOption3(question.getOption3());
        wrapper.setOption4(question.getOption4());
        wrappers.add(wrapper);
    }

    return new ResponseEntity<>(wrappers, HttpStatus.OK);
}
```

Because we now use `new QuestionWrapper()` (no arguments), the wrapper class needs a **no-argument constructor**:

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class QuestionWrapper {
    private Integer id;
    private String questionTitle;
    private String option1;
    private String option2;
    private String option3;
    private String option4;
}
```

- `findById(id).get()` returns the question (and `Optional` is unwrapped).
- We **copy** values from `Question` to `QuestionWrapper`. We do not convert the object.

### 5.6 API 3 — Get the score

The quiz service sends the user's responses. The question service has the right answers, so it calculates the score.

**Controller:**

```java
@PostMapping("getScore")
public ResponseEntity<Integer> getScore(@RequestBody List<Response> responses) {
    return questionService.getScore(responses);
}
```

**Service:**

```java
public ResponseEntity<Integer> getScore(List<Response> responses) {
    int right = 0;

    for (Response response : responses) {
        Question question = questionDao.findById(response.getId()).get();
        if (response.getResponse().equals(question.getRightAnswer())) {
            right++;
        }
    }
    return new ResponseEntity<>(right, HttpStatus.OK);
}
```

How it differs from the monolith:

- Before, we loaded the whole quiz and used the **list position** to match answers.
- Now, each `Response` has the **question id**. We load that question by id and compare the answer. This is simpler and safer.

### 5.7 The complete `QuestionController`

```java
@RestController
@RequestMapping("question")
public class QuestionController {

    @Autowired
    QuestionService questionService;

    @GetMapping("allQuestions")
    public ResponseEntity<List<Question>> getAllQuestions() {
        return questionService.getAllQuestions();
    }

    @GetMapping("category/{category}")
    public ResponseEntity<List<Question>> getQuestionsByCategory(@PathVariable String category) {
        return questionService.getQuestionsByCategory(category);
    }

    @PostMapping("add")
    public ResponseEntity<String> addQuestion(@RequestBody Question question) {
        return questionService.addQuestion(question);
    }

    // ---- APIs used by the Quiz Service ----

    @GetMapping("generate")
    public ResponseEntity<List<Integer>> getQuestionsForQuiz(
            @RequestParam String categoryName,
            @RequestParam Integer numQuestions) {
        return questionService.getQuestionsForQuiz(categoryName, numQuestions);
    }

    @PostMapping("getQuestions")
    public ResponseEntity<List<QuestionWrapper>> getQuestionsFromId(@RequestBody List<Integer> questionIds) {
        return questionService.getQuestionsFromId(questionIds);
    }

    @PostMapping("getScore")
    public ResponseEntity<Integer> getScore(@RequestBody List<Response> responses) {
        return questionService.getScore(responses);
    }
}
```

---

## 6. Final Summary

**Concepts**

- **Monolith** = one big application. **Microservices** = many small, independent services.
- Microservices give independent deployment, scaling, technology choice, and fault isolation, but need extra tools: **Service Registry, API Gateway, Load Balancer, Circuit Breaker, Feign**.
- **12-Factor App** rules help you build cloud-native applications.

**Spring Boot skills used**

| Topic | What to remember |
|---|---|
| Layers | Controller → Service → DAO → Database |
| `@RestController`, `@RequestMapping` | Build REST APIs |
| `@GetMapping`, `@PostMapping` | Fetch data / send data |
| `@PathVariable` | Read value from URL path (`/category/{category}`) |
| `@RequestParam` | Read value from query string (`?a=1&b=2`) |
| `@RequestBody` | Convert JSON body to a Java object |
| `@Entity`, `@Id`, `@GeneratedValue` | Map class to table |
| `@ManyToMany` | Many quizzes ↔ many questions |
| `JpaRepository` | Ready-made database methods |
| Derived query (`findByCategory`) | JPA writes the query from the method name |
| `@Query(nativeQuery = true)` | Write your own SQL |
| `ResponseEntity` + `HttpStatus` | Return data with a status code |
| DTO / Wrapper class | Send only safe fields to the client |
| Lombok | `@Data`, `@AllArgsConstructor`, `@NoArgsConstructor` |

**Question Microservice APIs**

| API | Method | Input | Output |
|---|---|---|---|
| `/question/generate` | GET | `categoryName`, `numQuestions` | `List<Integer>` (question ids) |
| `/question/getQuestions` | POST | `List<Integer>` (ids) | `List<QuestionWrapper>` |
| `/question/getScore` | POST | `List<Response>` | `Integer` (score) |

**Practice tasks**

1. Add `delete` and `update` APIs for questions.
2. Add proper exception handling (try/catch) to every service method that returns `ResponseEntity`.
3. Use `Optional.isPresent()` instead of calling `.get()` directly.
4. Create your own `question-service` project and test all APIs in Postman.