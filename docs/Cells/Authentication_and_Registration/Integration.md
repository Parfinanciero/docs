## 📝 5. Integration with Other Services

### 📌 External Dependencies

This authentication and registration microservice integrates with multiple external services to ensure a secure and efficient workflow. Below are the main dependencies along with their roles within the system:

- **Firebase Authentication**: Manages OAuth 2.0 authentication with Google and Microsoft. Provides identity tokens that are validated in the backend.
- **PostgreSQL**: A relational database used to store users, roles, and permissions, ensuring data persistence and security.
- **Spring Security**: A security framework that implements authentication and authorization management in the backend.
- **JWT (JSON Web Tokens)**: A mechanism for secure authentication between microservices and clients through signed tokens.
- **API Gateway**: Acts as an intermediary between clients and internal services, ensuring security and traffic control.

---

### 🔹 API Contracts with Other Services

The authentication microservice communicates with other microservices within the **Parfinanciero** ecosystem through **RESTful APIs**. Below are the primary interactions:

#### **1. Integration with the Finance Microservice**
**Purpose:** Ensure that only authenticated users can access financial functions.

**Mechanism:**
- Clients send a request with their **JWT token** in the `Authorization` header.
- The Finance microservice delegates token validation to the authentication microservice.
- If the token is valid, access is granted; otherwise, a `401 Unauthorized` error is returned.

#### **2. Integration with the Role Management Microservice**
**Purpose:** Assign and verify user roles within the ecosystem.

**Mechanism:**
- During login, the authentication microservice retrieves user roles from the database.
- When validating a JWT token, the user’s permissions are extracted and transmitted to other services.
- Services can restrict certain functions based on assigned roles (`ADMIN`, `USER`, `ANALYST`, etc.).

#### **3. Integration with the Notification Microservice**
**Purpose:** Send notifications to users after authentication and registration events.

**Mechanism:**
- When a user registers, the authentication microservice emits an event to trigger notifications.
- A **welcome or account verification email** is sent to the user.
- Alerts are generated for events such as suspicious login attempts.

---

### 🔄 Authentication and Authorization Mechanisms

This microservice implements multiple authentication and authorization methods to ensure a secure and efficient environment:

#### **1. Authentication via Firebase OAuth 2.0**
- Login is allowed using Google and Microsoft credentials.
- Firebase provides an **identity token**, which the backend validates and exchanges for a JWT for internal requests.

#### **2. Authentication via JWT**
- Each authenticated user receives a **JWT token**, which contains their `ID`, `roles`, and other metadata.
- In each protected request, the client sends the JWT in the `Authorization` header.
- Microservices validate the JWT before processing the request to ensure that the user has the correct permissions.

#### **3. Security Middleware**
- All requests to protected endpoints are intercepted.
- The validity of the JWT token is verified.
- It is checked whether the user has the appropriate permissions before executing an action.

#### **JWT Validation in Spring Security**

The following is a Java implementation example for **JWT token validation** using `Spring Security`:

```java
public class JwtRequestFilter extends OncePerRequestFilter {
    @Autowired
    private JwtTokenUtil jwtTokenUtil;
    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
            throws ServletException, IOException {
        final String authorizationHeader = request.getHeader("Authorization");
        
        String username = null;
        String jwt = null;
        
        if (authorizationHeader != null && authorizationHeader.startsWith("Bearer ")) {
            jwt = authorizationHeader.substring(7);
            username = jwtTokenUtil.extractUsername(jwt);
        }
        
        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = this.userDetailsService.loadUserByUsername(username);
            
            if (jwtTokenUtil.validateToken(jwt, userDetails)) {
                UsernamePasswordAuthenticationToken authToken = new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
                authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }
        chain.doFilter(request, response);
    }
}
```

---

### 📌 Implementation Benefits
- **Security**: Robust authentication implementation based on **JWT and OAuth 2.0**.
- **Scalability**: Modular integration with other microservices.
- **Efficiency**: Fast token validation and role-based authorization.
- **Maintainability**: Well-structured code for easy updates and improvements.

