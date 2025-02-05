## 📝 4. API Endpoints

### 📌 Endpoint Documentation
This microservice exposes the following endpoints for authentication and user management. The documentation details the HTTP methods, routes, required parameters, and expected responses, including technical explanations of their internal functionality.

---

### 🔹 1. User Registration
- **Method:** `POST`
- **URL:** `/api/auth/register`

#### **Request Parameters**
```json
{
  "email": "user@example.com",
  "password": "SecurePassword123",
  "name": "John Doe"
}
```

#### **Code Explanation**
This endpoint allows new users to register on the platform.

1. **Data Validation:** Ensures that the `email`, `password`, and `name` fields follow the correct format and are not empty.
2. **Existence Check:** Queries the database to check if a user with the same email already exists.
3. **Password Hashing:** Uses a secure hashing algorithm (e.g., BCrypt) to encrypt the password before storing it in the database.
4. **User Creation:** A new entry is created in the PostgreSQL database with the validated and encrypted data.
5. **Response:** Returns a success or error message based on the operation outcome.

#### **Code Snippet (Java)**
```java
@PostMapping("/register")
public ResponseEntity<?> registerUser(@RequestBody RegisterRequest request) {
    if (userService.existsByEmail(request.getEmail())) {
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("User already exists.");
    }
    User user = new User(request.getEmail(), passwordEncoder.encode(request.getPassword()), request.getName());
    userService.save(user);
    return ResponseEntity.status(HttpStatus.CREATED).body("User registered successfully.");
}
```

#### **Response Structure**
**Success (201 Created)**
```json
{
  "message": "User registered successfully.",
  "user": {
    "id": "123e4567-e89b-12d3-a456-426614174000",
    "email": "user@example.com",
    "name": "John Doe"
  }
}
```

**Possible HTTP Status Codes:**
- `201 Created`: User registered successfully.
- `400 Bad Request`: Invalid data or email already in use.
- `500 Internal Server Error`: Server error.

---

### 🔹 2. User Login
- **Method:** `POST`
- **URL:** `/api/auth/login`

#### **Request Parameters**
```json
{
  "email": "user@example.com",
  "password": "SecurePassword123"
}
```

#### **Code Explanation**
This endpoint allows users to authenticate and receive a JWT token.

1. **Data Validation:** Ensures that the `email` and `password` fields are present and formatted correctly.
2. **Authentication:** Searches for the user in the database and compares the provided password with the stored hash.
3. **JWT Generation:** If credentials are valid, generates a JWT containing user information.
4. **Response:** Returns the JWT token and user details.

#### **Code Snippet (Java)**
```java
@PostMapping("/login")
public ResponseEntity<?> loginUser(@RequestBody LoginRequest request) {
    User user = userService.findByEmail(request.getEmail())
                  .orElseThrow(() -> new UsernameNotFoundException("User not found"));
    if (!passwordEncoder.matches(request.getPassword(), user.getPassword())) {
        return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Invalid credentials.");
    }
    String token = jwtProvider.generateToken(user);
    return ResponseEntity.ok(new LoginResponse(token, user.getId(), user.getEmail(), user.getName()));
}
```

---

### 🔹 3. External Provider Authentication (Google/Microsoft)
- **Method:** `POST`
- **URL:** `/api/auth/oauth`

#### **Request Parameters**
```json
{
  "provider": "google",
  "token": "ya29.a0AfH6SMA..."
}
```

#### **Code Explanation**
This endpoint allows user authentication via external providers like Google and Microsoft.

1. **Data Validation:** Ensures that the `provider` and `token` fields are present and properly formatted.
2. **Token Verification:** Queries the corresponding provider (Google/Microsoft) to validate the received token.
3. **User Information Retrieval:** Extracts the authenticated user’s details from the provider's token.
4. **User Registration/Login:** If the user does not exist in the database, a new record is created; otherwise, access is granted.
5. **JWT Generation:** A valid JWT token is generated for the authenticated user.
6. **Response:** Returns the JWT token along with user details.

#### **Code Snippet (Java)**
```java
@PostMapping("/oauth")
public ResponseEntity<?> oauthLogin(@RequestBody OAuthRequest request) {
    OAuthUser oAuthUser = externalAuthService.verifyToken(request.getProvider(), request.getToken());
    User user = userService.findOrCreateUser(oAuthUser);
    String token = jwtProvider.generateToken(user);
    return ResponseEntity.ok(new LoginResponse(token, user.getId(), user.getEmail(), user.getName()));
}
```

---

### 🔹 4. User Profile Information Retrieval
- **Method:** `GET`
- **URL:** `/api/auth/me`

#### **Request Headers**
```http
Authorization: Bearer <jwt-token>
```

#### **Code Explanation**
This endpoint allows an authenticated user to retrieve their profile information.

1. **Authentication:** Extracts and validates the JWT token from the `Authorization` header.
2. **Token Decoding:** Retrieves the user identifier from the token.
3. **Database Query:** Fetches the authenticated user’s details from PostgreSQL.
4. **Response:** Returns the user's information if valid.

#### **Code Snippet (Java)**
```java
@GetMapping("/me")
public ResponseEntity<?> getUserProfile(@RequestHeader("Authorization") String authHeader) {
    String token = authHeader.replace("Bearer ", "");
    if (jwtProvider.validateToken(token)) {
        String userId = jwtProvider.getUserIdFromJWT(token);
        Optional<User> user = userService.findById(userId);
        return user.map(ResponseEntity::ok)
                   .orElseGet(() -> ResponseEntity.status(HttpStatus.NOT_FOUND).body("User not found."));
    }
    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Invalid or expired token.");
}
```