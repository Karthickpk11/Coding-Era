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

---

Find the depart count for each user?

```sql
-- Group By User(employer_id)
Select employer_name, count(*) from employers emp 
LEFT JOIN departments dep ON emp.employer_id = dep.employer_id Group By emp.employer_id;
```

---

How to add new columns in the tables?
```sql
ALTER TABLE employers ADD salary INT;
```

---

How to Update value in new columns in the tables?
```sql
Update employers SET  salary = 12000 where employer_id = 2;
```

This increases salary by 5000 for condition base
```sql
Update employers SET  salary = salary + 5000 where employer_id = 2;
```

HOw to view the index in POstgreSQL?
```sql
SELECT indexname, indexdef FROM pg_indexes WHERE tablename = 'employers';
```
<img width="811" height="97" alt="image" src="https://github.com/user-attachments/assets/6c580799-7154-4e8f-8b7b-44e132968c60" />

---
