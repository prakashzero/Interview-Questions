# 📘 Java & Spring Boot – Concurrency, Parallelism & Core Concepts

This guide explains **how modern Spring Boot applications handle concurrent requests, parallel execution, authentication, scheduling, and background tasks**, with diagrams, mindmaps, and flashcards for quick revision.

---

## 📌 Table of Contents

1. Concurrency vs Parallelism
2. Thread Pools & Task Executors
3. Runnable vs Callable
4. CompletableFuture (Parallel API Calls)
5. Spring Security – Authentication & Authorization Flow
6. Cron Expressions
7. Visual Mindmap
8. Interview Flashcards

---

## 1️⃣ Concurrency vs Parallelism

### 🔹 Concurrency

* Multiple tasks **in progress** at the same time
* May run on a **single core** using context switching
* Focus: **task coordination**

**Example:**
Spring Boot handling 500 HTTP requests concurrently using a thread pool.

### 🔹 Parallelism

* Multiple tasks **executing simultaneously**
* Requires **multiple CPU cores**
* Focus: **performance improvement**

**Example:**
Calling 3 downstream services at the same time using `CompletableFuture`.

![Image](https://devopedia.org/images/article/339/6574.1623908671.jpg)

![Image](https://jenkov.com/images/java-concurrency/concurrency-vs-parallelism-3.png)

---

## 2️⃣ Thread Pools & Task Executors (Spring Boot)

### Why NOT `new Thread()`?

* Unbounded thread creation
* Memory leaks
* No lifecycle management

### ✅ Solution: `ThreadPoolTaskExecutor`

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean("taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(50);
        executor.setThreadNamePrefix("Async-");
        executor.initialize();
        return executor;
    }
}
```

### Using it with `@Async`

```java
@Async("taskExecutor")
public CompletableFuture<String> process(int id) {
    return CompletableFuture.completedFuture("Processed " + id);
}
```

📌 **Real-world use:**

* Background processing
* Parallel API calls
* Email/SMS notifications

![Image](https://www.baeldung.com/wp-content/uploads/2016/08/2016-08-10_10-16-52-1024x572.png)

![Image](https://blog.rheinwerk-computing.com/hs-fs/hubfs/2475_04_004.png?height=575\&name=2475_04_004.png\&width=659)

---

## 3️⃣ Runnable vs Callable

| Feature            | Runnable          | Callable        |
| ------------------ | ----------------- | --------------- |
| Method             | `run()`           | `call()`        |
| Return Value       | ❌ No              | ✅ Yes           |
| Checked Exceptions | ❌ No              | ✅ Yes           |
| Used With          | Thread / Executor | ExecutorService |

### Runnable Example

```java
Runnable task = () -> System.out.println("Running");
new Thread(task).start();
```

### Callable Example

```java
Callable<String> task = () -> "Result";
Future<String> future = executor.submit(task);
future.get();
```

📌 **Rule of thumb:**

* Fire-and-forget → `Runnable`
* Need result → `Callable`

---

## 4️⃣ CompletableFuture – Parallel API Calls

### Problem

Fetch user details for multiple IDs **without waiting sequentially**.

### Solution

```java
CompletableFuture<User> u1 = service.getUser(1L);
CompletableFuture<User> u2 = service.getUser(2L);
CompletableFuture<User> u3 = service.getUser(3L);

CompletableFuture.allOf(u1, u2, u3).join();

List<User> users = List.of(u1.get(), u2.get(), u3.get());
```

### Why `CompletableFuture`?

* Non-blocking
* Composable
* Exception handling
* True parallel execution

![Image](https://media.licdn.com/dms/image/v2/C5612AQFiILQe97UHAg/article-inline_image-shrink_400_744/article-inline_image-shrink_400_744/0/1614704959284?e=1766016000\&t=hKOu5Bjo2AwCi_AAv-IN1zVFjFFYunrA-F7er79nFRQ\&v=beta)

![Image](https://i.sstatic.net/iph5W.png)

---

## 5️⃣ Spring Security – Authentication & Authorization

### 🔐 Authentication (Who are you?)

1. User → `/login`
2. Filter extracts credentials
3. `AuthenticationManager`
4. `AuthenticationProvider`
5. `UserDetailsService`
6. PasswordEncoder validation
7. Authentication stored in `SecurityContext`

### 🛂 Authorization (What can you do?)

* Roles / Authorities checked per request
* URL-level or Method-level

```java
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/admin")
public String adminAccess() { return "Allowed"; }
```

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2Af1NZoTCuXYPzZlVAnmKhbA.png)

![Image](https://docs.spring.io/spring-security/reference/_images/servlet/architecture/securityfilterchain.png)

---

## 6️⃣ Cron Expressions (Spring Boot)

### Format

```
second minute hour day-of-month month day-of-week
```

### Common Examples

| Cron                  | Meaning            |
| --------------------- | ------------------ |
| `0 0 12 * * ?`        | Every day at 12 PM |
| `0 0/5 * * * ?`       | Every 5 minutes    |
| `0 0 0 L * ?`         | Last day of month  |
| `0 15 10 ? * MON-FRI` | Weekdays 10:15 AM  |

📌 `?` means **no specific value** (used to avoid conflicts).

---

## 7️⃣ Visual Mindmap (Mental Model)

![Image](https://media.licdn.com/dms/image/v2/D4D22AQFK4Pkl6iL0EA/feedshare-shrink_800/feedshare-shrink_800/0/1687839961104?e=2147483647\&t=LSo6sRRJm4v0g2u0LstnR9J3LqnRr9qdLZ70Hg3JEMQ\&v=beta)

![Image](https://www.researchgate.net/publication/385686967/figure/fig1/AS%3A11431281289420869%401731185740700/Spring-Boot-Microservice-Architecture.ppm)

**Think in layers:**

* HTTP Requests → Thread Pool
* Business Logic → Async / Parallel
* DB → Transactions & Locks
* Security → Filters & Context

---

## 8️⃣ Interview Flashcards 🧠

### 🔹 Concurrency vs Parallelism

> Concurrency manages multiple tasks; parallelism executes them simultaneously.

### 🔹 Why ThreadPoolTaskExecutor?

> Controls thread lifecycle and prevents resource exhaustion.

### 🔹 Runnable vs Callable

> Callable returns results and supports checked exceptions.

### 🔹 CompletableFuture use case?

> Aggregating multiple downstream API calls efficiently.

### 🔹 Authentication vs Authorization?

> Authentication = identity, Authorization = permissions.

### 🔹 Why `?` in cron?

> Quartz does not allow both day-of-week and day-of-month together.

---

## ✅ Final Takeaway

Modern Spring Boot applications rely on:

* **Thread pools** for concurrency
* **CompletableFuture** for parallelism
* **Spring Security** for controlled access
* **Cron & schedulers** for automation

Mastering these concepts makes you **production-ready and interview-strong**.

