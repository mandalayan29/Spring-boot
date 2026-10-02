# 03 – Prompt Templates, Embeddings and Cosine Similarity

---

## 1. Prompt Templates

### Why use a template?

A normal user types a free prompt. As a developer, you often want:

1. To **improve** the prompt so the model gives a better answer.
2. To ask the user only for **values**, and build the prompt yourself.

**Example:** a movie recommendation API. The user sends `type`, `year`, `lang`. We build the full prompt.

### Controller

```java
@PostMapping("/api/recommend")
public String recommend(@RequestParam String type,
                        @RequestParam String year,
                        @RequestParam String lang) {

    String temp = """
            I want to watch a {type} movie tonight with good rating,
            looking for movies around this year {year}.
            The language I am looking for is {lang}.
            Suggest one specific movie and tell me the cast and length of the movie.

            Response format should be:
            1. Movie name
            2. Basic Plot
            3. Cast
            4. Length
            5. IMDB rating
            """;

    PromptTemplate promptTemplate = new PromptTemplate(temp);

    Prompt prompt = promptTemplate.create(Map.of(
            "type", type,
            "year", year,
            "lang", lang));

    return chatClient
            .prompt(prompt)
            .call()
            .content();
}
```

### Explanation

| Part | Meaning |
|------|---------|
| `""" ... """` | Java **text block**: a multi-line string, no `\n` needed. |
| `{type}`, `{year}`, `{lang}` | **Placeholders**. They are replaced later. |
| `new PromptTemplate(temp)` | Creates the template. |
| `promptTemplate.create(Map)` | Replaces each placeholder with the value in the map and returns a `Prompt`. |
| `chatClient.prompt(prompt)` | Sends the final prompt. |

> If you forget to give a value for a placeholder, you get an error like *"Missing variable names: [type, year, lang]"*. Every placeholder needs a value in the map.

**Test:** `POST /api/recommend?type=comedy&year=2010&lang=English` → one movie with plot, cast, length and rating, in the format you asked.

You can change the response format just by editing the template text. Be clear about what you want (for example "under 110 minutes").

---

## 2. What are Embeddings?

Computers only understand numbers. For AI to know that **"puppy" and "dog" are related**, text is converted into numbers. These numbers are called **embeddings**.

- An embedding is a **list of floating-point numbers** (a **vector**) for a word, sentence, or image.
- Similar things get **close** numbers. Different things get **far** numbers.

| Words | Relation |
|-------|----------|
| dog, cat, puppy, lion | Close to each other (animals) |
| Java, Python, C++ | Close to each other (programming languages) |
| laptop, smartphone | Close to each other (devices) |
| dog and laptop | Far apart |

### Dimensions

- With **2 numbers** per word you can plot it on a 2D graph (x, y). Good for learning, but not accurate.
- Real models use **many dimensions**. More dimensions = more accurate.

| OpenAI model | Dimensions |
|--------------|-----------|
| `text-embedding-3-small` | 1536 |
| `text-embedding-3-large` | 3072 |

With the `3-small` and `3-large` models you can **reduce** the number of dimensions with the `dimensions` option.

### Where embeddings are used

Search, recommendations, clustering, classification, anomaly detection, spam filtering. They are stored in a **vector database** (see the vector store chapter).

---

## 3. Create Embeddings with an API Client

Call OpenAI directly (as a test):

- **POST** `https://api.openai.com/v1/embeddings`
- Header: `Authorization: Bearer <your API key>`
- Body (JSON):

```json
{
  "model": "text-embedding-3-large",
  "input": "dog"
}
```

The response has a very long list of numbers (more than 3000). To get only 2 numbers, add:

```json
{
  "model": "text-embedding-3-large",
  "input": "dog",
  "dimensions": 2
}
```

If you plot "dog", "laptop", "India", "USA", "Russia" in 2D, you see India and Russia nearer to each other than to "laptop". With only 2 dimensions the plot is rough, so real systems use many dimensions.

---

## 4. Create Embeddings with Spring AI

Use `EmbeddingModel`.

```java
@RestController
public class EmbeddingController {

    @Autowired
    @Qualifier("openAiEmbeddingModel")
    private EmbeddingModel embeddingModel;

    @PostMapping("/api/embedding")
    public float[] getEmbedding(@RequestParam String text) {
        return embeddingModel.embed(text);
    }
}
```

| Part | Meaning |
|------|---------|
| `EmbeddingModel` | Interface to create embeddings. |
| `@Qualifier("openAiEmbeddingModel")` | Needed only if **two** embedding models exist (for example Ollama and OpenAI). |
| `embed(text)` | Returns the vector as `float[]`. |

Choose the model in `application.properties`:

```properties
spring.ai.openai.embedding.options.model=text-embedding-3-large
```

---

## 5. Cosine Similarity

### Why?

After we store embeddings, we need to find **how close** two vectors are.

- In 2D, the distance between two points can be found with **Pythagoras**: `c² = a² + b²` (only for a right-angled triangle).
- For any triangle, use the **law of cosines**: `c² = a² + b² − 2ab·cos(θ)`.
- For similarity we use the **cosine of the angle (θ)** between the two vectors:

```
cosine similarity = (A · B) / (|A| × |B|)
```

- `A · B` = dot product = sum of (A[i] × B[i]) for all dimensions
- `|A|` = square root of sum of (A[i])²

**Result:** close to **1** = very similar. Close to **0** = not related.

### Code

```java
@PostMapping("/api/similarity")
public double getSimilarity(@RequestParam String text1, @RequestParam String text2) {

    float[] embedding1 = embeddingModel.embed(text1);
    float[] embedding2 = embeddingModel.embed(text2);

    double dotProduct = 0.0;
    double norm1 = 0.0;
    double norm2 = 0.0;

    for (int i = 0; i < embedding1.length; i++) {
        dotProduct += embedding1[i] * embedding2[i];
        norm1 += Math.pow(embedding1[i], 2);
        norm2 += Math.pow(embedding2[i], 2);
    }

    return (dotProduct / (Math.sqrt(norm1) * Math.sqrt(norm2))) * 100;
}
```

- The loop runs over **all dimensions** (the "summation" in the formula).
- Multiplying by `100` gives a **percentage**.

**Example results (depend on the model):**

| Text 1 | Text 2 | Result |
|--------|--------|--------|
| computer | laptop | high |
| happy | joy | about 53% |
| computer | banana | very low (about 20%) |

Different models give different numbers. Some are better at this than others.

### Why it matters: semantic search

Normal search matches **words**. If you search "headphones" but your product list only says "Bluetooth earbuds" or "speaker", a word search finds nothing.
**Semantic search** compares **meaning** using embeddings, so it can still find related products.

---

## Quick Reference

| Term | Meaning |
|------|---------|
| `PromptTemplate` | Prompt text with `{placeholders}` |
| Embedding | Numbers (vector) that represent meaning |
| Dimension | Length of the vector |
| `EmbeddingModel.embed()` | Gets the vector |
| Cosine similarity | Measures closeness: 1 = same, 0 = unrelated |
| Semantic search | Search by meaning, not by exact words |

## Practice Questions

**Q1. What error appears if a placeholder has no value?**
**A:** "Missing variable names" – every `{placeholder}` must be in the map.

**Q2. What does an embedding model return?**
**A:** A vector (list of floats) that represents the meaning of the text.

**Q3. What does a cosine similarity near 1 mean?**
**A:** The two texts are very similar in meaning.
