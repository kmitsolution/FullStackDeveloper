# Chapter – Extension Methods in C#

## What are Extension Methods?

**Extension Methods** allow us to **add new methods to an existing class without modifying its source code or creating a derived class**.

They appear as if they are instance methods of the existing type.

Extension methods were introduced in **C# 3.0** and are heavily used by **LINQ**.

---

# Why Do We Need Extension Methods?

Suppose we have:

```csharp id="z17x2l"
string name = "raman";
```

We can already call:

```csharp id="k0a92d"
name.ToUpper();
name.ToLower();
```

Imagine if we wanted a method:

```csharp id="8krh2g"
name.ToProperCase();
```

We cannot modify the `string` class because it is part of .NET.

Extension methods solve this problem.

---

# Real World Analogy

Think of a mobile phone.

Initially it has some built-in features.

Later, you install new apps.

```text id="5f5l9o"
Phone

↓

Install Application

↓

New Features Available
```

The phone itself was not modified.

Similarly:

```text id="z8b55n"
Existing Class

↓

Extension Method

↓

New Functionality Added
```

---

# Syntax of Extension Method

An extension method:

1. Must be inside a **static class**.
2. Must itself be **static**.
3. First parameter must use the **this** keyword.

Syntax:

```csharp id="wry6cc"
public static returnType MethodName(
    this ExistingType obj)
{
}
```

---

# Example 1

Create Extension Method for `string`.

```csharp id="oq0r1l"
public static class StringExtensions
{
    public static void Welcome(
        this string name)
    {
        Console.WriteLine(
            $"Welcome {name}");
    }
}
```

Usage:

```csharp id="lmop2m"
string name = "Raman";

name.Welcome();
```

Output:

```text id="0uw3ht"
Welcome Raman
```

Notice:

It looks like `Welcome()` is a method of `string`.

But it is actually an extension method.

---

# How Compiler Treats It

This:

```csharp id="jhn09m"
name.Welcome();
```

is internally converted to:

```csharp id="em6n2x"
StringExtensions.Welcome(name);
```

Extension methods are actually **static methods behind the scenes**.

---

# Example 2 – String Extension

```csharp id="vyr4t9"
public static class StringExtensions
{
    public static string ReverseText(
        this string text)
    {
        char[] chars = text.ToCharArray();

        Array.Reverse(chars);

        return new string(chars);
    }
}
```

Usage:

```csharp id="fjk2cf"
string name = "Raman";

Console.WriteLine(
    name.ReverseText());
```

Output:

```text id="h3ljlwm"
namaR
```

---

# Example 3 – Integer Extension

```csharp id="i3lx1y"
public static class IntegerExtensions
{
    public static bool IsEven(
        this int number)
    {
        return number % 2 == 0;
    }
}
```

Usage:

```csharp id="jlwm42"
int number = 20;

Console.WriteLine(
    number.IsEven());
```

Output:

```text id="hjlwm43"
True
```

---

# Extension Method with Parameters

```csharp id="2jlwm4"
public static class MathExtensions
{
    public static int Multiply(
        this int number,
        int value)
    {
        return number * value;
    }
}
```

Usage:

```csharp id="mjlwm44"
int number = 10;

Console.WriteLine(
    number.Multiply(5));
```

Output:

```text id="jlwm45"
50
```

Internally:

```csharp id="vjlwm46"
MathExtensions.Multiply(
    number,
    5);
```

---

# Example – Proper Case

```csharp id="6jlwm47"
public static class StringExtensions
{
    public static string ToProperCase(
        this string text)
    {
        return
            char.ToUpper(text[0]) +
            text.Substring(1).ToLower();
    }
}
```

Usage:

```csharp id="cjlwm48"
string name = "RAMAN";

Console.WriteLine(
    name.ToProperCase());
```

Output:

```text id="wjlwm49"
Raman
```

---

# Extension Method on Custom Class

```csharp id="pjlwm50"
class Employee
{
    public string Name
    {
        get;
        set;
    }
}
```

Extension

```csharp id="gjlwm51"
public static class EmployeeExtensions
{
    public static void Display(
        this Employee employee)
    {
        Console.WriteLine(
            employee.Name);
    }
}
```

Usage

```csharp id="qjlwm52"
Employee employee =
    new Employee
    {
        Name = "Raman"
    };

employee.Display();
```

Output

```text id="rjlwm53"
Raman
```

---

# Namespace Requirement

Extension methods become available only when the namespace is imported.

Example:

```csharp id="sjlwm54"
using MyExtensions;
```

Without `using`, the extension method will not appear.

---

# Priority Rule

If a class already contains a method with the same signature, the instance method takes precedence.

Example:

```csharp id="tjlwm55"
class Employee
{
    public void Display()
    {
        Console.WriteLine("Class Method");
    }
}
```

Extension:

```csharp id="ujlwm56"
public static void Display(
    this Employee employee)
{
    Console.WriteLine(
        "Extension Method");
}
```

Usage:

```csharp id="vjlwm57"
employee.Display();
```

Output:

```text id="wjlwm58"
Class Method
```

The class method wins.

---

# Extension Methods and LINQ

LINQ is built almost entirely using extension methods.

Example:

```csharp id="xjlwm59"
numbers.Where(x => x > 10);

numbers.OrderBy(x => x);

numbers.Select(x => x * 2);
```

These are actually:

```csharp id="yjlwm60"
Enumerable.Where();

Enumerable.OrderBy();

Enumerable.Select();
```

All of them are extension methods.

---

# Example

```csharp id="zjlwm61"
var result =
    numbers.Where(x => x > 5);
```

Internally:

```csharp id="ajlwm62"
Enumerable.Where(
    numbers,
    x => x > 5);
```

---

# Extension Methods with IEnumerable

```csharp id="bjlwm63"
public static class CollectionExtensions
{
    public static void Print<T>(
        this IEnumerable<T> items)
    {
        foreach (var item in items)
        {
            Console.WriteLine(item);
        }
    }
}
```

Usage:

```csharp id="cjlwm64"
List<int> numbers =
    new List<int>
    {
        1,
        2,
        3
    };

numbers.Print();
```

Output:

```text id="djlwm65"
1
2
3
```

---

# Extension Methods in ASP.NET Core

Extension methods are used extensively in ASP.NET Core.

Examples:

```csharp id="ejlwm66"
builder.Services.AddControllers();

builder.Services.AddSwaggerGen();

app.UseRouting();

app.UseAuthentication();

app.MapControllers();
```

All these methods are extension methods.

---

Example:

```csharp id="fjlwm67"
services.AddControllers();
```

Internally:

```csharp id="gjlwm68"
MvcServiceCollectionExtensions
    .AddControllers(
        services);
```

---

Middleware methods are also extension methods.

```csharp id="hjlwm69"
app.UseStaticFiles();

app.UseAuthorization();

app.UseEndpoints();
```

---

# Benefits of Extension Methods

* Add functionality to existing types.
* No need to modify source code.
* Improve readability.
* Enable fluent APIs.
* Widely used in LINQ and ASP.NET Core.

---

# Limitations

Extension methods:

* Cannot access private members.
* Cannot override existing methods.
* Are actually static methods.
* Instance methods take priority.

---

# Extension Methods vs Inheritance

| Extension Method          | Inheritance                   |
| ------------------------- | ----------------------------- |
| No new class needed       | Requires derived class        |
| Works with sealed classes | Cannot inherit sealed classes |
| Cannot override methods   | Can override virtual methods  |
| Adds helper functionality | Extends behavior              |

---

# Best Practices

* Use extension methods for helper functionality.
* Keep extension methods small and focused.
* Avoid adding too many methods to primitive types.
* Prefer meaningful namespaces.
* Use extension methods to build fluent APIs.

---

# Summary

| Concept          | Description                              |
| ---------------- | ---------------------------------------- |
| Extension Method | Adds functionality to existing classes   |
| Static Class     | Required container for extension methods |
| `this` Keyword   | Identifies the type being extended       |
| Namespace Import | Required to use extension methods        |
| LINQ             | Built using extension methods            |

---

# Interview Questions

### 1. What is an Extension Method?

An extension method allows adding new methods to an existing type **without modifying the original type**.

---

### 2. What are the requirements for an extension method?

1. Must be inside a static class.
2. Must be static.
3. First parameter must use `this`.

---

### 3. Are extension methods really instance methods?

No.

They are actually **static methods** that the compiler allows to be called using instance syntax.

---

### 4. Can extension methods access private members?

No.

They can access only public or accessible members.

---

### 5. Can extension methods override existing methods?

No.

Instance methods always take precedence.

---

### 6. Where are extension methods heavily used?

* LINQ
* ASP.NET Core Middleware
* Dependency Injection APIs
* Fluent APIs
* Helper libraries

---

### 7. Give examples of extension methods in ASP.NET Core.

```csharp id="ijlwm70"
builder.Services.AddControllers();

builder.Services.AddSwaggerGen();

app.UseRouting();

app.UseAuthentication();

app.MapControllers();
```

All of the above are extension methods that extend framework types such as `IServiceCollection`, `IApplicationBuilder`, and `WebApplication`.
