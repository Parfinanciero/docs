# 🚀 Development Notes for Parfinanciero Authentication & Registration Microservice

## 📌 Overview
This document provides insights into the **development decisions, challenges encountered, and solutions** applied during the implementation of the **Parfinanciero Authentication & Registration Microservice**. The goal is to maintain a structured development workflow and document key learnings for future improvements.

---

## 🏗️ Architectural Decisions
### 1. **Microservices Architecture**
- **Decision:** The project follows a microservices-based architecture to ensure scalability and modularity.
- **Justification:**
  - Enables independent deployment of authentication services.
  - Allows easy integration with other microservices (Finance, Notifications, Role Management).
  - Improves fault isolation and resilience.

### 2. **Technology Stack**
- **Backend Framework:** Spring Boot (Java) for a robust and scalable backend.
- **Database:** PostgreSQL for structured storage and ACID compliance.
- **Authentication:** Firebase Authentication for OAuth 2.0 (Google & Microsoft).
- **Security:** JWT for secure token-based authentication.
- **Containerization:** Docker to ensure consistency across development and production.

### 3. **API Design & RESTful Principles**
- **Decision:** Implement a RESTful API with clear endpoint structures.
- **Justification:**
  - Ensures predictability and ease of integration.
  - Allows standardized authentication flows with OAuth and JWT.
  - Implements proper HTTP methods (`POST`, `GET`, `PUT`, `DELETE`).

---

## 🔥 Challenges & Solutions
### 1. **Handling OAuth Token Validation Issues**
- **Problem:** Some OAuth tokens from Google/Microsoft were expiring too quickly, causing failed authentication.
- **Solution:** Implemented a token refresh strategy, where expired tokens trigger a new request for a fresh access token before invalidating the session.

### 2. **Ensuring Secure Role-Based Access Control (RBAC)**
- **Problem:** Users were able to access admin-restricted endpoints due to incorrect role validation logic.
- **Solution:**
  - Implemented a Spring Security filter that checks the user role in every request.
  - Introduced custom annotations for role-based authorization (`@PreAuthorize("hasRole('ADMIN')")`).
  - Enforced permission validation at both controller and service levels.

### 3. **Database Performance Optimization**
- **Problem:** User authentication queries were causing slowdowns under heavy load.
- **Solution:**
  - Optimized PostgreSQL indexing for frequent queries (`CREATE INDEX ON users (email)`).
  - Used **connection pooling** with HikariCP to handle multiple database requests efficiently.
  - Implemented caching mechanisms for role-based permissions using **Redis**.

### 4. **Managing JWT Expiry & Refresh Tokens**
- **Problem:** JWT tokens were expiring too soon, forcing users to re-authenticate frequently.
- **Solution:**
  - Introduced **refresh tokens** to allow users to obtain new JWTs without logging in again.
  - Extended expiration duration for non-critical API interactions.
  - Implemented token invalidation on logout by storing blacklisted tokens in a cache.

---

## 🔄 CI/CD & Deployment Strategies
### 1. **Continuous Integration (CI)**
- **GitHub Actions Workflow:**
  - Runs unit and integration tests on every push and pull request.
  - Performs security scans using OWASP Dependency Check.
  - Example workflow snippet:
    ```yaml
    jobs:
      test:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout repository
            uses: actions/checkout@v2
          - name: Set up JDK
            uses: actions/setup-java@v1
            with:
              java-version: '11'
          - name: Run tests
            run: mvn test
    ```

### 2. **Continuous Deployment (CD)**
- **Docker-based Deployments:**
  - The microservice is deployed as a **Docker container**.
  - Uses **Docker Compose** for managing multiple services (PostgreSQL, Redis, API Gateway).
- **Staging & Production Environments:**
  - Uses separate databases and security configurations for testing vs. live environments.
  - Deployed on AWS EC2 instances with auto-scaling capabilities.

---

## 📊 Performance & Monitoring
### 1. **Metrics Collection**
- Integrated **Prometheus** and **Grafana** for real-time monitoring of:
  - API response times.
  - Database query performance.
  - Authentication success/failure rates.

### 2. **Load Testing**
- Used **JMeter** to simulate **1000 concurrent users** logging in simultaneously.
- **Result:** The system maintained an average response time of **<500ms** under peak load.

---
