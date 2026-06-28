This is called **Conventional Middleware** because it follows the ASP.NET Core middleware convention. Unlike `IMiddleware`, it **does not implement any interface**. ASP.NET Core recognizes it because:

1. It has a constructor that accepts a `RequestDelegate`.
2. It has an `Invoke()` or `InvokeAsync()` method.

This is the pattern used by many of ASP.NET Core's built-in middleware.

---

# What is Conventional Middleware?

A conventional middleware is simply a C# class that follows these conventions:

* Constructor accepts `RequestDelegate next`
* Contains an `InvokeAsync(HttpContext context)` method

No interface is required.

```text
Request
   │
   ▼
Conventional Middleware
   │
   ▼
Next Middleware
```

---

# Project Structure

```text
MiddlewareDemo
│
├── Program.cs
│
├── Middleware
│      │
│      └── FullNameMiddleware.cs
│
└── Extensions
       │
       └── FullNameMiddlewareExtensions.cs
```

---

# Step 1 - Create the Middleware Class

Create a folder named:

```text
Middleware
```

Create:

```text
FullNameMiddleware.cs
```

```csharp
namespace MiddlewareDemo.Middleware;

public class FullNameMiddleware
{
    private readonly RequestDelegate _next;

    public FullNameMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        string? firstName = context.Request.Query["firstName"];
        string? lastName = context.Request.Query["lastName"];

        string fullName = $"{firstName} {lastName}".Trim();

        await context.Response.WriteAsync($"Hello {fullName}");
    }
}
```

---

# Understanding the Code

## Constructor

```csharp
private readonly RequestDelegate _next;

public FullNameMiddleware(RequestDelegate next)
{
    _next = next;
}
```

ASP.NET Core injects the next middleware into the constructor.

Think of it like:

```text
Current Middleware

↓

Next Middleware
```

Although `_next` is available, in this example we **don't call it** because this middleware generates the response itself.

---

## InvokeAsync()

```csharp
public async Task InvokeAsync(HttpContext context)
```

ASP.NET Core automatically calls this method whenever a request reaches this middleware.

The `HttpContext` contains:

* Request
* Response
* Headers
* Cookies
* Query String
* User

---

## Reading Query String

```csharp
string? firstName = context.Request.Query["firstName"];
string? lastName = context.Request.Query["lastName"];
```

Suppose the browser requests:

```text
https://localhost:5001/?firstName=Raman&lastName=Sharma
```

The middleware reads:

```text
firstName = Raman

lastName = Sharma
```

---

## Create Full Name

```csharp
string fullName = $"{firstName} {lastName}".Trim();
```

Result:

```text
Raman Sharma
```

---

## Send Response

```csharp
await context.Response.WriteAsync($"Hello {fullName}");
```

The browser receives:

```text
Hello Raman Sharma
```

---

# Step 2 - Register the Middleware

In `Program.cs`

```csharp
using MiddlewareDemo.Middleware;

var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.UseMiddleware<FullNameMiddleware>();

app.Run();
```

Notice there is **no terminal middleware** after it because this middleware itself writes the response.

---

# Step 3 - Run the Application

Open the browser:

```text
https://localhost:5001/?firstName=Raman&lastName=Sharma
```

Output:

```text
Hello Raman Sharma
```

---

# Request Flow

```text
Browser

↓

https://localhost:5001/?firstName=Raman&lastName=Sharma

↓

Kestrel

↓

FullNameMiddleware

↓

Read Query String

↓

Create Full Name

↓

Write Response

↓

Browser
```

---

# What Happens Internally?

The browser sends:

```http
GET /?firstName=Raman&lastName=Sharma HTTP/1.1

Host: localhost:5001
```

ASP.NET Core creates:

```text
HttpContext
```

The middleware reads:

```csharp
context.Request.Query["firstName"]

context.Request.Query["lastName"]
```

Then writes:

```text
Hello Raman Sharma
```

---

# Visual Representation

```text
               Browser
                  │
                  ▼
HTTP GET

/?firstName=Raman&lastName=Sharma
                  │
                  ▼
           Kestrel Server
                  │
                  ▼
        FullNameMiddleware
                  │
                  ├── Read firstName
                  │
                  ├── Read lastName
                  │
                  ├── Build Full Name
                  │
                  ▼
WriteAsync("Hello Raman Sharma")
                  │
                  ▼
               Browser
```

---

# Adding an Extension Method (Recommended)

Create:

```text
Extensions

↓

FullNameMiddlewareExtensions.cs
```

```csharp
using MiddlewareDemo.Middleware;

namespace MiddlewareDemo.Extensions;

public static class FullNameMiddlewareExtensions
{
    public static IApplicationBuilder UseFullNameMiddleware(
        this IApplicationBuilder app)
    {
        return app.UseMiddleware<FullNameMiddleware>();
    }
}
```

Now `Program.cs` becomes:

```csharp
using MiddlewareDemo.Extensions;

var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.UseFullNameMiddleware();

app.Run();
```

This is cleaner and follows the same pattern as built-in middleware.

---

# What if We Want the Pipeline to Continue?

Suppose after greeting the user, you want another middleware to execute.

Modify the middleware:

```csharp
public async Task InvokeAsync(HttpContext context)
{
    string? firstName = context.Request.Query["firstName"];
    string? lastName = context.Request.Query["lastName"];

    Console.WriteLine($"Hello {firstName} {lastName}");

    await _next(context);
}
```

And add a terminal middleware:

```csharp
app.UseMiddleware<FullNameMiddleware>();

app.Run(async context =>
{
    await context.Response.WriteAsync("Welcome to ASP.NET Core");
});
```

Console:

```text
Hello Raman Sharma
```

Browser:

```text
Welcome to ASP.NET Core
```

Here:

* The middleware logs the name.
* The terminal middleware generates the response.

---

# Complete Request Pipeline

```text
                    Browser
                       │
                       ▼
https://localhost:5001/?firstName=Raman&lastName=Sharma
                       │
                       ▼
                 Kestrel Server
                       │
                       ▼
             FullNameMiddleware
                       │
      Read firstName & lastName
                       │
      Build "Raman Sharma"
                       │
     WriteAsync("Hello Raman Sharma")
                       │
                       ▼
                    Browser
```

# Conventional Middleware vs IMiddleware

| Conventional Middleware                    | `IMiddleware`                                                                    |
| ------------------------------------------ | -------------------------------------------------------------------------------- |
| Plain C# class                             | Implements `IMiddleware`                                                         |
| No interface required                      | Must implement `InvokeAsync()` from the interface                                |
| Constructor accepts `RequestDelegate next` | `RequestDelegate` is passed as a parameter to `InvokeAsync()`                    |
| Registered using `app.UseMiddleware<T>()`  | Registered with DI (typically `AddTransient<T>()`) and then `UseMiddleware<T>()` |
| Commonly used for custom middleware        | Useful when middleware depends heavily on Dependency Injection lifetimes         |

## Interview Answer

> **A conventional middleware in ASP.NET Core is a plain C# class that follows a naming convention. It has a constructor that accepts a `RequestDelegate` and an `Invoke` or `InvokeAsync` method that accepts an `HttpContext`. ASP.NET Core automatically recognizes this pattern and executes the middleware when it is added using `app.UseMiddleware<T>()`. In this example, the middleware reads `firstName` and `lastName` from the query string, combines them into a full name, and returns a response such as `Hello Raman Sharma`.**
