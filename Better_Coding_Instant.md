Get better at solving problems in Java while balancing **time complexity, space usage, and readability**, you need a structured way of thinking—not just coding immediately. Here’s a practical approach you can apply to almost any problem.

## 1. Understand the problem deeply

Before writing any code, clarify:

* What are the **inputs and outputs**?
* Are there **constraints** (size of input, value ranges)?
* What are **edge cases** (empty input, duplicates, negatives)?

Try a few small examples manually. This builds intuition.

## 2. Start with a brute-force solution

Don’t aim for perfection first. Write the simplest working solution:

* Use basic loops or recursion
* Ignore optimization initially

This helps you:

* Ensure correctness
* Understand where inefficiencies are

Example: nested loops → usually **O(n²)**

## 3. Analyze time and space complexity

Use concepts from Big O notation:

* Time: How does runtime grow with input size?
* Space: Extra memory used (arrays, maps, recursion stack)

Ask:

* Can I reduce nested loops?
* Am I recomputing values?

## 4. Optimize step by step

Look for patterns:

### Common optimization techniques:

* Use a **HashMap** → reduce lookup to O(1)
* Use **two pointers** → avoid nested loops
* Use **sliding window** → for subarrays/strings
* Use **sorting** → sometimes enables better logic
* Use **dynamic programming** → avoid recomputation

Example:

* Brute force: O(n²)
* Optimized: O(n) using a HashMap

## 5. Balance readability with performance

Good Java code should be:

* Easy to read
* Modular
* Well-named

### Tips:

* Use meaningful variable names (`maxSum` instead of `ms`)
* Break logic into methods
* Avoid over-complicating for tiny gains

Bad (hard to read):

```java
for(int i=0;i<n;i++){
    for(int j=i+1;j<n;j++){
        // logic
    }
}
```

Better:

```java
for (int start = 0; start < n; start++) {
    for (int end = start + 1; end < n; end++) {
        // clearer intent
    }
}
```

## 6. Think in trade-offs

You often trade:

* **Time vs Space**

Example:

* Faster solution → uses extra memory (HashMap)
* Less memory → slower (nested loops)

There’s no “perfect” solution—choose based on constraints.

## 7. Practice common patterns

Focus on recurring problem types:

* Arrays & Strings
* Hashing
* Recursion
* Trees & Graphs
* Dynamic Programming

Over time, you’ll recognize patterns instantly.

## 8. Test thoroughly

Always check:

* Edge cases
* Large inputs
* Invalid inputs (if applicable)

## 9. Refactor your solution

After solving:

* Simplify logic
* Remove redundancy
* Improve naming and structure

## Simple Example

**Problem:** Find two numbers that sum to a target

### Brute force:

```java
for (int i = 0; i < nums.length; i++) {
    for (int j = i + 1; j < nums.length; j++) {
        if (nums[i] + nums[j] == target) {
            return new int[]{i, j};
        }
    }
}
```

Time: O(n²)

### Optimized:

```java
Map<Integer, Integer> map = new HashMap<>();

for (int i = 0; i < nums.length; i++) {
    int complement = target - nums[i];
    if (map.containsKey(complement)) {
        return new int[]{map.get(complement), i};
    }
    map.put(nums[i], i);
}
```

Time: O(n), Space: O(n)

## Final mindset

When solving problems:

1. Correctness first
2. Then optimize
3. Then improve readability
