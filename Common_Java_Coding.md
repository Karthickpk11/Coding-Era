Here are **10 tricky, interview-level questions** focused on **Java Streams + `groupingBy`**, including patterns often asked in real company coding rounds. These go beyond basics and test how well you *think in streams*.

## 1. Group strings by length and count frequency

**Question:**
Given a list of strings, group them by length and count how many strings fall into each group.

👉 Expected output: `Map<Integer, Long>`

```java
Map<Integer, Long> result = list.stream()
    .collect(Collectors.groupingBy(String::length, Collectors.counting()));
```

## 2. Group employees by department and find highest salary in each

**Twist:** Avoid sorting entire groups.

```java
Map<String, Optional<Employee>> result = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.maxBy(Comparator.comparing(Employee::getSalary))
    ));
```

## 3. Multi-level grouping (Department → Role)

**Question:**
Group employees first by department, then by role.

```java
Map<String, Map<String, List<Employee>>> result =
    employees.stream()
        .collect(Collectors.groupingBy(
            Employee::getDepartment,
            Collectors.groupingBy(Employee::getRole)
        ));
```

## 4. Find duplicate elements using grouping

**Question:**
Find all duplicate integers in a list.

```java
List<Integer> duplicates = list.stream()
    .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
    .entrySet().stream()
    .filter(e -> e.getValue() > 1)
    .map(Map.Entry::getKey)
    .toList();
```

## 5. Group and transform (mapping downstream)

**Question:**
Group employees by department but collect only their names.

```java
Map<String, List<String>> result = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.mapping(Employee::getName, Collectors.toList())
    ));
```

## 6. Partition vs grouping (tricky conceptual)

**Question:**
Split numbers into even and odd using streams.

👉 Trick: Use `partitioningBy`, not `groupingBy`.

```java
Map<Boolean, List<Integer>> result = list.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

## 7. Group by and sum values

**Question:**
Find total salary per department.

```java
Map<String, Double> result = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.summingDouble(Employee::getSalary)
    ));
```

## 8. Group by composite key (advanced)

**Question:**
Group employees by department + role together.

```java
Map<String, List<Employee>> result = employees.stream()
    .collect(Collectors.groupingBy(
        e -> e.getDepartment() + "-" + e.getRole()
    ));
```
👉 Better approach (custom key class) is often expected in interviews.

## 9. Top N elements per group (very common in product companies)

**Question:**
For each department, find top 2 highest-paid employees.

```java
Map<String, List<Employee>> result = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.collectingAndThen(
            Collectors.toList(),
            list -> list.stream()
                .sorted(Comparator.comparing(Employee::getSalary).reversed())
                .limit(2)
                .toList()
        )
    ));
```

## 10. Flatten + group (real-world tricky)

**Question:**
Given `List<List<String>>`, flatten and group by string length.

```java
Map<Integer, List<String>> result = listOfLists.stream()
    .flatMap(List::stream)
    .collect(Collectors.groupingBy(String::length));
```

# 🔥 Bonus: Real Company-Style Problem

## 11. Most frequent word in a paragraph

```java
String paragraph = "java streams are powerful java streams are tricky";

String result = Arrays.stream(paragraph.split(" "))
    .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
    .entrySet().stream()
    .max(Map.Entry.comparingByValue())
    .get()
    .getKey();
```
