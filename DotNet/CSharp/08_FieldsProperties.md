# Chapter – Fields and Properties in C#

Fields and Properties are one of the most important concepts in C#. They define **what data an object stores** and **how that data is accessed**.

In ASP.NET Core, almost every Model, Entity, DTO, ViewModel, and Configuration class is built using **properties**.

For example:

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public decimal Salary { get; set; }
}
```

This is a class with **three properties**.

---

# Why Do We Need Fields and Properties?

Consider an Employee object.

An Employee has:

* Employee Id
* Employee Name
* Salary
* Department

These are the **data** that belong to an Employee.

There are two ways to store this data:

* Fields
* Properties

---

# What is a Field?

A **field** is a variable declared inside a class.

It stores the actual data of an object.

## Syntax

```csharp
class Employee
{
    public int Id;

    public string Name;

    public double Salary;
}
```

Here,

* Id
* Name
* Salary

are fields.

---

# Creating an Object

```csharp
Employee emp = new Employee();

emp.Id = 1001;
emp.Name = "Raman";
emp.Salary = 50000;
```

Output

```text
1001 Raman 50000
```

---

# Memory Representation

```text
STACK

emp
 │
 ▼

HEAP

Employee Object

----------------------

Id      =1001

Name    =Raman

Salary  =50000

----------------------
```

The object stores the values of the fields.

---

# Problems with Public Fields

Suppose we have

```csharp
class Employee
{
    public double Salary;
}
```

Now

```csharp
Employee emp = new Employee();

emp.Salary = -50000;
```

This is valid.

But can an employee have a negative salary?

No.

Since fields can be directly modified, there is **no validation**.

This is one reason why public fields are generally avoided in production code.

---

# What is a Property?

A **property** provides controlled access to the data stored in a field.

It allows you to:

* Read data
* Write data
* Validate data
* Restrict access

Think of a property as a **gatekeeper** between the object and the outside world.

---

## Property Syntax

```csharp
class Employee
{
    private double salary;

    public double Salary
    {
        get
        {
            return salary;
        }

        set
        {
            salary = value;
        }
    }
}
```

Here,

* `salary` → Field
* `Salary` → Property

---

# Understanding get and set

```csharp
public double Salary
{
    get
    {
        return salary;
    }

    set
    {
        salary = value;
    }
}
```

### get

Returns the value.

```csharp
Console.WriteLine(emp.Salary);
```

Calls

```csharp
get
{
    return salary;
}
```

---

### set

Assigns the value.

```csharp
emp.Salary = 50000;
```

Calls

```csharp
set
{
    salary = value;
}
```

The keyword `value` represents the value being assigned.

---

# Property with Validation

Properties can validate data before storing it.

```csharp
class Employee
{
    private double salary;

    public double Salary
    {
        get
        {
            return salary;
        }

        set
        {
            if (value >= 0)
                salary = value;
            else
                Console.WriteLine("Salary cannot be negative.");
        }
    }
}
```

Using it

```csharp
Employee emp = new Employee();

emp.Salary = -5000;
```

Output

```text
Salary cannot be negative.
```

---

# Field vs Property

| Field           | Property                              |
| --------------- | ------------------------------------- |
| Stores data     | Provides controlled access to data    |
| No validation   | Validation can be added               |
| Variable        | Special member with get/set accessors |
| Usually private | Usually public                        |
| Direct access   | Indirect access                       |

---

# Auto-Implemented Properties

Writing a private field and a property for every member became repetitive.

C# introduced **Auto-Implemented Properties**.

Instead of writing

```csharp
private string name;

public string Name
{
    get
    {
        return name;
    }

    set
    {
        name = value;
    }
}
```

You simply write

```csharp
public string Name
{
    get;
    set;
}
```

The compiler automatically creates a hidden private field.

This hidden field is called a **backing field**.

---

## Example

```csharp
class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public double Salary { get; set; }
}
```

Usage

```csharp
Employee emp = new Employee
{
    Id = 1,
    Name = "Raman",
    Salary = 50000
};
```

This is the most common approach in modern C#.

---

# Read-Only Properties

Sometimes data should be readable but not modifiable after initialization.

Example

```csharp
public class Employee
{
    public int Id { get; }

    public Employee(int id)
    {
        Id = id;
    }
}
```

Using it

```csharp
Employee emp = new Employee(100);

Console.WriteLine(emp.Id);
```

Trying to modify it later

```csharp
emp.Id = 200;
```

Compiler Error

---

## Auto-Property Initializer

A read-only property can also be initialized directly.

```csharp
public class Company
{
    public string Name { get; } = "Microsoft";
}
```

---

# Default Values of Fields

When an object is created, the CLR automatically initializes fields to their default values.

Example

```csharp
class Employee
{
    public int Id;

    public string Name;

    public bool IsActive;

    public double Salary;
}
```

Creating an object

```csharp
Employee emp = new Employee();
```

Memory

```text
Id = 0

Name = null

IsActive = false

Salary = 0
```

---

# Default Values of Common Data Types

| Data Type       | Default Value |
| --------------- | ------------- |
| byte            | 0             |
| short           | 0             |
| int             | 0             |
| long            | 0             |
| float           | 0             |
| double          | 0             |
| decimal         | 0             |
| bool            | false         |
| char            | '\0'          |
| string          | null          |
| object          | null          |
| Reference Types | null          |

---

## Important Note

Only **fields** receive default values automatically.

Local variables do **not**.

Example

```csharp
void Test()
{
    int number;

    Console.WriteLine(number);
}
```

Compiler Error

Local variables must be initialized before use.

---

# Backing Fields

A backing field is a private field that stores the property's value.

Example

```csharp
class Employee
{
    private string name;

    public string Name
    {
        get
        {
            return name;
        }

        set
        {
            name = value;
        }
    }
}
```

Here,

```text
name
```

is called the **Backing Field**.

---

# Why Do We Need Backing Fields?

Suppose every employee name must be uppercase.

```csharp
private string name;

public string Name
{
    get
    {
        return name;
    }

    set
    {
        name = value.ToUpper();
    }
}
```

Using it

```csharp
Employee emp = new Employee();

emp.Name = "Raman";

Console.WriteLine(emp.Name);
```

Output

```text
RAMAN
```

---

# Auto Property vs Backing Field

Auto Property

```csharp
public string Name
{
    get;
    set;
}
```

Compiler creates

```csharp
private string _name;
```

automatically.

---

Manual Backing Field

```csharp
private string name;

public string Name
{
    get
    {
        return name;
    }

    set
    {
        name = value;
    }
}
```

You create the field yourself.

---

# Expression-Bodied Properties

If a property contains only one statement, C# allows a shorter syntax.

Traditional

```csharp
public string FullName
{
    get
    {
        return firstName + " " + lastName;
    }
}
```

Expression-Bodied

```csharp
public string FullName =>
    firstName + " " + lastName;
```

---

Another Example

```csharp
class Circle
{
    public double Radius { get; set; }

    public double Area =>
        Math.PI * Radius * Radius;
}
```

Using it

```csharp
Circle circle = new Circle
{
    Radius = 5
};

Console.WriteLine(circle.Area);
```

Output

```text
78.53981633974483
```

Notice

No separate field is needed for `Area`.

It is calculated every time the property is accessed.

---

# Computed Properties

Expression-bodied properties are often called **computed properties** because their value is calculated rather than stored.

Example

```csharp
class Employee
{
    public string FirstName { get; set; }

    public string LastName { get; set; }

    public string FullName =>
        $"{FirstName} {LastName}";
}
```

Usage

```csharp
Employee emp = new Employee
{
    FirstName = "Raman",
    LastName = "Sharma"
};

Console.WriteLine(emp.FullName);
```

Output

```text
Raman Sharma
```

---

# Fields and Properties in ASP.NET Core

Almost every ASP.NET Core model uses properties.

Example

```csharp
public class Product
{
    public int Id { get; set; }

    public string Name { get; set; }

    public decimal Price { get; set; }
}
```

When a client sends JSON:

```json
{
  "id": 1,
  "name": "Laptop",
  "price": 65000
}
```

ASP.NET Core automatically maps the JSON values to the **properties** of the `Product` class during model binding.

---

# Best Practices

* Prefer **properties** over public fields in production code.
* Keep fields **private** and expose them through properties when validation or business logic is required.
* Use **auto-implemented properties** when no additional logic is needed.
* Use **read-only properties** for values that should not change after object creation.
* Use **backing fields** only when custom logic is required in `get` or `set`.
* Use **expression-bodied properties** for simple computed values.

---

# Summary

| Concept                    | Description                                                                   |
| -------------------------- | ----------------------------------------------------------------------------- |
| Field                      | A variable declared inside a class that stores data.                          |
| Property                   | Provides controlled access to a field using `get` and `set`.                  |
| `get`                      | Returns the property's value.                                                 |
| `set`                      | Assigns a value to the property using the `value` keyword.                    |
| Auto-Implemented Property  | Property where the compiler generates the hidden backing field.               |
| Read-Only Property         | Property that can be assigned only during initialization or in a constructor. |
| Backing Field              | A private field that stores the value used by a property.                     |
| Expression-Bodied Property | A concise property syntax for returning a calculated value.                   |
| Default Field Values       | Fields are automatically initialized by the CLR when an object is created.    |

---

# Interview Questions

### 1. What is the difference between a Field and a Property?

| Field                         | Property                           |
| ----------------------------- | ---------------------------------- |
| Stores data directly          | Controls access to data            |
| Cannot validate automatically | Can perform validation in `set`    |
| Usually private               | Usually public                     |
| Lower-level storage           | Higher-level interface to the data |

---

### 2. Why are properties preferred over public fields?

Properties provide **encapsulation**, allowing you to validate, transform, or restrict access to data without changing the public interface of your class.

---

### 3. What is a Backing Field?

A backing field is a private field used internally by a property to store its value. Auto-implemented properties create a hidden backing field automatically.

---

### 4. What is the default value of a `string` field?

`null`.

---

### 5. Why does this code produce a compiler error?

```csharp
void Test()
{
    int number;
    Console.WriteLine(number);
}
```

Because `number` is a **local variable**, and local variables are **not automatically initialized**. Only fields are initialized to default values by the CLR.
