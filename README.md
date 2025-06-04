 
### 🔹 1. **@Controller vs @RestController**

#### 📘 Explanation:

* `@Controller` is part of the Spring MVC framework. It’s designed to return **views** (like JSP, Thymeleaf).
* `@RestController` is a specialization of `@Controller` that adds `@ResponseBody` to every method. So the return value is written directly to the HTTP response body (typically as JSON or XML).

#### 📌 Key Difference:

| Aspect               | @Controller | @RestController       |
| -------------------- | ----------- | --------------------- |
| Return               | HTML/View   | JSON/XML              |
| Use-case             | Web UI      | REST API              |
| Needs @ResponseBody? | Yes         | No (already included) |

#### 💡 Example:

```java
@Controller
public class WebController {
    @GetMapping("/home")
    public String homePage() {
        return "home"; // Resolves to home.html or home.jsp
    }
}

@RestController
public class ApiController {
    @GetMapping("/api/data")
    public Map<String, String> data() {
        return Map.of("key", "value");
    }
}
```

#### 🧠 Memory Tip:

“RestController → REST API, ResponseBody by default.”

#### 💬 Interview Highlight:

> If you're building an API-based microservice, use `@RestController`. If you're rendering web pages, use `@Controller`.

---

### 🔹 2. **Advantages and Disadvantages of Spring Boot**

#### ✅ Advantages:

* **Auto-configuration:** Reduces boilerplate. Automatically configures beans based on classpath.
* **Standalone apps:** Comes with embedded servers (Tomcat, Jetty).
* **Production features:** Health checks, metrics (via Spring Actuator).
* **Spring Initializr:** Rapid bootstrapping.
* **Dependency Management:** Uses Spring Boot Starter dependencies.

#### ❌ Disadvantages:

* **Less control:** You may lose visibility over internal configurations.
* **Startup time & size:** Can be slower and heavier than bare Spring.
* **Magic configurations:** Implicit behavior may confuse debugging.

#### 💬 Interview Highlight:

> “Spring Boot trades fine-grained control for speed and productivity. In enterprise microservices, this is usually a win.”

#### 🧠 Memory Tip:

Think of Spring Boot like automatic gear – faster, easier, but you lose the clutch control.

---

### 🔹 3. **Bean in Spring**

#### 📘 Explanation:

A **Bean** is any object that is managed by the **Spring IoC (Inversion of Control) container**. The container creates, configures, and manages the lifecycle of beans.

Beans can be:

* **Singleton (default)** – one instance per Spring context
* **Prototype** – new instance every time it's injected

#### 🧠 Remember:

If Spring manages it = it's a Bean.

#### 💡 Example:

```java
@Component
public class MyService {
    // This is a Bean
}
```

#### 💬 Interview Highlight:

> “Spring beans promote loose coupling and dependency injection. They live in the container, not created manually.”

---

### 🔹 4. **ApplicationContext vs BeanFactory**

#### 📘 Explanation:

| Feature             | BeanFactory      | ApplicationContext                 |
| ------------------- | ---------------- | ---------------------------------- |
| Lazy Initialization | Yes              | No (eager by default)              |
| Advanced Features   | No               | Yes (AOP, i18n, Event Propagation) |
| Suitable for        | Lightweight apps | Enterprise apps                    |

* **BeanFactory** is the root interface.
* **ApplicationContext** extends BeanFactory and adds enterprise features.

#### 💡 Use-case Scenario:

* Use **BeanFactory** in resource-constrained environments like IoT.
* Use **ApplicationContext** in most web apps, as you’ll need events, i18n, and auto bean post-processors.

#### 🧠 Memory Tip:

BeanFactory is a raw spring – light and basic. ApplicationContext is like a full machine – robust and enterprise-ready.

---

### 🔹 5. **IoC Container**

#### 📘 Explanation:

IoC = Inversion of Control. Spring’s **IoC Container** manages objects’ creation and dependencies.

Instead of `new`, you use:

* **@Autowired**
* **Constructor injection**
* **XML or Java Config**

#### 💡 Example:

```java
@Service
public class OrderService {
    private final PaymentService paymentService;

    @Autowired
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

#### 💬 Interview Highlight:

> “IoC decouples class behavior from dependency creation, making code more testable and modular.”

---

### 🔹 6. **Circular Dependency Issue in Spring Boot**

#### 📘 Problem:

Occurs when Bean A depends on B and B depends on A. Spring cannot resolve the cycle during autowiring.

#### 🔧 How to Fix:

1. Use **`@Lazy`** to defer bean loading.
2. Break dependency using **setter injection**.
3. Use **`@Autowired(required = false)`** if optional.
4. In application.properties:

```properties
spring.main.allow-circular-references=true
```

#### 💬 Interview Highlight:

> “Circular dependencies are signs of tight coupling. Better design is to refactor dependencies, not just suppress errors.”

---

### 🔹 7. **JpaRepository vs CrudRepository**

| Feature              | CrudRepository | JpaRepository                    |
| -------------------- | -------------- | -------------------------------- |
| Basic CRUD methods   | Yes            | Yes                              |
| Pagination, Sorting  | No             | Yes (findAll(Pageable), etc.)    |
| JPA-specific methods | No             | Yes (flush, deleteInBatch, etc.) |

#### 💬 Interview Highlight:

> “Use JpaRepository for rich features like pagination and batch deletes. CrudRepository is minimal.”

---

### 🔹 8. **Log4j vs Logback**

* **Logback** is the default logger in Spring Boot.
* If you want to use **Log4j2**, you must exclude Logback:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
    <scope>provided</scope>
</dependency>
```

Then add:

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-spring-boot</artifactId>
</dependency>
 

### 🔹 9. **Spring Profiles**

#### 📘 Explanation:

Spring Profiles allow you to segregate parts of your configuration and beans based on the environment (dev, test, prod, etc.).

You can annotate beans or configs:

```java
@Profile("dev")
@Bean
public DataSource devDataSource() {
    return new H2DataSource();
}
```

Activate via:

* `application.properties`:

  ```properties
  spring.profiles.active=dev
  ```
* Command line:

  ```bash
  --spring.profiles.active=prod
  ```

#### 💬 Interview Highlight:

> “Profiles help isolate configuration logic across environments, avoiding accidental use of prod configs during development.”

---

### 🔹 10. **@Primary vs @Qualifier**

#### 📘 Explanation:

Both help Spring decide **which bean to inject** when multiple beans of the same type exist.

* `@Primary`: Tells Spring to prefer this bean when multiple candidates are found.
* `@Qualifier`: Explicitly specify the bean to inject.

#### 💡 Example:

```java
@Bean
@Primary
public PaymentService paytmService() {
    return new PaytmService();
}

@Bean
public PaymentService googlePayService() {
    return new GooglePayService();
}

// Injecting explicitly
@Autowired
@Qualifier("googlePayService")
private PaymentService paymentService;
```

#### 💬 Interview Highlight:

> “Use @Primary when there's a default choice; @Qualifier when you need to override that choice.”

---

### 🔹 11. **JWT Token vs OAuth**

#### 📘 JWT (JSON Web Token):

* A compact, URL-safe token used for stateless authentication.
* Token contains **claims (user info)**.
* Stored client-side (in browser or mobile).
* Server doesn't store sessions — token is self-validating.

#### 📘 OAuth:

* Protocol for **authorization**, not authentication.
* Delegates access: “Login with Google”
* Used with tokens like JWT or opaque tokens.

#### 🔒 JWT Structure:

```
Header.Payload.Signature
e.g., eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiQmhhbnUifQ.ZXhhbXBsZXNpZ25hdHVyZQ
```

#### 💬 Interview Highlight:

> “JWT is often used inside OAuth as a token format. OAuth handles who’s allowed; JWT tells who the user is.”

---

### 🔹 12. **Microservice Communication**

#### 📘 Synchronous:

* Uses **HTTP/REST** or **gRPC**
* Real-time communication
* Simple and intuitive

#### 📘 Asynchronous:

* Uses **Kafka**, **RabbitMQ**, etc.
* Loose coupling
* Good for event-driven architecture

#### 💡 Rule of Thumb:

* **Immediate response needed?** Use HTTP.
* **Fire-and-forget, decoupled?** Use Kafka.

---

### 🔹 13. **Microservices Design Patterns**

#### 1. **Eventual Consistency:**

Data is eventually consistent across systems. Used with Kafka, not real-time, but reliable.

#### 2. **Saga Pattern:**

Breaks long transactions into multiple local ones, coordinated through events (choreography or orchestration).

#### 3. **API Gateway:**

Single entry point, routes to services, adds auth/logging/rate-limiting.

#### 4. **Service Registry (Eureka):**

Keeps track of services and their locations.

---

### 🔹 14. **Java 8 Features**

#### ✅ Stream API:

Process collections with functional style.

```java
list.stream()
    .filter(e -> e.startsWith("A"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

#### ✅ Date and Time API:

```java
LocalDate today = LocalDate.now();
LocalDate birth = LocalDate.of(1990, 1, 1);
```

#### ✅ Optional:

Avoid NullPointerException.

```java
Optional<String> name = Optional.ofNullable(getName());
name.ifPresent(System.out::println);
```

---

### 🔹 15. **Stream vs For Loop**

#### 📘 Recommendation:

* **Streams**: Best for small to medium datasets, readable, functional style.
* **For/While Loops**: Prefer for large datasets, high-performance use-cases.

#### 💬 Interview Highlight:

> “Streams are elegant but may have overhead; for loops are low-level and faster in hot paths.”

---

### 🔹 16. **Intermediate vs Terminal Operations in Streams**

#### 📘 Intermediate:

* Lazy
* Don’t trigger processing
* Examples: `filter`, `map`, `sorted`

#### 📘 Terminal:

* Triggers processing
* Examples: `collect`, `forEach`, `reduce`

---

### 🔹 17. **Functional Interface**

* An interface with **only one abstract method**.
* Can have default and static methods.
* Used in Lambdas.

#### 💡 Example:

```java
@FunctionalInterface
public interface Calculator {
    int calculate(int a, int b);
}
```

#### 📘 Built-in:

* `Runnable`
* `Callable`
* `Comparator`

---

### 🔹 18. **Anonymous Class**

* A one-time-use class without a name.
* Useful to override methods without creating a separate class.

```java
Runnable r = new Runnable() {
    public void run() {
        System.out.println("Run!");
    }
};
```

---

### 🔹 19. **Java 8: Interface static vs default methods**

* **default** → Can be overridden by implementing class
* **static** → Belongs to interface only, can’t be overridden

---

### 🔹 20. **Heap vs Stack Memory**

| Heap                | Stack                |
| ------------------- | -------------------- |
| Objects stored here | Method calls, refs   |
| GC managed          | Auto-managed         |
| Slower access       | Fast (stack pointer) |

---

### 🔹 21. **String is Immutable**

* String pool reuse
* Thread-safe
* Prevents security issues like password changes

```java
String s = "pass123";
// s = "newPass"; creates new object
```

---

### 🔹 22. **ArrayList vs LinkedList**

| Feature       | ArrayList   | LinkedList  |
| ------------- | ----------- | ----------- |
| Access        | Fast (O(1)) | Slow (O(n)) |
| Insert/Delete | Slow (O(n)) | Fast (O(1)) |

---

### 🔹 23. **LinkedHashSet for Order Preservation**

* Maintains insertion order
* Removes duplicates

---

### 🔹 24. **Hash Collision & equals/hashCode**

#### 📘 Problem:

If two keys have the same hashcode, Java checks:

1. Equals() method
2. Bucket (Linked list → Balanced tree if too many entries)

#### 💡 Rule:

> Always override `hashCode()` when you override `equals()`. Otherwise, HashMap/HashSet won't behave correctly.

