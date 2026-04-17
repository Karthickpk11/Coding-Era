Difference between map and flatMap?

The difference between **`map`** and **`flatMap`** shows up when your transformation produces *nested collections* (or similar structures like arrays, lists, optionals, streams, etc.).

### 🧠 Core idea

* **`map`** → transforms each element and keeps the structure
* **`flatMap`** → transforms each element **and flattens the result**

### 🔹 Example (with lists)

#### Using `map`

```java
List<List<Integer>> result = list.stream()
    .map(x -> Arrays.asList(x, x * 2))
    .collect(Collectors.toList());
```

👉 Output:

```
[[1, 2], [2, 4], [3, 6]]
```

You get a **list of lists** (nested structure).

#### Using `flatMap`

```java
List<Integer> result = list.stream()
    .flatMap(x -> Arrays.asList(x, x * 2).stream())
    .collect(Collectors.toList());
```

👉 Output:

```
[1, 2, 2, 4, 3, 6]
```

Now it’s a **single flattened list**.

### 🔹 Simple analogy

Think of:

* `map` = “apply a function”
* `flatMap` = “apply a function + remove one level of wrapping”

### 🔹 When to use each

* Use **`map`** when:

  * One input → one output
  * Structure stays the same

* Use **`flatMap`** when:

  * One input → multiple outputs (like lists/streams)
  * You want a **single combined result**, not nested

### 🔹 Common real-world cases

* **`map`**

  * Converting values (`int → string`)
  * Formatting data

* **`flatMap`**

  * Working with nested collections
  * Streams of streams
  * Optional chaining (e.g., avoiding `Optional<Optional<T>>`)

### ⚡ One-line summary

> **`map` keeps nesting, `flatMap` removes one level of nesting.**

---
