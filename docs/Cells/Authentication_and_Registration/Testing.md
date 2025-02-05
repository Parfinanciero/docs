# 🛠️ Testing Strategy for Parfinanciero Authentication & Registration Microservice

## 📌 Introduction
This document outlines the testing approach used in the **Parfinanciero Authentication & Registration Microservice**, focusing on the actual implementation within the repository. It covers the testing strategy, tools, coverage, and examples of test cases derived from the project’s test suite.

---

## 🎯 Testing Objectives
- Ensure user registration and authentication processes function correctly.
- Validate the integration with **Google & Microsoft OAuth 2.0**.
- Verify JWT token generation, validation, and security.
- Ensure correct role-based access control (RBAC) enforcement.
- Detect security vulnerabilities through automated testing.
- Maintain code reliability with high test coverage and CI/CD automation.

---

## 🏗️ Testing Implementation in the Project
### 1. **Unit Testing**
- **Scope:** Validates core functionalities such as user creation, password hashing, JWT token handling, and authentication logic.
- **Tools:** JUnit 5, Mockito.
- **Implementation:**
  - Unit tests are located in `src/test/java/com/parfinanciero/authentication`.
  - Mockito is used for mocking dependencies such as database queries and authentication providers.
  - Example:
    ```java
    @Test
    void shouldCreateUserSuccessfully() {
        when(userRepository.save(any(User.class))).thenReturn(mockUser);
        User savedUser = userService.createUser(mockUser);
        assertNotNull(savedUser);
    }
    ```

### 2. **Integration Testing**
- **Scope:** Ensures proper interaction between components such as the database, authentication services, and JWT handling.
- **Tools:** Spring Boot Test, TestContainers.
- **Implementation:**
  - Integration tests validate real interactions with PostgreSQL and external authentication services.
  - Example:
    ```java
    @Test
    void shouldAuthenticateWithValidOAuthToken() {
        String token = "validGoogleToken";
        OAuthUser oAuthUser = externalAuthService.verifyToken("google", token);
        assertNotNull(oAuthUser);
    }
    ```

### 3. **Functional Testing**
- **Scope:** Verifies that API endpoints function as expected.
- **Tools:** Postman, RestAssured.
- **Implementation:**
  - Test cases cover user registration, login, role validation, and JWT validation.
  - Example:
    ```java
    given()
        .contentType("application/json")
        .body(new LoginRequest("user@example.com", "password123"))
    .when()
        .post("/api/auth/login")
    .then()
        .statusCode(200)
        .body("token", notNullValue());
    ```

### 4. **Security Testing**
- **Scope:** Ensures security mechanisms prevent vulnerabilities like SQL Injection and XSS.
- **Tools:** OWASP ZAP, Burp Suite.
- **Implementation:**
  - Automated security scans are performed using OWASP ZAP.
  - Tests validate proper JWT handling to prevent token tampering.
  - Example:
    ```java
    @Test
    void shouldRejectTamperedJwtToken() {
        String tamperedToken = "maliciousToken";
        assertThrows(JwtException.class, () -> jwtProvider.validateToken(tamperedToken));
    }
    ```

### 5. **Performance Testing**
- **Scope:** Evaluates system performance under heavy load.
- **Tools:** JMeter.
- **Implementation:**
  - Simulated user logins and registrations at scale.
  - Example scenario:
    - 1000 concurrent users attempt to log in within 10 seconds.

### 6. **Continuous Integration (CI) Testing**
- **Scope:** Ensures that new commits do not break functionality.
- **Tools:** GitHub Actions.
- **Implementation:**
  - A GitHub Actions workflow (`.github/workflows/ci.yml`) runs all tests on every commit.
  - Example:
    ```yaml
    jobs:
      test:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout code
            uses: actions/checkout@v2
          - name: Run tests
            run: mvn test
    ```

---

## 📝 Example Test Cases

### **User Registration**
| ID  | Test Case Description  | Expected Result  |
|---|---|---|
| TC-01 | Register with valid details | 201 Created, user saved |
| TC-02 | Register with duplicate email | 400 Bad Request, "User already exists" |
| TC-03 | Register with weak password | 400 Bad Request, "Password too weak" |

### **Login**
| ID  | Test Case Description  | Expected Result  |
|---|---|---|
| TC-10 | Login with correct credentials | 200 OK, JWT issued |
| TC-11 | Login with incorrect password | 401 Unauthorized |
| TC-12 | Login with expired token | 401 Unauthorized, "Token expired" |

### **Security**
| ID  | Test Case Description  | Expected Result  |
|---|---|---|
| TC-20 | SQL Injection in login | 400 Bad Request, input sanitized |
| TC-21 | Access admin endpoint as regular user | 403 Forbidden |
| TC-22 | Tamper JWT payload | 401 Unauthorized, "Invalid token" |

