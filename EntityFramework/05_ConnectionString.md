# Entity Framework Core - Lesson 5

# Connecting Entity Framework Core to SQL Server

Excellent! In the previous lesson, we created our `CompanyDbContext`.

Today, we'll connect our ASP.NET Core application to a real SQL Server database.

This is one of the most important lessons because **without a connection string, EF Core doesn't know which database to use.**

---

# Learning Objectives

By the end of this lesson, you will understand:

* What is a Connection String?
* What is `appsettings.json`?
* How to configure SQL Server
* How to register `DbContext`
* How `UseSqlServer()` works
* The complete request flow from API to SQL Server

---

# Current Project Structure

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
└── appsettings.json
```

---

# Step 1: What is a Connection String?

A **Connection String** tells your application:

* Which SQL Server to connect to
* Which database to use
* How to authenticate

Think of it as the **address** of your database.

### Real-life Example

Suppose you want to visit your friend.

You need:

* House Number
* Street
* City

Similarly, EF Core needs:

* SQL Server Name
* Database Name
* Username/Password (or Windows Authentication)

---

# Step 2: appsettings.json

ASP.NET Core stores configuration values in:

```text
appsettings.json
```

Typical file:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

We'll add a **ConnectionStrings** section.

---

# Step 3: Add Connection String

If you're using **Windows Authentication**:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=CompanyDB;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

### Explanation

```text
Server=.
```

`.` means:

> Connect to the local SQL Server instance.

You can also use:

```text
Server=localhost
```

or

```text
Server=Raman-PC
```

or

```text
Server=.\SQLEXPRESS
```

depending on your SQL Server installation.

---

### Database

```text
Database=CompanyDB
```

This is the database name.

---

### Trusted_Connection=True

This means:

Use Windows login instead of SQL Server username/password.

---

### TrustServerCertificate=True

Accept the SQL Server certificate.

Useful during local development.

---

# SQL Authentication Example

If using SQL login:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=CompanyDB;User Id=sa;Password=YourPassword;TrustServerCertificate=True;"
  }
}
```

---

# Step 4: Read Connection String

Open `Program.cs`.

Register the `DbContext`:

```csharp
using EmployeeAPI.Data;
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<CompanyDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection"));
});

var app = builder.Build();

app.Run();
```

---

# Understanding Every Line

## builder.Services

```csharp
builder.Services
```

This is the **Dependency Injection (DI) container**.

We register services here.

Examples:

* DbContext
* Logging
* Authentication
* Swagger

---

## AddDbContext

```csharp
builder.Services.AddDbContext<CompanyDbContext>()
```

This tells ASP.NET Core:

> "Whenever someone asks for `CompanyDbContext`, create one and inject it."

---

## options

```csharp
options =>
```

`options` allows us to configure the `DbContext`.

Example:

```csharp
options.UseSqlServer(...)
```

---

## UseSqlServer

```csharp
options.UseSqlServer(...)
```

This tells EF Core:

> "Use SQL Server as the database provider."

Remember from Lesson 2:

We installed:

```text
Microsoft.EntityFrameworkCore.SqlServer
```

This package provides the `UseSqlServer()` method.

---

## GetConnectionString

```csharp
builder.Configuration.GetConnectionString("DefaultConnection")
```

This reads:

```json
{
  "ConnectionStrings": {
      "DefaultConnection": "..."
  }
}
```

The name **must match**.

For example:

```json
"DefaultConnection"
```

matches:

```csharp
GetConnectionString("DefaultConnection")
```

---

# Complete Flow

```text
appsettings.json
        │
        ▼
Connection String
        │
        ▼
Program.cs
        │
        ▼
UseSqlServer()
        │
        ▼
CompanyDbContext
        │
        ▼
SQL Server
```

---

# What Happens Internally?

Suppose you call:

```csharp
db.Employees.ToListAsync();
```

The flow is:

```text
API Request

↓

CompanyDbContext

↓

SQL Server Provider

↓

SQL Server Database

↓

Returns Employee Objects
```

---

# Visual Architecture

```text
Browser

↓

Employee API

↓

CompanyDbContext

↓

UseSqlServer()

↓

SQL Server

↓

CompanyDB

↓

Employees Table
```

---

# How Does `DbContext` Get the Connection?

You never write:

```csharp
new SqlConnection(...)
```

EF Core does that internally.

You simply register:

```csharp
builder.Services.AddDbContext<CompanyDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection"));
});
```

After that, EF Core manages the database connection for each request.

---

# Common Connection Strings

## Local SQL Server

```text
Server=.;
```

---

## SQL Express

```text
Server=.\SQLEXPRESS;
```

---

## LocalDB (Visual Studio)

```text
Server=(localdb)\MSSQLLocalDB;
```

---

## Named Server

```text
Server=Raman-PC;
```

---

# Common Mistakes

### Wrong Database Name

```text
Database=CompanyDB
```

If the database name doesn't exist, the connection will fail.

---

### Wrong Server Name

```text
Server=ABC
```

If SQL Server isn't installed on that machine, the connection will fail.

---

### Missing SQL Server Package

If you forget to install:

```text
Microsoft.EntityFrameworkCore.SqlServer
```

then this line:

```csharp
options.UseSqlServer(...)
```

will show a compile-time error because the extension method isn't available.

---

### Typo in Connection String Name

If `appsettings.json` contains:

```json
"MyConnection"
```

but your code says:

```csharp
GetConnectionString("DefaultConnection")
```

the result will be `null`, causing startup or runtime errors.

---

# Summary

* `appsettings.json` stores application configuration.
* The connection string tells EF Core how to connect to SQL Server.
* `UseSqlServer()` configures EF Core to use SQL Server.
* `AddDbContext()` registers `CompanyDbContext` with Dependency Injection.
* `GetConnectionString()` reads the configured connection string from `appsettings.json`.

---

# Interview Questions

### Q1. What is a Connection String?

**Answer:**
A Connection String contains the information needed to connect to a database, such as the server name, database name, and authentication details.

---

### Q2. Where are Connection Strings usually stored in ASP.NET Core?

**Answer:**
In the `appsettings.json` file under the `ConnectionStrings` section.

---

### Q3. What is the purpose of `UseSqlServer()`?

**Answer:**
It configures Entity Framework Core to use SQL Server as its database provider.

---

### Q4. What does `AddDbContext()` do?

**Answer:**
It registers the `DbContext` with ASP.NET Core's Dependency Injection container so it can be injected where needed.

---

### Q5. What does `GetConnectionString("DefaultConnection")` do?

**Answer:**
It retrieves the value of the `"DefaultConnection"` entry from the `ConnectionStrings` section of `appsettings.json`.

---

# Practice Exercise

1. Create a database named **CompanyDB** in SQL Server (or plan to create it in the next lesson).
2. Add the following to `appsettings.json`:

```json
"ConnectionStrings": {
  "DefaultConnection": "Server=.;Database=CompanyDB;Trusted_Connection=True;TrustServerCertificate=True;"
}
```

3. Register `CompanyDbContext` in `Program.cs` using `AddDbContext()` and `UseSqlServer()`.
4. Verify that your project builds successfully.

---

# Next Lesson (Lesson 6)

In the next lesson, you'll learn one of EF Core's most powerful features: **Migrations**.

We'll cover:

* What are Migrations?
* Why do we use Migrations?
* `Add-Migration`
* `Update-Database`
* How EF Core creates tables automatically from your C# classes
* Understanding the generated migration files

By the end of Lesson 6, you'll have your **Employees** table created in SQL Server without writing any SQL manually.
