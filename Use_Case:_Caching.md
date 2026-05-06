## 💳 Use Case: Caching Customer Risk Scores per Session

### Scenario

A banking application calculates a **risk score** for a customer (used for fraud detection, loan eligibility, etc.).

* Each `Customer` object exists only while the user is active in the system (session-based processing).
* Risk score calculation is **expensive** (ML model / external service).
* Once the `Customer` object is no longer in use (session ends), we don’t want to keep its risk score in memory.

## 🧠 Why WeakHashMap fits here

We want:

* Cache risk scores → avoid recomputation
* But do NOT keep customer objects alive just because they are in cache
* Automatically clean up when customer session ends

So we use:

👉 `WeakHashMap<Customer, RiskProfile>`

## 🧾 Example Code

```java
import java.util.WeakHashMap;

class Customer {
    String customerId;
    String name;

    Customer(String customerId, String name) {
        this.customerId = customerId;
        this.name = name;
    }
}

class RiskProfile {
    double score;

    RiskProfile(double score) {
        this.score = score;
    }
}

public class RiskEngine {

    // Weak key: Customer objects can be GC'd when no longer used
    private WeakHashMap<Customer, RiskProfile> riskCache = new WeakHashMap<>();

    public RiskProfile getRiskProfile(Customer customer) {
        RiskProfile profile = riskCache.get(customer);

        if (profile == null) {
            profile = computeRisk(customer);
            riskCache.put(customer, profile);
        }

        return profile;
    }

    private RiskProfile computeRisk(Customer customer) {
        // Simulate expensive computation / ML model call
        double score = Math.random() * 100;
        return new RiskProfile(score);
    }
}
```

## 🏦 Real Banking Flow

### Step-by-step:

1. Customer logs into internet banking
2. System creates a `Customer` object
3. Risk score is computed and cached in `WeakHashMap`
4. Customer performs actions (loan request, transfer, etc.)
5. Session ends → no references to `Customer` remain
6. Garbage Collector removes `Customer`
7. Automatically:

   * Entry in `WeakHashMap` is removed
   * Risk score is also freed from memory

## ⚠️ Why NOT HashMap in banking here?

If we used:

```java
HashMap<Customer, RiskProfile>
```

Then:

* Even after session ends, `Customer` stays in memory
* Risk cache keeps growing silently
* Leads to **memory leaks in long-running banking servers**

That’s dangerous in systems like:

* Core banking apps
* Fraud detection engines
* Payment gateways

## 💡 Key Banking Insight

`WeakHashMap` is useful in banking systems for:

* Session-based caching (risk scores, preferences)
* Temporary metadata (audit hints, UI state, rule evaluations)
* Avoiding memory leaks in long-running services

But NOT for:

* Account data
* Transaction records
* Ledger entries
  (these must be persisted in databases, not weak references)

## 🧠 Simple Analogy

Think of it like this:

* Customer session = visitor in bank branch 🧍
* Risk score cache = sticky note about visitor 📝

When the visitor leaves, the sticky note is thrown away automatically.

---

# 🏦 Project: Banking Risk Service (Spring Boot + WeakHashMap Cache)

## 🎯 Goal

* Compute customer risk score (expensive operation)
* Cache it temporarily per customer session/object
* Automatically free memory when customer object is no longer used

---

# 📁 Project Structure

```
bank-risk-service/
 ├── controller/
 │    └── RiskController.java
 ├── service/
 │    └── RiskService.java
 ├── model/
 │    ├── Customer.java
 │    └── RiskProfile.java
 ├── cache/
 │    └── RiskCache.java
 ├── BankRiskServiceApplication.java
```

# 1️⃣ Model Layer

## Customer.java

```java
package model;

public class Customer {
    private String id;
    private String name;

    public Customer(String id, String name) {
        this.id = id;
        this.name = name;
    }

    public String getId() { return id; }
    public String getName() { return name; }
}
```

## RiskProfile.java

```java
package model;

public class RiskProfile {
    private double score;

    public RiskProfile(double score) {
        this.score = score;
    }

    public double getScore() {
        return score;
    }
}
```

# 2️⃣ Cache Layer (🔥 Core WeakHashMap Usage)

## RiskCache.java

```java
package cache;

import model.Customer;
import model.RiskProfile;

import java.util.WeakHashMap;

public class RiskCache {

    // Weak key: Customer can be GC'd when no longer referenced
    private final WeakHashMap<Customer, RiskProfile> cache = new WeakHashMap<>();

    public RiskProfile get(Customer customer) {
        return cache.get(customer);
    }

    public void put(Customer customer, RiskProfile profile) {
        cache.put(customer, profile);
    }
}
```

# 3️⃣ Service Layer

## RiskService.java

```java
package service;

import cache.RiskCache;
import model.Customer;
import model.RiskProfile;

public class RiskService {

    private final RiskCache cache = new RiskCache();

    public RiskProfile getRisk(Customer customer) {

        // 1. Check cache
        RiskProfile cached = cache.get(customer);
        if (cached != null) {
            return cached;
        }

        // 2. Compute risk (simulate expensive ML / external call)
        RiskProfile profile = computeRisk(customer);

        // 3. Store in WeakHashMap cache
        cache.put(customer, profile);

        return profile;
    }

    private RiskProfile computeRisk(Customer customer) {
        System.out.println("🔄 Computing risk for " + customer.getName());

        double score = Math.random() * 100;
        return new RiskProfile(score);
    }
}
```

# 4️⃣ Controller Layer (REST API)

## RiskController.java

```java
package controller;

import model.Customer;
import model.RiskProfile;
import service.RiskService;

import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/risk")
public class RiskController {

    private final RiskService service = new RiskService();

    @GetMapping("/{id}/{name}")
    public double getRisk(@PathVariable String id,
                          @PathVariable String name) {

        Customer customer = new Customer(id, name);
        RiskProfile profile = service.getRisk(customer);

        return profile.getScore();
    }
}
```

# 5️⃣ Main Application

## BankRiskServiceApplication.java

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BankRiskServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(BankRiskServiceApplication.class, args);
    }
}
```

# 🚀 How It Works (Real Flow)

### First request:

```
GET /risk/101/Alice
```

Output:

```
🔄 Computing risk for Alice
```

* Risk computed
* Stored in `WeakHashMap`

### Second request (same object reference scenario):

* If same `Customer` object is reused → cache hit
* No recomputation

### After session ends:

* No references to `Customer`
* Garbage Collector removes:

  * Customer object
  * Cache entry automatically

# ⚠️ Important Reality Check (Banking Context)

In real banking systems:

❌ You should NOT rely on WeakHashMap for:

* Account balances
* Transaction history
* Fraud logs

✔ It is used only for:

* Temporary computation cache
* Session-level metadata
* Non-critical derived data

Real systems use:

* Redis (distributed cache)
* Hazelcast / Infinispan
* Database-backed persistence

---

# 💡 Why this design matters

Without WeakHashMap:

* Cache grows forever → memory leak risk
* Manual cleanup required → error-prone

With WeakHashMap:

* JVM automatically cleans unused customers
* No explicit eviction logic needed
* Safer for long-running microservices

---
