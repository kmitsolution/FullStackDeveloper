# Entity Framework Core - Lesson 7

# CRUD Operations with ASP.NET Core Minimal API + EF Core

Congratulations! 🎉

Up to now you've learned:

* ✅ Lesson 1 - What is EF Core?
* ✅ Lesson 2 - Installing EF Core
* ✅ Lesson 3 - Creating Entity
* ✅ Lesson 4 - DbContext & DbSet
* ✅ Lesson 5 - Connecting SQL Server
* ✅ Lesson 6 - Migrations

Now we're going to build a **real Employee Management API**.

This lesson is where everything comes together.

---

# Learning Objectives

By the end of this lesson, you'll be able to:

* Read all employees
* Read a single employee
* Insert a new employee
* Update an employee
* Delete an employee
* Test APIs using Postman

---

# Our Project

```text
EmployeeAPI
│
├── Models
│      Employee.cs
│
├── Data
│      CompanyDbContext.cs
│
├── Program.cs
│
├── appsettings.json
│
└── Migrations
```

---

# Database

Table:

```text
Employees
```

| Id | Name | Department | Salary |
| -- | ---- | ---------- | ------ |

---

# Program.cs

Assume you've already configured:

```csharp
builder.Services.AddDbContext<CompanyDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection"));
});
```

Now let's add APIs one by one.

---

# 1. GET All Employees

```csharp
app.MapGet("/employees", async (CompanyDbContext db) =>
{
    return await db.Employees.ToListAsync();
});
```

---

## How It Works

Browser/Postman

↓

```http
GET /employees
```

↓

ASP.NET Core injects

```text
CompanyDbContext
```

↓

```csharp
db.Employees.ToListAsync()
```

↓

EF Core generates SQL:

```sql
SELECT *
FROM Employees
```

↓

Returns

```json
[
  {
    "id":1,
    "name":"John",
    "department":"IT",
    "salary":50000
  },
  {
    "id":2,
    "name":"David",
    "department":"HR",
    "salary":45000
  }
]
```

---

# What is ToListAsync()?

Suppose Employees table has:

| Id | Name  |
| -- | ----- |
| 1  | John  |
| 2  | David |

```csharp
var employees = await db.Employees.ToListAsync();
```

returns

```csharp
List<Employee>
```

Think of it as:

```csharp
List<Employee> employees =
[
    Employee,
    Employee
];
```

---

# 2. GET Employee By Id

```csharp
app.MapGet("/employees/{id}", async (int id,
                                     CompanyDbContext db) =>
{
    var employee = await db.Employees.FindAsync(id);

    if(employee == null)
        return Results.NotFound();

    return Results.Ok(employee);
});
```

---

## URL

```http
GET /employees/2
```

EF Core generates:

```sql
SELECT *
FROM Employees
WHERE Id=2
```

If employee exists:

```json
{
  "id":2,
  "name":"David",
  "department":"HR",
  "salary":45000
}
```

Otherwise:

```http
404 Not Found
```

---

# Why FindAsync()?

`FindAsync()` searches using the **Primary Key**.

```csharp
db.Employees.FindAsync(id);
```

Equivalent SQL:

```sql
SELECT *
FROM Employees
WHERE Id=@id
```

It's optimized for primary key lookups.

---

# 3. POST - Insert Employee

```csharp
app.MapPost("/employees", async (Employee employee,
                                 CompanyDbContext db) =>
{
    db.Employees.Add(employee);

    await db.SaveChangesAsync();

    return Results.Created($"/employees/{employee.Id}",
                            employee);
});
```

---

## Request

```http
POST /employees
```

Body

```json
{
  "name":"Raman",
  "department":"IT",
  "salary":70000
}
```

Notice:

Don't send Id if it is an Identity column.

---

## What Happens?

Step 1

```csharp
db.Employees.Add(employee);
```

Employee is added to EF Core's **change tracker**.

Database is **not updated yet**.

---

Step 2

```csharp
await db.SaveChangesAsync();
```

EF Core generates:

```sql
INSERT INTO Employees
(Name,Department,Salary)
VALUES
('Raman','IT',70000)
```

Now the database is updated.

---

# Why SaveChangesAsync()?

This is one of the most important methods.

Without:

```csharp
SaveChangesAsync();
```

Nothing is saved.

Example:

```csharp
db.Employees.Add(emp);

// Database still unchanged
```

After:

```csharp
await db.SaveChangesAsync();
```

Now SQL executes.

---

# 4. PUT - Update Employee

```csharp
app.MapPut("/employees/{id}",
async (int id,
Employee updatedEmployee,
CompanyDbContext db)=>
{
    var employee =
        await db.Employees.FindAsync(id);

    if(employee==null)
        return Results.NotFound();

    employee.Name = updatedEmployee.Name;
    employee.Department = updatedEmployee.Department;
    employee.Salary = updatedEmployee.Salary;

    await db.SaveChangesAsync();

    return Results.Ok(employee);
});
```

---

## URL

```http
PUT /employees/1
```

Body

```json
{
  "name":"John Smith",
  "department":"IT",
  "salary":90000
}
```

---

Internally

EF Core executes:

```sql
UPDATE Employees

SET Name='John Smith',
Department='IT',
Salary=90000

WHERE Id=1
```

---

# Why Didn't We Call Update()?

Notice:

```csharp
employee.Name =
updatedEmployee.Name;
```

We never wrote:

```csharp
db.Employees.Update(employee);
```

Why?

Because EF Core is already **tracking** the object returned by:

```csharp
FindAsync()
```

When you change its properties and call:

```csharp
SaveChangesAsync();
```

EF Core detects the changes automatically.

---

# 5. DELETE Employee

```csharp
app.MapDelete("/employees/{id}",
async (int id,
CompanyDbContext db)=>
{
    var employee =
        await db.Employees.FindAsync(id);

    if(employee==null)
        return Results.NotFound();

    db.Employees.Remove(employee);

    await db.SaveChangesAsync();

    return Results.Ok();
});
```

---

URL

```http
DELETE /employees/1
```

Generated SQL

```sql
DELETE
FROM Employees

WHERE Id=1
```

---

# CRUD Summary

| HTTP Method | URL             | EF Core Method                     | SQL Generated |
| ----------- | --------------- | ---------------------------------- | ------------- |
| GET         | /employees      | ToListAsync()                      | SELECT *      |
| GET         | /employees/{id} | FindAsync()                        | SELECT WHERE  |
| POST        | /employees      | Add()                              | INSERT        |
| PUT         | /employees/{id} | Modify entity + SaveChangesAsync() | UPDATE        |
| DELETE      | /employees/{id} | Remove()                           | DELETE        |

---

# Understanding SaveChangesAsync()

Imagine writing in Microsoft Word.

You type:

```text
Hello World
```

Is it saved?

No.

Only after pressing:

```text
Ctrl + S
```

Similarly

```csharp
db.Employees.Add(emp);
```

is like typing.

```csharp
SaveChangesAsync();
```

is like pressing **Save**.

---

# Complete Request Flow

```text
Postman

↓

Minimal API

↓

CompanyDbContext

↓

DbSet<Employee>

↓

SQL Server

↓

Employees Table
```

---

# Testing with Postman

## GET All

```http
GET
https://localhost:5001/employees
```

---

## GET By Id

```http
GET
https://localhost:5001/employees/1
```

---

## POST

```http
POST
https://localhost:5001/employees
```

Body:

```json
{
  "name":"Raman",
  "department":"IT",
  "salary":70000
}
```

---

## PUT

```http
PUT
https://localhost:5001/employees/1
```

Body:

```json
{
  "name":"Raman Sharma",
  "department":"Azure",
  "salary":90000
}
```

---

## DELETE

```http
DELETE
https://localhost:5001/employees/1
```

---

# Interview Questions

### Q1. What does `ToListAsync()` return?

A `List<Employee>` containing all rows from the `Employees` table.

---

### Q2. Why use `FindAsync()`?

Because it retrieves an entity by its **primary key** and is optimized for that purpose.

---

### Q3. What happens if you call `Add()` but don't call `SaveChangesAsync()`?

The entity is tracked in memory, but no data is saved to the database.

---

### Q4. Why doesn't the `PUT` example call `Update()`?

Because the entity retrieved with `FindAsync()` is already tracked by EF Core. `SaveChangesAsync()` detects the property changes automatically.

---

### Q5. What does `Remove()` do?

It marks the entity for deletion. The record is actually removed from the database when `SaveChangesAsync()` is called.

---

# Practice Exercise

1. Add all five endpoints (`GET`, `GET by Id`, `POST`, `PUT`, `DELETE`) to `Program.cs`.
2. Run the application.
3. Test each endpoint in Postman.
4. Verify the changes in SQL Server Management Studio (SSMS).

---

# Next Lesson (Lesson 8)

We'll dive into **LINQ with Entity Framework Core**, including:

* `Where()`
* `OrderBy()`
* `OrderByDescending()`
* `Select()`
* `FirstOrDefaultAsync()`
* `SingleOrDefaultAsync()`
* `AnyAsync()`
* `CountAsync()`
* `Skip()` and `Take()` for pagination

These are the queries you'll use daily in real-world ASP.NET Core applications.
