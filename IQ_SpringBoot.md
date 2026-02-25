Just telling 😊

# 1. What is Dependency Injection?

Dependency Injection is a design pattern where:

* Objects **do not create their dependencies**
* Dependencies are **provided (injected)** by the Spring IoC container

This promotes:

* Loose coupling
* Easier testing
* Better maintainability
---
#  2. How many Ways to Implement Dependency Injection in Spring Boot

Spring Boot supports **three main types** of dependency injection:    
---
## 1️⃣ Constructor Injection (Recommended ✅)

Dependencies are injected through the class constructor.

### ✔ Example:

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

With annotation:

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    @Autowired   // Optional in Spring Boot 4+ if single constructor
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

### ✔ Why Recommended?

* Makes dependencies **immutable**
* Ensures required dependencies are provided
* Easier for **unit testing**
* Prevents NullPointerException
* Encouraged by Spring team

---

## 2️⃣ Setter Injection

Dependencies are injected via setter methods.

### ✔ Example:

```java
@Service
public class OrderService {

    private PaymentService paymentService;

    @Autowired
    public void setPaymentService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

### ✔ When to Use?

* Optional dependencies
* When dependency might change

### ❌ Downsides:

* Dependency is mutable
* Object can exist in incomplete state

---

## 3️⃣ Field Injection (Not Recommended ❌)

Dependencies are injected directly into fields.

### ✔ Example:

```java
@Service
public class OrderService {

    @Autowired
    private PaymentService paymentService;
}
```

### ❌ Why Not Recommended?

* Hard to unit test
* Breaks encapsulation
* Reflection-based injection
* Cannot make fields `final`

---

# 3. What are annotation-Based Injection

Spring Boot commonly uses:

* `@Autowired` – Automatically injects dependency
* `@Qualifier` – Resolves ambiguity
* `@Primary` – Marks preferred bean
* `@Inject` (JSR-330 alternative)
* `@Resource` (JSR-250 alternative)

---

# 🔹 Example with Multiple Implementations

```java
public interface PaymentService {
    void pay();
}
```

### Two Implementations:

```java
@Service
public class PaypalService implements PaymentService {}

@Service
public class StripeService implements PaymentService {}
```

### Using @Qualifier:

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(@Qualifier("paypalService") PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

---

# 🔹 Java Configuration Based Injection

Instead of `@Service`, you can define beans manually:

```java
@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new PaypalService();
    }
}
```
---
# 🔹 Comparison Table

| Type        | Recommended | Testable | Immutable | Usage                 |
| ----------- | ----------- | -------- | --------- | --------------------- |
| Constructor | ✅ Yes       | ✅ Easy   | ✅ Yes     | Most cases            |
| Setter      | ⚠ Sometimes | ⚠ Medium | ❌ No      | Optional dependencies |
| Field       | ❌ No        | ❌ Hard   | ❌ No      | Avoid                 |
---

# 🔹 Best Practice in Spring Boot    
✔ Use **Constructor Injection**    
✔ Use `final` fields    
✔ Avoid field injection    
✔ Use `@Qualifier` when multiple beans exist    
---
---
#  4. Life Cycle of Spring Bean    
how a bean is created, initialized, used, and destroyed inside the Spring IoC container.

Complete Lifecycle Flow (Simple View):    
```
1. Instantiate Bean
2. Inject Dependencies
3. Aware Methods
4. BeanPostProcessor (before)
5. @PostConstruct
6. afterPropertiesSet()
7. Custom init()
8. BeanPostProcessor (after)
9. Bean Ready
10. @PreDestroy
11. destroy()
12. Custom destroy()
```
