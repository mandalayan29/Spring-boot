# 06 – Audio Models: Speech to Text and Text to Speech

---

## 1. Two Audio Features

| Feature | Input | Output | Spring AI model |
|---------|-------|--------|-----------------|
| **Transcription** (speech to text) | Audio file | Text | `OpenAiAudioTranscriptionModel` |
| **Text to speech (TTS)** | Text | Audio (bytes) | `OpenAiAudioSpeechModel` |

Uses: subtitles for videos, voice assistants, reading text aloud.

Both are in the OpenAI starter dependency we already have.

---

## 2. Speech to Text

### Controller (basic)

```java
@RestController
public class AudioGenController {

    private final OpenAiAudioTranscriptionModel audioModel;

    public AudioGenController(OpenAiAudioTranscriptionModel audioModel) {
        this.audioModel = audioModel;
    }

    @PostMapping("/api/stt")
    public String speechToText(@RequestParam MultipartFile file) {
        return audioModel.call(file.getResource());
    }
}
```

- `file.getResource()` converts the uploaded file into a `Resource`.
- `audioModel.call(resource)` returns the **text** of the audio.

**Test (Postman/Insomnia):** `POST /api/stt` → Body: **form-data**, field `file` of type **File** → choose an audio file.

### Increase the upload size limit

Spring allows only **1 MB** for uploads by default. A larger file gives *"Maximum upload size exceeded"*. Change it:

```properties
spring.servlet.multipart.max-file-size=20MB
spring.servlet.multipart.max-request-size=20MB
```

> A long audio file can take about 30 seconds or more to transcribe.

---

## 3. Transcription Options (Subtitles and Language)

The simple `call(Resource)` does not accept options. To use options, call the other method that takes an **`AudioTranscriptionPrompt`**.

```java
@PostMapping("/api/stt")
public String speechToText(@RequestParam MultipartFile file) {

    OpenAiAudioTranscriptionOptions options = OpenAiAudioTranscriptionOptions.builder()
            .responseFormat(OpenAiAudioApi.TranscriptResponseFormat.SRT)
            .language("es")
            .build();

    AudioTranscriptionPrompt prompt =
            new AudioTranscriptionPrompt(file.getResource(), options);

    return audioModel.call(prompt).getResult().getOutput();
}
```

### Explanation

| Part | Meaning |
|------|---------|
| `AudioTranscriptionPrompt(resource, options)` | Prompt = audio file + options |
| `responseFormat(SRT)` | Output with **timestamps** in SRT subtitle format (VTT also works). Use it for video subtitles. |
| `language("es")` | Output language (here Spanish) |
| `call(prompt).getResult().getOutput()` | The text result |

Other options: model, temperature, and more.

---

## 4. Text to Speech

### Controller (basic)

```java
private final OpenAiAudioSpeechModel speechModel;

@PostMapping("/api/tts")
public byte[] textToSpeech(@RequestParam String text) {
    return speechModel.call(text);
}
```

- Java has no "speech" type, so the result is a **`byte[]`** (raw audio).
- A REST client cannot play raw bytes. To hear it, **save the response as an `.mp3` file** (for example, use "Export raw response" and name the file `output.mp3`). In a web or mobile app, pass the bytes to an audio player.

---

## 5. Speech Options (Voice and Speed)

Use a **`SpeechPrompt`** with options:

```java
@PostMapping("/api/tts")
public byte[] textToSpeech(@RequestParam String text) {

    OpenAiAudioSpeechOptions options = OpenAiAudioSpeechOptions.builder()
            .speed(1.5f)
            .voice(OpenAiAudioApi.SpeechRequest.Voice.NOVA)
            .build();

    SpeechPrompt prompt = new SpeechPrompt(text, options);

    return speechModel.call(prompt).getResult().getOutput();
}
```

| Option | Meaning |
|--------|---------|
| `speed` | A float from **0.25** (slowest) to **4.0** (fastest). `1.5f` = 1.5x. |
| `voice` | An enum: `ALLOY` (default), `ECHO`, `FABLE`, `ONYX`, `NOVA`, `SHIMMER` |
| `model` | `tts-1` or `tts-1-hd` |

---

## Quick Reference

| Task | Class | Method |
|------|-------|--------|
| Speech → text | `OpenAiAudioTranscriptionModel` | `call(Resource)` or `call(AudioTranscriptionPrompt)` |
| Text → speech | `OpenAiAudioSpeechModel` | `call(String)` or `call(SpeechPrompt)` |

### Common mistakes

| Mistake | Fix |
|---------|-----|
| "Payload too large" on upload | Increase `spring.servlet.multipart.max-file-size` |
| Cannot play TTS output in the REST client | Save the bytes as `.mp3` |
| Forgetting the file extension when saving | Use `.mp3` |

## Practice Questions

**Q1. Which format gives timestamps for subtitles?**
**A:** SRT (or VTT).

**Q2. What does the TTS model return?**
**A:** A `byte[]` with the audio.

**Q3. What is the speed range for OpenAI TTS?**
**A:** 0.25 to 4.0.
