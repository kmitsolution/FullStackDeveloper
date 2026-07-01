# Methods in C#

## What is a Method?

A **method** is a block of code that performs a specific task. It is also commonly referred to as a **function** in many programming languages.

Instead of writing the same code multiple times, you can place it inside a method and call it whenever needed.

### Why Do We Need Methods?

Suppose you want to calculate the sum of two numbers in different parts of your program.

Without methods:

```csharp
int a = 10;
int b = 20;
Console.WriteLine(a + b);

int x = 50;
int y = 30;
Console.WriteLine(x + y);

int p = 100;
int q = 200;
Console.WriteLine(p + q);
```

Notice that the same logic is repeated.

Using a method:

```csharp
Add(10, 20);
Add(50, 30);
Add(100, 200);
```

The code becomes cleaner, reusable, and easier to maintain.

---

# Advantages of Methods

* Code Reusability
* Better Readability
* Easier Maintenance
* Reduces Code Duplication
* Easier Testing
* Modular Programming

---

# Method Terminology

A method has four important parts:

```text
Method Declaration

↓

Method Definition

↓

Method Call (Invocation)

↓

Method Execution
```

---

# Syntax of a Method

```csharp
access_modifier return_type MethodName(parameters)
{
    // Method Body
}
```

Example

```csharp
public void Display()
{
    Console.WriteLine("Welcome to C#");
}
```

---

# Understanding Each Part

```csharp
public void Display()
{
    Console.WriteLine("Welcome");
}
```

| Part    | Meaning         |
| ------- | --------------- |
| public  | Access Modifier |
| void    | Return Type     |
| Display | Method Name     |
| ()      | Parameter List  |
| {}      | Method Body     |

---

# Method Declaration and Method Call

Example

```csharp
class Program
{
    static void Main(string[] args)
    {
        Display();
    }

    static void Display()
    {
        Console.WriteLine("Hello World");
    }
}
```

Output

```text
Hello World
```

Execution Flow

```text
Main()

↓

Display()

↓

Print Hello World

↓

Return to Main()
```

---

# Method Without Parameters and Without Return Value

This is the simplest type of method.

```csharp
class Program
{
    static void Welcome()
    {
        Console.WriteLine("Welcome to .NET");
    }

    static void Main()
    {
        Welcome();
    }
}
```

Output

```text
Welcome to .NET
```

---

# Method With Parameters

Parameters allow us to pass data into a method.

Example

```csharp
static void Greet(string name)
{
    Console.WriteLine($"Welcome {name}");
}

static void Main()
{
    Greet("Raman");
}
```

Output

```text
Welcome Raman
```

---

# Multiple Parameters

```csharp
static void Add(int a, int b)
{
    Console.WriteLine(a + b);
}

static void Main()
{
    Add(10,20);
}
```

Output

```text
30
```

---

# What are Parameters?

Parameters are variables declared in the method definition.

```csharp
static void Add(int a, int b)
```

Here,

* `a`
* `b`

are called **Parameters**.

---

# What are Arguments?

Arguments are the actual values passed to a method.

```csharp
Add(10,20);
```

Here,

* 10
* 20

are called **Arguments**.

---

# Return Type

A return type specifies what value a method sends back to the caller.

Example

```csharp
static int Add(int a, int b)
{
    return a + b;
}
```

Calling

```csharp
int result = Add(10,20);

Console.WriteLine(result);
```

Output

```text
30
```

---

# void Return Type

When a method does not return anything, use `void`.

Example

```csharp
static void Display()
{
    Console.WriteLine("Hello");
}
```

---

# Common Return Types

| Return Type    | Returns                        |
| -------------- | ------------------------------ |
| void           | Nothing                        |
| int            | Integer                        |
| double         | Decimal Number                 |
| bool           | True/False                     |
| string         | Text                           |
| char           | Character                      |
| object         | Any Object                     |
| Employee       | Custom Object                  |
| List<Employee> | Collection                     |
| Task           | Async Method                   |
| Task<T>        | Async Method Returning a Value |

---

# Method Returning a String

```csharp
static string GetName()
{
    return "Raman";
}

static void Main()
{
    string name = GetName();

    Console.WriteLine(name);
}
```

Output

```text
Raman
```

---

# Method Returning Boolean

```csharp
static bool IsEven(int number)
{
    return number % 2 == 0;
}

static void Main()
{
    Console.WriteLine(IsEven(20));
}
```

Output

```text
True
```

---

# Optional Parameters

Optional parameters allow a caller to omit one or more arguments. If omitted, the method uses the default value specified in the parameter list.

### Syntax

```csharp
method(type parameter = defaultValue)
```

---

## Example 1

```csharp
static void Display(string name = "Guest")
{
    Console.WriteLine(name);
}

static void Main()
{
    Display();
    Display("Raman");
}
```

Output

```text
Guest
Raman
```

---

## Example 2

```csharp
static void Add(int a, int b = 10)
{
    Console.WriteLine(a + b);
}

static void Main()
{
    Add(5);
    Add(5,20);
}
```

Output

```text
15
25
```

---

# Rules for Optional Parameters

1. Optional parameters must come **after required parameters**.

Correct

```csharp
static void Display(string name, int age = 18)
{
}
```

Incorrect

```csharp
static void Display(int age = 18, string name)
{
}
```

Compiler Error

---

# Named Arguments

Named arguments improve readability and allow parameters to be passed in any order.

Example

```csharp
static void Student(string name, int age)
{
    Console.WriteLine($"{name} - {age}");
}

static void Main()
{
    Student(age:25, name:"Raman");
}
```

Output

```text
Raman - 25
```

---

# Method Overloading

Method overloading means having multiple methods with the same name but different parameter lists.

Example

```csharp
static int Add(int a, int b)
{
    return a + b;
}

static int Add(int a, int b, int c)
{
    return a + b + c;
}
```

Calling

```csharp
Console.WriteLine(Add(10,20));

Console.WriteLine(Add(10,20,30));
```

Output

```text
30
60
```

The compiler selects the correct method based on the number and type of arguments.

---

# Expression-Bodied Methods

For simple methods, C# allows a shorter syntax.

Traditional

```csharp
static int Square(int number)
{
    return number * number;
}
```

Expression-bodied

```csharp
static int Square(int number) => number * number;
```

Both produce the same result.

---

# Recursive Methods

A recursive method is a method that calls itself.

Example: Factorial

```csharp
static int Factorial(int number)
{
    if (number == 1)
        return 1;

    return number * Factorial(number - 1);
}

static void Main()
{
    Console.WriteLine(Factorial(5));
}
```

Output

```text
120
```

Execution

```text
Factorial(5)

↓

5 × Factorial(4)

↓

4 × Factorial(3)

↓

3 × Factorial(2)

↓

2 × Factorial(1)

↓

1
```

---

# Passing Parameters by Value (Default Behavior)

By default, C# passes parameters **by value**, meaning the method receives a copy of the variable.

```csharp
static void Increment(int number)
{
    number++;
}

static void Main()
{
    int value = 10;

    Increment(value);

    Console.WriteLine(value);
}
```

Output

```text
10
```

The original variable is not modified.

---

# Passing Parameters by Reference (`ref`)

The `ref` keyword allows a method to modify the original variable.

```csharp
static void Increment(ref int number)
{
    number++;
}

static void Main()
{
    int value = 10;

    Increment(ref value);

    Console.WriteLine(value);
}
```

Output

```text
11
```

---

# Output Parameters (`out`)

The `out` keyword allows a method to return multiple values.

```csharp
static void Divide(int a, int b, out int quotient, out int remainder)
{
    quotient = a / b;
    remainder = a % b;
}

static void Main()
{
    Divide(10, 3, out int q, out int r);

    Console.WriteLine($"Quotient = {q}");
    Console.WriteLine($"Remainder = {r}");
}
```

Output

```text
Quotient = 3
Remainder = 1
```

---

# Params Keyword

The `params` keyword allows a method to accept a variable number of arguments.

```csharp
static int Sum(params int[] numbers)
{
    int total = 0;

    foreach (int number in numbers)
    {
        total += number;
    }

    return total;
}

static void Main()
{
    Console.WriteLine(Sum(10,20));
    Console.WriteLine(Sum(10,20,30,40));
}
```

Output

```text
30
100
```

---

# Methods in ASP.NET Core

Methods are used everywhere in ASP.NET Core.

Controller Action

```csharp
public IActionResult Index()
{
    return View();
}
```

Service Method

```csharp
public List<Employee> GetEmployees()
{
    return _repository.GetAll();
}
```

Web API Method

```csharp
[HttpGet]
public IActionResult GetProducts()
{
    return Ok(products);
}
```

Repository Method

```csharp
public Employee GetById(int id)
{
    return _context.Employees.Find(id);
}
```

---

# Best Practices

* Give methods meaningful names (`CalculateSalary()` instead of `Calc()`).
* Keep methods focused on a single responsibility.
* Avoid very long methods; break complex logic into smaller methods.
* Use return values instead of global variables whenever possible.
* Use optional parameters only when default values make sense.
* Prefer named arguments when calling methods with many optional parameters.
* Use `ref` and `out` sparingly, as they make code harder to understand.
* Use `params` when the number of arguments can vary.

---

# Summary

| Concept             | Description                                                       |
| ------------------- | ----------------------------------------------------------------- |
| Method / Function   | A reusable block of code that performs a specific task            |
| Parameters          | Variables declared in the method definition                       |
| Arguments           | Actual values passed to a method                                  |
| Return Type         | Specifies the type of value returned by the method                |
| `void`              | Indicates that the method does not return a value                 |
| Optional Parameters | Parameters with default values that callers may omit              |
| Named Arguments     | Arguments passed by parameter name rather than position           |
| Method Overloading  | Multiple methods with the same name but different parameter lists |
| Recursion           | A method calling itself                                           |
| `ref`               | Passes a variable by reference, allowing it to be modified        |
| `out`               | Returns additional values through parameters                      |
| `params`            | Allows a variable number of arguments                             |

## Interview Questions

**1. What is the difference between a parameter and an argument?**

* **Parameter:** A variable defined in the method signature.
* **Argument:** The actual value passed when calling the method.

---

**2. What is the difference between `void` and `int` as a return type?**

* `void` means the method does not return any value.
* `int` means the method returns an integer value.

---

**3. What is the difference between `ref` and `out`?**

| `ref`                                       | `out`                                                   |
| ------------------------------------------- | ------------------------------------------------------- |
| Variable must be initialized before passing | Variable does not need to be initialized before passing |
| Used to modify an existing value            | Used to return one or more values from a method         |

These method concepts form the foundation for learning **classes**, **objects**, **constructors**, and later **ASP.NET Core MVC controllers**, **Web API actions**, and **dependency injection**, where almost every operation is implemented as a method.
