### 🔹 What `GROUP BY` does

It groups rows that have the same values in specified columns, so you can apply aggregate functions like:

* `COUNT()`
* `SUM()`
* `AVG()`
* `MAX()`
* `MIN()`

## ✅ Basic Syntax

```sql id="a1b2c3"
SELECT column_name, AGG_FUNCTION(column_name)
FROM table_name
GROUP BY column_name;
```

## 🔹 Common Interview Questions & Examples

### 1. Count employees in each department

```sql id="d4e5f6"
SELECT department, COUNT(*) AS total_employees
FROM employee
GROUP BY department;
```

### 2. Find total salary by department

```sql id="g7h8i9"
SELECT department, SUM(salary) AS total_salary
FROM employee
GROUP BY department;
```

### 3. Find average salary per department

```sql id="j1k2l3"
SELECT department, AVG(salary) AS avg_salary
FROM employee
GROUP BY department;
```

### 4. Find departments with more than 5 employees (`HAVING`)

```sql id="m4n5o6"
SELECT department, COUNT(*) AS total_employees
FROM employee
GROUP BY department
HAVING COUNT(*) > 5;
```

👉 `HAVING` is like `WHERE`, but for grouped data.

### 5. Find highest salary in each department

```sql id="p7q8r9"
SELECT department, MAX(salary) AS highest_salary
FROM employee
GROUP BY department;
```

### 6. Group by multiple columns

```sql id="s1t2u3"
SELECT department, job_role, COUNT(*) AS total
FROM employee
GROUP BY department, job_role;
```

## 🔹 Important Interview Points

* Every column in `SELECT` must either be:

  * In `GROUP BY`, or
  * Used with an aggregate function
* `WHERE` filters rows **before grouping**
* `HAVING` filters groups **after grouping**

## 🔹 Trick Question (Very Common)

❓ *Why does this fail?*

```sql
SELECT department, salary
FROM employee
GROUP BY department;
```

👉 Because `salary` is neither aggregated nor grouped.

## 🔹 Bonus: Combine `WHERE` + `GROUP BY`

```sql id="v4w5x6"
SELECT department, COUNT(*) AS total
FROM employee
WHERE salary > 50000
GROUP BY department;
```

### 🟢 Basic Level

### 1. Count employees in each department

```sql id="q1a2"
-- Write query
```

💡 Hint: `COUNT(*) + GROUP BY department`

### 2. Find total salary per department

```sql id="q2b3"
-- Write query
```

💡 Hint: `SUM(salary)`

### 3. Find average salary per department

```sql id="q3c4"
-- Write query
```

💡 Hint: `AVG(salary)`

### 4. Find the maximum salary in each department

```sql id="q4d5"
-- Write query
```

💡 Hint: `MAX(salary)`

### 5. Count employees in each department where salary > 50,000

```sql id="q5e6"
-- Write query
```

💡 Hint: Use `WHERE` before grouping

# 🟡 Intermediate Level

### 6. Find departments having more than 5 employees

```sql id="q6f7"
-- Write query
```

💡 Hint: `HAVING COUNT(*) > 5`

### 7. Find the second highest salary

```sql id="q7g8"
-- Write query
```

💡 Hint: Use subquery or `DENSE_RANK()`

### 8. Find duplicate records in a table

```sql id="q8h9"
-- Write query
```

💡 Hint: `GROUP BY + HAVING COUNT(*) > 1`

### 9. Find employees whose salary is above department average

```sql id="q9i0"
-- Write query
```

💡 Hint: Correlated subquery or window function

### 10. Count employees by department and job role

```sql id="q10j1"
-- Write query
```

💡 Hint: `GROUP BY department, job_role`

# 🟠 Advanced Level

### 11. Find highest salary employee in each department

```sql id="q11k2"
-- Write query
```

💡 Hint: Use `ROW_NUMBER()` or subquery

### 12. Find departments where average salary > 60,000

```sql id="q12l3"
-- Write query
```

💡 Hint: `HAVING AVG(salary) > 60000`

### 13. Get department-wise top 3 salaries

```sql id="q13m4"
-- Write query
```

💡 Hint: `DENSE_RANK()` partitioned by department

### 14. Find employees who earn the same salary

```sql id="q14n5"
-- Write query
```

💡 Hint: `GROUP BY salary HAVING COUNT(*) > 1`

### 15. Find the lowest salary in each department

```sql id="q15o6"
-- Write query
```

💡 Hint: `MIN(salary)`

# 🔴 Expert Level

### 16. Find departments with no employees

```sql id="q16p7"
-- Write query
```

💡 Hint: `LEFT JOIN + NULL check`

### 17. Find running total of salary by department

```sql id="q17q8"
-- Write query
```

💡 Hint: `SUM() OVER (PARTITION BY ...)`

### 18. Find percentage of employees in each department

```sql id="q18r9"
-- Write query
```

💡 Hint: `COUNT(*) / total_count`

### 19. Find employees who earn more than overall average

```sql id="q19s0"
-- Write query
```

💡 Hint: Subquery with `AVG()`

### 20. Rank employees within each department by salary

```sql id="q20t1"
-- Write query
```

💡 Hint: `RANK()` or `DENSE_RANK() OVER (PARTITION BY department)`

