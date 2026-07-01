# Chapter – Destructor / Finalizer in C#

## What is a Destructor?

A **Destructor** is a special member of a class that is executed **automatically by the Garbage Collector before an object's memory is reclaimed**.

Its primary purpose is to release **unmanaged resources** such as:

* File handles
* Database connections (if not disposed)
* Network sockets
* Operating system handles
* Native memory

Unlike constructors, **you cannot call a destructor directly**.

> **Note:** In C#, a destructor is actually compiled into a **Finalizer** (`Finalize()` method).

---

# Why Do We Need a Destructor?

Normally, the **Garbage Collector (GC)** automatically frees managed memory.

However, some objects may hold **unmanaged resources** that the GC cannot clean up automatically.

Example:

* Open file
* Printer handle
* Windows handle
* Native C/C++ memory

A destructor gives the runtime a chance to clean up those unmanaged resources.

---

# Destructor Syntax

A destructor:

* Has the same name as the class.
* Is prefixed with `~`.
* Has no parameters.
* Has no return type.
* Cannot have access modifiers (`public`, `private`, etc.).

Example:

```csharp id="a2t1b7"
class Employee
{
    ~Employee()
    {
        Console.WriteLine("Destructor Executed");
    }
}
```

---

# Constructor vs Destructor

| Constructor                      | Destructor                                     |
| -------------------------------- | ---------------------------------------------- |
| Creates/initializes an object    | Cleans up an object before memory is reclaimed |
| Called when an object is created | Called by the Garbage Collector                |
| Can have parameters              | Cannot have parameters                         |
| Can be overloaded                | Cannot be overloaded                           |
| Can have access modifiers        | Cannot have access modifiers                   |

---

# Example

```csharp id="f1l9qw"
class Employee
{
    public Employee()
    {
        Console.WriteLine("Constructor");
    }

    ~Employee()
    {
        Console.WriteLine("Destructor");
    }
}
```

Usage:

```csharp id="2ph9ek"
Employee emp = new Employee();

emp = null;
```

Output (eventually):

```text id="0ozpjt"
Constructor

Destructor
```

> The destructor does **not** execute immediately after `emp = null`. It runs only when the Garbage Collector decides to collect the object.

---

# How C# Converts a Destructor into `Finalize()`

When you write:

```csharp id="qrxjlwm"
class Employee
{
    ~Employee()
    {
        Console.WriteLine("Destructor");
    }
}
```

The C# compiler automatically converts it into something conceptually similar to:

```csharp id="0nlyxh"
protected override void Finalize()
{
    try
    {
        Console.WriteLine("Destructor");
    }
    finally
    {
        base.Finalize();
    }
}
```

You cannot override `Finalize()` directly in C#; writing a destructor is the supported syntax.

---

# When Does a Destructor Execute?

A destructor executes only when:

1. The object becomes unreachable.
2. The Garbage Collector runs.
3. The object is selected for collection.
4. The runtime invokes its finalizer.

Example:

```csharp id="gzkzsv"
Employee emp = new Employee();

emp = null;
```

Execution:

```text id="slpwzj"
Object Created

↓

Object Used

↓

Reference Removed

↓

GC Runs

↓

Destructor Executes

↓

Memory Released
```

The exact timing is **not predictable**.

---

# Object Life Cycle with Finalizer

```text id="fxi0hl"
Object Created

        │

        ▼

Constructor Executes

        │

        ▼

Object Used

        │

        ▼

Object Becomes Unreachable

        │

        ▼

Garbage Collector Detects Object

        │

        ▼

Finalizer Executes

        │

        ▼

Memory Reclaimed
```

---

# Relationship with the Garbage Collector

The Garbage Collector is responsible for:

* Detecting unreachable objects.
* Invoking the finalizer (if one exists).
* Reclaiming managed memory.

Important points:

* The Garbage Collector controls **when** the finalizer executes.
* You cannot call a destructor yourself.
* Finalizers make object collection slower because the GC must perform additional work.

---

# Why Should You Rarely Use Destructors?

In modern C# applications, destructors are rarely needed because:

* The Garbage Collector automatically frees managed memory.
* Finalizers delay memory reclamation.
* Finalizers negatively impact performance.
* Most .NET classes implement `IDisposable` instead.

Use a destructor only when your class directly manages unmanaged resources.

---

# Implementing `IDisposable`

Instead of relying on a destructor, .NET recommends implementing the `IDisposable` interface.

Example:

```csharp id="vq0grm"
class FileManager : IDisposable
{
    public void Dispose()
    {
        Console.WriteLine("Resources Released");
    }
}
```

Usage:

```csharp id="shnqde"
FileManager manager = new FileManager();

manager.Dispose();
```

Output:

```text id="s87bgh"
Resources Released
```

---

# Using Statement

The `using` statement automatically calls `Dispose()`.

```csharp id="6mjlwm"
using (FileStream file = new FileStream("test.txt", FileMode.OpenOrCreate))
{
    Console.WriteLine("Working with file...");
}
```

Execution:

```text id="42x65x"
Create Object

↓

Use File

↓

Dispose()

↓

Close File
```

This is the preferred approach when working with disposable resources.

---

# Dispose() vs Finalizer

| Dispose()                                | Finalizer                                         |
| ---------------------------------------- | ------------------------------------------------- |
| Called manually or by `using`            | Called automatically by the Garbage Collector     |
| Executes immediately                     | Executes at an unpredictable time                 |
| Releases managed and unmanaged resources | Primarily for unmanaged resource cleanup          |
| Better performance                       | Slower because it involves the finalization queue |
| Recommended approach                     | Used only when necessary                          |

---

# Dispose Pattern

Many .NET classes implement both `Dispose()` and a finalizer.

Simplified pattern:

```csharp id="gpmjlwm"
class ResourceManager : IDisposable
{
    public void Dispose()
    {
        Console.WriteLine("Dispose Called");

        GC.SuppressFinalize(this);
    }

    ~ResourceManager()
    {
        Console.WriteLine("Finalizer Called");
    }
}
```

Usage:

```csharp id="y6dysh"
ResourceManager manager = new ResourceManager();

manager.Dispose();
```

Output:

```text id="skm9qh"
Dispose Called
```

The finalizer does **not** run because:

```csharp id="lmo3je"
GC.SuppressFinalize(this);
```

tells the Garbage Collector that cleanup has already been performed.

---

# Why Use `GC.SuppressFinalize()`?

After `Dispose()` releases resources, there is no need for the finalizer to execute.

Calling:

```csharp id="um8cgo"
GC.SuppressFinalize(this);
```

improves performance by removing the object from the finalization queue.

---

# Real-World Example

```csharp id="mqy8bo"
class DatabaseConnection : IDisposable
{
    public void Dispose()
    {
        Console.WriteLine("Closing Database Connection");

        GC.SuppressFinalize(this);
    }

    ~DatabaseConnection()
    {
        Console.WriteLine("Finalizer Executed");
    }
}
```

Usage:

```csharp id="5mh6cz"
using DatabaseConnection db = new DatabaseConnection();
```

Output:

```text id="r09gnc"
Closing Database Connection
```

The finalizer is skipped because `Dispose()` has already performed the cleanup.

---

# Destructor in ASP.NET Core

In ASP.NET Core applications:

* Controllers **do not** usually have destructors.
* Services **rarely** require destructors.
* Entity Framework's `DbContext` implements `IDisposable`.
* File streams, database connections, and HTTP clients rely on `Dispose()` rather than finalizers.

Most cleanup is handled through:

* Dependency Injection (DI)
* `IDisposable`
* `IAsyncDisposable`
* `using` statements

---

# Best Practices

* Avoid writing destructors unless your class directly owns unmanaged resources.
* Prefer implementing `IDisposable` for resource cleanup.
* Use the `using` statement or `using` declaration to ensure `Dispose()` is called automatically.
* Call `GC.SuppressFinalize(this)` inside `Dispose()` when a finalizer is present.
* Do not depend on a destructor for timely resource release because its execution time is non-deterministic.
* Do not call the Garbage Collector manually (`GC.Collect()`) in normal application code.

---

# Summary

| Concept                 | Description                                                                            |
| ----------------------- | -------------------------------------------------------------------------------------- |
| Destructor              | Special member executed by the Garbage Collector before reclaiming an object's memory. |
| Finalizer               | The method generated by the compiler from a destructor (`Finalize()`).                 |
| `Finalize()`            | Invoked automatically by the Garbage Collector for objects with finalizers.            |
| `IDisposable`           | Interface that provides deterministic resource cleanup through `Dispose()`.            |
| `Dispose()`             | Preferred mechanism for releasing resources promptly.                                  |
| `GC.SuppressFinalize()` | Prevents the finalizer from running after cleanup has already occurred.                |
| `using`                 | Automatically calls `Dispose()` at the end of the scope.                               |

---

# Interview Questions

### 1. What is a destructor?

A destructor is a special member that the C# compiler converts into a `Finalize()` method. It is executed by the Garbage Collector before an object's memory is reclaimed.

---

### 2. Can we call a destructor directly?

**No.** A destructor is invoked automatically by the Garbage Collector.

---

### 3. What is the difference between `Dispose()` and a destructor?

| `Dispose()`                                     | Destructor                                 |
| ----------------------------------------------- | ------------------------------------------ |
| Deterministic (called explicitly or by `using`) | Non-deterministic (called by the GC)       |
| Executes immediately when called                | Executes only when the GC decides          |
| Preferred for releasing resources               | Fallback mechanism for unmanaged resources |

---

### 4. Why is `IDisposable` preferred over destructors?

`IDisposable` provides **deterministic cleanup**, allowing resources to be released immediately rather than waiting for the Garbage Collector.

---

### 5. What is the purpose of `GC.SuppressFinalize(this)`?

It tells the Garbage Collector not to call the finalizer because the object has already released its resources through `Dispose()`, improving performance.

---

### 6. Do all objects have a finalizer?

**No.** Only classes that define a destructor have a finalizer. Most classes in .NET do not require one because managed memory is reclaimed automatically by the Garbage Collector.
