This is the **recommended approach** for creating reusable middleware in ASP.NET Core. Earlier, we created middleware using:

```csharp
app.Use(async (context, next) =>
{
    // Logic
});
```

This is called **inline middleware**.

For larger applications, we create a **middleware class** that implements the `IMiddleware` interface. This makes the middleware reusable, testable, and easier to maintain.

---

# Two Ways to Create Custom Middleware

## 1. Inline Middleware

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Inline Middleware");

    await next();
});
```

Good for:

* Learning
* Small projects
* Simple middleware

---

## 2. Middleware Class (Recommended)

```text
Request

↓

LoggingMiddleware

↓

AuthenticationMiddleware

↓

UploadMiddleware

↓

SubmitMiddleware
```

Each middleware lives in its own class.

---

# Project Structure

```text
MiddlewareDemo
│
├── Program.cs
│
├── Middleware
│      │
│      └── UploadMiddleware.cs
│
└── MiddlewareDemo.csproj
```

---

# Step 1 – Create Middleware Folder

```
Middleware
```

Inside it create

```
UploadMiddleware.cs
```

---

# Step 2 – Create Middleware Class

```csharp
using Microsoft.AspNetCore.Http;

namespace MiddlewareDemo.Middleware;

public class UploadMiddleware : IMiddleware
{
    public async Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        Console.WriteLine("Upload Middleware Started");

        await next(context);

        Console.WriteLine("Upload Middleware Completed");
    }
}
```

---

# Understanding the Code

```csharp
public class UploadMiddleware : IMiddleware
```

By implementing `IMiddleware`, ASP.NET Core knows this class is a middleware component.

---

## InvokeAsync()

Every `IMiddleware` must implement:

```csharp
Task InvokeAsync(HttpContext context, RequestDelegate next)
```

Parameters:

```text
HttpContext
```

Contains

* Request
* Response
* Headers
* Cookies
* User

---

```text
RequestDelegate next
```

Represents the **next middleware** in the pipeline.

To continue the pipeline:

```csharp
await next(context);
```

---

# Flow

```text
Request

↓

Upload Middleware

↓

next(context)

↓

Next Middleware

↓

Response

↓

Upload Middleware Ends
```

---

# Step 3 – Register Middleware in DI Container

In `Program.cs`

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddTransient<UploadMiddleware>();

var app = builder.Build();
```

Why?

ASP.NET Core creates middleware using **Dependency Injection (DI)**.

Therefore it must be registered.

---

## Why `AddTransient`?

```csharp
builder.Services.AddTransient<UploadMiddleware>();
```

Means

> Create a new instance of `UploadMiddleware` whenever it is needed.

For middleware, **Transient** is the most common lifetime because middleware often contains no shared state.

---

# Step 4 – Use Middleware

```csharp
app.UseMiddleware<UploadMiddleware>();
```

Complete `Program.cs`

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddTransient<UploadMiddleware>();

var app = builder.Build();

app.UseMiddleware<UploadMiddleware>();

app.Run(async context =>
{
    Console.WriteLine("Submit Middleware");

    await context.Response.WriteAsync("Application Submitted");
});

app.Run();
```

---

# Output

Console

```text
Upload Middleware Started

Submit Middleware

Upload Middleware Completed
```

Browser

```text
Application Submitted
```

---

# Execution Flow

```text
Browser

↓

Upload Middleware

↓

Run Middleware

↓

Response

↓

Upload Middleware Ends

↓

Browser
```

---

# Multiple Middleware Classes

Suppose we have

```text
UploadMiddleware

ValidationMiddleware

SubmitMiddleware
```

---

## UploadMiddleware

```csharp
public class UploadMiddleware : IMiddleware
{
    public async Task InvokeAsync(HttpContext context,
                                  RequestDelegate next)
    {
        Console.WriteLine("Upload Started");

        await next(context);

        Console.WriteLine("Upload Completed");
    }
}
```

---

## ValidationMiddleware

```csharp
public class ValidationMiddleware : IMiddleware
{
    public async Task InvokeAsync(HttpContext context,
                                  RequestDelegate next)
    {
        Console.WriteLine("Validation Started");

        await next(context);

        Console.WriteLine("Validation Completed");
    }
}
```

---

## Register

```csharp
builder.Services.AddTransient<UploadMiddleware>();

builder.Services.AddTransient<ValidationMiddleware>();
```

---

## Pipeline

```csharp
app.UseMiddleware<UploadMiddleware>();

app.UseMiddleware<ValidationMiddleware>();

app.Run(async context =>
{
    Console.WriteLine("Submit");

    await context.Response.WriteAsync("Done");
});
```

---

# Output

```text
Upload Started

Validation Started

Submit

Validation Completed

Upload Completed
```

Exactly like nested function calls.

---

# Visual Representation

```text
                Browser
                   │
                   ▼
        Upload Middleware
        Before Next
                   │
                   ▼
      Validation Middleware
        Before Next
                   │
                   ▼
         Submit Middleware
         Generates Response
                   │
                   ▲
      Validation Middleware
        After Next
                   ▲
                   │
        Upload Middleware
        After Next
                   ▲
                   │
                Browser
```

---

# Short-Circuit Example

Suppose Upload Middleware decides the request should stop.

```csharp
public class UploadMiddleware : IMiddleware
{
    public async Task InvokeAsync(HttpContext context,
                                  RequestDelegate next)
    {
        Console.WriteLine("Upload Required");

        await context.Response.WriteAsync("Please Upload Documents");

        // Notice:
        // next(context) is NOT called
    }
}
```

Output

Browser

```text
Please Upload Documents
```

Console

```text
Upload Required
```

Validation Middleware

❌ Never Executes

Run Middleware

❌ Never Executes

---

# Pipeline

```text
Browser

↓

Upload Middleware

↓

Response Sent

↓

Finished
```

---

# Why Use Middleware Classes?

Instead of putting everything in `Program.cs`

```csharp
app.Use(...);

app.Use(...);

app.Use(...);

app.Use(...);
```

you separate responsibilities:

```text
LoggingMiddleware

AuthenticationMiddleware

UploadMiddleware

ValidationMiddleware

ExceptionMiddleware
```

Each class has **one responsibility**, making the application easier to maintain and test.

---

# Inline Middleware vs IMiddleware

| Inline Middleware (`app.Use`)               | Class-based Middleware (`IMiddleware`)       |
| ------------------------------------------- | -------------------------------------------- |
| Written directly in `Program.cs`            | Written in a separate class                  |
| Good for simple logic                       | Better for reusable and complex logic        |
| Harder to maintain as the application grows | Easier to organize and unit test             |
| Doesn't require DI registration             | Must be registered with Dependency Injection |
| Created inline                              | Created by the DI container                  |

---

# Complete Flow

```text
                 Browser
                    │
                    ▼
            HTTP Request
                    │
                    ▼
       UploadMiddleware
       InvokeAsync()
            │
    Before next(context)
            │
            ▼
     ValidationMiddleware
       InvokeAsync()
            │
    Before next(context)
            │
            ▼
       app.Run()
    "Application Submitted"
            │
            ▲
 ValidationMiddleware
 After next(context)
            ▲
            │
 UploadMiddleware
 After next(context)
            ▲
            │
         Browser
```

# Interview Points

* `IMiddleware` is an interface for creating reusable middleware classes.
* Every middleware class implements `InvokeAsync(HttpContext context, RequestDelegate next)`.
* `HttpContext` provides access to the current request and response.
* `RequestDelegate next` represents the next middleware in the pipeline. Calling `await next(context)` passes control to it.
* Middleware classes are created by the **Dependency Injection** container, so they must be registered (commonly with `AddTransient()`).
* `app.UseMiddleware<T>()` adds the middleware class to the request pipeline in the order it is registered.
* If a middleware does **not** call `await next(context)`, it **short-circuits** the pipeline and prevents the remaining middleware from executing.
