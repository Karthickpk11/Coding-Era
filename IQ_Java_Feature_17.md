Java 17 (an LTS release) introduced several important features and finalized some preview ones 👇

---

# 🚀 Key Features in Java 17

## 1. Sealed Classes (Finalized)

Restrict which classes can extend or implement a class.

```java
public sealed class Shape 
    permits Circle, Rectangle {
}

final class Circle extends Shape {
    double radius;
}

final class Rectangle extends Shape {
    double length, width;
}
```

👉 Benefits:

* Better control over inheritance
* Works well with pattern matching

---

## 2. Pattern Matching for `switch` (Preview)

Enhances `switch` with type patterns.

```java
static String formatter(Object obj) {
    return switch (obj) {
        case Integer i -> "Integer: " + i;
        case String s  -> "String: " + s;
        default        -> "Unknown";
    };
}
```

👉 Cleaner than multiple `instanceof` checks.

---

## 3. Pattern Matching for `instanceof` (Improved)

Introduced earlier but refined.

```java
if (obj instanceof String str) {
    System.out.println(str.toUpperCase());
}
```

👉 No need for explicit casting.

---

## 4. Records (Standard Feature)

Compact way to create immutable data classes.

```java
public record Person(String name, int age) {}

Person p = new Person("Alice", 25);
System.out.println(p.name());
```

👉 Automatically provides:

* Constructor
* Getters
* `equals()`, `hashCode()`, `toString()`

---

## 5. Text Blocks (Standard)

Multi-line string literals.

```java
String json = """
    {
        "name": "John",
        "age": 30
    }
    """;

System.out.println(json);
```

👉 Great for JSON, SQL, HTML.

---

## 6. New Random Number Generator API

More flexible random number generation.

```java
import java.util.random.*;

RandomGenerator generator = RandomGenerator.getDefault();

int num = generator.nextInt(100);
System.out.println(num);
```

👉 Supports multiple algorithms.

---

## 7. Enhanced Pseudo-Random Generators

You can choose specific algorithms:

```java
RandomGenerator generator = RandomGenerator.of("L64X128MixRandom");

System.out.println(generator.nextInt());
```

---

## 8. Strong Encapsulation of JDK Internals

* Internal APIs are now strongly encapsulated
* Illegal reflective access is restricted

👉 Improves security and maintainability.

How Strong Encapsulation is Applied

✅ Internal packages are NOT exported

* internal classes cannot be accessed outside their module

✅ Only APIs are exposed

* exports com.bank.accounts.api
* exports com.bank.security.api

✅ Sensitive logic hidden

* Encryption logic is private
* Account balance not directly accessible

---

## 9. Foreign Function & Memory API (Incubator)

Allows calling native code without JNI.

```java
// Simplified conceptual example
// (Actual API is more verbose)

import jdk.incubator.foreign.*;

MemorySegment segment = MemorySegment.allocateNative(100);
```

👉 Still evolving but powerful for high-performance apps.

---

## 10. Deprecation & Removal Updates

* Removal of old features like:

  * Applet API (deprecated for removal)
  * Security Manager (deprecated)

---

# 🧠 Bonus: Switch Expressions (from Java 14+, widely used in 17)

```java
int day = 2;

String result = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    default -> "Other";
};
```

---

# ✅ Summary

Java 17 focuses on:

* 🔒 Better **type safety** (sealed classes, pattern matching)
* 🧱 Less boilerplate (records)
* 🧼 Cleaner syntax (text blocks, switch)
* ⚡ Improved performance & APIs (random generators, FFM API)
