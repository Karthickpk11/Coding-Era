## 1. Duplicate handling (silent logic bug)
Where it fails:
* Two Sum
* Index mapping problems
### Bug:
```java map.put(nums[i], i); ```

👉 Overwrites previous index → wrong answer

### Fix:
```java if (!map.containsKey(nums[i])) {
    map.put(nums[i], i);
}  ```

👉 Interviewer expects:

“I should avoid overwriting if first occurrence matters.”

