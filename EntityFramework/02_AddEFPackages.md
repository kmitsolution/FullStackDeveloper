# Lesson 2: Installing Entity Framework Core and Creating Your First EF Core Project

In Lesson 1, you learned **what EF Core is** and **why we use it**.

Today we'll actually set up an ASP.NET Core project with SQL Server and EF Core.

---

# Learning Objectives

By the end of this lesson, you will be able to:

* Install Entity Framework Core
* Install SQL Server Provider
* Install EF Core Tools
* Create an ASP.NET Core Web API project
* Understand what each package does
* Verify the installation

---

# What We Will Build

We are not connecting to the database yet.

Today's goal is only to prepare our project.

```text
ASP.NET Core Web API
        |
        |
   Entity Framework Core
        |
        |
 SQL Server (Later)
```

In the next lesson, we'll connect to SQL Server.

---

# Step 1: Create a New Project

Open Visual Studio.

Select:

```
Create New Project
```

Choose

```
ASP.NET Core Web API
```

Click **Next**.

Example:

```
Project Name:
EmployeeAPI
```

Click **Next**

Choose

```
.NET 9
```

Click **Create**

Visual Studio creates the project.

---

# Project Structure

Initially you'll see something like:

```text
EmployeeAPI
│
├── Properties
├── appsettings.json
├── Program.cs
├── EmployeeAPI.http
└── EmployeeAPI.csproj
```

Notice

There is **NO Entity Framework** yet.

---

# Step 2: Open NuGet Package Manager

There are two ways.

### Method 1

Right Click Project

```
Manage NuGet Packages
```

---

### Method 2

```
Tools

↓

NuGet Package Manager

↓

Manage NuGet Packages for Solution
```

---

# Step 3: Install Required Packages

We need **three packages**.

---

## Package 1

```
Microsoft.EntityFrameworkCore
```

Purpose

This is the main EF Core package.

It contains:

* DbContext
* DbSet
* Change Tracking
* LINQ support
* SaveChanges()

Without this package, EF Core won't work.

---

## Package 2

```
Microsoft.EntityFrameworkCore.SqlServer
```

Purpose

Allows EF Core to communicate with SQL Server.

Think of it as a translator.

```text
EF Core

↓

SQL Server Provider

↓

SQL Server
```

Without it, EF Core doesn't know how to talk to SQL Server.

---

## Package 3

```
Microsoft.EntityFrameworkCore.Tools
```

Purpose

Provides EF Core commands such as:

```bash
Add-Migration
```

```bash
Update-Database
```

```bash
Script-Migration
```

These are used for database migrations.

---

# Installing via .NET CLI

If you prefer the terminal, run:

```bash
dotnet add package Microsoft.EntityFrameworkCore
```

```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

```bash
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

---

# What Happens After Installation?

Your project file (`.csproj`) will include entries like:

```xml
<ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="9.0.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="9.0.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="9.0.0" />
</ItemGroup>
```

*(The version number depends on the .NET version you're targeting.)*

---

# Step 4: Verify Installation

Expand

```
Dependencies

↓

Packages
```

You should see:

```text
Packages

Microsoft.EntityFrameworkCore

Microsoft.EntityFrameworkCore.SqlServer

Microsoft.EntityFrameworkCore.Tools
```

Congratulations!

EF Core is now installed.

---

# Understanding the Three Packages

Imagine you're buying a car.

### Microsoft.EntityFrameworkCore

The **engine**.

Without it, the car cannot move.

---

### Microsoft.EntityFrameworkCore.SqlServer

The **road adapter**.

It knows how to drive on SQL Server roads.

---

### Microsoft.EntityFrameworkCore.Tools

The **mechanic's toolbox**.

Used for creating and updating the database schema (migrations).

---

# How They Work Together

```text
Your Application
        |
        |
Entity Framework Core
        |
SQL Server Provider
        |
SQL Server Database
```

---

# Important Names You'll Hear Often

## DbContext

Represents the database.

Example:

```csharp
CompanyDbContext
```

Think of it as a connection/session with the database.

---

## DbSet

Represents a table.

Example:

```csharp
DbSet<Employee>
```

means

```text
Employees Table
```

---

## Entity

A C# class.

Example

```csharp
Employee
```

It represents one row in the Employees table.

---

# What We Have So Far

Right now:

```text
Project

✓ Created

↓

EF Core Installed

↓

Ready for Database Connection
```

Notice that we still have **no database**, **no entity**, and **no DbContext**. We'll create those in the upcoming lessons.

---

# What We'll Create in the Next Few Lessons

We'll build an Employee Management API with this structure:

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

# Visual Overview

```text
Lesson 1
--------
What is EF Core?
        ✓

Lesson 2
--------
Install EF Core
        ✓

Lesson 3
--------
Create Entity Class
        ↓

Lesson 4
--------
Create DbContext
        ↓

Lesson 5
--------
Connect SQL Server
        ↓

Lesson 6
--------
Create Database Using Migration
        ↓

Lesson 7
--------
CRUD Operations
```

---

# Interview Questions

### Q1. What is the purpose of `Microsoft.EntityFrameworkCore`?

**Answer:**
It is the core EF Core library that provides features like `DbContext`, `DbSet`, change tracking, and LINQ support.

---

### Q2. Why do we install `Microsoft.EntityFrameworkCore.SqlServer`?

**Answer:**
It enables EF Core to communicate with SQL Server by providing the SQL Server database provider.

---

### Q3. What is `Microsoft.EntityFrameworkCore.Tools` used for?

**Answer:**
It provides tooling for creating and applying migrations, such as `Add-Migration` and `Update-Database`.

---

### Q4. Can EF Core work without a database provider?

**Answer:**
No. EF Core needs a provider (such as SQL Server, SQLite, or PostgreSQL) to communicate with a specific database.

---

### Q5. Which package contains `DbContext`?

**Answer:**
`Microsoft.EntityFrameworkCore`

---

# Practice Exercise

1. Create a new **ASP.NET Core Web API** project named **EmployeeAPI**.
2. Install:

   * `Microsoft.EntityFrameworkCore`
   * `Microsoft.EntityFrameworkCore.SqlServer`
   * `Microsoft.EntityFrameworkCore.Tools`
3. Verify that all three packages appear under **Dependencies → Packages**.
4. Open the `.csproj` file and confirm the `<PackageReference>` entries were added.

Once you've completed this, we'll move to **Lesson 3: Creating Your First Entity (Employee) and Understanding How C# Classes Map to Database Tables**.
