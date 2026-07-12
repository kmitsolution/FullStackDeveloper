# Chapter – Anonymous Methods in C#

## What is an Anonymous Method?

An **Anonymous Method** is a method **without a name**.

Instead of creating a separate method and then assigning it to a delegate, we can directly write the method body where the delegate is assigned.

Anonymous methods were introduced in **C# 2.0** to simplify delegate programming.

---

# Why Do We Need Anonymous Methods?

Without anonymous methods:

```csharp id="v3eq3f"
delegate void Message();

class Program
{
    static void Display()
    {
        Console.WriteLine("Hello");
    }

    static void Main()
    {
        Message msg = Display;

        msg();
    }
}
```

If the method is used only once, creating a separate method is unnecessary.

Anonymous methods solve this problem.

---

# Syntax

```csharp id="w4wdvf"
delegate(parameters)
{
    // Method Body
};
```

---

# Basic Example

```csharp id="u20vcv"
delegate void Message();

class Program
{
    static void Main()
    {
        Message msg =
            delegate()
            {
                Console.WriteLine("Hello");
            };

        msg();
    }
}
```

Output

```text id="1zthlr"
Hello
```

Notice:

* No method name.
* No separate method declaration.
* Method body is written directly.

---

# Anonymous Method with Parameters

Example

```csharp id="lmgbbf"
delegate void Calculation(int x, int y);

class Program
{
    static void Main()
    {
        Calculation add =
            delegate(int a, int b)
            {
                Console.WriteLine(a + b);
            };

        add(10,20);
    }
}
```

Output

```text id="d0mhys"
30
```

---

# Anonymous Method Returning Value

Example

```csharp id="1vlv4l"
delegate int Calculation(int x, int y);

class Program
{
    static void Main()
    {
        Calculation multiply =
            delegate(int a, int b)
            {
                return a * b;
            };

        Console.WriteLine(
            multiply(10,20));
    }
}
```

Output

```text id="bnn74u"
200
```

---

# Memory Representation

```text id="s9cy2w"
Delegate Variable

↓

Anonymous Method

↓

Execute Method Body
```

The delegate stores a reference to the anonymous method.

---

# Anonymous Methods with Built-in Delegates

Anonymous methods are often used with:

* Action
* Func
* Predicate

---

# Using Action

```csharp id="hlyuix"
Action display =
    delegate()
    {
        Console.WriteLine("Welcome");
    };

display();
```

Output

```text id="7l4lxg"
Welcome
```

---

# Using Action with Parameters

```csharp id="17p4z4"
Action<string> greet =
    delegate(string name)
    {
        Console.WriteLine(
            $"Welcome {name}");
    };

greet("Raman");
```

Output

```text id="uc2iwz"
Welcome Raman
```

---

# Using Func

```csharp id="q40b2a"
Func<int,int,int> add =
    delegate(int a,int b)
    {
        return a + b;
    };

Console.WriteLine(add(10,20));
```

Output

```text id="dn0c0f"
30
```

---

# Using Predicate

```csharp id="a2tzdc"
Predicate<int> isEven =
    delegate(int x)
    {
        return x % 2 == 0;
    };

Console.WriteLine(isEven(10));
```

Output

```text id="cgl20h"
True
```

---

# Anonymous Methods and Events

Anonymous methods are frequently used while subscribing to events.

Example

```csharp id="yrmu8u"
button.Click +=
delegate(object sender,
         EventArgs e)
{
    Console.WriteLine(
        "Button Clicked");
};
```

This avoids creating a separate event handler method.

---

# Capturing Outer Variables (Closures)

Anonymous methods can access local variables from the enclosing method.

Example

```csharp id="j3jvzk"
int number = 100;

Action display =
    delegate()
    {
        Console.WriteLine(number);
    };

display();
```

Output

```text id="ft0p0w"
100
```

This is called a **Closure**.

---

# Modifying Captured Variables

```csharp id="8ftgtx"
int count = 0;

Action increment =
    delegate()
    {
        count++;

        Console.WriteLine(count);
    };

increment();

increment();
```

Output

```text id="0g4sje"
1
2
```

The anonymous method remembers the outer variable.

---

# Anonymous Method vs Normal Method

### Normal Method

```csharp id="8j2fkh"
static void Display()
{
    Console.WriteLine("Hello");
}

Action action = Display;
```

---

### Anonymous Method

```csharp id="nprmk6"
Action action =
    delegate()
    {
        Console.WriteLine("Hello");
    };
```

---

# Anonymous Method vs Lambda Expression

Anonymous methods were later simplified by Lambda Expressions.

### Anonymous Method

```csharp id="h9c0y1"
Action action =
    delegate()
    {
        Console.WriteLine("Hello");
    };
```

### Lambda Expression

```csharp id="m4v3l5"
Action action =
    () =>
    {
        Console.WriteLine("Hello");
    };
```

Lambda expressions are shorter and more commonly used today.

---

# Example Comparison

### Anonymous Method

```csharp id="1l3z2h"
Func<int,int,int> add =
    delegate(int x,int y)
    {
        return x + y;
    };
```

### Lambda

```csharp id="m34ofh"
Func<int,int,int> add =
    (x,y) => x + y;
```

Both produce the same result.

---

# Limitations of Anonymous Methods

Anonymous methods:

* Cannot contain type parameters.
* Cannot be reused easily.
* Are less readable when logic becomes large.
* Have mostly been replaced by lambda expressions.

---

# Anonymous Methods in ASP.NET Core

Anonymous methods are occasionally used in:

### Event Handling

```csharp id="byc0ib"
service.Completed +=
delegate
{
    Console.WriteLine(
        "Completed");
};
```

### Middleware (older style)

```csharp id="8bvyj6"
app.Use(
delegate(HttpContext context,
         Func<Task> next)
{
    return next();
});
```

Today lambda expressions are preferred.

---

# Real Example

```csharp id="4h9z6w"
delegate void Notify();

class Program
{
    static void Main()
    {
        Notify notification =
            delegate()
            {
                Console.WriteLine(
                    "Email Sent");
            };

        notification();
    }
}
```

Output

```text id="ccz03q"
Email Sent
```

---

# Best Practices

* Use anonymous methods when the logic is small and used only once.
* Prefer lambda expressions in modern C# because they are shorter and more readable.
* Avoid large anonymous methods because they reduce readability.
* Use closures carefully to avoid unintended side effects.

---

# Summary

| Concept          | Description                                |
| ---------------- | ------------------------------------------ |
| Anonymous Method | Method without a name                      |
| delegate keyword | Used to define anonymous methods           |
| Closure          | Anonymous method accessing outer variables |
| Action           | Built-in delegate returning void           |
| Func             | Built-in delegate returning value          |
| Predicate        | Built-in delegate returning bool           |

---

# Interview Questions

### 1. What is an Anonymous Method?

An anonymous method is a **method without a name** that is directly assigned to a delegate.

---

### 2. Which keyword is used to create an anonymous method?

```csharp id="0x4y9l"
delegate
```

---

### 3. Can anonymous methods have parameters?

Yes.

Example:

```csharp id="pwy6t1"
delegate(int x)
{
}
```

---

### 4. Can anonymous methods return values?

Yes.

```csharp id="9rlw3i"
delegate(int x,int y)
{
    return x + y;
}
```

---

### 5. What is a Closure?

A closure occurs when an anonymous method accesses variables from its outer scope.

---

### 6. Difference between Anonymous Methods and Lambda Expressions?

| Anonymous Method         | Lambda Expression      |
| ------------------------ | ---------------------- |
| Uses `delegate` keyword  | Uses `=>` operator     |
| More verbose             | More concise           |
| Introduced in C# 2.0     | Introduced in C# 3.0   |
| Less commonly used today | Preferred in modern C# |

---

### 7. Which one should you use in modern .NET applications?

In modern .NET and ASP.NET Core applications, **Lambda Expressions** are generally preferred because they are shorter, cleaner, and integrate naturally with LINQ, delegates, async programming, and event handling. Anonymous methods are still supported and useful to understand because lambda expressions evolved from them and compile to similar delegate-based mechanisms.
