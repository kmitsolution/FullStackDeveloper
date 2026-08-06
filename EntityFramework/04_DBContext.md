# Entity Framework Core - Lesson 4

# Understanding DbContext and DbSet

Welcome to Lesson 4! This is the **most important lesson** in Entity Framework Core.

If you understand **DbContext**, learning EF Core becomes much easier.

---

# What You Will Learn

By the end of this lesson, you will understand:

* What is `DbContext`?
* What is `DbSet`?
* Why do we need `DbContext`?
* How does `DbContext` communicate with SQL Server?
* Create your first `DbContext`
* Register `DbContext` in Dependency Injection

---

# What is DbContext?

Imagine you're working in an office.

You want to:

* Read employees
* Add employees
* Update employees
* Delete employees

Can your application directly communicate with SQL Server?

**No.**

It needs someone in the middle.

That "someone" is **DbContext**.

```text
ASP.NET Core Application
          |
          |
     CompanyDbContext
          |
          |
     SQL Server
```

**DbContext is the bridge between your application and the database.**

---

# Real-Life Example

Imagine a bank.

```text
Customer

↓

Bank Employee

↓

Database
```

The customer doesn't directly access the database.

The bank employee acts as the mediator.

Similarly,

```text
Application

↓

DbContext

↓

SQL Server
```

---

# What Does DbContext Do?

DbContext is responsible for:

* Opening the database connection
* Reading data
* Saving data
* Updating data
* Deleting data
* Tracking changes
* Executing SQL commands

Think of `DbContext` as the **manager** of all database operations.

---

# What is DbSet?

Suppose your database has these tables:

```text
Employees

Departments

Students

Products
```

Inside `DbContext`, each table is represented by a `DbSet`.

Example:

```csharp
public DbSet<Employee> Employees { get; set; }
```

This represents the **Employees** table.

Another example:

```csharp
public DbSet<Department> Departments { get; set; }
```

This represents the **Departments** table.

Think of `DbSet<T>` as a collection of entities that maps to a database table.

---

# Visual Representation

```text
CompanyDbContext
│
├── Employees (DbSet<Employee>)
│
├── Departments (DbSet<Department>)
│
└── Students (DbSet<Student>)
```

Each `DbSet` corresponds to one table.

---

# Create the Data Folder

Your project structure should now look like:

```text
EmployeeAPI
│
├── Models
│      Employee.cs
│
├── Data
│
├── Program.cs
│
└── appsettings.json
```

Create a folder named:

```text
Data
```

---

# Create CompanyDbContext

Inside the **Data** folder, create:

```text
CompanyDbContext.cs
```

---

# CompanyDbContext Code

```csharp
using EmployeeAPI.Models;
using Microsoft.EntityFrameworkCore;

namespace EmployeeAPI.Data;

public class CompanyDbContext : DbContext
{
    public CompanyDbContext(DbContextOptions<CompanyDbContext> options)
        : base(options)
    {
    }

    public DbSet<Employee> Employees { get; set; } = null!;
}
```

Let's understand every line.

---

# Line 1

```csharp
using EmployeeAPI.Models;
```

This imports the `Employee` class.

Without it, C# won't know what `Employee` is.

---

# Line 2

```csharp
using Microsoft.EntityFrameworkCore;
```

This imports EF Core classes like:

* `DbContext`
* `DbSet`
* `DbContextOptions`

---

# Inheritance

```csharp
public class CompanyDbContext : DbContext
```

This means:

> **CompanyDbContext inherits all the functionality of DbContext.**

Just like:

```csharp
public class Dog : Animal
```

A `Dog` gets all the features of `Animal`.

Similarly, `CompanyDbContext` gets all the database features from `DbContext`.

---

# Constructor

```csharp
public CompanyDbContext(DbContextOptions<CompanyDbContext> options)
    : base(options)
{
}
```

This constructor receives configuration (such as the connection string) and passes it to the base `DbContext`.

For now, remember:

> Every `DbContext` needs this constructor so ASP.NET Core can configure it through Dependency Injection.

We'll see exactly where `options` comes from in `Program.cs`.

---

# DbSet Property

```csharp
public DbSet<Employee> Employees { get; set; } = null!;
```

Meaning:

```text
Employee Entity

↓

Employees Table
```

When you write:

```csharp
db.Employees
```

you're referring to the **Employees** table.

---

# Why is the Property Name "Employees"?

Notice:

```csharp
DbSet<Employee> Employees
```

* `Employee` (singular) → Entity (one object)
* `Employees` (plural) → Table (many records)

Example:

```text
Employee Class

↓

Employees Table
```

This naming convention makes the code easy to read.

---

# Register DbContext

Open `Program.cs`.

Later, after adding the connection string, you'll register the context like this:

```csharp
builder.Services.AddDbContext<CompanyDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection"));
});
```

Don't worry if `UseSqlServer` shows an error right now—you'll fix that in the next lesson by adding the connection string to `appsettings.json`.

---

# Dependency Injection

You don't create the `DbContext` yourself.

❌ Avoid:

```csharp
CompanyDbContext db = new CompanyDbContext(...);
```

Instead, ASP.NET Core creates it for you.

Later you'll simply write:

```csharp
app.MapGet("/employees", async (CompanyDbContext db) =>
{
    return await db.Employees.ToListAsync();
});
```

ASP.NET Core automatically injects the `CompanyDbContext`.

This is called **Dependency Injection (DI)**.

---

# Complete Flow

```text
Browser

↓

ASP.NET Core API

↓

CompanyDbContext

↓

Employees DbSet

↓

SQL Server
```

---

# Analogy

Imagine a library.

```text
Library

↓

Books Section

↓

Books
```

* Library → `DbContext`
* Books Section → `DbSet<Book>`
* Book → `Book` Entity

Similarly,

```text
CompanyDbContext

↓

Employees DbSet

↓

Employee Entity
```

---

# Summary

* `DbContext` is the bridge between your application and the database.
* `DbSet<TEntity>` represents a table in the database.
* One `DbContext` can contain many `DbSet` properties.
* `CompanyDbContext` inherits from `DbContext`.
* ASP.NET Core creates and injects `CompanyDbContext` using Dependency Injection.
* `DbContext` manages connections, queries, inserts, updates, deletes, and change tracking.

---

# Interview Questions

### Q1. What is `DbContext`?

**Answer:**
`DbContext` is the primary class in EF Core that manages the connection to the database and performs CRUD operations.

---

### Q2. What is `DbSet`?

**Answer:**
`DbSet<TEntity>` represents a table in the database and allows querying and saving instances of that entity.

---

### Q3. Can one `DbContext` have multiple `DbSet` properties?

**Answer:**
Yes. Each `DbSet` represents a different table.

Example:

```csharp
public DbSet<Employee> Employees { get; set; } = null!;
public DbSet<Department> Departments { get; set; } = null!;
public DbSet<Product> Products { get; set; } = null!;
```

---

### Q4. Why does `CompanyDbContext` inherit from `DbContext`?

**Answer:**
Because `DbContext` provides all the database functionality required by EF Core.

---

### Q5. Who creates the `CompanyDbContext` object in an ASP.NET Core application?

**Answer:**
The ASP.NET Core Dependency Injection container creates and manages it.

---

# Practice Exercise

1. Create a **Data** folder.
2. Add `CompanyDbContext.cs`.
3. Inherit it from `DbContext`.
4. Add the constructor with `DbContextOptions`.
5. Add the following property:

```csharp
public DbSet<Employee> Employees { get; set; } = null!;
```

---

# Next Lesson (Lesson 5)

We'll connect everything to a real SQL Server database by learning:

* Connection strings
* `appsettings.json`
* `UseSqlServer()`
* Registering `DbContext`
* Testing the database connection

This is where your application will be able to communicate with SQL Server for the first time.
