# 🚀 Complete GPT-4 Usage Example with OPENAI_API_KEY

## Overview

This guide demonstrates **exactly how** the Spring AI Demo application uses the `OPENAI_API_KEY` environment variable to interact with OpenAI's GPT-4 model.

---

## 📋 Architecture Flow

```
┌─────────────────────────────────────────────────────────────────┐
│  1. Environment Variable                                        │
│     OPENAI_API_KEY=sk-proj-xxxxx                                │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│  2. application.properties                                      │
│     spring.ai.openai.api-key=${OPENAI_API_KEY}                  │
│     spring.ai.openai.chat.options.model=gpt-4                   │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│  3. Spring AI Boot Starter Auto-Configuration                   │
│     Creates ChatClient bean with OpenAI API                     │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│  4. OpenAiConfig (Validation)                                   │
│     Validates OPENAI_API_KEY is set correctly                   │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│  5. AiService                                                   │
│     Injects ChatClient and uses it for AI operations            │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│  6. AiController                                                │
│     Exposes REST endpoints for GPT-4 interactions               │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│  7. OpenAI GPT-4 API                                            │
│     Processes requests and returns AI-generated responses       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔧 Step-by-Step Implementation

### Step 1: Set OPENAI_API_KEY Environment Variable

**Windows PowerShell:**
```powershell
# Set for current session
$env:OPENAI_API_KEY="sk-proj-your-actual-openai-api-key-here"

# Verify
echo $env:OPENAI_API_KEY
```

**Linux/Mac:**
```bash
# Set for current session
export OPENAI_API_KEY="sk-proj-your-actual-openai-api-key-here"

# Verify
echo $OPENAI_API_KEY
```

---

### Step 2: Application Properties Configuration

**File:** `src/main/resources/application.properties`

```properties
# Application Configuration
spring.application.name=spring-ai-demo
server.port=8080

# OpenAI Configuration - GPT-4 Model
# Reads OPENAI_API_KEY from environment variable
spring.ai.openai.api-key=${OPENAI_API_KEY:your-api-key-here}
spring.ai.openai.chat.options.model=gpt-4
spring.ai.openai.chat.options.temperature=0.7
spring.ai.openai.chat.options.max-tokens=1000

# Embedding Configuration
spring.ai.openai.embedding.options.model=text-embedding-ada-002
```

**How it works:**
- `${OPENAI_API_KEY:your-api-key-here}` reads from environment variable
- If not set, falls back to `your-api-key-here` (which triggers validation error)
- Spring AI Boot Starter auto-configures ChatClient with this API key

---

### Step 3: OpenAI Configuration Class

**File:** `src/main/java/com/example/springaidemo/config/OpenAiConfig.java`

```java
package com.example.springaidemo.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import jakarta.annotation.PostConstruct;

@Slf4j
@Configuration
public class OpenAiConfig {

    @Value("${spring.ai.openai.api-key}")
    private String apiKey;

    @Value("${spring.ai.openai.chat.options.model:gpt-4}")
    private String model;

    @PostConstruct
    public void validateConfiguration() {
        log.info("OpenAI Configuration Validation");
        log.info("Model: {}", model);
        
        // Validate API key is set
        if (apiKey == null || apiKey.equals("your-api-key-here")) {
            throw new IllegalStateException(
                "OPENAI_API_KEY environment variable must be set"
            );
        }
        
        log.info("API Key: {}...{}", 
                apiKey.substring(0, 7),
                apiKey.substring(apiKey.length() - 4));
        log.info("ChatClient will use GPT-4 model with OPENAI_API_KEY");
    }
}
```

**Purpose:**
- Validates OPENAI_API_KEY is properly configured on startup
- Logs configuration details (masked API key)
- Prevents application from starting with invalid configuration

---

### Step 4: AI Service Implementation

**File:** `src/main/java/com/example/springaidemo/service/AiService.java`

```java
package com.example.springaidemo.service;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.ai.chat.ChatClient;
import org.springframework.ai.chat.ChatResponse;
import org.springframework.ai.chat.messages.UserMessage;
import org.springframework.ai.chat.prompt.Prompt;
import org.springframework.stereotype.Service;
import com.example.springaidemo.dto.*;

import java.time.LocalDateTime;
import java.util.List;

@Slf4j
@Service
@RequiredArgsConstructor
public class AiService {

    // ChatClient is auto-configured by Spring AI Boot Starter
    // It uses OPENAI_API_KEY from application.properties
    private final ChatClient chatClient;

    /**
     * Sends a chat message to GPT-4 using OPENAI_API_KEY
     */
    public ChatResponse chat(ChatRequest request) {
        log.info("Processing chat request with GPT-4: {}", request.getMessage());

        try {
            // Create prompt with user message
            UserMessage userMessage = new UserMessage(request.getMessage());
            Prompt prompt = new Prompt(List.of(userMessage));

            // Call OpenAI GPT-4 API using ChatClient
            // ChatClient internally uses OPENAI_API_KEY
            ChatResponse aiResponse = chatClient.call(prompt);

            // Build response
            return ChatResponse.builder()
                    .response(aiResponse.getResult().getOutput().getContent())
                    .model("gpt-4")
                    .timestamp(LocalDateTime.now())
                    .tokensUsed(aiResponse.getMetadata().getUsage().getTotalTokens())
                    .build();

        } catch (Exception e) {
            log.error("Error calling GPT-4 API", e);
            throw new RuntimeException("Failed to process chat request: " + e.getMessage(), e);
        }
    }
}
```

**How ChatClient uses OPENAI_API_KEY:**
1. Spring AI Boot Starter creates `ChatClient` bean
2. `ChatClient` is configured with `OpenAiApi` instance
3. `OpenAiApi` uses the API key from `spring.ai.openai.api-key` property
4. When `chatClient.call()` is invoked, it sends HTTP request to OpenAI with:
   - Header: `Authorization: Bearer ${OPENAI_API_KEY}`
   - Model: `gpt-4`
   - Request body with user message

---

### Step 5: REST Controller

**File:** `src/main/java/com/example/springaidemo/controller/AiController.java`

```java
package com.example.springaidemo.controller;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import com.example.springaidemo.dto.*;
import com.example.springaidemo.service.AiService;

@Slf4j
@RestController
@RequestMapping("/api/v1/ai")
@RequiredArgsConstructor
public class AiController {

    private final AiService aiService;

    /**
     * Chat endpoint - uses GPT-4 with OPENAI_API_KEY
     */
    @PostMapping("/chat")
    public ResponseEntity<ChatResponse> chat(@RequestBody ChatRequest request) {
        log.info("Received chat request for GPT-4");
        ChatResponse response = aiService.chat(request);
        return ResponseEntity.ok(response);
    }
}
```

---

## 🧪 Complete Usage Example

### 1. Start the Application

```bash
# Set OPENAI_API_KEY
export OPENAI_API_KEY="sk-proj-your-actual-api-key"

# Run the application
mvn spring-boot:run
```

**Expected Startup Logs:**
```
==================================================
OpenAI Configuration Validation
==================================================
Model: gpt-4
Temperature: 0.7
Max Tokens: 1000
API Key: sk-proj...xyz (length: 56)
OpenAI configuration validated successfully!
ChatClient will use GPT-4 model with OPENAI_API_KEY
==================================================
```

---

### 2. Test Chat Endpoint

**Request:**
```bash
curl -X POST http://localhost:8080/api/v1/ai/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Explain how Spring AI uses OPENAI_API_KEY to connect to GPT-4",
    "temperature": 0.7,
    "maxTokens": 500
  }'
```

**Response:**
```json
{
  "response": "Spring AI uses the OPENAI_API_KEY environment variable to authenticate with OpenAI's API. Here's how it works:\n\n1. The API key is read from the environment variable OPENAI_API_KEY\n2. Spring AI's auto-configuration creates a ChatClient bean\n3. The ChatClient is configured with the API key\n4. When you call chatClient.call(), it sends an HTTP request to OpenAI\n5. The request includes: Authorization: Bearer ${OPENAI_API_KEY}\n6. OpenAI validates the key and processes the request with GPT-4\n7. The response is returned to your application\n\nThis design keeps your API key secure and separate from your code.",
  "model": "gpt-4",
  "timestamp": "2024-01-15T14:30:00",
  "tokensUsed": 156
}
```

---

### 3. Behind the Scenes: HTTP Request to OpenAI

When you call the `/chat` endpoint, here's what happens:

**HTTP Request sent to OpenAI:**
```http
POST https://api.openai.com/v1/chat/completions
Content-Type: application/json
Authorization: Bearer sk-proj-your-actual-api-key

{
  "model": "gpt-4",
  "messages": [
    {
      "role": "user",
      "content": "Explain how Spring AI uses OPENAI_API_KEY to connect to GPT-4"
    }
  ],
  "temperature": 0.7,
  "max_tokens": 500
}
```

**OpenAI Response:**
```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "model": "gpt-4",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Spring AI uses the OPENAI_API_KEY..."
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 20,
    "completion_tokens": 136,
    "total_tokens": 156
  }
}
```

---

## 🔍 Code Walkthrough

### How OPENAI_API_KEY Flows Through the Application

```java
// 1. Environment Variable
// OPENAI_API_KEY=sk-proj-xxxxx

// 2. Application Properties reads it
// spring.ai.openai.api-key=${OPENAI_API_KEY}

// 3. Spring AI Boot Starter creates ChatClient
@Bean
public ChatClient chatClient(OpenAiApi openAiApi) {
    // openAiApi is configured with OPENAI_API_KEY
    return new OpenAiChatClient(openAiApi, options);
}

// 4. AiService injects ChatClient
@Service
public class AiService {
    private final ChatClient chatClient; // Uses OPENAI_API_KEY internally
    
    public ChatResponse chat(ChatRequest request) {
        // This call uses OPENAI_API_KEY to authenticate with OpenAI
        ChatResponse response = chatClient.call(prompt);
        return response;
    }
}

// 5. Controller exposes endpoint
@RestController
public class AiController {
    private final AiService aiService;
    
    @PostMapping("/chat")
    public ResponseEntity<ChatResponse> chat(@RequestBody ChatRequest request) {
        // User request → AiService → ChatClient → OpenAI API (with OPENAI_API_KEY)
        return ResponseEntity.ok(aiService.chat(request));
    }
}
```

---

## 🧪 Testing with OPENAI_API_KEY

### Unit Tests (No API Key Required)

```java
@ExtendWith(MockitoExtension.class)
class AiServiceTest {
    @Mock
    private ChatClient chatClient; // Mocked - no real API calls
    
    @InjectMocks
    private AiService aiService;
    
    @Test
    void testChat() {
        // Mock response - no OPENAI_API_KEY needed
        when(chatClient.call(any())).thenReturn(mockResponse);
        
        ChatResponse response = aiService.chat(request);
        assertNotNull(response);
    }
}
```

### Integration Tests (Requires API Key)

```java
@SpringBootTest
@TestPropertySource(properties = {
    "spring.ai.openai.api-key=${OPENAI_API_KEY}"
})
class AiServiceIntegrationTest {
    @Autowired
    private AiService aiService;
    
    @Test
    void testRealGpt4Call() {
        // This makes a real API call to GPT-4
        // Requires OPENAI_API_KEY environment variable
        ChatRequest request = ChatRequest.builder()
            .message("Hello GPT-4!")
            .build();
            
        ChatResponse response = aiService.chat(request);
        assertNotNull(response.getResponse());
    }
}
```

**Run integration tests:**
```bash
export OPENAI_API_KEY="sk-proj-your-api-key"
mvn test -Dtest=AiServiceIntegrationTest
```

---

## 📊 Summary

### Where OPENAI_API_KEY is Used:

| Component | Purpose | How it Uses API Key |
|-----------|---------|--------------------|
| **Environment Variable** | Store API key securely | `OPENAI_API_KEY=sk-proj-xxx` |
| **application.properties** | Read from environment | `${OPENAI_API_KEY}` |
| **Spring AI Boot Starter** | Auto-configure ChatClient | Creates `OpenAiApi` with API key |
| **OpenAiConfig** | Validate configuration | Checks API key is set |
| **ChatClient** | Make API calls | Adds `Authorization: Bearer ${API_KEY}` header |
| **AiService** | Business logic | Uses ChatClient (which uses API key) |
| **AiController** | REST endpoints | Calls AiService |

### Key Points:

1. ✅ **OPENAI_API_KEY** is read from environment variable
2. ✅ **Spring AI Boot Starter** auto-configures ChatClient with the API key
3. ✅ **ChatClient** uses the API key to authenticate all requests to OpenAI
4. ✅ **GPT-4 model** is specified in application.properties
5. ✅ **All AI operations** (chat, summarize, analyze, generate) use the same ChatClient
6. ✅ **API key is never logged** or exposed in responses
7. ✅ **Validation happens on startup** to ensure API key is set

---

## 🚀 Next Steps

1. **Get your API key**: https://platform.openai.com/api-keys
2. **Set environment variable**: `export OPENAI_API_KEY="your-key"`
3. **Run the application**: `mvn spring-boot:run`
4. **Test the endpoints**: Use curl or Postman
5. **Monitor usage**: https://platform.openai.com/usage

---

**Made with ❤️ using Spring AI and OpenAI GPT-4**
