# Chapter – Static Members in C#

## What are Static Members?

In C#, members of a class can be categorized into two types:

1. **Instance Members**
2. **Static Members**

A **static member** belongs to the **class itself**, not to any individual object.

This means:

* Only **one copy** of a static member exists.
* It is shared by all objects of the class.
* It can be accessed without creating an object.

---

# Instance Members vs Static Members

Consider an Employee class.

```csharp
class Employee
{
    public int Id;
    public string Name;
}
```

Every object has its own copy.

```csharp
Employee emp1 = new Employee();
Employee emp2 = new Employee();
```

Memory

```text
emp1

Id = 101
Name = Raman

---------------------

emp2

Id = 102
Name = John
```

Each object stores its own data.

Now consider a company name.

Every employee belongs to the same company.

Should every object store the company name?

No.

Instead, make it static.

---

# Why Do We Need Static Members?

Suppose

```csharp
class Employee
{
    public string Company = "Microsoft";
}
```

Creating 1000 objects

```csharp
Employee emp1 = new Employee();
Employee emp2 = new Employee();
Employee emp3 = new Employee();
```

Memory

```text
Object 1

Company = Microsoft

--------------------

Object 2

Company = Microsoft

--------------------

Object 3

Company = Microsoft
```

The same value is duplicated.

Instead

```csharp
class Employee
{
    public static string Company = "Microsoft";
}
```

Memory

```text
Employee Class

Company = Microsoft

--------------------

emp1

Id

Name

--------------------

emp2

Id

Name
```

Only **one copy** exists.

---

# Static Variable

A **static variable** belongs to the class rather than individual objects.

## Syntax

```csharp
class Employee
{
    public static string Company = "Microsoft";
}
```

Accessing

```csharp
Console.WriteLine(Employee.Company);
```

Output

```text
Microsoft
```

Notice

No object is created.

---

# Example

```csharp
class Employee
{
    public static string Company = "Microsoft";

    public string Name;
}

Employee emp1 = new Employee();

emp1.Name = "Raman";

Employee emp2 = new Employee();

emp2.Name = "John";

Console.WriteLine(Employee.Company);
```

Output

```text
Microsoft
```

Both employees share the same company.

---

# Static Variable Memory

```text
STACK

emp1

emp2

------------------------

HEAP

Employee Object

Name = Raman

------------------------

Employee Object

Name = John

------------------------

STATIC AREA

Company = Microsoft
```

There is only one static variable.

---

# Static Method

A **static method** belongs to the class.

It can be called without creating an object.

Example

```csharp
class Calculator
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
}
```

Calling

```csharp
Console.WriteLine(Calculator.Add(10,20));
```

Output

```text
30
```

---

# Rules for Static Methods

A static method:

✔ Can access static members.

✘ Cannot directly access instance members.

Example

```csharp
class Employee
{
    public string Name;

    public static void Display()
    {
        Console.WriteLine(Name);
    }
}
```

Compiler Error

Why?

Because there is no object.

---

Correct

```csharp
class Employee
{
    public static string Company = "Microsoft";

    public static void Display()
    {
        Console.WriteLine(Company);
    }
}
```

---

# Calling Instance Members

A static method can access instance members only through an object.

```csharp
class Employee
{
    public string Name;

    public static void Display(Employee employee)
    {
        Console.WriteLine(employee.Name);
    }
}
```

---

# Static Property

A property can also be static.

Example

```csharp
class Company
{
    public static string Name { get; set; } = "Microsoft";
}
```

Access

```csharp
Console.WriteLine(Company.Name);
```

Output

```text
Microsoft
```

---

# Static Constructor

A **static constructor** initializes static members.

Characteristics

* No parameters
* No access modifier
* Executes only once
* Called automatically

Example

```csharp
class Employee
{
    static Employee()
    {
        Console.WriteLine("Static Constructor");
    }

    public Employee()
    {
        Console.WriteLine("Instance Constructor");
    }
}
```

Usage

```csharp
Employee emp1 = new Employee();

Employee emp2 = new Employee();
```

Output

```text
Static Constructor

Instance Constructor

Instance Constructor
```

Notice

The static constructor executes only once.

---

# When Does a Static Constructor Execute?

It executes

* Before the first object is created.

OR

* Before the first static member is accessed.

Example

```csharp
Console.WriteLine(Employee.Company);
```

The static constructor executes first.

---

# Static Class

A **static class** contains only static members.

Characteristics

✔ Cannot create objects.

✔ Cannot inherit.

✔ Cannot be inherited.

✔ Contains only static members.

Example

```csharp
static class MathUtility
{
    public static int Square(int number)
    {
        return number * number;
    }
}
```

Using

```csharp
Console.WriteLine(MathUtility.Square(5));
```

Output

```text
25
```

---

Invalid

```csharp
MathUtility utility = new MathUtility();
```

Compiler Error

---

# Real Example

The .NET Framework itself contains many static classes.

Example

```csharp
Math
Console
Convert
Environment
GC
File
Path
```

Usage

```csharp
Console.WriteLine(Math.Sqrt(25));

Console.WriteLine(DateTime.Now);

Console.WriteLine(Convert.ToInt32("100"));
```

---

# Static Class vs Normal Class

| Static Class                           | Normal Class                |
| -------------------------------------- | --------------------------- |
| Cannot create object                   | Can create objects          |
| Only static members                    | Static and instance members |
| Cannot inherit                         | Can inherit                 |
| Cannot implement instance constructors | Can have constructors       |

---

# Static Variable Example (Object Counter)

```csharp
class Employee
{
    public static int Count = 0;

    public Employee()
    {
        Count++;
    }
}
```

Usage

```csharp
new Employee();

new Employee();

new Employee();

Console.WriteLine(Employee.Count);
```

Output

```text
3
```

This is a common interview example.

---

# Static Property Example

```csharp
class Company
{
    public static int EmployeeCount
    {
        get;
        set;
    }
}
```

Usage

```csharp
Company.EmployeeCount++;

Console.WriteLine(Company.EmployeeCount);
```

---

# Static Method Example

```csharp
class Utility
{
    public static void Welcome()
    {
        Console.WriteLine("Welcome to .NET");
    }
}
```

Usage

```csharp
Utility.Welcome();
```

Output

```text
Welcome to .NET
```

---

# Static Members in ASP.NET Core

Examples

Configuration

```csharp
public static class Constants
{
    public const string AdminRole = "Admin";
}
```

Usage

```csharp
if(role == Constants.AdminRole)
{
}
```

Utility Class

```csharp
public static class Helper
{
    public static string GetFullName(string first,string last)
    {
        return first + " " + last;
    }
}
```

Calling

```csharp
string name = Helper.GetFullName("Raman","Sharma");
```

---

# When to Use Static?

Use static when

* The data is common to all objects.
* No object-specific state is required.
* Creating an object is unnecessary.
* Utility/helper methods are needed.
* Constants or application-wide values are required.

Examples

* Mathematical calculations
* Utility methods
* Configuration values
* Constants
* Logging helpers

---

# When NOT to Use Static?

Avoid static when

* Each object should have its own data.
* Dependency Injection is required.
* Unit testing and mocking are important.
* State differs between objects.

Example

Employee Name

```csharp
public string Name;
```

Should **not** be static because every employee has a different name.

---

# Static vs Instance Members

| Feature         | Static Member                                 | Instance Member                                     |
| --------------- | --------------------------------------------- | --------------------------------------------------- |
| Belongs To      | Class                                         | Object                                              |
| Memory          | One copy                                      | One copy per object                                 |
| Object Required | No                                            | Yes                                                 |
| Access Using    | Class Name                                    | Object                                              |
| Constructor     | Static Constructor                            | Instance Constructor                                |
| Lifetime        | Until application ends (or AppDomain unloads) | Until the object is eligible for Garbage Collection |

---

# Best Practices

* Use **static** only when the member truly belongs to the class rather than an individual object.
* Prefer **instance members** for business data that varies per object.
* Keep static methods stateless whenever possible.
* Use static classes for utility or helper functions.
* Avoid excessive use of static variables because they represent shared state and can make testing more difficult.
* In ASP.NET Core, prefer **Dependency Injection** over static classes for services that have dependencies or need to be mocked during testing.

---

# Summary

| Static Member      | Description                                                           |
| ------------------ | --------------------------------------------------------------------- |
| Static Variable    | Shared by all objects of the class.                                   |
| Static Method      | Can be called without creating an object.                             |
| Static Property    | Shared property belonging to the class.                               |
| Static Constructor | Initializes static members and executes only once.                    |
| Static Class       | A class that contains only static members and cannot be instantiated. |

---

# Interview Questions

### 1. What is a static member?

A static member belongs to the **class**, not to an individual object, and is shared among all instances.

---

### 2. Can a static method access instance variables directly?

**No.** A static method has no reference to a specific object, so it cannot directly access instance members.

---

### 3. Can we create an object of a static class?

**No.** Static classes cannot be instantiated.

---

### 4. How many times does a static constructor execute?

Only **once**, before the first object is created or before the first static member is accessed.

---

### 5. What is the difference between a static variable and an instance variable?

| Static Variable                | Instance Variable            |
| ------------------------------ | ---------------------------- |
| One copy shared by all objects | Each object has its own copy |
| Belongs to the class           | Belongs to an object         |
| Accessed using the class name  | Accessed using an object     |

---

### 6. Why are static classes like `Math` and `Console` implemented as static?

Because they provide **utility functionality** that does not require object-specific data. Creating an object would provide no benefit, so the methods are exposed as static members.
