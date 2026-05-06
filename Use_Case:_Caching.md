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
