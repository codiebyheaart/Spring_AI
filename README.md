# 🤖 Spring AI Demo Application

[![Java](https://img.shields.io/badge/Java-17-orange.svg)](https://openjdk.java.net/projects/jdk/17/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2.1-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Spring AI](https://img.shields.io/badge/Spring%20AI-0.8.1-blue.svg)](https://spring.io/projects/spring-ai)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## 📋 Overview

A comprehensive demonstration of **Spring AI** integration with **OpenAI GPT-4**, showcasing various AI capabilities including:

- 💬 **Conversational AI** - Interactive chat with GPT-4
- 📝 **Text Summarization** - Intelligent text condensation
- 🔍 **Code Analysis** - Code review, optimization, and security analysis
- ✨ **Content Generation** - Creative content creation

## 🚀 Features

### Core Capabilities

- **Chat API** - Natural language conversations with configurable temperature and token limits
- **Summarization** - Automatic text summarization with customizable length
- **Code Analysis** - Multi-mode code analysis:
  - `REVIEW` - Code quality and best practices
  - `OPTIMIZE` - Performance optimization suggestions
  - `EXPLAIN` - Detailed code explanations
  - `SECURITY` - Security vulnerability detection
- **Content Generation** - Topic-based creative content generation

### Technical Features

- ✅ RESTful API architecture
- ✅ Comprehensive input validation
- ✅ Global exception handling
- ✅ Structured logging
- ✅ Extensive unit and integration tests
- ✅ JaCoCo code coverage reporting
- ✅ Production-ready error handling

## 🛠️ Technology Stack

- **Java 17** - Latest LTS version
- **Spring Boot 3.2.1** - Application framework
- **Spring AI 0.8.1** - AI integration framework
- **OpenAI GPT-4** - Language model
- **Lombok** - Boilerplate reduction
- **JUnit 5** - Testing framework
- **Mockito** - Mocking framework
- **JaCoCo** - Code coverage
- **Maven** - Build tool

## 📦 Prerequisites

- Java 17 or higher
- Maven 3.8+
- OpenAI API Key ([Get one here](https://platform.openai.com/api-keys))

## ⚙️ Installation & Setup

### 1. Clone the Repository

```bash
git clone <repository-url>
cd spring-ai-demo
```

### 2. Configure OpenAI API Key (REQUIRED)

**⚠️ IMPORTANT**: This application uses GPT-4 model and requires a valid OpenAI API key.

**Option A: Environment Variable (Recommended for Production)**

**Windows (PowerShell):**

```powershell
$env:OPENAI_API_KEY="your-actual-openai-api-key"
```

**Windows (Command Prompt):**

```cmd
set OPENAI_API_KEY=your-actual-openai-api-key
```

**Linux/Mac:**

```bash
export OPENAI_API_KEY=your-actual-openai-api-key
```

**Permanent Setup (Linux/Mac):**

```bash
echo 'export OPENAI_API_KEY="your-actual-openai-api-key"' >> ~/.bashrc
source ~/.bashrc
```

**Option B: Application Properties (Development Only)**

```properties
# src/main/resources/application.properties
spring.ai.openai.api-key=your-actual-openai-api-key
```

**⚠️ Security Warning**: Never commit your API key to version control!

**How to Get OpenAI API Key:**

1. Visit [OpenAI Platform](https://platform.openai.com/api-keys)
2. Sign in or create an account
3. Navigate to API Keys section
4. Click "Create new secret key"
5. Copy the key (you won't be able to see it again)
6. Set it as environment variable as shown above

**Verify API Key Setup:**

```bash
# Windows PowerShell
echo $env:OPENAI_API_KEY

# Linux/Mac
echo $OPENAI_API_KEY
```

### 3. Build the Project

```bash
mvn clean install
```

### 4. Run the Application

```bash
mvn spring-boot:run
```

**If OPENAI_API_KEY is not set, you'll see an error:**

```
IllegalStateException: OPENAI_API_KEY environment variable must be set.
Please set it using: export OPENAI_API_KEY=your-actual-api-key
```

The application will start on `http://localhost:8080` once the API key is properly configured.

## 📚 API Documentation

### Base URL
```
http://localhost:8080/api/v1/ai
```

### Endpoints

#### 1. Chat
**POST** `/chat`

Send a message to the AI and receive a response.

**Request Body:**
```json
{
  "message": "Explain quantum computing in simple terms",
  "temperature": 0.7,
  "maxTokens": 500
}
```

**Response:**
```json
{
  "response": "Quantum computing is...",
  "model": "gpt-4",
  "timestamp": "2024-01-15T10:30:00",
  "tokensUsed": 150
}
```

**cURL Example:**
```bash
curl -X POST http://localhost:8080/api/v1/ai/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "What is Spring AI?",
    "temperature": 0.7,
    "maxTokens": 500
  }'
```

---

#### 2. Summarize Text
**POST** `/summarize`

Summarize long text into a concise summary.

**Request Body:**
```json
{
  "text": "Long article text here...",
  "maxSummaryLength": 100
}
```

**Response:**
```json
{
  "summary": "Concise summary of the text..."
}
```

**cURL Example:**
```bash
curl -X POST http://localhost:8080/api/v1/ai/summarize \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Spring AI is a framework that simplifies the integration of AI capabilities into Spring applications. It provides abstractions for various AI services including OpenAI, Azure OpenAI, and more.",
    "maxSummaryLength": 50
  }'
```

---

#### 3. Analyze Code
**POST** `/analyze-code`

Analyze code with different analysis types.

**Request Body:**
```json
{
  "code": "public void processData(List<String> data) { for(int i=0; i<data.size(); i++) { System.out.println(data.get(i)); } }",
  "language": "Java",
  "analysisType": "OPTIMIZE"
}
```

**Analysis Types:**
- `REVIEW` - Code quality review
- `OPTIMIZE` - Performance optimization
- `EXPLAIN` - Code explanation
- `SECURITY` - Security analysis

**Response:**
```json
{
  "analysis": "The code can be optimized by using enhanced for-loop..."
}
```

**cURL Example:**
```bash
curl -X POST http://localhost:8080/api/v1/ai/analyze-code \
  -H "Content-Type: application/json" \
  -d '{
    "code": "function add(a, b) { return a + b; }",
    "language": "JavaScript",
    "analysisType": "EXPLAIN"
  }'
```

---

#### 4. Generate Content
**GET** `/generate-content?topic={topic}`

Generate creative content on a given topic.

**Query Parameters:**
- `topic` (required) - The topic for content generation

**Response:**
```json
{
  "content": "Generated creative content about the topic..."
}
```

**cURL Example:**
```bash
curl -X GET "http://localhost:8080/api/v1/ai/generate-content?topic=Artificial%20Intelligence%20in%20Healthcare"
```

---

#### 5. Health Check
**GET** `/health`

Check application health status.

**Response:**
```json
{
  "status": "UP",
  "service": "Spring AI Demo",
  "version": "1.0.0"
}
```

**cURL Example:**
```bash
curl -X GET http://localhost:8080/api/v1/ai/health
```

## 🧪 Testing

### Configure Test Environment

**For Unit Tests** (no API key needed - uses mocks):

```bash
mvn test -Dtest=AiServiceTest
```

**For Integration Tests** (requires API key):

```bash
# Set OPENAI_API_KEY first
export OPENAI_API_KEY=your-actual-api-key
mvn test -Dtest=AiServiceIntegrationTest
```

### Run All Tests

```bash
# Unit tests only (no API key required)
mvn test -Dtest=*Test

# All tests including integration (requires OPENAI_API_KEY)
export OPENAI_API_KEY=your-actual-api-key
mvn test
```

### Run Specific Test Class

```bash
mvn test -Dtest=AiServiceTest
mvn test -Dtest=AiControllerTest
mvn test -Dtest=OpenAiConfigTest
```

### Generate Code Coverage Report

```bash
mvn clean test jacoco:report
```

View the report at: `target/site/jacoco/index.html`

### Test Coverage

The project includes comprehensive tests:

- ✅ **Unit Tests** - Service layer, DTOs, Controllers
- ✅ **Integration Tests** - End-to-end API testing
- ✅ **Validation Tests** - Input validation scenarios
- ✅ **Exception Handling Tests** - Error scenarios

**Test Statistics:**
- Total Test Classes: 11
- Total Test Methods: 50+
- Code Coverage: ~85%

## 📁 Project Structure

```
spring-ai-demo/
├── src/
│   ├── main/
│   │   ├── java/com/example/springaidemo/
│   │   │   ├── config/
│   │   │   │   └── OpenAiConfig.java          # OpenAI API & GPT-4 configuration
│   │   │   ├── controller/
│   │   │   │   └── AiController.java          # REST API endpoints
│   │   │   ├── dto/
│   │   │   │   ├── ChatRequest.java           # Chat request DTO
│   │   │   │   ├── ChatResponse.java          # Chat response DTO
│   │   │   │   ├── SummaryRequest.java        # Summary request DTO
│   │   │   │   └── CodeAnalysisRequest.java   # Code analysis DTO
│   │   │   ├── exception/
│   │   │   │   └── GlobalExceptionHandler.java # Global error handling
│   │   │   ├── service/
│   │   │   │   └── AiService.java             # AI service using ChatClient
│   │   │   └── SpringAiDemoApplication.java   # Main application class
│   │   └── resources/
│   │       └── application.properties         # App config with OPENAI_API_KEY
│   └── test/
│       ├── java/com/example/springaidemo/
│       │   ├── config/
│       │   │   └── OpenAiConfigTest.java       # OpenAI config tests
│       │   ├── controller/
│       │   │   ├── AiControllerTest.java       # Controller unit tests
│       │   │   └── AiControllerIntegrationTest.java # API integration tests
│       │   ├── dto/
│       │   │   ├── ChatRequestTest.java        # DTO validation tests
│       │   │   ├── SummaryRequestTest.java
│       │   │   ├── CodeAnalysisRequestTest.java
│       │   │   └── ChatResponseTest.java
│       │   ├── exception/
│       │   │   └── GlobalExceptionHandlerTest.java # Error handling tests
│       │   ├── service/
│       │   │   ├── AiServiceTest.java          # Service unit tests (mocked)
│       │   │   └── AiServiceIntegrationTest.java # Service integration tests
│       │   └── SpringAiDemoApplicationTests.java
│       └── resources/
│           └── application-test.properties     # Test config with API key
└── pom.xml                                     # Maven dependencies
```

## 🔧 Configuration

### Application Properties

```properties
# Application Configuration
spring.application.name=spring-ai-demo
server.port=8080
# OpenAI Configuration - GPT-4 Model
# The API key is read from OPENAI_API_KEY environment variable
spring.ai.openai.api-key=${OPENAI_API_KEY:your-api-key-here}
spring.ai.openai.chat.options.model=gpt-4
spring.ai.openai.chat.options.temperature=0.7
spring.ai.openai.chat.options.max-tokens=1000
# Embedding Configuration
spring.ai.openai.embedding.options.model=text-embedding-ada-002
# Logging Configuration
logging.level.root=INFO
logging.level.com.example.springaidemo=DEBUG
logging.level.org.springframework.ai=DEBUG
```

### How the Application Uses OPENAI_API_KEY

1. **Configuration Class** (`OpenAiConfig.java`):
    - Reads `OPENAI_API_KEY` from environment variable
    - Creates `OpenAiApi` bean with the API key
    - Configures `ChatClient` bean with GPT-4 model settings

2. **Service Layer** (`AiService.java`):
    - Injects `ChatClient` bean (configured with OPENAI_API_KEY)
    - Uses ChatClient to make API calls to OpenAI GPT-4
    - All AI operations (chat, summarize, analyze, generate) use this client

3. **Runtime Validation**:
    - Application validates OPENAI_API_KEY on startup
    - Throws `IllegalStateException` if key is missing or invalid
    - Prevents application from starting with invalid configuration

### Customization

**Change AI Model:**
```properties
spring.ai.openai.chat.options.model=gpt-3.5-turbo
```

**Adjust Default Temperature:**
```properties
spring.ai.openai.chat.options.temperature=0.5
```

**Modify Token Limit:**
```properties
spring.ai.openai.chat.options.max-tokens=2000
```

## 🐛 Error Handling

The application includes comprehensive error handling:

### Validation Errors (400 Bad Request)
```json
{
  "timestamp": "2024-01-15T10:30:00",
  "status": 400,
  "errors": {
    "message": "Message cannot be blank"
  }
}
```

### Runtime Errors (500 Internal Server Error)
```json
{
  "timestamp": "2024-01-15T10:30:00",
  "status": 500,
  "error": "Internal Server Error",
  "message": "Failed to process chat request: API Error"
}
```

## 📊 Performance Considerations

- **Rate Limiting**: OpenAI API has rate limits. Implement caching for repeated requests.
- **Timeout Configuration**: Set appropriate timeouts for AI API calls.
- **Connection Pooling**: Configure HTTP client connection pooling.
- **Async Processing**: Consider async processing for long-running AI operations.

## 🔒 Security Best Practices

- ✅ Never commit API keys to version control
- ✅ Use environment variables for sensitive configuration
- ✅ Implement request validation
- ✅ Add rate limiting to prevent abuse
- ✅ Enable HTTPS in production
- ✅ Sanitize user inputs before sending to AI

## 🚀 Deployment

### Build JAR
```bash
mvn clean package
```

### Run JAR
```bash
java -jar target/spring-ai-demo-1.0.0.jar
```

### Docker (Optional)
```dockerfile
FROM openjdk:17-jdk-slim
COPY target/spring-ai-demo-1.0.0.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

## 📈 Future Enhancements

- [ ] Add vector database integration for RAG
- [ ] Implement conversation history management
- [ ] Add support for multiple AI providers
- [ ] Implement streaming responses
- [ ] Add WebSocket support for real-time chat
- [ ] Integrate with Spring Security
- [ ] Add API rate limiting
- [ ] Implement caching layer

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📧 Contact

For questions or support, please open an issue on GitHub.

## 🙏 Acknowledgments

- [Spring AI Team](https://spring.io/projects/spring-ai) for the amazing framework
- [OpenAI](https://openai.com/) for GPT-4 API
- Spring Boot community for excellent documentation

---

**Made with ❤️ using Spring AI**
