# 05 – Image Models: Generate and Describe Images

---

## 1. Two Image Features

| Feature | Input | Output |
|---------|-------|--------|
| **Image generation** | Text prompt | An image (link) |
| **Image description (vision)** | An image + a question | A text answer |

We use OpenAI. The image generation model behind the scenes is **DALL·E** (DALL·E 3 in these examples).

---

## 2. Generate an Image

### Main types

| Type | Meaning |
|------|---------|
| `ImageModel` | Interface with a `call(ImagePrompt)` method |
| `OpenAiImageModel` | OpenAI implementation |
| `ImagePrompt` | Input: the text (and optional options) |
| `ImageResponse` | Output: contains the generated image(s) |

### Controller

```java
@RestController
public class ImageGenController {

    private final OpenAiImageModel openAiImageModel;

    public ImageGenController(OpenAiImageModel openAiImageModel) {
        this.openAiImageModel = openAiImageModel;
    }

    @GetMapping("/image/{query}")
    public String genImage(@PathVariable String query) {

        ImagePrompt prompt = new ImagePrompt(query);

        ImageResponse response = openAiImageModel.call(prompt);

        return response.getResult().getOutput().getUrl();
    }
}
```

### Explanation

- `new ImagePrompt(query)` creates the prompt from the text.
- `openAiImageModel.call(prompt)` generates the image. It can take **10 seconds or more**.
- `getResult().getOutput().getUrl()` returns a **link** to the image. A REST client cannot show a picture, so we return the link and open it in the browser. A front end can use the same link.

**Test:** `GET /image/Deadpool writing Java code` → returns a link to the image.

> The link is temporary. Download the image if you need to keep it.

---

## 3. Image Options

By default you get a standard-quality image. To change it, pass **options** in the `ImagePrompt`:

```java
ImagePrompt prompt = new ImagePrompt(query,
        OpenAiImageOptions.builder()
                .quality("hd")
                .style("natural")
                .height(1024)
                .width(1024)
                .N(1)
                .build());
```

| Option | Allowed values | Notes |
|--------|----------------|-------|
| `quality` | `standard`, `hd` | Lowercase. Other values give an error. |
| `style` | `vivid`, `natural` | `natural` looks more real. |
| `height` / `width` | `1024` (square), also `1792` for some sizes | Other sizes (like 100) fail with DALL·E 3. |
| `N` | `1` | DALL·E 3 can create only **one** image per request. |
| `model` | `dall-e-3` | Optional |

> **Tip:** If you are not sure what values are allowed, try a value and read the error message in the console. It lists the valid values.

---

## 4. Describe an Image (Vision)

Here the user sends an **image file** and a **question**. The model answers about the image.

### Request

- `POST /image/describe`
- Params: `query` (text)
- Body: form-data (multipart) with a `file` field

### Controller

```java
@PostMapping("/image/describe")
public String describeImage(@RequestParam String query,
                            @RequestParam MultipartFile file) {

    return chatClient
            .prompt()
            .user(u -> u.text(query)
                        .media(MimeTypeUtils.IMAGE_JPEG, file.getResource()))
            .call()
            .content();
}
```

### Explanation

| Part | Meaning |
|------|---------|
| `MultipartFile` | Spring type for an uploaded file. |
| `.prompt()` (no text) | Start with an empty prompt, then add messages. |
| `.user(...)` | The **user message**. It takes a lambda (a `Consumer`) to build the message. |
| `u.text(query)` | The question text. |
| `.media(mimeType, resource)` | Attach the image. We give the type (`IMAGE_JPEG`; use `IMAGE_PNG` for PNG) and the file as a `Resource`. |
| `file.getResource()` | Converts the `MultipartFile` into a `Resource`. |

You can also add `.system("...")` to give your own instructions to the model (system message), separate from the user's message.

**Examples:**

- Photo of a Deadpool figure + `query=Name the superhero` → "The superhero in this image is Deadpool."
- Photo of an office + `query=Items in this image` → list of bookshelf, plants, chair, etc.

> This works because the OpenAI chat model is **multimodal** (it understands text and images). You can ask for a product name, count items, read details, and so on.

---

## Quick Reference

| Item | Use |
|------|-----|
| `OpenAiImageModel.call(ImagePrompt)` | Generate an image |
| `OpenAiImageOptions` | Quality, style, size, number |
| `getOutput().getUrl()` | Link to the image |
| `.user(u -> u.text().media())` | Send text + image to a chat model |

## Practice Questions

**Q1. Which values can `quality` have for OpenAI images?**
**A:** `standard` and `hd`.

**Q2. How many images can DALL·E 3 create in one call?**
**A:** One.

**Q3. How do you send an image to a chat model?**
**A:** Use `.user(u -> u.text(query).media(mimeType, resource))`.
