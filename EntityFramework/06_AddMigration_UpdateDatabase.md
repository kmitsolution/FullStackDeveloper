# Entity Framework Core - Lesson 6

# Migrations (Creating Database and Tables Automatically)

Welcome to **Lesson 6**.

This is one of the **most powerful features of Entity Framework Core**.

Before EF Core, developers used to manually write SQL like:

```sql
CREATE DATABASE CompanyDB;

CREATE TABLE Employees
(
    Id INT PRIMARY KEY,
    Name NVARCHAR(100),
    Department NVARCHAR(50),
    Salary DECIMAL(18,2)
);
```

With EF Core, **you don't have to write this SQL**. EF Core can generate it for you using **Migrations**.

---

# Learning Objectives

By the end of this lesson, you'll understand:

* What is a Migration?
* Why do we use Migrations?
* What is Code First?
* How to create a Migration
* How to create a database automatically
* What files EF Core generates
* How `Update-Database` works

---

# What is a Migration?

A **Migration** is a set of instructions that tells EF Core how to create or update your database schema.

Think of it as a **history of changes** made to your database.

---

## Real-Life Example

Imagine you're building a house.

### Day 1

Build:

* Foundation
* Walls
* Roof

### Day 2

Add:

* Windows
* Doors

### Day 3

Add:

* Paint
* Lights

Instead of rebuilding the house every day, you apply only the new changes.

EF Core Migrations work the same way.

---

# Before Migration

Current project:

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

Notice:

We have an `Employee` class.

But there is **no Employees table** in SQL Server yet.

---

# How Does EF Core Know What to Create?

Our entity is:

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public string Department { get; set; } = "";

    public decimal Salary { get; set; }
}
```

Our DbContext contains:

```csharp
public DbSet<Employee> Employees { get; set; } = null!;
```

EF Core reads these classes and understands:

> Create a table called **Employees** with four columns.

---

# Migration Process

```text
Employee Class

↓

DbContext

↓

Add-Migration

↓

Migration File

↓

Update-Database

↓

SQL Server
```

---

# Step 1: Install EF Tools (Already Done)

From Lesson 2, we already installed:

```text
Microsoft.EntityFrameworkCore.Tools
```

This package enables migration commands.

---

# Step 2: Open Package Manager Console

In Visual Studio:

```text
Tools

↓

NuGet Package Manager

↓

Package Manager Console
```

---

# Step 3: Create First Migration

Run:

```powershell
Add-Migration InitialCreate
```

Or using the .NET CLI:

```bash
dotnet ef migrations add InitialCreate
```

### What does `InitialCreate` mean?

It's just the **name of the migration**.

Common names:

```text
InitialCreate

AddDepartmentTable

AddSalaryColumn

UpdateEmployeeTable
```

Choose names that describe the change.

---

# What Happens Internally?

EF Core compares:

* Your entities (`Employee`)
* Your `DbContext`

It notices:

> There is no database yet.

So it generates code to create it.

---

# New Folder Created

After running the command, you'll see:

```text
EmployeeAPI
│
├── Migrations
│      20260806120000_InitialCreate.cs
│      20260806120000_InitialCreate.Designer.cs
│      CompanyDbContextModelSnapshot.cs
```

*(The timestamp will be different on your machine.)*

---

# Understanding the Migration File

Inside the migration:

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "Employees",
        columns: table => new
        {
            Id = table.Column<int>(),
            Name = table.Column<string>(),
            Department = table.Column<string>(),
            Salary = table.Column<decimal>()
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Employees", x => x.Id);
        });
}
```

### What is `Up()`?

`Up()` contains the changes to **apply**.

Think:

```text
Up = Move Forward
```

---

# What is `Down()`?

```csharp
protected override void Down(MigrationBuilder migrationBuilder)
{
    migrationBuilder.DropTable("Employees");
}
```

`Down()` reverses the migration.

Think:

```text
Down = Undo
```

If you roll back the migration, EF Core uses `Down()`.

---

# Step 4: Apply the Migration

Run:

```powershell
Update-Database
```

Or:

```bash
dotnet ef database update
```

EF Core will:

1. Connect to SQL Server.
2. Create the `CompanyDB` database if it doesn't already exist.
3. Create the `Employees` table.
4. Create a special table called `__EFMigrationsHistory`.

---

# What is `__EFMigrationsHistory`?

EF Core creates this table automatically.

Example:

| MigrationId   |
| ------------- |
| InitialCreate |

Purpose:

It keeps track of which migrations have already been applied.

Without it, EF Core wouldn't know what changes are pending.

---

# Database After Migration

```text
CompanyDB
│
├── Employees
│
└── __EFMigrationsHistory
```

---

# Employees Table

| Id | Name | Department | Salary |
| -- | ---- | ---------- | ------ |

Initially, the table is empty.

---

# Complete Flow

```text
Employee.cs

↓

CompanyDbContext

↓

Add-Migration InitialCreate

↓

Migration Files

↓

Update-Database

↓

CompanyDB

↓

Employees Table
```

---

# Adding a New Property

Suppose you update the entity:

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public string Department { get; set; } = "";

    public decimal Salary { get; set; }

    public string Email { get; set; } = "";
}
```

The database doesn't change automatically.

Create a new migration:

```powershell
Add-Migration AddEmployeeEmail
```

Then apply it:

```powershell
Update-Database
```

EF Core generates SQL similar to:

```sql
ALTER TABLE Employees
ADD Email NVARCHAR(MAX);
```

No manual SQL is required.

---

# Migration Naming Best Practices

Good names:

```text
InitialCreate

AddEmployeeEmail

AddDepartmentTable

RemoveSalaryColumn

RenameEmployeeTable
```

Avoid names like:

```text
Test

ABC

Migration1

NewMigration
```

Use meaningful names so the project history is easy to understand.

---

# Common Commands

### Add Migration

```powershell
Add-Migration InitialCreate
```

---

### Apply Migration

```powershell
Update-Database
```

---

### List Migrations

```powershell
Get-Migration
```

or

```bash
dotnet ef migrations list
```

---

### Remove Last Migration (only if it hasn't been applied to the database)

```powershell
Remove-Migration
```

---

# Common Mistakes

### Forgetting `Update-Database`

Running only:

```powershell
Add-Migration InitialCreate
```

creates the migration files **only**.

It does **not** create the database or tables.

You must also run:

```powershell
Update-Database
```

---

### Changing the Entity Without a New Migration

If you modify the `Employee` class but don't create a new migration, the database schema and your C# model become out of sync.

---

### Incorrect Connection String

If your connection string points to the wrong SQL Server instance, `Update-Database` will fail.

---

# Summary

* A **Migration** represents changes to the database schema.
* `Add-Migration` creates migration files based on your entities.
* `Update-Database` applies those migrations to SQL Server.
* `Up()` applies changes.
* `Down()` reverts changes.
* `__EFMigrationsHistory` tracks applied migrations.
* Whenever you change your entity classes, create a new migration and apply it.

---

# Interview Questions

### Q1. What is a Migration?

**Answer:**
A Migration is a mechanism in EF Core that tracks and applies database schema changes based on changes to your C# model.

---

### Q2. What does `Add-Migration` do?

**Answer:**
It compares the current model with the previous model and generates migration files describing the schema changes.

---

### Q3. What does `Update-Database` do?

**Answer:**
It executes pending migrations against the configured database.

---

### Q4. What is the purpose of the `Up()` method?

**Answer:**
It contains the operations to apply the migration, such as creating tables or adding columns.

---

### Q5. What is the purpose of the `Down()` method?

**Answer:**
It contains the operations to undo the migration, such as dropping tables or removing columns.

---

### Q6. Why does EF Core create the `__EFMigrationsHistory` table?

**Answer:**
It records which migrations have already been applied so EF Core knows which ones are still pending.

---

# Practice Exercise

1. Create the `Employee` entity.
2. Create `CompanyDbContext`.
3. Configure the SQL Server connection string.
4. Register `CompanyDbContext` in `Program.cs`.
5. Run:

```powershell
Add-Migration InitialCreate
```

6. Then run:

```powershell
Update-Database
```

7. Open SQL Server Management Studio (SSMS) and verify that:

   * `CompanyDB` exists.
   * `Employees` table exists.
   * `__EFMigrationsHistory` table exists.

---

# Next Lesson (Lesson 7)

In the next lesson, we'll build a complete **Employee CRUD API** using EF Core. You'll learn how to:

* Insert records (`Add`)
* Retrieve records (`ToListAsync`, `FindAsync`)
* Update records
* Delete records
* Save changes with `SaveChangesAsync()`
* Test everything using Postman

This will be your first end-to-end ASP.NET Core + EF Core + SQL Server application.
