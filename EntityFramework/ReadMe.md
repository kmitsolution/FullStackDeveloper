Since you're learning **ASP.NET Core step by step**, the best approach is to learn **Entity Framework Core (EF Core)** from the basics to advanced. EF Core is Microsoft's ORM (Object Relational Mapper) that allows you to work with databases using C# objects instead of writing SQL for every operation.

# Entity Framework Core Roadmap

## Module 1: Introduction to Entity Framework Core

### What is Entity Framework Core?

Entity Framework Core (EF Core) is an ORM framework developed by Microsoft.

Without EF Core:

```
Application
     |
     | SQL Query
     |
SQL Server
```

You write SQL queries like:

```sql
SELECT * FROM Employees;
```

With EF Core:

```
Application
     |
 Employee Objects
     |
 EF Core
     |
 SQL Server
```

You write:

```csharp
var employees = context.Employees.ToList();
```

EF Core converts this into SQL automatically.

---

## Advantages

* Less SQL code
* Strongly typed programming
* LINQ support
* Cross-platform
* Supports SQL Server, MySQL, PostgreSQL, Oracle, SQLite
* Migration support
* Change Tracking

---

# Module 2: EF Core Architecture

```
Application
      |
 DbContext
      |
 DbSet<TEntity>
      |
 Provider (SQL Server)
      |
 Database
```

Components:

### DbContext

Represents a database session.

Example:

```csharp
public class AppDbContext : DbContext
{
}
```

---

### DbSet

Represents a table.

```csharp
public DbSet<Employee> Employees { get; set; }
```

---

### Entity

Represents a table row.

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public decimal Salary { get; set; }
}
```

---

### Provider

Examples:

* SQL Server
* MySQL
* PostgreSQL
* SQLite

---

# Module 3: Install EF Core

Create ASP.NET Core Web API

```
dotnet new webapi
```

Install packages

```
dotnet add package Microsoft.EntityFrameworkCore

dotnet add package Microsoft.EntityFrameworkCore.SqlServer

dotnet add package Microsoft.EntityFrameworkCore.Tools
```

---

# Module 4: SQL Server Database

Suppose database:

```
CompanyDB
```

Table:

```
Employees
```

| Id | Name  | Department | Salary |
| -- | ----- | ---------- | ------ |
| 1  | John  | IT         | 50000  |
| 2  | David | HR         | 40000  |

---

# Module 5: Create Entity

Employee.cs

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public string Department { get; set; }

    public decimal Salary { get; set; }
}
```

Notice

One Employee object represents one row.

---

# Module 6: Create DbContext

```csharp
using Microsoft.EntityFrameworkCore;

public class CompanyDbContext : DbContext
{
    public CompanyDbContext(DbContextOptions<CompanyDbContext> options)
        : base(options)
    {
    }

    public DbSet<Employee> Employees { get; set; }
}
```

---

# Module 7: Connection String

appsettings.json

```json
{
  "ConnectionStrings": {
    "DefaultConnection":
      "Server=.;Database=CompanyDB;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

For SQL Authentication

```json
"Server=.;Database=CompanyDB;User Id=sa;Password=Password123;TrustServerCertificate=True;"
```

---

# Module 8: Register DbContext

Program.cs

```csharp
builder.Services.AddDbContext<CompanyDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection"));
});
```

Now EF Core can connect to SQL Server.

---

# Module 9: Dependency Injection

Inject DbContext

```csharp
app.MapGet("/employees",
    async (CompanyDbContext db) =>
{
    return await db.Employees.ToListAsync();
});
```

No need to create:

```csharp
new CompanyDbContext();
```

DI creates it automatically.

---

# Module 10: CRUD Operations

## Read

```csharp
app.MapGet("/employees",
async (CompanyDbContext db)=>
{
    return await db.Employees.ToListAsync();
});
```

---

## Read Single

```csharp
app.MapGet("/employees/{id}",
async (int id, CompanyDbContext db)=>
{
    return await db.Employees.FindAsync(id);
});
```

---

## Insert

```csharp
app.MapPost("/employees",
async (Employee emp, CompanyDbContext db)=>
{
    db.Employees.Add(emp);

    await db.SaveChangesAsync();

    return Results.Created($"/employees/{emp.Id}", emp);
});
```

---

## Update

```csharp
app.MapPut("/employees/{id}",
async (int id,
Employee updated,
CompanyDbContext db)=>
{
    var emp = await db.Employees.FindAsync(id);

    if(emp==null)
        return Results.NotFound();

    emp.Name = updated.Name;
    emp.Department = updated.Department;
    emp.Salary = updated.Salary;

    await db.SaveChangesAsync();

    return Results.Ok(emp);
});
```

---

## Delete

```csharp
app.MapDelete("/employees/{id}",
async (int id,
CompanyDbContext db)=>
{
    var emp = await db.Employees.FindAsync(id);

    if(emp==null)
        return Results.NotFound();

    db.Employees.Remove(emp);

    await db.SaveChangesAsync();

    return Results.Ok();
});
```

---

# Module 11: SaveChanges()

Nothing is saved until:

```csharp
await db.SaveChangesAsync();
```

Example

```csharp
db.Employees.Add(employee);

// Database still unchanged

await db.SaveChangesAsync();

// Now INSERT executes
```

Think of it like clicking **Save** in Microsoft Word—your edits remain in memory until you save them.

---

# Module 12: LINQ Queries

All Employees

```csharp
db.Employees.ToListAsync();
```

Salary > 50000

```csharp
db.Employees
.Where(x=>x.Salary>50000)
.ToListAsync();
```

Department

```csharp
db.Employees
.Where(x=>x.Department=="IT")
.ToListAsync();
```

Sorting

```csharp
db.Employees
.OrderBy(x=>x.Name)
.ToListAsync();
```

Top 5

```csharp
db.Employees
.Take(5)
.ToListAsync();
```

---

# Module 13: Migrations

Instead of manually creating tables, EF Core can generate them from your C# classes.

Create Migration

```bash
dotnet ef migrations add InitialCreate
```

Apply Migration

```bash
dotnet ef database update
```

EF Core generates SQL such as:

```sql
CREATE TABLE Employees
(
    Id int PRIMARY KEY,
    Name nvarchar(max),
    Department nvarchar(max),
    Salary decimal(18,2)
)
```

---

# Module 14: Relationships

One-to-Many

```
Department
----------
Id
Name

Employee
---------
Id
Name
DepartmentId
```

Models:

```csharp
public class Department
{
    public int Id { get; set; }

    public string Name { get; set; }

    public List<Employee> Employees { get; set; } = new();
}
```

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public int DepartmentId { get; set; }

    public Department Department { get; set; } = null!;
}
```

---

# Module 15: Loading Related Data

Eager Loading

```csharp
var employees = await db.Employees
    .Include(e => e.Department)
    .ToListAsync();
```

---

# Module 16: Best Practices

* Use Dependency Injection for `DbContext`.
* Use asynchronous methods (`ToListAsync`, `FindAsync`, `SaveChangesAsync`).
* Keep connection strings in `appsettings.json`.
* Use migrations to manage schema changes.
* Prefer LINQ over raw SQL where appropriate.
* Use repositories or services for larger applications.
* Keep `DbContext` short-lived (the default scoped lifetime is appropriate for web requests).

---

# Module 17: Real Project Structure

```
EmployeeApi
│
├── Models
│      Employee.cs
│      Department.cs
│
├── Data
│      CompanyDbContext.cs
│
├── Services
│
├── Repositories
│
├── Program.cs
│
├── appsettings.json
│
└── Migrations
```

---

# Learning Path (Recommended Order)

1. What is an ORM?
2. EF Core Architecture
3. Installing EF Core Packages
4. Creating Entities
5. Creating `DbContext`
6. Configuring SQL Server Connection
7. Dependency Injection with `DbContext`
8. CRUD Operations
9. LINQ Queries
10. Change Tracking
11. Migrations
12. Relationships (One-to-One, One-to-Many, Many-to-Many)
13. Loading Related Data (`Include`, Lazy Loading, Explicit Loading)
14. Fluent API
15. Data Annotations
16. Transactions
17. Stored Procedures and Raw SQL
18. Performance Optimization (No Tracking, Compiled Queries, Batching)
19. Repository and Unit of Work Pattern
20. Building a complete ASP.NET Core Web API using EF Core

Since you've already started learning **Minimal APIs with `MapGet` and `MapPost`**, the next logical step is to build a **complete Employee Management REST API** using **ASP.NET Core + SQL Server + Entity Framework Core**. This will let you see how `DbContext`, dependency injection, migrations, CRUD operations, and LINQ work together in a real application before moving on to advanced EF Core features.
