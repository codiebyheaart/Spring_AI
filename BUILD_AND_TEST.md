# 🛠️ Build and Test Instructions

## Prerequisites Check

Before building the project, ensure you have the following installed:

### 1. Verify Java Installation
```bash
java -version
```
Expected output: Java 17 or higher

### 2. Verify Maven Installation
```bash
mvn --version
```
Expected output: Maven 3.8+ with Java 17

### 3. Set OpenAI API Key
```bash
# Windows PowerShell
$env:OPENAI_API_KEY="your-api-key-here"

# Windows Command Prompt
set OPENAI_API_KEY=your-api-key-here

# Linux/Mac
export OPENAI_API_KEY=your-api-key-here
```

## 💻 Build Instructions

### Option 1: Using Maven Wrapper (Recommended)

If Maven is not installed, use the Maven Wrapper:

**Windows:**
```powershell
.\mvnw.cmd clean install
```

**Linux/Mac:**
```bash
./mvnw clean install
```

### Option 2: Using System Maven

```bash
mvn clean install
```

### Build Without Tests (Faster)

```bash
mvn clean install -DskipTests
```

### Build with Specific Profile

```bash
mvn clean install -Ptest
```

## 🧪 Testing Instructions

### Run All Tests

```bash
mvn test
```

### Run Specific Test Class

```bash
mvn test -Dtest=AiServiceTest
```

### Run Tests with Coverage

```bash
mvn clean test jacoco:report
```

View coverage report: `target/site/jacoco/index.html`

### Run Only Unit Tests

```bash
mvn test -Dtest=*Test
```

### Run Only Integration Tests

```bash
mvn test -Dtest=*IntegrationTest
```

### Run Tests in Parallel

```bash
mvn test -T 4
```

## 🚀 Running the Application

### Using Maven

```bash
mvn spring-boot:run
```

### Using JAR

```bash
java -jar target/spring-ai-demo-1.0.0.jar
```

### With Custom Port

```bash
mvn spring-boot:run -Dspring-boot.run.arguments=--server.port=9090
```

### With Profile

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

## 🔍 Troubleshooting

### Issue: Maven Not Found

**Solution:** Install Maven or use Maven Wrapper

**Install Maven:**
- Windows: `choco install maven` (using Chocolatey)
- Mac: `brew install maven` (using Homebrew)
- Linux: `sudo apt-get install maven`

### Issue: Java Version Mismatch

**Solution:** Ensure Java 17 is installed and set as default

```bash
# Check Java version
java -version

# Set JAVA_HOME (Windows)
set JAVA_HOME=C:\Program Files\Java\jdk-17

# Set JAVA_HOME (Linux/Mac)
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
```

### Issue: Tests Failing Due to Missing API Key

**Solution:** Tests use mock data and should not require actual API key. If tests fail:

1. Check `application-test.properties` has test configuration
2. Run tests with test profile: `mvn test -Dspring.profiles.active=test`

### Issue: Build Timeout

**Solution:** Increase Maven timeout

```bash
mvn clean install -Dmaven.test.failure.ignore=true
```

### Issue: Dependency Download Failures

**Solution:** Clear Maven cache and retry

```bash
# Windows
rmdir /s /q %USERPROFILE%\.m2\repository\org\springframework\ai

# Linux/Mac
rm -rf ~/.m2/repository/org/springframework/ai

# Then rebuild
mvn clean install -U
```

## 📊 Build Verification

After successful build, verify:

1. **JAR Created:**
   ```bash
   ls target/spring-ai-demo-1.0.0.jar
   ```

2. **Tests Passed:**
   ```bash
   # Check test results
   cat target/surefire-reports/*.txt
   ```

3. **Application Starts:**
   ```bash
   mvn spring-boot:run
   # Should see: "Started SpringAiDemoApplication"
   ```

4. **Health Check:**
   ```bash
   curl http://localhost:8080/api/v1/ai/health
   # Should return: {"status":"UP",...}
   ```

## 📑 CI/CD Integration

### GitHub Actions Example

```yaml
name: Build and Test

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'
      - name: Build with Maven
        run: mvn clean install
      - name: Run Tests
        run: mvn test
      - name: Generate Coverage Report
        run: mvn jacoco:report
```

## 📝 Manual Testing

Once the application is running, test endpoints manually:

### 1. Health Check
```bash
curl http://localhost:8080/api/v1/ai/health
```

### 2. Chat Endpoint
```bash
curl -X POST http://localhost:8080/api/v1/ai/chat \
  -H "Content-Type: application/json" \
  -d '{"message":"Hello AI!"}'
```

### 3. Summarize Endpoint
```bash
curl -X POST http://localhost:8080/api/v1/ai/summarize \
  -H "Content-Type: application/json" \
  -d '{"text":"Long text here...","maxSummaryLength":50}'
```

## ✅ Success Criteria

Your build is successful when:

- ✅ All tests pass (50+ tests)
- ✅ Code coverage > 80%
- ✅ JAR file created in `target/` directory
- ✅ Application starts without errors
- ✅ Health endpoint returns `{"status":"UP"}`
- ✅ No compilation errors or warnings

## 📞 Support

If you encounter issues:

1. Check the [README.md](README.md) for detailed setup instructions
2. Review the [Troubleshooting](#troubleshooting) section above
3. Check Maven logs: `target/maven-status/`
4. Review test reports: `target/surefire-reports/`
5. Open an issue on GitHub with error details

---

**Note:** The project is designed to work without an actual OpenAI API key for testing purposes. All tests use mocked responses.
