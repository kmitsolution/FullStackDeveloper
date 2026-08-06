# Entity Framework Core - Lesson 8

# LINQ Queries with Entity Framework Core

Welcome to **Lesson 8**.

So far, you've learned how to perform basic CRUD operations using EF Core. In real-world applications, however, we rarely retrieve **all** records. Instead, we filter, sort, search, count, and paginate data.

That's where **LINQ** comes in.

---

# Learning Objectives

By the end of this lesson, you'll understand:

* What is LINQ?
* How LINQ works with EF Core
* `Where()`
* `FirstOrDefaultAsync()`
* `SingleOrDefaultAsync()`
* `AnyAsync()`
* `CountAsync()`
* `OrderBy()`
* `OrderByDescending()`
* `Select()`
* `Skip()` and `Take()` (Pagination)

---

# What is LINQ?

**LINQ** stands for **Language Integrated Query**.

It lets you query data using C# instead of writing SQL.

Instead of:

```sql
SELECT * FROM Employees
WHERE Department='IT'
```

You write:

```csharp
var employees = await db.Employees
    .Where(e => e.Department == "IT")
    .ToListAsync();
```

EF Core automatically converts it into SQL.

---

# How LINQ Works

```text
C# LINQ Query

↓

Entity Framework Core

↓

SQL Query

↓

SQL Server

↓

Results

↓

C# Objects
```

You write **C#**, EF Core writes **SQL**.

---

# Sample Data

Assume the Employees table contains:

| Id | Name  | Department | Salary |
| -- | ----- | ---------- | ------ |
| 1  | John  | IT         | 50000  |
| 2  | David | HR         | 40000  |
| 3  | Alice | IT         | 70000  |
| 4  | Bob   | Finance    | 60000  |
| 5  | Raman | IT         | 90000  |

---

# 1. Where()

Used for filtering records.

```csharp
app.MapGet("/employees/department/{department}",
async (string department, CompanyDbContext db) =>
{
    return await db.Employees
        .Where(e => e.Department == department)
        .ToListAsync();
});
```

Request:

```http
GET /employees/department/IT
```

Generated SQL:

```sql
SELECT *
FROM Employees
WHERE Department='IT'
```

Result:

```json
[
  {
    "id":1,
    "name":"John"
  },
  {
    "id":3,
    "name":"Alice"
  },
  {
    "id":5,
    "name":"Raman"
  }
]
```

---

# Multiple Conditions

```csharp
var employees = await db.Employees
    .Where(e => e.Department == "IT"
             && e.Salary > 60000)
    .ToListAsync();
```

Generated SQL:

```sql
SELECT *
FROM Employees
WHERE Department='IT'
AND Salary > 60000
```

---

# 2. FirstOrDefaultAsync()

Returns the **first matching record**.

```csharp
var employee = await db.Employees
    .FirstOrDefaultAsync(e => e.Department == "IT");
```

Generated SQL:

```sql
SELECT TOP(1) *
FROM Employees
WHERE Department='IT'
```

If no record exists:

```csharp
employee == null
```

---

# FirstOrDefault vs FindAsync

### FindAsync

```csharp
await db.Employees.FindAsync(1);
```

* Searches by **Primary Key only**
* Faster for primary key lookups

---

### FirstOrDefaultAsync

```csharp
await db.Employees
    .FirstOrDefaultAsync(e => e.Name == "John");
```

* Can search by **any column**
* More flexible

---

# 3. SingleOrDefaultAsync()

```csharp
var employee = await db.Employees
    .SingleOrDefaultAsync(e => e.Id == 1);
```

Returns exactly **one** record.

If multiple records match:

```text
Exception!
```

Use when you're sure only one record should exist (for example, querying by a unique key).

---

# FirstOrDefault vs SingleOrDefault

Suppose:

| Name |
| ---- |
| John |
| John |

### FirstOrDefault()

Returns:

```text
First John
```

No exception.

---

### SingleOrDefault()

Throws an exception because two rows matched.

---

# 4. AnyAsync()

Checks whether any matching record exists.

```csharp
bool exists = await db.Employees
    .AnyAsync(e => e.Department == "Finance");
```

Generated SQL:

```sql
SELECT CASE
WHEN EXISTS(...)
THEN 1
ELSE 0
END
```

Result:

```text
true
```

or

```text
false
```

---

# 5. CountAsync()

Count employees.

```csharp
int total = await db.Employees.CountAsync();
```

SQL:

```sql
SELECT COUNT(*)
FROM Employees
```

---

Count only IT employees.

```csharp
int total = await db.Employees
    .CountAsync(e => e.Department == "IT");
```

SQL:

```sql
SELECT COUNT(*)
FROM Employees
WHERE Department='IT'
```

---

# 6. OrderBy()

Ascending order.

```csharp
var employees = await db.Employees
    .OrderBy(e => e.Name)
    .ToListAsync();
```

SQL:

```sql
SELECT *
FROM Employees
ORDER BY Name ASC
```

---

# 7. OrderByDescending()

Descending order.

```csharp
var employees = await db.Employees
    .OrderByDescending(e => e.Salary)
    .ToListAsync();
```

SQL:

```sql
SELECT *
FROM Employees
ORDER BY Salary DESC
```

Highest salary appears first.

---

# 8. Select()

Returns only required columns.

Instead of:

```csharp
var employees = await db.Employees.ToListAsync();
```

Use:

```csharp
var employees = await db.Employees
    .Select(e => new
    {
        e.Name,
        e.Department
    })
    .ToListAsync();
```

SQL:

```sql
SELECT Name,
Department
FROM Employees
```

Much faster because fewer columns are retrieved.

---

# Why Use Select()?

Suppose your Employee table has:

* Profile Picture
* Resume
* Address
* Mobile Number
* Salary
* PAN
* Aadhaar

If the client only needs:

```text
Name
Department
```

don't fetch everything.

This improves performance.

---

# 9. Skip() and Take()

Useful for **pagination**.

Suppose there are 1000 employees.

You don't want all 1000 at once.

---

### First Page

```csharp
var employees = await db.Employees
    .Skip(0)
    .Take(10)
    .ToListAsync();
```

SQL:

```sql
SELECT *
FROM Employees
ORDER BY Id
OFFSET 0 ROWS
FETCH NEXT 10 ROWS ONLY
```

---

### Second Page

```csharp
var employees = await db.Employees
    .Skip(10)
    .Take(10)
    .ToListAsync();
```

Returns records 11–20.

---

# Real Pagination API

```csharp
app.MapGet("/employees",
async (int page,
       int pageSize,
       CompanyDbContext db)=>
{
    return await db.Employees
        .Skip((page - 1) * pageSize)
        .Take(pageSize)
        .ToListAsync();
});
```

Request:

```http
GET /employees?page=2&pageSize=5
```

Returns:

Employees 6–10.

---

# Combining LINQ

```csharp
var employees = await db.Employees
    .Where(e => e.Department == "IT")
    .OrderByDescending(e => e.Salary)
    .Select(e => new
    {
        e.Name,
        e.Salary
    })
    .Take(3)
    .ToListAsync();
```

Generated SQL:

```sql
SELECT TOP 3
Name,
Salary
FROM Employees
WHERE Department='IT'
ORDER BY Salary DESC
```

This returns the **top 3 highest-paid IT employees**.

---

# Complete LINQ Flow

```text
LINQ Query

↓

EF Core

↓

SQL

↓

SQL Server

↓

Employee Objects
```

---

# Summary Table

| LINQ Method              | Purpose            | SQL Equivalent               |
| ------------------------ | ------------------ | ---------------------------- |
| `Where()`                | Filter records     | `WHERE`                      |
| `FirstOrDefaultAsync()`  | First matching row | `TOP 1`                      |
| `SingleOrDefaultAsync()` | Exactly one row    | `WHERE` (expects one result) |
| `AnyAsync()`             | Check existence    | `EXISTS`                     |
| `CountAsync()`           | Count rows         | `COUNT(*)`                   |
| `OrderBy()`              | Sort ascending     | `ORDER BY ASC`               |
| `OrderByDescending()`    | Sort descending    | `ORDER BY DESC`              |
| `Select()`               | Select columns     | `SELECT`                     |
| `Skip()`                 | Skip rows          | `OFFSET`                     |
| `Take()`                 | Limit rows         | `TOP` / `FETCH NEXT`         |

---

# Best Practices

* Use `Where()` before `ToListAsync()` so filtering happens in the database.
* Use `Select()` to fetch only the columns you need.
* Prefer `AnyAsync()` over `CountAsync() > 0` when checking for existence.
* Always use `OrderBy()` before `Skip()` and `Take()` for predictable pagination.
* Use asynchronous methods (`ToListAsync`, `CountAsync`, etc.) in ASP.NET Core applications.

---

# Interview Questions

### Q1. What is LINQ?

**Answer:**
LINQ (Language Integrated Query) allows you to query data using C# syntax instead of writing SQL.

---

### Q2. What is the difference between `FindAsync()` and `FirstOrDefaultAsync()`?

**Answer:**

* `FindAsync()` searches only by the primary key.
* `FirstOrDefaultAsync()` can search using any condition.

---

### Q3. When should you use `SingleOrDefaultAsync()`?

**Answer:**
When you expect **zero or one** matching record. If multiple records match, it throws an exception.

---

### Q4. Why use `Select()`?

**Answer:**
To retrieve only the required columns, reducing data transfer and improving performance.

---

### Q5. How do `Skip()` and `Take()` help?

**Answer:**
They implement pagination by skipping a specified number of rows and returning a limited set of records.

---

# Practice Exercise

Using your `Employee` table, write LINQ queries to:

1. Retrieve all employees from the **IT** department.
2. Find the employee with **Id = 3**.
3. Check whether any employee belongs to **Finance**.
4. Count the total number of employees.
5. Retrieve employees ordered by **Salary** in descending order.
6. Return only **Name** and **Department**.
7. Display the **first 5 employees**.
8. Display the **next 5 employees** (pagination).

---

# Next Lesson (Lesson 9)

We'll explore **Change Tracking** in Entity Framework Core, one of its core features. You'll learn:

* What Change Tracking is
* How EF Core detects changes automatically
* Why `SaveChangesAsync()` knows what to update
* Tracked vs. No-Tracking queries (`AsNoTracking()`)
* When to use tracking and when to disable it for better performance
