# 02 – First Spring AI Project: ChatModel, ChatClient, Memory and Ollama

> **Tech:** Spring Boot 3.3+, Java 21, Spring AI 1.0.0, OpenAI, Ollama, Insomnia/Postman

---

## 1. Create the Project

Open **start.spring.io** and choose:

| Setting | Value |
|---------|-------|
| Project | Maven |
| Language | Java |
| Group | `com.telusko` |
| Dependencies | **Spring Web**, **OpenAI** (select the plain *OpenAI*, not *Azure OpenAI*) |

Other providers have their own dependency: type **Anthropic**, **Ollama**, **Vertex AI** and so on.

The OpenAI dependency in `pom.xml` is:

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
</dependency>
```

If you run the app now, it **fails**: *"OpenAI API key must be set"*. We need the key.

---

## 2. Create the OpenAI API Key

1. Log in to the **OpenAI API platform** (not the ChatGPT page).
2. Go to **Settings → Billing** and add some credit (a few dollars is enough). Keep **auto-recharge off** so it does not add money again by itself.
3. Go to **API keys → Create new secret key**. Give it a name and create it.
4. **Copy the key and save it.** You cannot see it again.
5. Never share the key.

Add the key in `application.properties`:

```properties
spring.ai.openai.api-key=${OPENAI_API_KEY}
```

> Keeping the key in an **environment variable** (like above) is safer than writing it in the file. Never push a real key to Git.

Now the app starts without error.

---

## 3. Ask a Question using `ChatModel`

We build a very simple API: send a question in the URL, get the answer from the model.

### Controller

```java
@RestController
public class OpenAIController {

    private final ChatModel chatModel;

    public OpenAIController(OpenAiChatModel chatModel) {
        this.chatModel = chatModel;
    }

    @GetMapping("/api/{message}")
    public String getAnswer(@PathVariable String message) {
        return chatModel.call(message);
    }
}
```

### Explanation

| Part | Meaning |
|------|---------|
| `ChatModel` | Spring AI interface to talk to an LLM. Each provider has its own implementation (`OpenAiChatModel` etc.). |
| Constructor injection | Spring creates the `OpenAiChatModel` bean (because of the starter) and gives it to us. |
| `@PathVariable message` | The question comes from the URL. |
| `chatModel.call(message)` | Sends the text to OpenAI and returns the answer as a `String`. |

**Test:** `GET http://localhost:8080/api/what is Java` → you get the answer from OpenAI. The call takes a few seconds because it goes to the OpenAI server.

`ChatModel` is simple but limited. For more control (system messages, prompts, metadata, advisors) we use **`ChatClient`**.

---

## 4. Using `ChatClient`

`ChatClient` is a **fluent API** built **on top of** `ChatModel`. It does not depend on one provider.

### Way 1: create it from a `ChatModel`

```java
private final ChatClient chatClient;

public OpenAIController(OpenAiChatModel chatModel) {
    this.chatClient = ChatClient.create(chatModel);
}

@GetMapping("/api/{message}")
public ResponseEntity<String> getAnswer(@PathVariable String message) {
    String response = chatClient
            .prompt(message)
            .call()
            .content();
    return ResponseEntity.ok(response);
}
```

### The chain, step by step

| Call | What it does |
|------|--------------|
| `prompt(message)` | Creates a request object (`ChatClientRequestSpec`) with your text. Nothing is sent yet. |
| `call()` | **Now** the request goes to the LLM. Returns `CallResponseSpec`. |
| `content()` | Gets the answer as a `String`. |

`prompt(...)` has three forms: no argument (build later), a `String`, or a `Prompt` object.

`ResponseEntity.ok(response)` lets us send a proper HTTP status with the answer.

---

## 5. `ChatResponse` and Metadata

Instead of `content()`, use `chatResponse()` to get the **full response** with extra data (metadata).

```java
@GetMapping("/api/{message}")
public ResponseEntity<String> getAnswer(@PathVariable String message) {

    ChatResponse chatResponse = chatClient
            .prompt(message)
            .call()
            .chatResponse();

    String response = chatResponse
            .getResult()
            .getOutput()
            .getText();

    System.out.println(chatResponse.getMetadata().getModel());

    return ResponseEntity.ok(response);
}
```

### Explanation

| Call | Meaning |
|------|---------|
| `getResult()` | A `Generation` (one answer from the model). `getResults()` gives all answers if there are many. |
| `getOutput()` | The assistant message. |
| `getText()` | The answer text. |
| `getMetadata()` | Extra data: model name, usage (token count), rate limit, etc. |
| `getMetadata().getModel()` | Prints the model used (for example `gpt-4o-mini`). |

> In real projects, check for `null` (for example, `getMetadata()` can be null). We skip it here to keep the code short.

---

## 6. `ChatClient.Builder` (Auto-configured)

There are two ways to get a `ChatClient`:

| Way | When to use |
|-----|-------------|
| `ChatClient.create(chatModel)` | You have **many models** (OpenAI + Ollama...) and you choose the model yourself. |
| Inject `ChatClient.Builder` | You use **only one model**. Spring Boot auto-configures the builder for it. |

```java
private final ChatClient chatClient;

public OpenAIController(ChatClient.Builder builder) {
    this.chatClient = builder.build();
}
```

If you have more than one model dependency, the auto-configured builder gets confused. Then create the client manually, or disable the builder auto-configuration.

---

## 7. Chat Memory with an Advisor

### The problem: the LLM has no memory

Ask: *"Tell me a joke"* → you get a joke. Then ask: *"more"* → the model says: *"Your request is vague..."*

Each API call is **independent**. ChatGPT and similar apps remember the chat because **they add memory around the model**. In Spring AI we add memory using an **Advisor**.

### What is an Advisor?

An **advisor** sits between your app and the LLM. It can **change or watch** the request before it goes, and the response when it comes back.

```mermaid
flowchart LR
    A[Your app] --> B[Advisor] --> C[LLM]
    C --> B --> A
```

Uses: add chat memory, add your own data (RAG), safety checks, censor text, logging.

### Add memory

```java
public OpenAIController(ChatClient.Builder builder) {

    ChatMemory chatMemory = MessageWindowChatMemory.builder().build();

    this.chatClient = builder
            .defaultAdvisors(MessageChatMemoryAdvisor.builder(chatMemory).build())
            .build();
}
```

| Part | Meaning |
|------|---------|
| `MessageWindowChatMemory` | Keeps the last few messages in memory (in-memory storage). |
| `MessageChatMemoryAdvisor` | Adds the saved conversation to each new request. |
| `defaultAdvisors(...)` | Applies the advisor to **every** call made by this client. |

> In the older (milestone) versions the code was `new MessageChatMemoryAdvisor(new InMemoryChatMemory())`. In 1.0.0 use the builder style above.

**Test:** "Tell me a joke" → joke. Then "more" → now you get more jokes. "What is Spring AI?" then "explain in one line" → it knows what you mean.

> The memory above is shared by all callers. For a real app you keep a **separate conversation id** for each user.

---

## 8. Running a Model Locally with Ollama

### Why Ollama?

Cloud models need an account and money. Open-source models can run **on your machine for free**. **Ollama** is a tool for this. It can run Llama, DeepSeek, Mistral, Gemma, Phi and more.

### Setup

1. Download and install Ollama from its website (about 1 GB).
2. Open a terminal and use:

| Command | Meaning |
|---------|---------|
| `ollama` | Check that Ollama is installed |
| `ollama list` | Show models already on your machine |
| `ollama run mistral` | Run (and download, if needed) the Mistral model |
| `ollama rm mistral` | Remove a model |

You can chat with the model in the terminal. Local models are trained on older data, so they may not know about new things.

> **Hardware:** Models are heavy. A 7B model is about 4 GB; a 32B model can be 20 GB. You need enough RAM and CPU/GPU. If a big model does not run, try a smaller one.

### Use Ollama in Spring AI

1. Add the dependency:

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-ollama</artifactId>
</dependency>
```

2. Create a controller that uses `OllamaChatModel`:

```java
@RestController
public class OllamaController {

    private final ChatClient chatClient;

    public OllamaController(OllamaChatModel chatModel) {
        this.chatClient = ChatClient.create(chatModel);
    }

    @GetMapping("/api/{message}")
    public ResponseEntity<String> getAnswer(@PathVariable String message) {
        String response = chatClient.prompt(message).call().content();
        return ResponseEntity.ok(response);
    }
}
```

> Both controllers cannot use the **same URL**. Comment out the mapping of one controller, or use different URLs. Also, with two models in the project, the auto-configured `ChatClient.Builder` conflicts, so use `ChatClient.create(model)`.

3. Choose the model. By default Spring AI uses **Mistral**. If it is not installed you get an error. To use another model:

```properties
spring.ai.ollama.chat.options.model=deepseek-r1:7b
```

**No API key is needed** for Ollama.

---

## 9. Quick Reference

| Item | Use |
|------|-----|
| `ChatModel.call(String)` | Simple question → answer |
| `ChatClient.create(model)` | Make a client from a model |
| `ChatClient.Builder` | Auto-configured, for a single model |
| `.prompt().call().content()` | Get the answer text |
| `.chatResponse()` | Get answer + metadata |
| Advisor | Add memory, RAG, safety around calls |
| Ollama | Run models locally, no key, no cost |

### Anti-patterns

| Anti-pattern | Why it is bad |
|--------------|---------------|
| Real API key in code/properties pushed to Git | Others can use your money |
| Assuming the model remembers old messages | LLM calls are stateless; add memory |
| Using the auto-configured builder with many models | Causes bean conflicts |

## 10. Practice Questions

**Q1. What does `call()` do in `chatClient.prompt(...).call()`?**
**A:** It sends the request to the LLM. `prompt()` only prepares the request.

**Q2. Why does the model say "your request is vague" for "more"?**
**A:** The LLM has no memory. You must add a memory advisor.

**Q3. Which property selects the Ollama model?**
**A:** `spring.ai.ollama.chat.options.model`.
