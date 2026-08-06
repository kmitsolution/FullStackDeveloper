# Entity Framework Core - Lesson 9

# Change Tracking in Entity Framework Core

Welcome to **Lesson 9**.

This is one of the most important concepts in Entity Framework Core.

Many interview questions are asked about **Change Tracking**, and understanding it will help you understand why `SaveChangesAsync()` works without writing SQL.

---

# Learning Objectives

By the end of this lesson, you'll understand:

* What is Change Tracking?
* Why EF Core tracks entities
* Entity States
* How `SaveChangesAsync()` works
* Tracked vs No-Tracking queries
* `AsNoTracking()`
* When to use tracking and when not to

---

# What is Change Tracking?

**Change Tracking** means:

> EF Core automatically keeps track of entities that it has loaded from the database and monitors any changes made to them.

When you call:

```csharp
var employee = await db.Employees.FindAsync(1);
```

EF Core remembers:

```text
Employee
Id = 1
Name = John
Salary = 50000
```

If you later change:

```csharp
employee.Salary = 70000;
```

EF Core notices:

```text
Old Salary = 50000

↓

New Salary = 70000
```

This is called **Change Tracking**.

---

# Real-Life Example

Imagine your teacher writes your exam marks.

Initial marks:

```text
Math = 80
Science = 90
English = 70
```

Later:

```text
Math = 95
```

The teacher immediately knows:

```text
80

↓

95
```

The teacher is **tracking the changes**.

EF Core does exactly the same.

---

# Example

Suppose the database contains:

| Id | Name | Salary |
| -- | ---- | ------ |
| 1  | John | 50000  |

You write:

```csharp
var employee = await db.Employees.FindAsync(1);
```

EF Core stores:

```text
Original Values

Id = 1

Name = John

Salary = 50000
```

Now change:

```csharp
employee.Salary = 80000;
```

Current values become:

```text
Id = 1

Name = John

Salary = 80000
```

EF Core compares:

```text
Original

↓

Current
```

Difference:

```text
Salary changed
```

---

# SaveChangesAsync()

When you execute:

```csharp
await db.SaveChangesAsync();
```

EF Core generates:

```sql
UPDATE Employees

SET Salary = 80000

WHERE Id = 1;
```

Notice:

You never wrote SQL.

---

# How Does EF Core Know?

Because it remembers:

```text
Old Value

↓

New Value
```

This comparison happens automatically.

---

# Entity States

Every entity has a **State**.

There are **five** states.

---

## 1. Detached

Entity is not tracked.

```text
Employee

×

DbContext
```

Example:

```csharp
Employee emp = new Employee();
```

Not loaded from EF Core.

Not tracked.

---

## 2. Unchanged

Loaded from database.

Nothing modified.

Example:

```csharp
var emp = await db.Employees.FindAsync(1);
```

State:

```text
Unchanged
```

---

## 3. Modified

After:

```csharp
emp.Salary = 90000;
```

State becomes:

```text
Modified
```

EF Core now knows an UPDATE is required.

---

## 4. Added

```csharp
db.Employees.Add(employee);
```

State:

```text
Added
```

After `SaveChangesAsync()`

↓

SQL

```sql
INSERT INTO Employees...
```

---

## 5. Deleted

```csharp
db.Employees.Remove(employee);
```

State:

```text
Deleted
```

After saving

↓

SQL

```sql
DELETE FROM Employees
WHERE Id=1
```

---

# Visual State Diagram

```text
            Add()
Detached -------------> Added
   ▲                     |
   |                     |
   |                     |
Remove()              SaveChanges()
   |                     |
   |                     ▼
Deleted <---------- Unchanged
     ▲                 |
     |                 |
     |                 |
     -------- Modified
```

---

# Checking Entity State

You can inspect an entity's state.

```csharp
var employee = await db.Employees.FindAsync(1);

Console.WriteLine(db.Entry(employee).State);
```

Output:

```text
Unchanged
```

Now:

```csharp
employee.Name = "John Smith";

Console.WriteLine(db.Entry(employee).State);
```

Output:

```text
Modified
```

---

# Change Tracking Example

```csharp
var employee = await db.Employees.FindAsync(1);

employee.Department = "Azure";

employee.Salary = 90000;

await db.SaveChangesAsync();
```

Generated SQL:

```sql
UPDATE Employees

SET Department='Azure',
Salary=90000

WHERE Id=1
```

Only modified columns are updated.

---

# AsNoTracking()

Sometimes you only want to **read** data.

Example:

* Dashboard
* Reports
* Search page

No updates.

In such cases:

```csharp
var employees = await db.Employees
    .AsNoTracking()
    .ToListAsync();
```

EF Core **does not track** these objects.

Advantages:

* Faster
* Less memory usage

---

# Without AsNoTracking()

```csharp
var employees =
await db.Employees.ToListAsync();
```

Every employee is tracked.

1000 employees

↓

1000 tracked objects.

---

# With AsNoTracking()

```csharp
var employees =
await db.Employees
        .AsNoTracking()
        .ToListAsync();
```

EF Core simply reads the data and returns it.

No tracking.

---

# When Should You Use AsNoTracking()?

### Good Candidates

* Reports
* Search results
* Dashboards
* Read-only APIs
* Large datasets

---

### Avoid It When

You plan to update the entity.

Example:

```csharp
var employee =
await db.Employees
    .AsNoTracking()
    .FirstAsync();

employee.Name = "ABC";

await db.SaveChangesAsync();
```

Nothing happens because EF Core isn't tracking the entity.

---

# How to Update After AsNoTracking()

Suppose you loaded an entity without tracking but now want to update it.

```csharp
var employee = await db.Employees
    .AsNoTracking()
    .FirstAsync(e => e.Id == 1);

employee.Name = "John Smith";

db.Employees.Update(employee);

await db.SaveChangesAsync();
```

Here, `Update()` tells EF Core to start tracking the entity as **Modified**.

---

# Tracking Comparison

## Tracked Query

```csharp
var employee =
await db.Employees.FindAsync(1);

employee.Salary = 70000;

await db.SaveChangesAsync();
```

Works because EF Core is tracking the entity.

---

## No Tracking Query

```csharp
var employee =
await db.Employees
    .AsNoTracking()
    .FirstAsync(e => e.Id == 1);

employee.Salary = 70000;

await db.SaveChangesAsync();
```

Nothing is updated because the entity is not tracked.

---

# Complete Flow

```text
FindAsync()

↓

Entity Loaded

↓

EF Core Starts Tracking

↓

Property Changed

↓

Entity State = Modified

↓

SaveChangesAsync()

↓

UPDATE SQL
```

---

# Summary Table

| State     | Description             | SQL Generated |
| --------- | ----------------------- | ------------- |
| Detached  | Not tracked             | None          |
| Unchanged | Loaded, no changes      | None          |
| Added     | New entity              | INSERT        |
| Modified  | Existing entity changed | UPDATE        |
| Deleted   | Marked for deletion     | DELETE        |

---

# Best Practices

✅ Use tracking when:

* Updating data
* Deleting data
* Inserting related entities

✅ Use `AsNoTracking()` when:

* Reading data only
* Building dashboards
* Generating reports
* Returning large result sets

---

# Interview Questions

### Q1. What is Change Tracking?

**Answer:**
Change Tracking is the mechanism by which EF Core monitors entities loaded into a `DbContext` and detects changes made to them.

---

### Q2. Why does `SaveChangesAsync()` know what to update?

**Answer:**
Because EF Core compares the original values of tracked entities with their current values and generates the required SQL statements.

---

### Q3. What is the difference between `FindAsync()` and `AsNoTracking()`?

**Answer:**

* `FindAsync()` returns a tracked entity.
* `AsNoTracking()` returns an untracked entity, which is more efficient for read-only operations.

---

### Q4. Name the five entity states.

**Answer:**

1. Detached
2. Unchanged
3. Added
4. Modified
5. Deleted

---

### Q5. When should you use `AsNoTracking()`?

**Answer:**
For read-only scenarios where the data won't be modified, such as reports, dashboards, or search results.

---

# Practice Exercise

Create the following endpoints:

### 1. Read-only endpoint

```csharp
app.MapGet("/employees/readonly", async (CompanyDbContext db) =>
{
    return await db.Employees
        .AsNoTracking()
        .ToListAsync();
});
```

### 2. Update endpoint

```csharp
app.MapPut("/employees/{id}/salary", async (int id, decimal salary, CompanyDbContext db) =>
{
    var employee = await db.Employees.FindAsync(id);

    if (employee is null)
        return Results.NotFound();

    employee.Salary = salary;

    await db.SaveChangesAsync();

    return Results.Ok(employee);
});
```

Run both endpoints and observe:

* The first endpoint simply returns data without tracking.
* The second endpoint updates the salary because the entity is tracked.

---

# Next Lesson (Lesson 10)

In the next lesson, we'll cover **Relationships in Entity Framework Core**, including:

* One-to-One
* One-to-Many
* Many-to-Many
* Navigation Properties
* Foreign Keys
* `Include()` for loading related data

These concepts are essential for building real-world applications with multiple related tables such as **Employees**, **Departments**, **Orders**, and **Customers**.
