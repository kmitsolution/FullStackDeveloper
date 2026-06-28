`UseWhen()` is one of the most interesting middleware features in ASP.NET Core because it allows you to **branch the middleware pipeline**.

Instead of executing a middleware for **every request**, you can execute it **only when a specific condition is true**.

---

# What is `UseWhen()`?

`UseWhen()` creates a **conditional branch** in the middleware pipeline.

Syntax

```csharp
app.UseWhen(
    condition,
    branch =>
    {
        // Branch middleware
    });
```

It asks:

> **"If this condition is true, execute this middleware branch; otherwise continue with the normal pipeline."**

---

# Why Do We Need `UseWhen()`?

Suppose your application has two types of requests.

```text
/api/products

/home
```

You want logging only for API requests.

Without `UseWhen()`

```text
Browser

↓

Logging

↓

Home

↓

Logging Executed
```

Even though Home doesn't need logging.

With `UseWhen()`

```text
Browser

↓

API?

↓

Yes

↓

Logging

↓

Controller
```

Otherwise

```text
Browser

↓

API?

↓

No

↓

Skip Logging

↓

Controller
```

---

# Visual Representation

```text
                  Browser
                     │
                     ▼
              Middleware Pipeline
                     │
             Is Condition True?
              /              \
            Yes              No
            │                 │
            ▼                 ▼
     Branch Middleware   Continue Pipeline
            │                 │
            └─────────┬───────┘
                      ▼
              Remaining Middleware
```

---

# Example 1 – Basic `UseWhen()`

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.UseWhen(context => context.Request.Path.StartsWithSegments("/admin"),
    branch =>
    {
        branch.Use(async (context, next) =>
        {
            Console.WriteLine("Admin Middleware");

            await next();
        });
    });

app.Run(async context =>
{
    await context.Response.WriteAsync("Welcome");
});

app.Run();
```

---

## Test 1

Open

```text
https://localhost:5001/admin
```

Console

```text
Admin Middleware
```

Browser

```text
Welcome
```

---

## Test 2

Open

```text
https://localhost:5001/home
```

Console

```text
(No Output)
```

Browser

```text
Welcome
```

Notice

The middleware executes only for

```text
/admin
```

---

# How It Works

Condition

```csharp
context.Request.Path.StartsWithSegments("/admin")
```

Returns

```text
True
```

for

```text
/admin

/admin/users

/admin/settings
```

Returns

```text
False
```

for

```text
/home

/products

/contact
```

---

# Execution Flow

```text
Request

↓

Is Path = /admin ?

      │

 Yes──┘

↓

Admin Middleware

↓

Run Middleware

↓

Response
```

Otherwise

```text
Request

↓

Is Path = /admin ?

      │

 No──┘

↓

Skip Branch

↓

Run Middleware

↓

Response
```

---

# Example 2 – Query String Condition

Suppose only VIP users should execute a middleware.

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.UseWhen(context =>
    context.Request.Query["type"] == "VIP",
    branch =>
    {
        branch.Use(async (context, next) =>
        {
            Console.WriteLine("VIP Middleware");

            await next();
        });
    });

app.Run(async context =>
{
    await context.Response.WriteAsync("Order Placed");
});

app.Run();
```

---

## Request

```text
https://localhost:5001/?type=VIP
```

Console

```text
VIP Middleware
```

Browser

```text
Order Placed
```

---

## Request

```text
https://localhost:5001/?type=Normal
```

Console

```text
(No Output)
```

Browser

```text
Order Placed
```

---

# Example 3 – Authentication Example

Suppose you want extra logging only for authenticated users.

```csharp
app.UseWhen(context =>
    context.User.Identity?.IsAuthenticated == true,
    branch =>
    {
        branch.Use(async (context, next) =>
        {
            Console.WriteLine("Authenticated User");

            await next();
        });
    });

app.Run(async context =>
{
    await context.Response.WriteAsync("Hello");
});
```

Only authenticated users execute the branch.

---

# Example 4 – API Requests

```csharp
app.UseWhen(context =>
    context.Request.Path.StartsWithSegments("/api"),
    branch =>
    {
        branch.Use(async (context, next) =>
        {
            Console.WriteLine("API Request");

            await next();
        });
    });

app.Run(async context =>
{
    await context.Response.WriteAsync("Response");
});
```

Request

```text
/api/products
```

Console

```text
API Request
```

---

Request

```text
/home
```

Console

```text
(No Output)
```

---

# Complete Flow

```text
                     Browser
                        │
                        ▼
                 HTTP Request
                        │
                        ▼
                 UseWhen()
                        │
          Is Condition True?
             /             \
          Yes               No
          │                 │
          ▼                 ▼
 Branch Middleware      Skip Branch
          │                 │
          └─────────┬───────┘
                    ▼
             Remaining Pipeline
                    │
                    ▼
               app.Run()
                    │
                    ▼
                HTTP Response
```

---

# Difference Between `Use()` and `UseWhen()`

### `Use()`

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Executed");

    await next();
});
```

Runs for

```text
Every Request
```

---

### `UseWhen()`

```csharp
app.UseWhen(
    context => context.Request.Path == "/admin",
    branch =>
    {
        branch.Use(async (context, next) =>
        {
            Console.WriteLine("Admin Only");

            await next();
        });
    });
```

Runs only when

```text
/admin
```

is requested.

---

# Real-Life Analogy

Imagine entering a company.

```text
Visitor

↓

Reception

↓

Are You a Visitor?

      │

Yes───┘

↓

Visitor Pass Counter

↓

Meeting Room
```

Employees skip the Visitor Pass Counter.

This is exactly how `UseWhen()` works.

---

# Example with Multiple Middlewares

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Use(async (context, next) =>
{
    Console.WriteLine("Main Middleware");

    await next();
});

app.UseWhen(context => context.Request.Path.StartsWithSegments("/admin"),
    branch =>
    {
        branch.Use(async (context, next) =>
        {
            Console.WriteLine("Admin Branch");

            await next();
        });
    });

app.Run(async context =>
{
    Console.WriteLine("Terminal Middleware");

    await context.Response.WriteAsync("Done");
});

app.Run();
```

### Request

```text
/admin
```

Output

```text
Main Middleware

Admin Branch

Terminal Middleware
```

### Request

```text
/home
```

Output

```text
Main Middleware

Terminal Middleware
```

Notice that the **main pipeline always continues**. The branch is **added only when the condition is true**.

---

# `UseWhen()` vs `MapWhen()`

Students often confuse these two.

| Feature                                       | `UseWhen()`                                        | `MapWhen()`                                                    |
| --------------------------------------------- | -------------------------------------------------- | -------------------------------------------------------------- |
| Creates a conditional branch                  | ✅ Yes                                              | ✅ Yes                                                          |
| Returns to the main pipeline after the branch | ✅ Yes                                              | ❌ No (unless you explicitly continue)                          |
| Typical use                                   | Conditional logging, auditing, headers, validation | Separate pipeline for admin, API versioning, special endpoints |
| Main pipeline continues                       | ✅ Yes                                              | Usually No                                                     |

---

# Complete Pipeline Diagram

```text
                    Browser
                       │
                       ▼
                Main Middleware
                       │
                       ▼
             UseWhen Condition
               Is /admin ?
              /            \
           Yes              No
           │                │
           ▼                │
   Admin Branch Middleware  │
           │                │
           └───────┬────────┘
                   ▼
          Terminal Middleware
                   │
                   ▼
                Browser
```

# Interview Points

* `UseWhen()` creates a **conditional branch** in the middleware pipeline.
* It evaluates a condition for every request.
* If the condition is **true**, the branch middleware executes and then returns to the main pipeline.
* If the condition is **false**, the branch is skipped and the request continues through the normal middleware pipeline.
* `UseWhen()` is ideal for scenarios such as API-only logging, admin-only middleware, conditional auditing, feature flags, or request-specific processing without affecting every request.
* Unlike `MapWhen()`, `UseWhen()` **rejoins the main pipeline** after the branch completes.
