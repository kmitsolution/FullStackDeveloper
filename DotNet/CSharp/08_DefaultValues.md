# Chapter – Default Values of Class Members in C#

One of the important features of C# is that the **Common Language Runtime (CLR)** automatically initializes **class members (fields)** with default values when an object is created.

This prevents objects from containing random or garbage values.

However, **local variables are not automatically initialized**, which is an important difference every C# developer should understand.

---

# What are Class Members?

A class can contain several types of members:

* Fields
* Properties
* Methods
* Constructors
* Events
* Delegates
* Nested Classes

In this chapter, we focus on **fields** because they receive automatic default values.

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

When an object is created, all these fields are automatically initialized.

---

# Why Does C# Initialize Fields?

Suppose fields were not initialized.

```csharp
Employee emp = new Employee();

Console.WriteLine(emp.Id);
```

If `Id` contained a random memory value, programs would behave unpredictably.

To prevent this, the **CLR initializes every field with a default value** before the constructor executes.

Execution Flow

```text
Create Object

       │

       ▼

Allocate Memory

       │

       ▼

Initialize Fields to Default Values

       │

       ▼

Call Constructor

       │

       ▼

Object Ready
```

---

# Default Values of Value Types

## Numeric Types

All numeric types are initialized to **0**.

Example

```csharp
class Demo
{
    public byte A;

    public short B;

    public int C;

    public long D;

    public float E;

    public double F;

    public decimal G;
}
```

Creating an object

```csharp
Demo demo = new Demo();

Console.WriteLine(demo.A);
Console.WriteLine(demo.B);
Console.WriteLine(demo.C);
Console.WriteLine(demo.D);
Console.WriteLine(demo.E);
Console.WriteLine(demo.F);
Console.WriteLine(demo.G);
```

Output

```text
0
0
0
0
0
0
0
```

---

# Boolean

Boolean fields are initialized to **false**.

Example

```csharp
class Student
{
    public bool IsPassed;
}
```

```csharp
Student student = new Student();

Console.WriteLine(student.IsPassed);
```

Output

```text
False
```

---

# Character

A `char` field is initialized to the null character.

```csharp
class Demo
{
    public char Grade;
}
```

```csharp
Demo demo = new Demo();

Console.WriteLine((int)demo.Grade);
```

Output

```text
0
```

The actual default value is

```text
'\0'
```

which represents the Unicode character with value **0**.

---

# Enum

Enums are initialized to **0**, which corresponds to the first enum value if one is defined with value 0.

Example

```csharp
enum Status
{
    Pending,
    Approved,
    Rejected
}

class Order
{
    public Status OrderStatus;
}
```

Output

```text
Pending
```

---

# Default Values of Reference Types

All reference types are initialized to **null**.

Examples include:

* string
* class
* array
* object
* interface
* delegate

---

## String

```csharp
class Employee
{
    public string Name;
}
```

```csharp
Employee emp = new Employee();

Console.WriteLine(emp.Name == null);
```

Output

```text
True
```

Memory

```text
STACK

emp
 │
 ▼

HEAP

Employee Object

----------------

Name = null

----------------
```

---

## Class Reference

```csharp
class Address
{
}

class Employee
{
    public Address Address;
}
```

```csharp
Employee emp = new Employee();

Console.WriteLine(emp.Address == null);
```

Output

```text
True
```

No `Address` object exists until it is explicitly created.

```csharp
emp.Address = new Address();
```

---

## Array

```csharp
class Demo
{
    public int[] Numbers;
}
```

Output

```csharp
Demo demo = new Demo();

Console.WriteLine(demo.Numbers == null);
```

Output

```text
True
```

---

# Nullable Types

Nullable types allow value types to store `null`.

Syntax

```csharp
int?

double?

bool?
```

Example

```csharp
class Employee
{
    public int? Age;
}
```

Creating an object

```csharp
Employee emp = new Employee();

Console.WriteLine(emp.Age == null);
```

Output

```text
True
```

Memory

```text
Age = null
```

Without nullable types

```csharp
public int Age;
```

Output

```text
0
```

With nullable types

```csharp
public int? Age;
```

Output

```text
null
```

---

# Complete Default Value Table

| Data Type   | Category            | Default Value        |
| ----------- | ------------------- | -------------------- |
| byte        | Value Type          | 0                    |
| short       | Value Type          | 0                    |
| int         | Value Type          | 0                    |
| long        | Value Type          | 0                    |
| float       | Value Type          | 0                    |
| double      | Value Type          | 0                    |
| decimal     | Value Type          | 0                    |
| bool        | Value Type          | false                |
| char        | Value Type          | '\0'                 |
| enum        | Value Type          | 0 (first enum value) |
| DateTime    | Value Type          | 01/01/0001 00:00:00  |
| string      | Reference Type      | null                 |
| object      | Reference Type      | null                 |
| array       | Reference Type      | null                 |
| class       | Reference Type      | null                 |
| interface   | Reference Type      | null                 |
| delegate    | Reference Type      | null                 |
| Nullable<T> | Nullable Value Type | null                 |

---

# Static Fields

Static fields also receive default values.

Example

```csharp
class Employee
{
    public static int Count;
}
```

Output

```csharp
Console.WriteLine(Employee.Count);
```

Output

```text
0
```

---

Another Example

```csharp
class Company
{
    public static string Name;
}
```

Output

```csharp
Console.WriteLine(Company.Name == null);
```

Output

```text
True
```

---

# Instance Fields vs Static Fields

```csharp
class Employee
{
    public int Id;

    public static int Count;
}
```

Memory

```text
Employee Class

-------------------------

Static Field

Count = 0

-------------------------

Object 1

Id = 0

-------------------------

Object 2

Id = 0

-------------------------
```

Notice

* Every object gets its own copy of **instance fields**.
* Static fields belong to the **class**, not to individual objects.

---

# Local Variables

A **local variable** is declared inside a method.

Example

```csharp
void Display()
{
    int number = 10;
}
```

Unlike fields, **local variables are NOT initialized automatically**.

---

## Example

```csharp
class Program
{
    static void Main()
    {
        int number;

        Console.WriteLine(number);
    }
}
```

Compiler Error

```text
Use of unassigned local variable 'number'
```

Why?

The compiler forces you to initialize local variables before using them.

Correct

```csharp
int number = 0;

Console.WriteLine(number);
```

Output

```text
0
```

---

# Why Doesn't the Compiler Initialize Local Variables?

Suppose a method contains hundreds of local variables.

Automatically initializing every local variable would waste CPU cycles and memory.

Instead, C# requires the programmer to explicitly initialize them.

This helps:

* Improve performance.
* Prevent accidental use of unintended values.
* Make the code clearer and more predictable.

---

# Fields vs Local Variables

| Feature        | Field                        | Local Variable               |
| -------------- | ---------------------------- | ---------------------------- |
| Declared       | Inside a class               | Inside a method              |
| Default Value  | Yes                          | No                           |
| Initialized by | CLR                          | Programmer                   |
| Scope          | Entire class                 | Method only                  |
| Lifetime       | As long as the object exists | Only during method execution |

---

# Memory Representation

```csharp
class Employee
{
    public int Id;
}

Employee emp = new Employee();
```

Memory

```text
STACK

emp
 │
 ▼

HEAP

Employee Object

----------------

Id = 0

----------------
```

---

Local Variable

```csharp
void Test()
{
    int number;
}
```

Memory

```text
STACK

number

(No value assigned)
```

The compiler does not allow it to be used until it is initialized.

---

# Example Combining Fields and Local Variables

```csharp
class Student
{
    public int Marks;

    public bool Passed;

    static void Main()
    {
        Student student = new Student();

        Console.WriteLine(student.Marks);

        Console.WriteLine(student.Passed);

        int total = 100;

        Console.WriteLine(total);
    }
}
```

Output

```text
0
False
100
```

If `total` were not initialized

```csharp
int total;

Console.WriteLine(total);
```

Compiler Error

---

# Fields in ASP.NET Core

Consider a model

```csharp
public class Product
{
    public int Id;

    public string Name;

    public decimal Price;
}
```

Creating an object

```csharp
Product product = new Product();
```

Memory

```text
Id = 0

Name = null

Price = 0
```

When a client sends data through an HTTP request, ASP.NET Core's **model binding** populates these values from the request.

---

# Best Practices

* Do not rely on default values to represent valid business data; initialize members with meaningful values where appropriate.
* Prefer **properties** over public fields in production code.
* Always initialize local variables before using them.
* Use nullable types (`int?`, `DateTime?`, etc.) when the absence of a value is meaningful.
* Understand the distinction between instance fields and static fields.
* Remember that reference-type fields start as `null`, so check for `null` before accessing their members.

---

# Summary

| Member          | Default Value                                        |
| --------------- | ---------------------------------------------------- |
| Numeric Types   | `0`                                                  |
| `bool`          | `false`                                              |
| `char`          | `'\0'`                                               |
| `enum`          | `0` (first enum value)                               |
| `string`        | `null`                                               |
| Reference Types | `null`                                               |
| Nullable Types  | `null`                                               |
| Static Fields   | Initialized to their type's default value            |
| Local Variables | **No default value**; must be initialized before use |

---

# Interview Questions

### 1. Who initializes the default values of fields?

The **Common Language Runtime (CLR)** initializes all fields to their default values before the constructor is executed.

---

### 2. Why do local variables not have default values?

Local variables are **not initialized automatically** to improve performance and to prevent the accidental use of uninitialized data. The compiler requires the programmer to assign a value before using them.

---

### 3. What is the default value of a `string` field?

`null`

---

### 4. What is the default value of a `bool` field?

`false`

---

### 5. What is the default value of an `int?` (nullable integer)?

`null`

---

### 6. What is the difference between the default value of `int` and `int?`?

| Type   | Default Value |
| ------ | ------------- |
| `int`  | `0`           |
| `int?` | `null`        |

This distinction is especially important in ASP.NET Core applications when modeling optional values, such as optional dates, ages, or foreign keys in Entity Framework Core.
