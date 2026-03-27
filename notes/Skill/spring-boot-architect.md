---
name: spring-boot-architect
description: Provides technical guidance for building standard, robust systems with Spring Boot. Recommends technology stacks, architecture patterns, and best practices for data persistence, caching, security, observability, testing, and deployment.
license: MIT
metadata:
  domain: architecture
  framework: spring-boot
  version: "1.0"
---
# Spring Boot Architect
## Purpose
Act as a technical advisor to help design and build a standard, robust system using Spring Boot as the backend. Provide curated technology recommendations, architectural patterns, and best practices tailored to the user’s needs. This skill ensures the system is scalable, maintainable, and follows industry standards.
## When to Use
- User asks for help building a new Spring Boot system.
- User wants technology stack recommendations.
- User needs architectural guidance for a Spring Boot project.
- User is uncertain about choices for persistence, caching, security, or deployment.
- User requests a review of proposed architecture.
## Core Principles
- **Standards First**: Prefer proven, widely adopted technologies.
- **Simplicity**: Choose simplicity unless complexity is justified.
- **Scalability**: Design for horizontal scaling from the start.
- **Observability**: Build in metrics, logs, and traces.
- **Security**: Apply security at all layers.
- **Testability**: Architect for easy unit and integration testing.
---
## Technology Stack Recommendations
### Core Framework
| Technology | Recommendation | Rationale |
|------------|----------------|-----------|
| Java | 17+ (LTS) | Long-term support, modern features (records, sealed classes). |
| Spring Boot | 3.x | Latest, Jakarta EE 10, improved observability, AOT support. |
| Build Tool | Maven | Predictable convention; Gradle if advanced customisation needed. |
### Data Persistence
| Technology | Recommendation | Rationale |
|------------|----------------|-----------|
| Primary Database | PostgreSQL | Robust, ACID, open source, excellent Spring support. |
| NoSQL (if needed) | MongoDB | For flexible schemas; avoid unless necessary. |
| Migrations | Flyway | Version-controlled schema changes, simple SQL-based. |
| ORM | Spring Data JPA (Hibernate) | Standard, but caution with N+1 queries. |
| Complex Queries | jOOQ or MyBatis | Use if JPA becomes cumbersome. |
| Connection Pool | HikariCP | Default, best performance. |
### Caching
| Technology | Recommendation | Rationale |
|------------|----------------|-----------|
| Local Cache | Caffeine | High-performance, low-latency for single node. |
| Distributed Cache | Redis | Multi-node, session storage, rate limiting, pub/sub. |
### Security
| Technology | Recommendation | Rationale |
|------------|----------------|-----------|
| Authentication | Spring Security + JWT | Stateless, scalable. |
| Identity Provider | Keycloak (or Okta/Auth0) | For SSO and user management. |
| Password Hashing | BCrypt / Argon2 | Spring Security defaults. |
### API & Communication
| Technology | Recommendation | Rationale |
|------------|----------------|-----------|
| REST API | Spring Web + OpenAPI 3 (SpringDoc) | Standard, with auto‑documentation. |
| Internal Services | gRPC | For high-performance microservices. |
| Messaging | Kafka or RabbitMQ | Async processing, decoupling. |
### Observability
| Technology | Recommendation | Rationale |
|------------|----------------|-----------|
| Metrics | Micrometer + Prometheus | Built‑in, de facto standard. |
| Dashboards | Grafana | Visualize metrics. |
| Log Aggregation | ELK (Elasticsearch, Logstash, Kibana) or Loki | Centralized logs. |
| Tracing | OpenTelemetry (Micrometer Tracing) | Distributed tracing. |
### Testing
| Technology | Recommendation | Rationale |
|------------|----------------|-----------|
| Unit | JUnit 5 + Mockito | Standard. |
| Integration | Testcontainers | Real dependencies in tests. |
| API Testing | RestAssured | Fluent API for REST. |
| Coverage | Jacoco | Set thresholds (e.g., 80% service layer). |
### Development & Deployment
| Technology | Recommendation | Rationale |
|------------|----------------|-----------|
| Containerization | Docker | Reproducible environments. |
| Orchestration | Kubernetes | Scalability, resilience. |
| CI/CD | GitHub Actions / GitLab CI | Automate build, test, deploy. |
| Infrastructure as Code | Terraform | Manage cloud resources. |
---
## Architecture Patterns
### Layered Architecture

Controller → Service → Repository

text

- **Controller**: Handles HTTP, validation, DTO mapping.
- **Service**: Business logic, transactions, caching.
- **Repository**: Data access (JPA, SQL).
### DTO Pattern
- Never expose entities directly.
- Use `Request` and `Response` DTOs.
- Map with **MapStruct** (compile‑time, type‑safe).
### Global Exception Handling
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusiness(BusinessException ex) {
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
            .body(new ErrorResponse(ex.getMessage(), LocalDateTime.now()));
    }
}
```

### Externalized Configuration

- Use `application-{profile}.yml` files.
    
- Profile‑specific properties: `local`, `dev`, `prod`.
    
- Prefer environment variables for secrets.
    

### Statelessness

- Store session state in **Redis** if needed.
    
- Design services to be horizontally scalable.
    

---

## Robustness Considerations

### Resilience

- Use **Resilience4j** for circuit breakers, retries, timeouts.
    
- Implement **idempotency keys** for critical operations.
    

### Performance

- Enable caching with **Caffeine** or **Redis**.
    
- Optimize JPA queries: use fetch joins, DTO projections, avoid N+1.
    
- Implement pagination for large datasets.
    

### Security

- Enforce HTTPS (even in internal communication).
    
- Use **Spring Security** method‑level security (`@PreAuthorize`).
    
- Sanitize inputs, use parameterised queries.
    

### Observability

- Expose `/actuator/health`, `/actuator/metrics`.
    
- Add custom metrics with Micrometer.
    
- Ensure logs are structured (JSON) for easy aggregation.
    

### Graceful Shutdown

- Enable `server.shutdown=graceful` in Spring Boot.
    
- Set appropriate timeout.
    

---

## Project Structure Example

```
├── pom.xml
├── src/main/java/com/example/app
│   ├── AppApplication.java
│   ├── common/
│   │   ├── config/         (Security, Swagger, Web, etc.)
│   │   ├── exception/      (Global handler, custom exceptions)
│   │   └── util/
│   ├── domain/
│   │   ├── user/
│   │   │   ├── controller/ UserController.java
│   │   │   ├── service/    UserService.java, UserServiceImpl.java
│   │   │   ├── repository/ UserRepository.java
│   │   │   ├── model/      User.java (Entity)
│   │   │   └── dto/        UserRequest.java, UserResponse.java
│   │   └── product/
│   └── infrastructure/
│       └── persistence/    (JPA config, auditing)
├── src/main/resources
│   ├── application.yml
│   ├── application-local.yml
│   └── db/migration/       (Flyway scripts)
└── src/test/               (Unit, integration tests)
```
