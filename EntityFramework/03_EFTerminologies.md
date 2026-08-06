# Entity Framework Core - Lesson 3

# Creating Your First Entity (Model)

Welcome to Lesson 3!

In the previous lessons:

* ✅ Lesson 1: What is Entity Framework Core?
* ✅ Lesson 2: Installing EF Core Packages

Today we'll learn one of the most important concepts in EF Core:

> **What is an Entity?**

Once you understand entities, everything else in EF Core becomes much easier.

---

# Learning Objectives

By the end of this lesson, you'll understand:

* What is an Entity?
* What is a Model?
* How does a C# class become a database table?
* What are Properties?
* What is a Primary Key?
* Entity Naming Conventions
* Create your first `Employee` entity

---

# What is an Entity?

An **Entity** is simply a **C# class** that represents a table in the database.

For example:

Suppose our SQL Server database has this table:

## Employees Table

| Id | Name  | Department | Salary |
| -- | ----- | ---------- | ------ |
| 1  | John  | IT         | 50000  |
| 2  | David | HR         | 45000  |

Now we create the equivalent C# class:

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public string Department { get; set; }

    public decimal Salary { get; set; }
}
```

This class is called an **Entity**.

---

# Why is it Called an Entity?

Think of it this way.

A table stores records.

Each record becomes one object.

Example:

Database

| Id | Name  |
| -- | ----- |
| 1  | John  |
| 2  | David |

becomes

```csharp
Employee emp1 = new Employee()
{
    Id = 1,
    Name = "John"
};

Employee emp2 = new Employee()
{
    Id = 2,
    Name = "David"
};
```

Each object represents **one row**.

---

# Real-Life Example

Imagine a school.

Student Table

| RollNo | Name  | Marks |
| ------ | ----- | ----- |
| 1      | Rahul | 90    |
| 2      | Priya | 95    |

Entity

```csharp
public class Student
{
    public int RollNo { get; set; }

    public string Name { get; set; }

    public int Marks { get; set; }
}
```

Again,

**One row = One Object**

---

# Where Do We Keep Entities?

In ASP.NET Core projects, we usually create a folder named:

```text
Models
```

Project structure:

```text
EmployeeAPI
│
├── Models
│      Employee.cs
│
├── Program.cs
│
├── appsettings.json
```

Why "Models"?

Because these classes represent the application's data model.

---

# Creating the Employee Entity

Right-click your project.

```
Add
```

↓

```
New Folder
```

Name it

```
Models
```

Inside the Models folder

```
Add

↓

Class
```

Name it

```
Employee.cs
```

---

# Employee.cs

```csharp
namespace EmployeeAPI.Models;

public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public string Department { get; set; } = "";

    public decimal Salary { get; set; }
}
```

Notice the use of `= ""` for strings. With nullable reference types enabled (default in modern .NET), this avoids compiler warnings about uninitialized non-nullable properties.

---

# Understanding Each Property

```csharp
public int Id { get; set; }
```

Represents

```text
Employee ID
```

Example

```
101
```

---

```csharp
public string Name { get; set; } = "";
```

Represents

```
John
```

---

```csharp
public string Department { get; set; } = "";
```

Represents

```
IT
```

---

```csharp
public decimal Salary { get; set; }
```

Represents

```
50000.75
```

We use `decimal` instead of `float` or `double` for money because it provides better precision for financial values.

---

# Visual Mapping

```text
Employee Class

Id
Name
Department
Salary
```

maps to

```text
Employees Table

Id
Name
Department
Salary
```

---

# Entity = Table

Think of it like this:

```text
C# Class
      ↓
Entity
      ↓
Database Table
```

---

# Object = Row

Employee table

| Id | Name  |
| -- | ----- |
| 1  | John  |
| 2  | David |

Objects

```csharp
Employee e1 = new Employee()
{
    Id = 1,
    Name = "John"
};

Employee e2 = new Employee()
{
    Id = 2,
    Name = "David"
};
```

Every row becomes an object.

---

# What is a Property?

Inside a class

```csharp
public string Name { get; set; }
```

is called a **Property**.

Each property becomes a **column** in the database.

| Property   | Database Column |
| ---------- | --------------- |
| Id         | Id              |
| Name       | Name            |
| Department | Department      |
| Salary     | Salary          |

---

# What is a Primary Key?

Every table should uniquely identify each row.

Example

| Id | Name  |
| -- | ----- |
| 1  | John  |
| 2  | David |

The **Id** uniquely identifies each employee.

EF Core follows a convention:

If your property is named:

```text
Id
```

or

```text
EmployeeId
```

EF Core automatically treats it as the **Primary Key**.

So this is enough:

```csharp
public int Id { get; set; }
```

No extra configuration is needed.

---

# Naming Conventions

### Entity Name

Use singular.

✔ Good

```text
Employee
Department
Student
```

✘ Avoid

```text
Employees
Departments
```

The class represents **one object**, not a collection.

---

### Table Name

By convention, EF Core names the table after the `DbSet` property (which we'll create in the next lesson). If you define:

```csharp
public DbSet<Employee> Employees { get; set; }
```

the table will typically be named **Employees**.

---

# Creating Objects

Now create an object.

```csharp
Employee emp = new Employee();

emp.Id = 1;

emp.Name = "John";

emp.Department = "IT";

emp.Salary = 50000;
```

Another way

```csharp
Employee emp = new Employee
{
    Id = 1,
    Name = "John",
    Department = "IT",
    Salary = 50000
};
```

Both create the same object.

---

# Multiple Objects

```csharp
List<Employee> employees = new List<Employee>
{
    new Employee
    {
        Id = 1,
        Name = "John",
        Department = "IT",
        Salary = 50000
    },
    new Employee
    {
        Id = 2,
        Name = "David",
        Department = "HR",
        Salary = 45000
    }
};
```

Later, EF Core will populate this list directly from SQL Server.

---

# Important Point

Currently

```text
Employee Class
```

exists only in C#.

There is **no database table yet**.

We'll create the database table later using **Migrations**.

---

# Summary Diagram

```text
SQL Server

Employees Table
        ▲
        │
        │
Entity Framework Core
        │
        ▼
Employee Class
```

EF Core maps between the class and the table.

---

# Interview Questions

## Q1. What is an Entity?

**Answer:**
An Entity is a C# class that represents a table in the database.

---

## Q2. What does one object represent?

**Answer:**
One object represents one row (record) in the database table.

---

## Q3. What does a property represent?

**Answer:**
A property represents a column in the database table.

---

## Q4. Which property becomes the Primary Key by convention?

**Answer:**
A property named `Id` or `<EntityName>Id` (for example, `EmployeeId`).

---

## Q5. Why do we use `decimal` for Salary?

**Answer:**
Because `decimal` provides high precision and is suitable for financial and currency values.

---

# Practice Exercise

Create the following entity in your project:

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public string Department { get; set; } = "";

    public decimal Salary { get; set; }
}
```

Then answer these questions:

1. Which property becomes the **Primary Key**?
2. Which property stores the employee's department?
3. How many columns will the table have?
4. If there are 100 rows in the table, how many `Employee` objects can EF Core create?
5. Why is the class named `Employee` instead of `Employees`?

---

## Next Lesson Preview

In **Lesson 4**, we'll create the **DbContext**, which is the heart of Entity Framework Core. You'll learn:

* What is `DbContext`?
* What is `DbSet`?
* How `DbContext` acts as a bridge between your application and SQL Server.
* How to register `DbContext` with Dependency Injection in ASP.NET Core.
