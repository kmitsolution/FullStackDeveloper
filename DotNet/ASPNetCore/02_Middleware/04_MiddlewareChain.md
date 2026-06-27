This is one of the **most important ASP.NET Core concepts**. Once you understand how `app.Use()` and `app.Run()` work together, you'll understand almost every built-in middleware such as Authentication, Authorization, Static Files, CORS, etc.

---

# Middleware Chaining

Middleware are executed in the order they are registered.

```text
Browser
   │
   ▼
Middleware 1
   │
   ▼
Middleware 2
   │
   ▼
Middleware 3
   │
   ▼
app.Run()
```

Each middleware decides:

* Continue to the next middleware (`await next()`)
* Stop the pipeline (don't call `next()`)

---

# app.Use()

`app.Use()` is used to create middleware that can **continue the pipeline**.

Signature

```csharp
app.Use(async (context, next) =>
{
    // Before Next

    await next();

    // After Next
});
```

Notice the two parameters:

```text
context
next
```

* `context` → Current HttpContext
* `next` → Delegate to call the next middleware

---

# app.Run()

`app.Run()` creates a **terminal middleware**.

```csharp
app.Run(async context =>
{
    await context.Response.WriteAsync("Pipeline End");
});
```

Notice

There is **no next parameter**.

Once execution reaches `Run()`, the request ends.

---

# Example 1 - Chaining Three Middlewares

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Use(async (context, next) =>
{
    Console.WriteLine("Middleware 1 - Before");

    await next();

    Console.WriteLine("Middleware 1 - After");
});

app.Use(async (context, next) =>
{
    Console.WriteLine("Middleware 2 - Before");

    await next();

    Console.WriteLine("Middleware 2 - After");
});

app.Run(async context =>
{
    Console.WriteLine("Middleware 3 (Run)");

    await context.Response.WriteAsync("Request Completed");
});

app.Run();
```

---

# Output

Open

```text
https://localhost:5001
```

Console

```text
Middleware 1 - Before

Middleware 2 - Before

Middleware 3 (Run)

Middleware 2 - After

Middleware 1 - After
```

Browser

```text
Request Completed
```

---

# Execution Flow

```text
Browser
   │
   ▼
Middleware 1
Before
   │
   ▼
Middleware 2
Before
   │
   ▼
Run Middleware
(Response Created)
   ▲
   │
Middleware 2
After
   ▲
   │
Middleware 1
After
   ▲
Browser
```

Notice

The request goes **down** the pipeline.

The response comes **back up**.

---

# Understanding `await next()`

Suppose

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("A");

    await next();

    Console.WriteLine("B");
});
```

Think of

```csharp
await next();
```

as saying

> "I'm finished with my first task. Let the next middleware execute. When it finishes, return back to me."

Exactly like calling another method.

---

# Example 2 - Passing HttpContext

Every middleware receives the **same HttpContext**.

Middleware 1

```csharp
app.Use(async (context, next) =>
{
    context.Items["Message"] = "Hello from Middleware 1";

    await next();
});
```

Middleware 2

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine(context.Items["Message"]);

    await next();
});
```

Run

```csharp
app.Run(async context =>
{
    await context.Response.WriteAsync("Completed");
});
```

Output

```text
Hello from Middleware 1
```

Notice

Middleware 1 stores data.

Middleware 2 reads it.

They share the same `HttpContext`.

---

# Visual Representation

```text
Browser

↓

HttpContext Created

↓

Middleware 1

context.Items["Message"]

↓

Middleware 2

Reads Message

↓

Run

↓

Response
```

---

# Short-Circuiting the Pipeline

Sometimes we don't want the request to continue.

Example

```text
Upload Required

↓

Stop

↓

Don't Submit
```

This is called **short-circuiting**.

---

# Example 3 - Stop the Pipeline

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Use(async (context, next) =>
{
    await context.Response.WriteAsync("Pipeline Stopped");
});

app.Run(async context =>
{
    await context.Response.WriteAsync("This will never execute");
});

app.Run();
```

Output

```text
Pipeline Stopped
```

Notice

There is

```csharp
await next();
```

❌ Missing

Therefore

`Run()` never executes.

---

# Visual Flow

```text
Browser

↓

Middleware 1

↓

Response Sent

↓

End
```

Run Middleware

❌ Never Reached

---

# Example 4 - Conditional Short-Circuit

Suppose only authenticated users may continue.

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Use(async (context, next) =>
{
    var authenticated = false;

    if (!authenticated)
    {
        await context.Response.WriteAsync("Please Login");

        return;
    }

    await next();
});

app.Run(async context =>
{
    await context.Response.WriteAsync("Welcome");
});

app.Run();
```

Output

```text
Please Login
```

Run Middleware

Never executes.

---

# Visual Flow

```text
Browser

↓

Authentication Middleware

↓

Authenticated?

      │

  No──┘

↓

Return Login Message

↓

End
```

---

# Example 5 - Continue Pipeline

Now

```csharp
bool authenticated = true;
```

Execution

```text
Browser

↓

Authentication Middleware

↓

Yes

↓

Run Middleware

↓

Welcome
```

Output

```text
Welcome
```

---

# Real-Life Example

Imagine entering a company.

```text
Visitor

↓

Security

↓

Reception

↓

Manager

↓

Meeting Room
```

Security Middleware

```text
ID Available?

↓

No

↓

Stop
```

Reception

Never executes.

Manager

Never executes.

Meeting

Never happens.

Exactly how middleware works.

---

# Combining Everything

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

// Logging Middleware
app.Use(async (context, next) =>
{
    Console.WriteLine("Logging Started");

    await next();

    Console.WriteLine("Logging Completed");
});

// Authentication Middleware
app.Use(async (context, next) =>
{
    bool authenticated = true;

    if (!authenticated)
    {
        await context.Response.WriteAsync("Access Denied");

        return;
    }

    Console.WriteLine("Authentication Successful");

    await next();
});

// Business Middleware
app.Use(async (context, next) =>
{
    Console.WriteLine("Business Logic");

    await next();
});

// Terminal Middleware
app.Run(async context =>
{
    Console.WriteLine("Generating Response");

    await context.Response.WriteAsync("Request Successful");
});

app.Run();
```

---

# Output

Console

```text
Logging Started

Authentication Successful

Business Logic

Generating Response

Logging Completed
```

Browser

```text
Request Successful
```

---

# Complete Pipeline

```text
                 Browser
                    │
             HTTP Request
                    │
                    ▼
        Logging Middleware
        Before Next
                    │
                    ▼
    Authentication Middleware
      Authenticated?
          │
   Yes ───┘
                    │
                    ▼
      Business Middleware
                    │
                    ▼
       Terminal Middleware
        app.Run()
        Response Created
                    │
                    ▲
      Business Middleware Ends
                    ▲
                    │
      Logging Middleware Ends
                    ▲
                    │
              Browser
```

If authentication fails:

```text
                 Browser
                    │
                    ▼
        Logging Middleware
                    │
                    ▼
    Authentication Middleware
                    │
      Authenticated?
           │
      No ──┘
           │
           ▼
   "Access Denied"
           │
           ▼
     Response Sent
           │
           ▼
        Browser

Business Middleware  ❌ Not Executed
Run Middleware       ❌ Not Executed
```

# `app.Use()` vs `app.Run()`

| Feature                                    | `app.Use()`                                                     | `app.Run()`                                |
| ------------------------------------------ | --------------------------------------------------------------- | ------------------------------------------ |
| Has `HttpContext`                          | ✅ Yes                                                           | ✅ Yes                                      |
| Has `next` delegate                        | ✅ Yes                                                           | ❌ No                                       |
| Can call next middleware                   | ✅ Yes (`await next()`)                                          | ❌ No                                       |
| Can execute code after the next middleware | ✅ Yes                                                           | ❌ No                                       |
| Can short-circuit the pipeline             | ✅ Yes (don't call `next()`)                                     | Always terminal                            |
| Typical use                                | Logging, authentication, authorization, CORS, custom middleware | Final request handler or fallback response |

## Key Interview Points

* `app.Use()` creates middleware that can inspect the request, optionally modify it, call `await next()` to continue the pipeline, and even execute additional code after the next middleware returns.
* `app.Run()` creates **terminal middleware**. It handles the request and ends the pipeline because it has no `next` delegate.
* The same `HttpContext` object is passed through every middleware, allowing them to share request-specific information (for example, through `context.Items`).
* A middleware **short-circuits** the pipeline by generating a response and **not** calling `await next()`. This prevents the remaining middleware and endpoints from executing.
