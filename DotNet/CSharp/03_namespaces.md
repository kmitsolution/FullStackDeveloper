# Namespaces in C#

A **namespace** is a logical container used to organize related classes, interfaces, structs, enums, delegates, and other namespaces.

Think of a namespace like a **folder** on your computer.

* A folder contains files.
* A namespace contains C# types (classes, interfaces, enums, etc.).

Namespaces help avoid naming conflicts and make applications easier to organize and maintain.

---

# Why Do We Need Namespaces?

Imagine you have two developers working on different modules.

Developer A creates:

```csharp
class Employee
{
}
```

Developer B also creates:

```csharp
class Employee
{
}
```

Without namespaces, the compiler would not know which `Employee` class you are referring to.

Namespaces solve this problem.

Example

```csharp
namespace Company.HR
{
    class Employee
    {
    }
}

namespace Company.Payroll
{
    class Employee
    {
    }
}
```

Now both classes can coexist because they belong to different namespaces.

---

# Syntax of a Namespace

```csharp
namespace Company.HR
{
    class Employee
    {
    }
}
```

Here,

* `Company` is the top-level namespace.
* `HR` is a child namespace.
* `Employee` belongs to `Company.HR`.

---

# Real-World Analogy

Consider a company with multiple departments.

```text
Company
│
├── HR
│     ├── Employee
│     ├── Manager
│
├── Payroll
│     ├── Employee
│     ├── Salary
│
└── Finance
      ├── Invoice
      ├── Tax
```

Each department may have an `Employee` class, but because they belong to different namespaces, there is no conflict.

---

# Example 1: Simple Namespace

```csharp
namespace Company.HR
{
    class Employee
    {
        public void Display()
        {
            Console.WriteLine("HR Employee");
        }
    }
}
```

Using it

```csharp
Company.HR.Employee emp = new Company.HR.Employee();

emp.Display();
```

Output

```text
HR Employee
```

---

# Using the `using` Keyword

Instead of writing the full namespace every time, import it with `using`.

```csharp
using Company.HR;

Employee emp = new Employee();

emp.Display();
```

Now the compiler already knows where `Employee` is located.

---

# Multiple Namespaces

One file can contain multiple namespaces.

```csharp
namespace Company.HR
{
    class Employee
    {
    }
}

namespace Company.Payroll
{
    class Salary
    {
    }
}
```

This is valid C# code.

---

# Nested Namespaces

A namespace can contain another namespace.

Example

```csharp
namespace Company
{
    namespace HR
    {
        class Employee
        {
        }
    }
}
```

This is called a **nested namespace**.

It is equivalent to writing:

```csharp
namespace Company.HR
{
    class Employee
    {
    }
}
```

Both declarations create the same namespace.

---

# Namespace Hierarchy

```text
Company
│
├── HR
│     ├── Employee
│     └── Manager
│
├── Payroll
│     ├── Salary
│     └── Bonus
│
└── Finance
      ├── Tax
      └── Invoice
```

Each level represents another namespace.

---

# Accessing Nested Namespaces

Suppose we have:

```csharp
namespace Company
{
    namespace HR
    {
        class Employee
        {
            public void Display()
            {
                Console.WriteLine("HR Employee");
            }
        }
    }
}
```

You can access it using its fully qualified name.

```csharp
Company.HR.Employee emp = new Company.HR.Employee();

emp.Display();
```

---

Or use the `using` directive.

```csharp
using Company.HR;

Employee emp = new Employee();

emp.Display();
```

---

# Importing a Parent Namespace

Consider the following:

```csharp
namespace Company
{
    namespace HR
    {
        class Employee
        {
        }
    }

    namespace Finance
    {
        class Invoice
        {
        }
    }
}
```

If you write:

```csharp
using Company;
```

You **cannot** directly use:

```csharp
Employee emp = new Employee();
```

This results in a compiler error because `Employee` is inside `Company.HR`, not directly inside `Company`.

You must either:

```csharp
using Company.HR;

Employee emp = new Employee();
```

or use the fully qualified name:

```csharp
Company.HR.Employee emp = new Company.HR.Employee();
```

---

# Namespace Alias

If namespace names are long, create an alias.

Example

```csharp
using HR = Company.HR;
```

Now use:

```csharp
HR.Employee emp = new HR.Employee();
```

This makes the code shorter and easier to read.

---

# Handling Name Conflicts

Suppose two namespaces contain an `Employee` class.

```csharp
namespace Company.HR
{
    class Employee
    {
    }
}

namespace Company.Payroll
{
    class Employee
    {
    }
}
```

If you import both namespaces:

```csharp
using Company.HR;
using Company.Payroll;
```

Then this becomes ambiguous:

```csharp
Employee emp = new Employee();
```

The compiler doesn't know which `Employee` you mean.

### Solution 1: Fully Qualified Name

```csharp
Company.HR.Employee emp1 = new Company.HR.Employee();

Company.Payroll.Employee emp2 = new Company.Payroll.Employee();
```

### Solution 2: Namespace Alias

```csharp
using HREmployee = Company.HR.Employee;
using PayrollEmployee = Company.Payroll.Employee;
```

Now you can write:

```csharp
HREmployee emp1 = new HREmployee();

PayrollEmployee emp2 = new PayrollEmployee();
```

---

# File-Scoped Namespace (C# 10 and Later)

Modern C# introduced file-scoped namespaces to reduce indentation.

Traditional style:

```csharp
namespace Company.HR
{
    class Employee
    {
    }
}
```

File-scoped style:

```csharp
namespace Company.HR;

class Employee
{
}
```

Both are equivalent, but the file-scoped version is cleaner and is commonly used in modern ASP.NET Core projects.

---

# Namespaces in ASP.NET Core

A typical ASP.NET Core project uses namespaces to organize different parts of the application.

```text
MyApplication
│
├── Controllers
│      └── HomeController.cs
│
├── Models
│      └── Employee.cs
│
├── Services
│      └── EmployeeService.cs
│
├── Repositories
│      └── EmployeeRepository.cs
│
└── Data
       └── AppDbContext.cs
```

Generated namespaces usually look like:

```csharp
namespace MyApplication.Controllers;
```

```csharp
namespace MyApplication.Models;
```

```csharp
namespace MyApplication.Services;
```

This organization makes the project easier to navigate and maintain.

---

# Best Practices

* Use namespaces to group related classes and components.
* Follow your project or company naming conventions (for example, `MyApplication.Services` or `MyApplication.Controllers`).
* Prefer file-scoped namespaces in new .NET projects.
* Avoid placing unrelated classes in the same namespace.
* Use namespace aliases when working with long namespace names or resolving naming conflicts.
* Keep namespaces aligned with your project's folder structure whenever possible.

---

# Summary

| Concept               | Description                                                                                               |
| --------------------- | --------------------------------------------------------------------------------------------------------- |
| Namespace             | A logical container for related types such as classes, interfaces, structs, and enums.                    |
| `using`               | Imports a namespace so you don't have to write the full name repeatedly.                                  |
| Nested Namespace      | A namespace declared inside another namespace (for example, `Company.HR`).                                |
| Fully Qualified Name  | The complete path to a type, such as `Company.HR.Employee`.                                               |
| Namespace Alias       | A short name for a namespace or type, created with the `using` keyword.                                   |
| File-Scoped Namespace | A modern syntax introduced in C# 10 that reduces indentation and is widely used in ASP.NET Core projects. |

**Interview Question:** *Does `using Company;` automatically import `Company.HR`?*

**Answer:** No. `using Company;` imports only the types that are directly inside the `Company` namespace. To use types in `Company.HR`, you must either write `using Company.HR;` or reference them with their fully qualified name, such as `Company.HR.Employee`.
