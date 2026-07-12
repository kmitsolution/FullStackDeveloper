# Chapter – Delegates in C#

## What is a Delegate?

A **Delegate** is a type-safe function pointer.

A delegate holds a reference to one or more methods and allows those methods to be invoked indirectly.

Think of a delegate as a variable that can store the address of a method.

---

## Why Do We Need Delegates?

Delegates are used when:

* Passing methods as parameters
* Implementing callbacks
* Implementing events
* Writing loosely coupled code
* LINQ and Lambda Expressions
* Asynchronous programming (`Task`, `async/await`)

Delegates are heavily used throughout ASP.NET Core and .NET.

---

# Real Life Example

Imagine a remote control.

```text
Remote Button

↓

TV Function Executes
```

The remote does not know how the TV works internally. It simply calls the appropriate function.

Similarly:

```text
Delegate

↓

Method Executes
```

---

# Without Delegate

```csharp id="q40p0x"
class Calculator
{
    public static void Add(int a, int b)
    {
        Console.WriteLine(a + b);
    }
}

Calculator.Add(10,20);
```

---

# With Delegate

```csharp id="drm0b3"
delegate void Calculate(int a, int b);

class Calculator
{
    public static void Add(int a, int b)
    {
        Console.WriteLine(a + b);
    }
}
```

Assigning method:

```csharp id="zc7qou"
Calculate calculate = Calculator.Add;

calculate(10,20);
```

Output

```text id="x1tb6r"
30
```

---

# Delegate Syntax

```csharp id="2x71f0"
delegate returnType DelegateName(parameters);
```

Example

```csharp id="r7e5nt"
delegate void MyDelegate();
```

---

# Steps to Use Delegates

### Step 1 – Declare Delegate

```csharp id="fgrmhv"
delegate void MyDelegate();
```

---

### Step 2 – Create Matching Method

```csharp id="ol6o0m"
static void Display()
{
    Console.WriteLine("Hello");
}
```

---

### Step 3 – Create Delegate Object

```csharp id="k38vvh"
MyDelegate del = Display;
```

---

### Step 4 – Invoke Delegate

```csharp id="j5f4qs"
del();
```

Output

```text id="hh2ucd"
Hello
```

---

# Memory Representation

```text
STACK

del

↓

Method Address

↓

Display()
```

The delegate stores the method reference.

---

# Delegate Signature Rule

A delegate can only point to methods having the same:

* Return Type
* Number of Parameters
* Parameter Types

Example

```csharp id="u6c4u7"
delegate int Calculation(int x, int y);
```

Valid

```csharp id="4r5gl0"
static int Add(int a, int b)
{
    return a + b;
}
```

Invalid

```csharp id="p0a5r6"
static void Add(int a, int b)
{
}
```

Compiler Error.

---

# Delegate Example

```csharp id="5g2x1r"
delegate int Calculation(int x, int y);

class Program
{
    static int Add(int a, int b)
    {
        return a + b;
    }

    static void Main()
    {
        Calculation del = Add;

        Console.WriteLine(del(10,20));
    }
}
```

Output

```text id="n1x5g3"
30
```

---

# Passing Delegate as Parameter

Delegates can be passed as parameters.

Example

```csharp id="n6n4aq"
delegate int Operation(int a, int b);

class Calculator
{
    public static int Add(int a, int b)
    {
        return a + b;
    }

    public static int Multiply(int a, int b)
    {
        return a * b;
    }
}
```

Method

```csharp id="zt3vsk"
static void Execute(Operation operation)
{
    Console.WriteLine(operation(10,20));
}
```

Usage

```csharp id="17rjlwm"
Execute(Calculator.Add);

Execute(Calculator.Multiply);
```

Output

```text id="qspm5t"
30

200
```

---

# Multicast Delegates

A delegate can point to multiple methods.

Example

```csharp id="d2ahiu"
delegate void Message();
```

Methods

```csharp id="jkgm50"
static void Welcome()
{
    Console.WriteLine("Welcome");
}

static void Hello()
{
    Console.WriteLine("Hello");
}
```

Assign

```csharp id="4y9wzc"
Message msg = Welcome;

msg += Hello;

msg();
```

Output

```text id="j5rkrq"
Welcome

Hello
```

---

# Removing Methods

```csharp id="e4v0kk"
msg -= Hello;
```

Now only:

```text
Welcome
```

will execute.

---

# Invocation List

```text
Delegate

↓

Welcome()

↓

Hello()

↓

GoodBye()
```

This is called a **Multicast Delegate**.

---

# Anonymous Methods

Instead of creating a separate method:

```csharp id="jby3l4"
delegate void Display();
```

```csharp id="of1tb9"
Display d =
delegate()
{
    Console.WriteLine("Hello");
};
```

```csharp id="pynoz4"
d();
```

Output

```text id="4szll5"
Hello
```

---

# Built-in Delegates

.NET provides three commonly used delegates:

1. Action
2. Func
3. Predicate

---

# Action Delegate

Represents methods that return **void**.

Syntax

```csharp id="j0lx2g"
Action<T>
```

Example

```csharp id="vpt73f"
Action<string> display =
    name => Console.WriteLine(name);

display("Raman");
```

Output

```text id="eqw4ek"
Raman
```

---

# Func Delegate

Represents methods that return a value.

Last generic parameter is the return type.

```csharp id="w5smjlwm"
Func<int,int,int> add =
    (a,b) => a + b;
```

Usage

```csharp id="llb52a"
Console.WriteLine(add(10,20));
```

Output

```text id="y9r6km"
30
```

---

# Predicate Delegate

Represents methods returning `bool`.

Syntax

```csharp id="n8hloc"
Predicate<T>
```

Example

```csharp id="m8s9f0"
Predicate<int> isEven =
    x => x % 2 == 0;
```

Usage

```csharp id="0z3mza"
Console.WriteLine(isEven(10));
```

Output

```text id="vvtfxe"
True
```

---

# Delegates and Lambda Expressions

Delegates are commonly used with lambda expressions.

Example

```csharp id="4hqybm"
Func<int,int,int> multiply =
    (a,b) => a * b;
```

Usage

```csharp id="y64fqm"
Console.WriteLine(multiply(10,20));
```

Output

```text id="qkfr0o"
200
```

---

# Delegates and Events

Events internally use delegates.

Example

```csharp id="6baxvl"
public delegate void Notify();
```

```csharp id="wqljgh"
public event Notify Notification;
```

Events are discussed separately.

---

# Delegates in ASP.NET Core

Delegates are used extensively in ASP.NET Core.

---

## Middleware Pipeline

```csharp id="5epmyi"
app.Use(async (context, next) =>
{
    await next();
});
```

Here,

```text
next
```

is actually a delegate.

---

## LINQ

```csharp id="u5wjil"
var result =
    numbers.Where(x => x > 10);
```

The lambda expression is converted into a delegate.

---

## Dependency Injection

Factories often use delegates.

```csharp id="s3uy1l"
services.AddSingleton(serviceProvider =>
{
    return new MyService();
});
```

---

## Task Parallel Library

```csharp id="48e9gt"
Task.Run(() =>
{
    Console.WriteLine("Running");
});
```

`Task.Run()` accepts delegates.

---

# Delegate vs Interface

| Delegate              | Interface                   |
| --------------------- | --------------------------- |
| Represents one method | Represents multiple methods |
| Used for callbacks    | Used for contracts          |
| Supports multicast    | Does not support multicast  |
| Lightweight           | More powerful               |

---

# When to Use Delegates?

Use delegates when:

* Method should be passed as parameter.
* Callback functionality is required.
* Event handling is required.
* Loose coupling is needed.
* LINQ and lambdas are used.

---

# Best Practices

* Prefer `Action`, `Func`, and `Predicate` instead of creating custom delegates unless necessary.
* Use delegates for callback scenarios.
* Use events when multiple subscribers need notifications.
* Use lambda expressions for cleaner delegate code.
* Avoid overly complex delegate chains.

---

# Summary

| Concept            | Description                              |
| ------------------ | ---------------------------------------- |
| Delegate           | Type-safe function pointer               |
| Multicast Delegate | Delegate that points to multiple methods |
| Action             | Delegate returning void                  |
| Func               | Delegate returning a value               |
| Predicate          | Delegate returning bool                  |
| Anonymous Method   | Method without explicit name             |
| Lambda Expression  | Short syntax for delegates               |

---

# Interview Questions

### 1. What is a Delegate?

A delegate is a **type-safe function pointer** that stores references to methods.

---

### 2. Why are delegates called type-safe?

Because the method signature must match the delegate signature.

---

### 3. Can a delegate point to multiple methods?

Yes.

This is called a **Multicast Delegate**.

---

### 4. Difference between Action and Func?

| Action       | Func            |
| ------------ | --------------- |
| Returns void | Returns a value |

---

### 5. Difference between Func and Predicate?

| Func                | Predicate        |
| ------------------- | ---------------- |
| Can return any type | Must return bool |

---

### 6. Where are delegates used in ASP.NET Core?

* Middleware pipeline
* Events
* LINQ
* Dependency Injection factories
* Async programming (`Task.Run`)
* Lambda expressions
* Event handlers

---

### 7. Which built-in delegates should you remember for interviews?

```text
Action      -> Returns void

Func        -> Returns value

Predicate   -> Returns bool
```

These three delegates are used extensively throughout modern .NET and ASP.NET Core applications.
