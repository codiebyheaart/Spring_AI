# 🤖 GPT-4 Integration Architecture - Complete Implementation Details

## Overview

This document explains **exactly how OPENAI_API_KEY is used** in this Spring AI application to make GPT-4 API calls.

---

## 🔑 OPENAI_API_KEY Flow

### 1. Environment Variable Setup

```bash
# Set environment variable (Windows PowerShell)
$env:OPENAI_API_KEY="sk-proj-abc123..."

# Set environment variable (Linux/Mac)
export OPENAI_API_KEY="sk-proj-abc123..."
```

### 2. Application Properties Configuration

**File:** `src/main/resources/application.properties`

```properties
# OpenAI Configuration
spring.ai.openai.api-key=${OPENAI_API_KEY:your-api-key-here}
spring.ai.openai.chat.options.model=gpt-4
spring.ai.openai.chat.options.temperature=0.7
spring.ai.openai.chat.options.max-tokens=1000
```

**Explanation:**
- `${OPENAI_API_KEY:your-api-key-here}` → Reads from environment variable
- If `OPENAI_API_KEY` is not set, uses default value `your-api-key-here`
- Spring Boot resolves this at application startup

### 3. Spring AI Auto-Configuration

**Spring AI Boot Starter** (`spring-ai-openai-spring-boot-starter`) automatically:

1. Reads `spring.ai.openai.api-key` from `application.properties`
2. Creates `OpenAiApi` instance with the API key
3. Configures `ChatClient` bean with GPT-4 model settings
4. Injects `ChatClient` into Spring context for dependency injection

**Auto-Configuration Class (Internal to Spring AI):**
```java
@Configuration
@ConditionalOnProperty("spring.ai.openai.api-key")
public class OpenAiAutoConfiguration {
    
    @Bean
    public OpenAiApi openAiApi(
            @Value("${spring.ai.openai.api-key}") String apiKey) {
        return new OpenAiApi(apiKey); // API key used here
    }
    
    @Bean
    public ChatClient chatClient(OpenAiApi openAiApi, 
                                  OpenAiChatOptions options) {
        return new OpenAiChatClient(openAiApi, options);
    }
}
```

### 4. Custom Configuration Validation

**File:** `src/main/java/com/example/springaidemo/config/OpenAiConfig.java`

```java
@Slf4j
@Configuration
public class OpenAiConfig {

    @Value("${spring.ai.openai.api-key}")
    private String apiKey;

    @PostConstruct
    public void validateConfiguration() {
        // Validate API key is set
        if (apiKey == null || apiKey.equals("your-api-key-here")) {
            throw new IllegalStateException(
                "OPENAI_API_KEY environment variable must be set"
            );
        }
        
        // Validate API key format
        if (!apiKey.startsWith("sk-")) {
            log.warn("API key format may be invalid");
        }
        
        // Log masked API key for verification
        log.info("API Key: {}...{} (length: {})",
                apiKey.substring(0, 7),
                apiKey.substring(apiKey.length() - 4),
                apiKey.length());
    }
}
```

**Purpose:**
- Validates `OPENAI_API_KEY` is properly set at startup
- Prevents application from starting with invalid configuration
- Logs masked API key for debugging (security best practice)

### 5. Service Layer - Using ChatClient

**File:** `src/main/java/com/example/springaidemo/service/AiService.java`

```java
@Service
@RequiredArgsConstructor
public class AiService {

    private final ChatClient chatClient; // Injected by Spring

    public ChatResponse chat(ChatRequest request) {
        // Create prompt with user message
        Message userMessage = new UserMessage(request.getMessage());
        Prompt prompt = new Prompt(List.of(userMessage));

        // Call GPT-4 API using ChatClient
        // ChatClient internally uses OPENAI_API_KEY for authentication
        ChatResponse aiResponse = chatClient.call(prompt);

        // Extract response
        return ChatResponse.builder()
                .response(aiResponse.getResult().getOutput().getContent())
                .model("gpt-4")
                .tokensUsed(aiResponse.getMetadata().getUsage().getTotalTokens())
                .build();
    }
}
```

**How API Key is Used:**
1. `chatClient.call(prompt)` makes HTTP request to OpenAI API
2. Request includes `Authorization: Bearer ${OPENAI_API_KEY}` header
3. OpenAI validates the API key and processes the request
4. Response is returned to the application

---

## 🔍 Complete API Call Flow

```
1. User Request
   ↓
2. AiController.chat(@RequestBody ChatRequest)
   ↓
3. AiService.chat(request)
   ↓
4. chatClient.call(prompt)
   ↓
5. OpenAiChatClient (Spring AI internal)
   ↓
6. HTTP POST to https://api.openai.com/v1/chat/completions
   Headers:
     Authorization: Bearer ${OPENAI_API_KEY}
     Content-Type: application/json
   Body:
     {
       "model": "gpt-4",
       "messages": [{"role": "user", "content": "..."}],
       "temperature": 0.7,
       "max_tokens": 1000
     }
   ↓
7. OpenAI GPT-4 API
   - Validates API key
   - Processes request
   - Generates response
   ↓
8. HTTP Response
   {
     "id": "chatcmpl-123",
     "model": "gpt-4",
     "choices": [{
       "message": {"role": "assistant", "content": "..."},
       "finish_reason": "stop"
     }],
     "usage": {"total_tokens": 150}
   }
   ↓
9. ChatResponse returned to AiService
   ↓
10. ChatResponse returned to AiController
   ↓
11. JSON Response to Client
```

---

## 💻 Code Examples

### Example 1: Simple Chat Request

**Request:**
```bash
curl -X POST http://localhost:8080/api/v1/ai/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "What is 2+2?"
  }'
```

**Internal Flow:**
```java
// 1. Controller receives request
@PostMapping("/chat")
public ResponseEntity<ChatResponse> chat(@RequestBody ChatRequest request) {
    return ResponseEntity.ok(aiService.chat(request));
}

// 2. Service processes request
public ChatResponse chat(ChatRequest request) {
    Prompt prompt = new Prompt(List.of(new UserMessage("What is 2+2?")));
    ChatResponse response = chatClient.call(prompt); // Uses OPENAI_API_KEY
    return response;
}

// 3. ChatClient makes API call with OPENAI_API_KEY
// HTTP POST https://api.openai.com/v1/chat/completions
// Authorization: Bearer sk-proj-abc123...
```

**Response:**
```json
{
  "response": "2+2 equals 4.",
  "model": "gpt-4",
  "timestamp": "2024-01-15T10:30:45.123",
  "tokensUsed": 15
}
```

### Example 2: Code Analysis with Custom Options

**Request:**
```java
CodeAnalysisRequest request = CodeAnalysisRequest.builder()
    .code("public void test() { System.out.println(\"Hello\"); }")
    .language("Java")
    .analysisType("REVIEW")
    .build();
```

**Service Method:**
```java
public String analyzeCode(CodeAnalysisRequest request) {
    String prompt = "Review the following Java code: " + request.getCode();
    
    Message userMessage = new UserMessage(prompt);
    Prompt chatPrompt = new Prompt(List.of(userMessage));
    
    // ChatClient uses OPENAI_API_KEY internally
    ChatResponse response = chatClient.call(chatPrompt);
    
    return response.getResult().getOutput().getContent();
}
```

**OpenAI API Request (Internal):**
```http
POST https://api.openai.com/v1/chat/completions
Authorization: Bearer sk-proj-abc123...
Content-Type: application/json

{
  "model": "gpt-4",
  "messages": [
    {
      "role": "user",
      "content": "Review the following Java code: public void test() { System.out.println(\"Hello\"); }"
    }
  ],
  "temperature": 0.7,
  "max_tokens": 1000
}
```

---

## 🔒 Security Best Practices

### 1. Never Hardcode API Keys

❌ **BAD:**
```properties
spring.ai.openai.api-key=sk-proj-abc123xyz456
```

✅ **GOOD:**
```properties
spring.ai.openai.api-key=${OPENAI_API_KEY}
```

### 2. Use Environment Variables

```bash
# Production deployment
export OPENAI_API_KEY="sk-proj-production-key"

# Development
export OPENAI_API_KEY="sk-proj-dev-key"

# Testing
export OPENAI_API_KEY="sk-proj-test-key"
```

### 3. Mask API Keys in Logs

```java
// Show only first 7 and last 4 characters
log.info("API Key: {}...{}",
        apiKey.substring(0, 7),
        apiKey.substring(apiKey.length() - 4));
// Output: API Key: sk-proj...xyz4
```

### 4. Validate API Key Format

```java
if (!apiKey.startsWith("sk-")) {
    throw new IllegalStateException("Invalid API key format");
}
```

### 5. Use Different Keys for Different Environments

```yaml
# application-dev.properties
spring.ai.openai.api-key=${OPENAI_DEV_API_KEY}

# application-prod.properties
spring.ai.openai.api-key=${OPENAI_PROD_API_KEY}
```

---

## 🧪 Testing with OPENAI_API_KEY

### Unit Tests (No API Key Required)

```java
@ExtendWith(MockitoExtension.class)
class AiServiceTest {
    
    @Mock
    private ChatClient chatClient; // Mocked, no real API calls
    
    @InjectMocks
    private AiService aiService;
    
    @Test
    void testChat() {
        // Mock response, no OPENAI_API_KEY needed
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
@EnabledIfEnvironmentVariable(named = "OPENAI_API_KEY", matches = "sk-.*")
class AiServiceIntegrationTest {
    
    @Autowired
    private AiService aiService; // Real ChatClient with API key
    
    @Test
    void testRealGPT4Call() {
        // Makes real API call using OPENAI_API_KEY
        ChatResponse response = aiService.chat(request);
        
        assertNotNull(response);
        assertTrue(response.getTokensUsed() > 0);
    }
}
```

**Run Integration Tests:**
```bash
# Set API key first
export OPENAI_API_KEY="sk-proj-your-key"

# Run tests
mvn test
```

---

## 📊 Monitoring API Usage

### Log Token Usage

```java
public ChatResponse chat(ChatRequest request) {
    ChatResponse response = chatClient.call(prompt);
    
    int tokensUsed = response.getMetadata().getUsage().getTotalTokens();
    log.info("GPT-4 API call completed. Tokens used: {}", tokensUsed);
    
    return response;
}
```

### Track Costs

```java
public ChatResponse chat(ChatRequest request) {
    ChatResponse response = chatClient.call(prompt);
    
    Usage usage = response.getMetadata().getUsage();
    int inputTokens = usage.getPromptTokens();
    int outputTokens = usage.getCompletionTokens();
    
    double cost = (inputTokens * 0.03 / 1000) + (outputTokens * 0.06 / 1000);
    log.info("API call cost: ${}", String.format("%.6f", cost));
    
    return response;
}
```

---

## ❓ FAQ

### Q: Where is OPENAI_API_KEY actually used?
**A:** In the `Authorization: Bearer ${OPENAI_API_KEY}` HTTP header when `ChatClient` makes requests to `https://api.openai.com/v1/chat/completions`

### Q: Can I use a different model instead of GPT-4?
**A:** Yes, change `spring.ai.openai.chat.options.model` to `gpt-3.5-turbo`, `gpt-4-turbo`, etc.

### Q: How do I verify my API key is working?
**A:** Run the application and check startup logs for "OpenAI configuration validated successfully!"

### Q: What happens if OPENAI_API_KEY is not set?
**A:** Application will throw `IllegalStateException` at startup and refuse to start.

### Q: Can I use multiple API keys?
**A:** Yes, create multiple `ChatClient` beans with different API keys.

---

## 🚀 Deployment

### Docker
```dockerfile
FROM openjdk:17-jdk-slim
COPY target/spring-ai-demo-1.0.0.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

```bash
docker run -e OPENAI_API_KEY="sk-proj-your-key" spring-ai-demo
```

### Kubernetes
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: openai-secret
type: Opaque
stringData:
  OPENAI_API_KEY: sk-proj-your-key
---
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: spring-ai-demo
        envFrom:
        - secretRef:
            name: openai-secret
```

### AWS Elastic Beanstalk
```bash
eb setenv OPENAI_API_KEY=sk-proj-your-key
eb deploy
```

---

**Last Updated:** 2024-01-15  
**Version:** 1.0.0
