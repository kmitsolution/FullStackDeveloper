# Chapter 5 – Constructors in C#

## What is a Constructor?

A **constructor** is a special method in a class that is **automatically executed when an object is created**. Its primary purpose is to initialize the object's data or perform setup tasks.

Unlike regular methods, constructors do not have a return type and have the same name as the class.

### Syntax

```csharp
class Employee
{
    public Employee()
    {
        Console.WriteLine("Constructor Executed");
    }
}
```

Creating an object:

```csharp
Employee emp = new Employee();
```

Output:

```text
Constructor Executed
```

---

# Why Do We Need Constructors?

Constructors are used to:

* Initialize object data.
* Assign default values.
* Validate input.
* Allocate resources.
* Ensure an object starts in a valid state.

Example:

```csharp
class Employee
{
    public string Name;

    public Employee()
    {
        Name = "Unknown";
    }
}
```

---

# Characteristics of Constructors

* Constructor name must be the same as the class name.
* Constructors do not have a return type (not even `void`).
* Constructors are called automatically when an object is created.
* A class can have multiple constructors (Constructor Overloading).
* Constructors can have access modifiers (`public`, `private`, etc.).
* Constructors cannot be inherited but can be called using `base()`.

---

# Default Constructor

A **default constructor** is a constructor with no parameters.

```csharp
class Student
{
    public Student()
    {
        Console.WriteLine("Default Constructor");
    }
}

Student student = new Student();
```

Output:

```text
Default Constructor
```

If you do not define any constructor, the C# compiler automatically provides a parameterless default constructor.

---

# Parameterized Constructor

A parameterized constructor accepts values during object creation.

```csharp
class Employee
{
    public int Id;
    public string Name;

    public Employee(int id, string name)
    {
        Id = id;
        Name = name;
    }
}

Employee emp = new Employee(101, "Raman");

Console.WriteLine($"{emp.Id} {emp.Name}");
```

Output:

```text
101 Raman
```

Parameterized constructors are commonly used to initialize objects with required values.

---

# Constructor Overloading

A class can have multiple constructors with different parameter lists.

```csharp
class Employee
{
    public Employee()
    {
        Console.WriteLine("Default Constructor");
    }

    public Employee(string name)
    {
        Console.WriteLine($"Employee: {name}");
    }

    public Employee(int id, string name)
    {
        Console.WriteLine($"{id} {name}");
    }
}
```

Usage:

```csharp
Employee emp1 = new Employee();
Employee emp2 = new Employee("Raman");
Employee emp3 = new Employee(101, "Raman");
```

Output:

```text
Default Constructor
Employee: Raman
101 Raman
```

---

# Constructor Chaining (`this`)

Constructor chaining allows one constructor to call another constructor in the same class using the `this` keyword.

```csharp
class Employee
{
    public Employee()
        : this("Unknown")
    {
    }

    public Employee(string name)
    {
        Console.WriteLine(name);
    }
}
```

Usage:

```csharp
Employee emp = new Employee();
```

Output:

```text
Unknown
```

### Why use `this()`?

* Avoid duplicate code.
* Centralize initialization logic.
* Improve maintainability.

---

# Base Constructor (`base`)

The `base` keyword is used to call the constructor of the parent (base) class.

```csharp
class Person
{
    public Person(string name)
    {
        Console.WriteLine($"Person: {name}");
    }
}

class Employee : Person
{
    public Employee(string name)
        : base(name)
    {
        Console.WriteLine("Employee Constructor");
    }
}
```

Usage:

```csharp
Employee emp = new Employee("Raman");
```

Output:

```text
Person: Raman
Employee Constructor
```

The base class constructor always executes before the derived class constructor.

---

# Static Constructor

A static constructor initializes static members of a class.

Characteristics:

* Has no parameters.
* Cannot have an access modifier.
* Executes only once during the application's lifetime.
* Called automatically before the first object is created or before any static member is accessed.

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

Usage:

```csharp
Employee emp1 = new Employee();
Employee emp2 = new Employee();
```

Output:

```text
Static Constructor
Instance Constructor
Instance Constructor
```

Notice that the static constructor runs only once.

---

# Private Constructor

A private constructor prevents objects from being created outside the class.

```csharp
class Utility
{
    private Utility()
    {
    }

    public static void Display()
    {
        Console.WriteLine("Utility Method");
    }
}
```

Usage:

```csharp
Utility.Display();
```

The following is not allowed:

```csharp
Utility obj = new Utility();
```

Compiler Error

Private constructors are commonly used in:

* Utility classes
* Singleton Pattern
* Static helper classes

---

# Constructor Execution Order

When inheritance is involved, constructors execute from the top of the inheritance hierarchy to the bottom.

Example:

```csharp
class A
{
    public A()
    {
        Console.WriteLine("A");
    }
}

class B : A
{
    public B()
    {
        Console.WriteLine("B");
    }
}

class C : B
{
    public C()
    {
        Console.WriteLine("C");
    }
}
```

Usage:

```csharp
C obj = new C();
```

Output:

```text
A
B
C
```

Execution Order:

```text
Base Class Constructor

↓

Derived Class Constructor

↓

Current Class Constructor
```

---

# Object Initialization Process

When the statement:

```csharp
Employee emp = new Employee();
```

is executed, the following steps occur:

```text
1. Memory is allocated on the Heap.

        ↓

2. Fields are initialized to default values.

        ↓

3. Base class constructor executes.

        ↓

4. Current class constructor executes.

        ↓

5. Constructor body initializes the object.

        ↓

6. Reference is returned and stored in the variable.
```

Memory Representation:

```text
STACK

emp
 │
 ▼

HEAP

-----------------------
Employee Object
-----------------------
Id = 0
Name = null
-----------------------

↓

Constructor Executes

↓

-----------------------
Id = 101
Name = Raman
-----------------------
```

---

# Constructors in ASP.NET Core

Constructors are heavily used in **Dependency Injection (DI)**.

Example:

```csharp
public class EmployeeService
{
    private readonly ILogger<EmployeeService> _logger;

    public EmployeeService(ILogger<EmployeeService> logger)
    {
        _logger = logger;
    }
}
```

The ASP.NET Core DI container automatically calls the constructor and injects the required dependencies.

Controllers also use constructor injection:

```csharp
public class EmployeeController : Controller
{
    private readonly EmployeeService _service;

    public EmployeeController(EmployeeService service)
    {
        _service = service;
    }
}
```

---

# Best Practices

* Use constructors to initialize objects.
* Keep constructors simple; avoid placing complex business logic inside them.
* Use constructor overloading when multiple initialization options are required.
* Use constructor chaining (`this`) to eliminate duplicate initialization code.
* Use `base()` when the parent class requires initialization.
* Use static constructors only for initializing static data.
* Use private constructors for utility or singleton classes.

---

# Summary

| Constructor Type              | Description                                               |
| ----------------------------- | --------------------------------------------------------- |
| Default Constructor           | No parameters; initializes an object with default values. |
| Parameterized Constructor     | Accepts values during object creation.                    |
| Constructor Overloading       | Multiple constructors with different parameter lists.     |
| Constructor Chaining (`this`) | Calls another constructor within the same class.          |
| Base Constructor (`base`)     | Calls the constructor of the parent class.                |
| Static Constructor            | Initializes static members and executes only once.        |
| Private Constructor           | Prevents object creation from outside the class.          |

---

# Interview Questions

### 1. What is a constructor?

A constructor is a special member of a class that is automatically executed when an object is created and is used to initialize the object's state.

---

### 2. Can a constructor return a value?

**No.** Constructors do not have a return type, not even `void`.

---

### 3. Can a class have multiple constructors?

**Yes.** This is called **Constructor Overloading**.

---

### 4. What is the difference between `this()` and `base()`?

| `this()`                                     | `base()`                                          |
| -------------------------------------------- | ------------------------------------------------- |
| Calls another constructor in the same class. | Calls the constructor of the base (parent) class. |

---

### 5. How many times does a static constructor execute?

Only **once** during the lifetime of the application.

---

### 6. What is the order of constructor execution in inheritance?

1. Base class constructor.
2. Derived class constructor.
3. Current class constructor.

---

### 7. What is the primary use of constructors in ASP.NET Core?

Constructors are primarily used for **Dependency Injection (DI)**, allowing the framework to automatically provide required services such as repositories, loggers, database contexts, and configuration objects.
