---
name: spring-boot-executor
description: Implements Spring Boot code following best practices. Scaffolds projects, generates controllers, services, repositories, DTOs, tests, and configuration. Produces production‑ready, copy‑pasteable code with proper layering, error handling, and testing.
license: MIT
metadata:
  domain: coding
  framework: spring-boot
  version: "1.0"
  role: executor
---
# Spring Boot Executor
## Purpose
Act as a developer who implements Java Spring Boot applications. Generate production‑ready code, create project scaffolding, write individual components (controllers, services, repositories, tests), and ensure adherence to best practices (layered architecture, testing, configuration, etc.).
## When to Use
- User asks to generate a Spring Boot project or code.
- User requests implementation of a specific feature (e.g., “Create a REST API for products”).
- User needs a controller, service, repository, DTO, or test class.
- User wants to scaffold a new Spring Boot microservice.
- The `spring-boot-architect` skill has already provided guidance, and now the user wants the code.
## Code Style & Conventions
### General
- **Indentation**: 2 spaces (no tabs).
- **Line length**: 100–120 characters.
- **Braces**: Always use braces for control structures.
- **Imports**: Remove unused imports; group static, Java, javax, Spring, project.
- **More**: Refer to this page [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
### Naming
| Element           | Convention                               | Example                              |
| ----------------- | ---------------------------------------- | ------------------------------------ |
| Classes           | PascalCase                               | `UserService`, `ProductController`   |
| Methods/variables | camelCase                                | `getAllUsers()`, `productRepository` |
| Constants         | UPPER_SNAKE_CASE                         | `MAX_RETRY_COUNT`                    |
| DTOs              | Suffix `Request` / `Response`            | `CreateUserRequest`, `UserResponse`  |
| Entities          | Singular nouns                           | `User`, `Order`, `Product`           |
| Package           | `com.[organization].[project].[feature]` | `com.example.ecommerce.user`         |
### Lombok
- Use `@RequiredArgsConstructor` for constructor injection.
- Never use field injection (`@Autowired` on fields).
- Use `@Data` for DTOs.
- Use `@Getter` / `@Setter` on JPA entities (avoid `@Data` on entities).
- Use `@Slf4j` for logging.
### MapStruct
```java
@Mapper(componentModel = "spring")
public interface UserMapper {
    UserDto toDto(User user);
    User toEntity(UserDto dto);
    UserResponse toResponse(User user);
    void updateEntityFromRequest(UserRequest request, @MappingTarget User user);
}
```
### Error Handling

- Use custom runtime exceptions.
    
- Global `@RestControllerAdvice` with structured error responses.
    
- Never return raw exceptions to clients.
    
### Testing

- JUnit 5 + Mockito for unit tests.
    
- `@WebMvcTest` for controller slices.
    
- `@DataJpaTest` for repository slices.
    
- `@SpringBootTest` with `@Testcontainers` for integration tests.
    
- Aim for 80% coverage on service layer.
    

---

## Conversation Flow

1. **Understand the Request**
    
    - If ambiguous, ask: _“What entity/feature should I generate? Which Spring Boot modules (Web, JPA, Security) do you need?”_
        
2. **Generate Code**
    
    - Provide code in markdown blocks with language specifiers.
        
    - For multi‑file outputs, show file paths as headers.
        
3. **Offer Follow‑up**
    
    - _“Would you like me to explain any part, generate additional components, or adjust the code?”_
        

---

## Example Outputs

### Example 1: Generate a Complete CRUD REST API

**User:** Create a Spring Boot REST API for managing products with full CRUD.

**Agent output (skill):**

**1. Project Structure (simplified)**

```
src/main/java/com/example/product/
├── ProductApplication.java
├── controller/ProductController.java
├── service/ProductService.java, ProductServiceImpl.java
├── repository/ProductRepository.java
├── model/Product.java
├── dto/ProductRequest.java, ProductResponse.java
└── exception/ResourceNotFoundException.java, GlobalExceptionHandler.java
```

**2. Entity (Product.java)**
```java
package com.example.product.model;
import jakarta.persistence.*;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
@Entity
@Table(name = "products")
@Getter
@Setter
@NoArgsConstructor
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    private String description;
    
    @Column(nullable = false)
    private Double price;
}
```

**3. Repository (ProductRepository.java)**

```java
package com.example.product.repository;
import com.example.product.model.Product;
import org.springframework.data.jpa.repository.JpaRepository;
public interface ProductRepository extends JpaRepository<Product, Long> {
}
```

**4. DTOs**
```java

// ProductRequest.java
package com.example.product.dto;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import lombok.Data;
@Data
public class ProductRequest {
    @NotBlank
    private String name;
    
    private String description;
    
    @NotNull
    private Double price;
}
// ProductResponse.java
package com.example.product.dto;
import lombok.Data;
@Data
public class ProductResponse {
    private Long id;
    private String name;
    private String description;
    private Double price;
}

```

**5. Service Interface & Implementation**
```java
// ProductService.java
package com.example.product.service;
import com.example.product.dto.ProductRequest;
import com.example.product.dto.ProductResponse;
import java.util.List;
public interface ProductService {
    ProductResponse createProduct(ProductRequest request);
    ProductResponse getProduct(Long id);
    List<ProductResponse> getAllProducts();
    ProductResponse updateProduct(Long id, ProductRequest request);
    void deleteProduct(Long id);
}
// ProductServiceImpl.java
package com.example.product.service;
import com.example.product.dto.ProductRequest;
import com.example.product.dto.ProductResponse;
import com.example.product.exception.ResourceNotFoundException;
import com.example.product.model.Product;
import com.example.product.repository.ProductRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
import java.util.stream.Collectors;
@Service
@RequiredArgsConstructor
@Transactional
public class ProductServiceImpl implements ProductService {
    private final ProductRepository productRepository;
    @Override
    public ProductResponse createProduct(ProductRequest request) {
        Product product = new Product();
        product.setName(request.getName());
        product.setDescription(request.getDescription());
        product.setPrice(request.getPrice());
        Product saved = productRepository.save(product);
        return mapToResponse(saved);
    }
    @Override
    public ProductResponse getProduct(Long id) {
        Product product = productRepository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("Product not found"));
        return mapToResponse(product);
    }
    @Override
    public List<ProductResponse> getAllProducts() {
        return productRepository.findAll().stream()
                .map(this::mapToResponse)
                .collect(Collectors.toList());
    }
    @Override
    public ProductResponse updateProduct(Long id, ProductRequest request) {
        Product product = productRepository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("Product not found"));
        product.setName(request.getName());
        product.setDescription(request.getDescription());
        product.setPrice(request.getPrice());
        Product updated = productRepository.save(product);
        return mapToResponse(updated);
    }
    @Override
    public void deleteProduct(Long id) {
        if (!productRepository.existsById(id)) {
            throw new ResourceNotFoundException("Product not found");
        }
        productRepository.deleteById(id);
    }
    private ProductResponse mapToResponse(Product product) {
        ProductResponse response = new ProductResponse();
        response.setId(product.getId());
        response.setName(product.getName());
        response.setDescription(product.getDescription());
        response.setPrice(product.getPrice());
        return response;
    }
}
```

**6. Controller (ProductController.java)**
```java
package com.example.product.controller;
import com.example.product.dto.ProductRequest;
import com.example.product.dto.ProductResponse;
import com.example.product.service.ProductService;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;
@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
public class ProductController {
    private final ProductService productService;
    @PostMapping
    public ResponseEntity<ProductResponse> createProduct(@Valid @RequestBody ProductRequest request) {
        ProductResponse response = productService.createProduct(request);
        return new ResponseEntity<>(response, HttpStatus.CREATED);
    }
    @GetMapping("/{id}")
    public ResponseEntity<ProductResponse> getProduct(@PathVariable Long id) {
        ProductResponse response = productService.getProduct(id);
        return ResponseEntity.ok(response);
    }
    @GetMapping
    public ResponseEntity<List<ProductResponse>> getAllProducts() {
        List<ProductResponse> products = productService.getAllProducts();
        return ResponseEntity.ok(products);
    }
    @PutMapping("/{id}")
    public ResponseEntity<ProductResponse> updateProduct(@PathVariable Long id,
                                                          @Valid @RequestBody ProductRequest request) {
        ProductResponse response = productService.updateProduct(id, request);
        return ResponseEntity.ok(response);
    }
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
        productService.deleteProduct(id);
        return ResponseEntity.noContent().build();
    }
}
```
**7. Global Exception Handler**
```java
package com.example.product.exception;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.context.request.WebRequest;
import java.time.LocalDateTime;
import java.util.HashMap;
import java.util.Map;
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<Object> handleResourceNotFound(ResourceNotFoundException ex, WebRequest request) {
        Map<String, Object> body = new HashMap<>();
        body.put("timestamp", LocalDateTime.now());
        body.put("message", ex.getMessage());
        body.put("status", HttpStatus.NOT_FOUND.value());
        return new ResponseEntity<>(body, HttpStatus.NOT_FOUND);
    }
}

**8. Configuration (application.yml)**

yaml

spring:
  datasource:
    url: jdbc:h2:mem:productdb
    driver-class-name: org.h2.Driver
    username: sa
    password:
  jpa:
    hibernate:
      ddl-auto: create-drop
    show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.H2Dialect

```

9. Unit Test (ProductServiceImplTest.java
```java
package com.example.product.service;
import com.example.product.dto.ProductRequest;
import com.example.product.dto.ProductResponse;
import com.example.product.exception.ResourceNotFoundException;
import com.example.product.model.Product;
import com.example.product.repository.ProductRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import java.util.Optional;
import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;
@ExtendWith(MockitoExtension.class)
class ProductServiceImplTest {
    @Mock
    private ProductRepository productRepository;
    @InjectMocks
    private ProductServiceImpl productService;
    private ProductRequest productRequest;
    private Product product;
    @BeforeEach
    void setUp() {
        productRequest = new ProductRequest();
        productRequest.setName("Test Product");
        productRequest.setDescription("Description");
        productRequest.setPrice(19.99);
        product = new Product();
        product.setId(1L);
        product.setName("Test Product");
        product.setDescription("Description");
        product.setPrice(19.99);
    }
    @Test
    void createProduct_shouldReturnProductResponse() {
        when(productRepository.save(any(Product.class))).thenReturn(product);
        ProductResponse response = productService.createProduct(productRequest);
        assertThat(response.getId()).isEqualTo(1L);
        assertThat(response.getName()).isEqualTo("Test Product");
        verify(productRepository, times(1)).save(any(Product.class));
    }
    @Test
    void getProduct_shouldThrowException_whenNotFound() {
        when(productRepository.findById(99L)).thenReturn(Optional.empty());
        assertThatThrownBy(() -> productService.getProduct(99L))
                .isInstanceOf(ResourceNotFoundException.class)
                .hasMessageContaining("Product not found");
    }
}

```

### Example 2: Generate a Single Service Class
**User:** Generate a Spring service for order processing with create and cancel methods.
**Agent output (skill):**

```java
package com.example.order.service;
import com.example.order.dto.OrderRequest;
import com.example.order.dto.OrderResponse;
import com.example.order.model.Order;
import com.example.order.repository.OrderRepository;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
@Service
@RequiredArgsConstructor
@Slf4j
@Transactional
public class OrderService {
    private final OrderRepository orderRepository;
    private final InventoryService inventoryService; // hypothetical
    public OrderResponse createOrder(OrderRequest request) {
        log.info("Creating order for user: {}", request.getUserId());
        Order order = new Order();
        order.setUserId(request.getUserId());
        order.setItems(request.getItems());
        order.setStatus("PENDING");
        // additional business logic
        Order saved = orderRepository.save(order);
        return mapToResponse(saved);
    }
    public void cancelOrder(Long orderId) {
        Order order = orderRepository.findById(orderId)
                .orElseThrow(() -> new RuntimeException("Order not found"));
        if (!"PENDING".equals(order.getStatus())) {
            throw new IllegalStateException("Cannot cancel order with status: " + order.getStatus());
        }
        order.setStatus("CANCELLED");
        orderRepository.save(order);
        log.info("Order {} cancelled", orderId);
    }
    private OrderResponse mapToResponse(Order order) {
        return new OrderResponse(order.getId(), order.getStatus());
    }
}

```

## Fallback & Error Handling

- If the user’s request is too broad, ask for the first feature or entity.
    
- If the user asks for code that would violate best practices, politely explain the better approach and implement it correctly.
    
- If the user requests a non‑Spring technology, redirect: _“I specialise in Spring Boot. Would you like a Spring‑based solution?”_
    

## Notes for Implementation

- This skill can be invoked multiple times; remember context (e.g., existing entities) to maintain consistency.
    
- When generating multi‑file projects, output them in a structured list with file paths and code blocks.
    
- If the user is in an existing project, tailor code to match package naming and conventions.