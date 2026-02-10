# 🤝 Contributing to Spring AI Demo

Thank you for your interest in contributing to the Spring AI Demo project! This document provides guidelines and instructions for contributing.

## 📜 Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)
- [Testing Guidelines](#testing-guidelines)
- [Commit Messages](#commit-messages)
- [Pull Request Process](#pull-request-process)
- [Reporting Issues](#reporting-issues)

## 🤝 Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inspiring community for all. Please be respectful and constructive in all interactions.

### Expected Behavior

- ✅ Be respectful and inclusive
- ✅ Provide constructive feedback
- ✅ Focus on what is best for the community
- ✅ Show empathy towards others

### Unacceptable Behavior

- ❌ Harassment or discriminatory language
- ❌ Personal attacks or trolling
- ❌ Publishing others' private information
- ❌ Unprofessional conduct

## 🚀 Getting Started

### 1. Fork the Repository

```bash
# Click the "Fork" button on GitHub
# Clone your fork
git clone https://github.com/YOUR_USERNAME/spring-ai-demo.git
cd spring-ai-demo
```

### 2. Set Up Development Environment

```bash
# Add upstream remote
git remote add upstream https://github.com/ORIGINAL_OWNER/spring-ai-demo.git

# Install dependencies
mvn clean install

# Run tests to verify setup
mvn test
```

### 3. Create a Branch

```bash
# Update your fork
git checkout main
git pull upstream main

# Create feature branch
git checkout -b feature/your-feature-name
```

## 💻 Development Workflow

### 1. Make Changes

- Write clean, readable code
- Follow existing code style
- Add comments for complex logic
- Update documentation as needed

### 2. Write Tests

- Add unit tests for new functionality
- Ensure all tests pass: `mvn test`
- Maintain code coverage >85%
- Test edge cases and error scenarios

### 3. Update Documentation

- Update README.md if needed
- Add JavaDoc for public methods
- Update API documentation
- Add examples for new features

### 4. Run Quality Checks

```bash
# Run all tests
mvn test

# Check code coverage
mvn jacoco:report

# Verify build
mvn clean install
```

## 📖 Coding Standards

### Java Code Style

```java
// Use descriptive variable names
String userMessage = "Hello";  // Good
String msg = "Hello";          // Avoid

// Follow Java naming conventions
public class AiService { }     // PascalCase for classes
private String apiKey;         // camelCase for variables
public static final int MAX = 100;  // UPPERCASE for constants

// Add JavaDoc for public methods
/**
 * Processes a chat request and returns AI response
 * 
 * @param request The chat request containing user message
 * @return ChatResponse with AI-generated content
 * @throws RuntimeException if AI service fails
 */
public ChatResponse chat(ChatRequest request) {
    // Implementation
}
```

### Code Organization

```
✅ Keep classes focused (Single Responsibility Principle)
✅ Use dependency injection
✅ Prefer composition over inheritance
✅ Keep methods short (<20 lines)
✅ Use meaningful package structure
```

### Best Practices

```java
// Use Lombok to reduce boilerplate
@Data
@Builder
public class ChatRequest {
    private String message;
}

// Use Optional for nullable values
public Optional<User> findUser(String id) {
    return userRepository.findById(id);
}

// Use proper exception handling
try {
    return aiService.chat(request);
} catch (Exception e) {
    log.error("Failed to process chat", e);
    throw new RuntimeException("Chat processing failed", e);
}

// Use constants instead of magic numbers
private static final int MAX_MESSAGE_LENGTH = 5000;
```

## 🧪 Testing Guidelines

### Unit Tests

```java
@Test
@DisplayName("Should process chat request successfully")
void testChatSuccess() {
    // Arrange
    ChatRequest request = ChatRequest.builder()
            .message("Test message")
            .build();
    when(chatClient.call(any())).thenReturn(mockResponse);

    // Act
    ChatResponse response = aiService.chat(request);

    // Assert
    assertNotNull(response);
    assertEquals("Expected response", response.getResponse());
    verify(chatClient, times(1)).call(any());
}
```

### Test Coverage Requirements

- ✅ All new code must have tests
- ✅ Maintain >85% overall coverage
- ✅ Test happy paths and error scenarios
- ✅ Test edge cases (null, empty, invalid inputs)
- ✅ Use descriptive test names

### Integration Tests

```java
@SpringBootTest
@AutoConfigureMockMvc
class AiControllerIntegrationTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Test
    void testChatEndpoint() throws Exception {
        mockMvc.perform(post("/api/v1/ai/chat")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"message\":\"Hello\"}"))
                .andExpect(status().isOk());
    }
}
```

## 📝 Commit Messages

### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

### Examples

```bash
# Good commit messages
feat(chat): add temperature parameter to chat request
fix(service): handle null pointer in summarization
docs(readme): update API documentation with examples
test(controller): add integration tests for chat endpoint

# Bad commit messages
Update code
Fix bug
Changes
```

### Best Practices

- ✅ Use present tense ("add" not "added")
- ✅ Keep subject line under 50 characters
- ✅ Capitalize subject line
- ✅ Don't end subject with period
- ✅ Use body to explain what and why

## 🔄 Pull Request Process

### 1. Before Submitting

```bash
# Update your branch
git checkout main
git pull upstream main
git checkout feature/your-feature
git rebase main

# Run all tests
mvn clean test

# Verify build
mvn clean install
```

### 2. Submit Pull Request

1. Push your branch: `git push origin feature/your-feature`
2. Go to GitHub and create Pull Request
3. Fill out the PR template completely
4. Link related issues
5. Request review from maintainers

### 3. PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] All tests pass
- [ ] New tests added
- [ ] Code coverage maintained

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] No new warnings
```

### 4. Review Process

- Maintainers will review your PR
- Address feedback promptly
- Make requested changes
- Keep PR focused and small
- Be responsive to comments

### 5. After Approval

- PR will be merged by maintainers
- Delete your feature branch
- Update your fork

## 🐛 Reporting Issues

### Bug Reports

**Include:**
- Clear, descriptive title
- Steps to reproduce
- Expected vs actual behavior
- Environment details (Java version, OS)
- Error messages and stack traces
- Screenshots if applicable

**Template:**
```markdown
## Bug Description
Clear description of the bug

## Steps to Reproduce
1. Step one
2. Step two
3. Step three

## Expected Behavior
What should happen

## Actual Behavior
What actually happens

## Environment
- Java Version: 17
- Spring Boot: 3.2.1
- OS: Windows 11

## Additional Context
Any other relevant information
```

### Feature Requests

**Include:**
- Clear use case
- Proposed solution
- Alternatives considered
- Benefits and impact

## 💬 Communication

- **GitHub Issues:** Bug reports and feature requests
- **Pull Requests:** Code discussions
- **Discussions:** General questions and ideas

## ✅ Checklist for Contributors

Before submitting your contribution:

- [ ] Code follows project style guidelines
- [ ] All tests pass (`mvn test`)
- [ ] New tests added for new functionality
- [ ] Code coverage >85%
- [ ] Documentation updated
- [ ] Commit messages follow conventions
- [ ] PR template filled out completely
- [ ] No merge conflicts
- [ ] Self-review completed

## 🎉 Recognition

All contributors will be recognized in:
- README.md Contributors section
- Release notes
- Project documentation

## 📞 Questions?

If you have questions:
1. Check existing documentation
2. Search closed issues
3. Open a new discussion
4. Reach out to maintainers

---

**Thank you for contributing to Spring AI Demo! 🚀**
