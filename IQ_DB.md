## 📌 What Is an Index in a Database?

In databases, an **index** is a data structure that improves the speed of data retrieval operations on a table.

An index in a database works similar to the index in a book.

* Instead of scanning every page (row) to find a topic (data),
* You look at the index,
* It tells you exactly where to go.

In databases, an index helps quickly locate rows in a table without scanning the entire table.

---

## ⚙️ How an Index Works Internally

Most relational databases like:

* MySQL
* PostgreSQL
* Oracle Database
* Microsoft SQL Server

use a data structure called a **B-Tree (Balanced Tree)** for indexes.

### 🔹 B-Tree Structure

A B-Tree:

* Stores indexed column values in sorted order
* Organizes them in a tree format
* Allows fast searching, inserting, and deleting

### 🔎 Example

Suppose you have a table:

| id | name  | age |
| -- | ----- | --- |
| 1  | John  | 25  |
| 2  | Alice | 30  |
| 3  | Bob   | 22  |

If you create:

```sql
CREATE INDEX idx_name ON users(name);
```

The database creates a B-Tree like:

```
        Alice
       /     \
     Bob     John
```

Now when you run:

```sql
SELECT * FROM users WHERE name = 'Bob';
```

Instead of scanning all rows, the database:

1. Looks into the index
2. Finds "Bob" quickly (logarithmic time)
3. Retrieves the exact row location

---

## 🎯 Purpose of an Index

### 1️⃣ Faster SELECT Queries

* Speeds up WHERE conditions
* Speeds up JOIN operations
* Speeds up ORDER BY
* Speeds up GROUP BY

---

### 2️⃣ Enforcing Uniqueness

If you create:

```sql
CREATE UNIQUE INDEX idx_email ON users(email);
```

The database ensures no duplicate emails exist.

Primary keys automatically create unique indexes.

---

### 3️⃣ Improve Query Performance from O(n) → O(log n)

Without index:

* Full table scan → O(n)

With B-Tree index:

* Tree search → O(log n)

Huge improvement for large tables.

---

## 📦 Types of Indexes

### 🔹 1. Primary Index

* Automatically created on PRIMARY KEY
* Unique
* One per table

### 🔹 2. Secondary Index

* Created manually
* Can be multiple per table

### 🔹 3. Composite Index

Index on multiple columns:

```sql
CREATE INDEX idx_name_age ON users(name, age);
```

---

### 🔹 4. Hash Index

* Used in some databases
* Very fast for equality searches
* Not good for range queries

---

### 🔹 5. Clustered vs Non-Clustered

**Clustered Index**

* Physically sorts table data
* Only one per table

**Non-Clustered Index**

* Separate structure
* Points to actual table rows
* Multiple allowed

Example:

* In Microsoft SQL Server, a table can have only one clustered index.

---

## ⚠️ Downsides of Indexes

Indexes are not always good.

### ❌ Slower INSERT / UPDATE / DELETE

Because the index must also be updated.

### ❌ Extra Storage

Indexes take disk space.

### ❌ Over-indexing hurts performance

---

## 🧠 When Should You Use Index?

Create index on:

* Columns frequently used in WHERE
* JOIN columns
* ORDER BY columns
* Columns with high selectivity (many unique values)

Avoid index on:

* Columns with very few unique values (like gender)
* Very small tables

---

## 🚀 Simple Performance Comparison

Imagine 10 million rows:

| Without Index | With Index       |
| ------------- | ---------------- |
| 3–10 seconds  | Few milliseconds |

---

## 🏁 Summary

An index:

* Is a special lookup structure
* Usually implemented using B-Tree
* Makes data retrieval much faster
* Trades faster reads for slower writes
* Uses extra storage

---

Find the user count for each department?

```sql
-- GROUP By Department(dept_id)
Select dept_name, count(*) from departments dept 
LEFT JOIN employers emp ON dept.employer_id = emp.employer_id Group By dept.dept_id;
```

Find the depart count for each user?

```sql
-- Group By User(employer_id)
Select employer_name, count(*) from employers emp 
LEFT JOIN departments dep ON emp.employer_id = dep.employer_id Group By emp.employer_id;
```

How to add new columns in the tables?
```sql
ALTER TABLE employers ADD salary INT;
```

How to Update value in new columns in the tables?
```sql
Update employers SET  salary = 12000 where employer_id = 2;
```

This increases salary by 5000 for condition base
```sql
Update employers SET  salary = salary + 5000 where employer_id = 2;
```

How to view the index in POstgreSQL?
```sql
SELECT indexname, indexdef FROM pg_indexes WHERE tablename = 'employers';
```
<img width="811" height="97" alt="image" src="https://github.com/user-attachments/assets/6c580799-7154-4e8f-8b7b-44e132968c60" />

What is correlated subquery?

A correlated subquery is a subquery that references columns from the outer query. Unlike a regular subquery, which is independent and runs once, a correlated subquery is evaluated for each row processed by the outer query. This makes it dependent on the outer query. In a correlated subquery, the subquery depends on the outer query, _meaning that for each row of the outer query, the inner query is executed_.

```sql
SELECT column1, column2
FROM outer_table
WHERE column3 = (
    SELECT column4
    FROM inner_table
    WHERE outer_table.column = inner_table.column
);
```
# Key Points to Remember About Correlated Subqueries:

* **Reference to Outer Query**: The subquery references columns from the outer query.

* **Evaluated for Each Row**: The subquery is executed once for each row in the outer query.

* **Efficiency**: Correlated subqueries can be less efficient than non-correlated subqueries, as the inner query is executed multiple times, once for each row in the outer query.


How to remove the duplicate row in the table?

To delete duplicate rows in an SQL table, you need to identify the rows that are duplicates and then remove them. There are different methods to do this depending on the SQL database you're using, but the general approach involves using a DELETE query with a **JOIN** or `ROW_NUMBER()` window function.

1) Using ROW_NUMBER() (For most SQL databases like PostgreSQL, SQL Server, MySQL 8.0+):

If you want to delete duplicates and keep one unique row, you can use a ROW_NUMBER() window function to assign a unique number to each row, and then delete the duplicates (rows where ROW_NUMBER > 1).

```sql
WITH DuplicateRows AS (
    SELECT 
        EmployeeID,
        FirstName,
        LastName,
        Department,
        ROW_NUMBER() OVER (PARTITION BY FirstName, LastName, Department ORDER BY EmployeeID) AS RowNum
    FROM Employees
)
DELETE FROM Employees
WHERE EmployeeID IN (
    SELECT EmployeeID
    FROM DuplicateRows
    WHERE RowNum > 1
);
```

Explanation:

* ROW_NUMBER() assigns a unique number to each row for a given group (in this case, FirstName, LastName, and Department).

* The PARTITION BY clause groups rows that have the same values for FirstName, LastName, and Department.

* The ORDER BY EmployeeID ensures that the rows are numbered in a consistent way.

* Then, we delete rows where RowNum > 1, i.e., all duplicate rows except the first one.

2) Using DISTINCT to Remove Duplicates (If you want to keep only unique rows in a new table):

If you want to remove duplicates and create a new table without the duplicates, you can use the DISTINCT keyword.
```sql
CREATE TABLE Employees_NoDuplicates AS
SELECT DISTINCT FirstName, LastName, Department
FROM Employees;
```

Then you can drop the old table and rename the new one:

```sql
DROP TABLE Employees;
ALTER TABLE Employees_NoDuplicates RENAME TO Employees;
```

**Explanation:**

* This query creates a new table Employees_NoDuplicates with unique rows (using DISTINCT).
* Then, it drops the old table and renames the new table to the original name.

### Which Method Should You Use?

* **`ROW_NUMBER()`** is the most efficient and modern way to handle this in databases that support it (PostgreSQL, SQL Server, MySQL 8.0+).
* **`JOIN` method** is more widely compatible but can be less efficient for larger tables.
* **`DISTINCT` and `GROUP BY`** are useful for creating a new table without duplicates, but may not preserve all original data if there are other columns you want to keep.
  
---
