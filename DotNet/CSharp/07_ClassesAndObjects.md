# Chapter – Classes and Objects in C#

Classes and Objects are the **foundation of Object-Oriented Programming (OOP)**. Almost everything you build in ASP.NET Core (Controllers, Services, Models, DbContext, Middleware, etc.) is based on classes and objects.

---

# What is Object-Oriented Programming (OOP)?

Object-Oriented Programming is a programming paradigm that organizes software around **objects** rather than functions.

Instead of writing one large program, OOP divides the application into smaller reusable components called **objects**.

Everything in an ASP.NET Core application revolves around objects.

Example:

* Controller → Object
* Service → Object
* Repository → Object
* Model → Object
* DbContext → Object

---

# What is a Class?

A **class** is a blueprint or template used to create objects.

It defines:

* Data (Fields / Properties)
* Behavior (Methods)

A class itself does **not** occupy memory for its instance members until an object is created.

## Real World Analogy

Think about building a house.

Before constructing a house, an architect prepares a **blueprint**.

The blueprint contains:

* Number of rooms
* Kitchen
* Windows
* Doors

But you cannot live inside the blueprint.

Similarly,

A **class** is only a blueprint.

---

Example

```csharp
class Employee
{
    public int Id;
    public string Name;
}
```

Here,

Employee is only a blueprint.

No memory has been allocated yet.

---

# What is an Object?

An **object** is a real instance of a class.

Objects occupy memory.

Objects contain actual values.

Example

```csharp
Employee emp = new Employee();
```

Now an Employee object exists in memory.

---

Real World Example

Blueprint

↓

House

Similarly

Class

↓

Object

---

# Class vs Object

| Class                          | Object                 |
| ------------------------------ | ---------------------- |
| Blueprint                      | Instance of class      |
| Logical entity                 | Physical entity        |
| No memory for instance members | Occupies memory        |
| Created once                   | Can create many        |
| Defines properties and methods | Contains actual values |

---

# Declaring a Class

Syntax

```csharp
class ClassName
{
    // Members
}
```

Example

```csharp
class Student
{
    public int RollNo;

    public string Name;

    public void Display()
    {
        Console.WriteLine($"{RollNo} {Name}");
    }
}
```

---

# Class Members

A class can contain:

* Fields
* Properties
* Methods
* Constructors
* Destructor
* Events
* Delegates
* Nested Classes

Example

```csharp
class Employee
{
    // Field
    public int Id;

    // Property
    public string Name { get; set; }

    // Method
    public void Display()
    {
    }

    // Constructor
    public Employee()
    {
    }
}
```

---

# Creating Objects

Objects are created using the **new** keyword.

Syntax

```csharp
ClassName objectName = new ClassName();
```

Example

```csharp
Employee emp = new Employee();
```

Breaking it down

```text
Employee
```

Data type

```text
emp
```

Reference variable

```text
new Employee()
```

Creates the object in memory

---

# What happens internally?

When

```csharp
Employee emp = new Employee();
```

is executed,

.NET performs the following steps.

Step 1

Allocate memory on the Heap.

↓

Step 2

Initialize all fields with default values.

↓

Step 3

Call the Constructor.

↓

Step 4

Return the reference.

↓

Step 5

Store the reference in emp.

---

Memory Representation

```text
STACK                         HEAP

emp --------------->      Employee Object

                           Id = 0

                           Name = null
```

Notice

Only the **reference** is stored on the Stack.

The object lives on the Heap.

---

# Accessing Members

Members are accessed using the **dot operator (.)**

Example

```csharp
Employee emp = new Employee();

emp.Id = 101;

emp.Name = "Raman";

Console.WriteLine(emp.Name);
```

Output

```
Raman
```

---

Memory After Assignment

```text
STACK

emp

│

▼

HEAP

Employee

Id = 101

Name = Raman
```

---

# Creating Multiple Objects

A class can create any number of objects.

Example

```csharp
Employee emp1 = new Employee();

Employee emp2 = new Employee();

Employee emp3 = new Employee();
```

Memory

```text
STACK

emp1 ----┐

emp2 ----┼------>

emp3 ----┘

HEAP

Employee Object 1

Employee Object 2

Employee Object 3
```

Each object has its own copy of instance data.

---

Example

```csharp
emp1.Name = "Raman";

emp2.Name = "Microsoft";

emp3.Name = "John";
```

Output

```
Raman

Microsoft

John
```

---

# Real World Example – Employee

```csharp
class Employee
{
    public int Id;

    public string Name;

    public double Salary;

    public void Display()
    {
        Console.WriteLine($"{Id} {Name} {Salary}");
    }
}
```

Creating Objects

```csharp
Employee emp1 = new Employee();

emp1.Id = 1;
emp1.Name = "Raman";
emp1.Salary = 50000;

Employee emp2 = new Employee();

emp2.Id = 2;
emp2.Name = "John";
emp2.Salary = 70000;

emp1.Display();

emp2.Display();
```

Output

```
1 Raman 50000

2 John 70000
```

---

# Real World Example – Bank Account

```csharp
class BankAccount
{
    public string AccountNo;

    public string HolderName;

    public double Balance;

    public void Deposit(double amount)
    {
        Balance += amount;
    }

    public void Display()
    {
        Console.WriteLine($"{HolderName} {Balance}");
    }
}
```

Creating Objects

```csharp
BankAccount account = new BankAccount();

account.AccountNo = "SB1001";

account.HolderName = "Raman";

account.Deposit(5000);

account.Display();
```

Output

```
Raman 5000
```

---

# Reference Assignment Behavior

This is one of the most important interview questions.

Suppose

```csharp
Employee emp1 = new Employee();

emp1.Name = "Raman";
```

Now

```csharp
Employee emp2 = emp1;
```

Question

How many objects exist?

Only **one**.

Memory

```text
STACK

emp1 ----┐

          │

emp2 ----┘

          │

          ▼

HEAP

Employee

Name = Raman
```

Both variables point to the same object.

---

Now

```csharp
emp2.Name = "Microsoft";
```

Output

```csharp
Console.WriteLine(emp1.Name);
```

Output

```
Microsoft
```

Why?

Because there is only **one object**.

Both variables reference the same object.

---

# Creating a Separate Object

```csharp
Employee emp1 = new Employee();

Employee emp2 = new Employee();
```

Memory

```text
STACK

emp1 ------>

Employee Object 1

emp2 ------>

Employee Object 2
```

Now changing one object does not affect the other.

---

# Object Initialization

Instead of writing

```csharp
Employee emp = new Employee();

emp.Id = 1;

emp.Name = "Raman";
```

we can initialize the object directly.

```csharp
Employee emp = new Employee()
{
    Id = 1,

    Name = "Raman"
};
```

This is called **Object Initialization**.

---

Another Example

```csharp
Student student = new Student()
{
    RollNo = 101,

    Name = "John"
};
```

This is cleaner and commonly used in ASP.NET Core.

---

# Anonymous Objects

Sometimes we need an object only temporarily.

Instead of creating a class,

```csharp
var employee = new
{
    Id = 1,

    Name = "Raman",

    Salary = 50000
};
```

Notice

No class has been created.

The compiler automatically creates one.

---

Accessing Anonymous Object

```csharp
Console.WriteLine(employee.Name);
```

Output

```
Raman
```

---

Limitations

Anonymous objects

* Cannot add new properties later.
* Property values cannot be reassigned because properties are read-only.
* Useful for LINQ projections and returning temporary data.

---

# Object Initializer Syntax

Normal

```csharp
Employee emp = new Employee();

emp.Id = 10;

emp.Name = "Raman";
```

Object Initializer

```csharp
Employee emp = new Employee
{
    Id = 10,

    Name = "Raman"
};
```

Another Example

```csharp
Student student = new Student
{
    RollNo = 100,

    Name = "John"
};
```

---

# Memory Representation of Object Initialization

```text
STACK

student

│

▼

HEAP

Student

RollNo =100

Name = John
```

---

# How ASP.NET Core Uses Objects

When a request comes to a controller:

```csharp
public class EmployeeController : Controller
{
}
```

ASP.NET Core creates an object of the controller.

Similarly,

```csharp
EmployeeService service = new EmployeeService();
```

Object created.

Repository

```csharp
EmployeeRepository repository = new EmployeeRepository();
```

Object created.

Entity Framework

```csharp
ApplicationDbContext context = new ApplicationDbContext();
```

Object created.

Almost everything in ASP.NET Core is an object created by the framework's **Dependency Injection (DI) container**.

---

# Best Practices

* Keep classes focused on a single responsibility.
* Use meaningful class names such as `Employee`, `Customer`, or `Order`.
* Prefer **properties** over public fields in production code.
* Use object initializer syntax for cleaner object creation.
* Understand that reference variables store addresses, not the objects themselves.
* Be cautious when assigning one object reference to another, as both variables will refer to the same object.

---

# Summary

| Concept            | Description                                                                                           |
| ------------------ | ----------------------------------------------------------------------------------------------------- |
| Class              | A blueprint or template for creating objects.                                                         |
| Object             | An instance of a class that occupies memory.                                                          |
| `new` Keyword      | Allocates memory, initializes fields, invokes the constructor, and returns a reference to the object. |
| Reference Variable | Holds the address of an object in memory.                                                             |
| Heap               | Stores objects and their data.                                                                        |
| Stack              | Stores local variables and object references.                                                         |
| Dot (`.`) Operator | Used to access object members (fields, properties, methods).                                          |
| Object Initializer | Initializes object properties at the time of creation.                                                |
| Anonymous Object   | A compiler-generated object without an explicitly defined class, useful for temporary data.           |

---

# Interview Questions

### 1. What is the difference between a Class and an Object?

| Class                        | Object                    |
| ---------------------------- | ------------------------- |
| Blueprint                    | Instance of a class       |
| Does not store instance data | Stores actual data        |
| Logical definition           | Physical entity in memory |
| Defined once                 | Can have many instances   |

---

### 2. Where is an object stored?

Objects are allocated on the **managed heap**, while the reference to the object is typically stored in a local variable on the **stack** (or as a field inside another object).

---

### 3. What does the `new` keyword do?

When you write:

```csharp
Employee emp = new Employee();
```

the `new` keyword performs four main actions:

1. Allocates memory on the managed heap.
2. Initializes fields to their default values.
3. Calls the class constructor.
4. Returns a reference to the newly created object.

---

### 4. What happens when you execute `Employee emp2 = emp1;`?

No new object is created. The reference stored in `emp1` is copied to `emp2`, so both variables point to the same object. Changes made through one reference are visible through the other until one of them points to a different object.
