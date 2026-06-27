Middleware is one of the **core concepts** in ASP.NET Core. Every HTTP request that enters your application passes through a **pipeline** of middleware components before reaching your endpoint (Minimal API, Controller, or Razor Page).

Each middleware has **one responsibility** and either:

1. Performs some work and passes the request to the next middleware.
2. Stops the pipeline and returns a response immediately.

This design follows the **Single Responsibility Principle (SRP)**.

---

# What is Middleware?

A **middleware** is a software component that sits between the web server (Kestrel) and your application.

It can:

* Read the request
* Modify the request
* Log information
* Authenticate users
* Authorize users
* Serve static files
* Redirect HTTP to HTTPS
* Handle exceptions
* Generate a response
* Pass the request to the next middleware

---

# Request Pipeline

```text
Browser
   │
HTTP Request
   │
   ▼
Kestrel
   │
   ▼
+--------------------------------------+
| Middleware Pipeline                  |
|                                      |
| Exception Handling                   |
|        ↓                             |
| HTTPS Redirection                    |
|        ↓                             |
| Static Files                         |
|        ↓                             |
| Routing                              |
|        ↓                             |
| Authentication                       |
|        ↓                             |
| Authorization                        |
|        ↓                             |
| Endpoint (API/Controller)            |
+--------------------------------------+
   │
HTTP Response
   ▼
Browser
```

---

# Why is it called a Pipeline?

Imagine a water pipeline.

```text
Water

↓

Filter

↓

Heater

↓

Cooler

↓

Tap
```

Water flows through every component.

Similarly,

```text
HTTP Request

↓

Middleware 1

↓

Middleware 2

↓

Middleware 3

↓

Endpoint
```

The request flows through every middleware.

---

# ASP.NET Core Pipeline

When an HTTP request arrives:

```text
Browser

↓

Kestrel

↓

Middleware 1

↓

Middleware 2

↓

Middleware 3

↓

Endpoint

↓

Response

↓

Browser
```

The response travels back through the same middleware in reverse order.

---

# Middleware Request and Response Flow

```text
Browser
    │
    ▼
Request
    │
    ▼
Middleware A
    │
    ▼
Middleware B
    │
    ▼
Middleware C
    │
    ▼
Controller / Minimal API
    │
    ▲
Response
    │
Middleware C
    ▲
Middleware B
    ▲
Middleware A
    ▲
Browser
```

This is why middleware can modify **both** the request and the response.

---

# Built-in Middleware

ASP.NET Core provides many middleware components.

```text
Middleware

│

├── Exception Handling

├── HTTPS Redirection

├── Static Files

├── Routing

├── CORS

├── Authentication

├── Authorization

├── Session

├── Response Compression

└── Endpoint Execution
```

Let's understand the most common ones.

---

# 1. HTTPS Redirection Middleware

Code

```csharp
app.UseHttpsRedirection();
```

Purpose

Redirect HTTP requests to HTTPS.

Example

User opens

```text
http://localhost:5000
```

Middleware redirects

```text
https://localhost:5001
```

Flow

```text
Browser

↓

HTTP

↓

HTTPS Redirection Middleware

↓

HTTPS

↓

Application
```

Without this middleware, users could access your site over insecure HTTP.

---

# 2. Static Files Middleware

Code

```csharp
app.UseStaticFiles();
```

Suppose your project has:

```text
wwwroot

│

├── logo.png

├── styles.css

└── app.js
```

Request

```text
https://localhost:5001/logo.png
```

Flow

```text
Browser

↓

Static File Middleware

↓

wwwroot/logo.png

↓

Image Returned
```

If the file exists, the request ends here. The controller or API is never called.

---

# 3. Routing Middleware

Code

```csharp
app.UseRouting();
```

Purpose

Find which endpoint should execute.

Example

```text
/student

↓

Student API
```

```text
/products

↓

Product API
```

Routing decides where to send the request.

---

# 4. Authentication Middleware

Code

```csharp
app.UseAuthentication();
```

Purpose

Determine **who the user is**.

Example

User sends

```text
Authorization: Bearer JWT_TOKEN
```

Authentication middleware validates the token.

If valid

```text
User = Raman
```

Otherwise

```text
Anonymous User
```

---

# 5. Authorization Middleware

Code

```csharp
app.UseAuthorization();
```

Purpose

Determine **what the user is allowed to do**.

Example

```text
User

↓

Admin?

↓

Yes

↓

Access Granted
```

or

```text
User

↓

Student

↓

Admin Page

↓

403 Forbidden
```

---

# Authentication vs Authorization

Authentication

```text
Who are you?
```

Authorization

```text
What are you allowed to do?
```

---

# Example Pipeline

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.UseHttpsRedirection();

app.UseStaticFiles();

app.UseRouting();

app.UseAuthentication();

app.UseAuthorization();

app.MapGet("/", () => "Hello ASP.NET Core");

app.Run();
```

Execution order

```text
Browser

↓

HTTPS

↓

Static Files

↓

Routing

↓

Authentication

↓

Authorization

↓

Endpoint
```

---

# Middleware Ordering Matters

Suppose we write

```csharp
app.UseAuthorization();

app.UseAuthentication();
```

This is incorrect.

Authorization runs before authentication.

At that point, ASP.NET Core doesn't know who the user is.

Correct order

```csharp
app.UseAuthentication();

app.UseAuthorization();
```

Authentication first

↓

Authorization second

---

# Creating Custom Middleware

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Use(async (context, next) =>
{
    Console.WriteLine("Before Next");

    await next();

    Console.WriteLine("After Next");
});

app.MapGet("/", () => "Hello");

app.Run();
```

Output

```text
Before Next

Hello

After Next
```

Notice

The middleware executes

* Before the endpoint
* After the endpoint

---

# Multiple Middleware Example

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Middleware 1 Start");

    await next();

    Console.WriteLine("Middleware 1 End");
});

app.Use(async (context, next) =>
{
    Console.WriteLine("Middleware 2 Start");

    await next();

    Console.WriteLine("Middleware 2 End");
});

app.MapGet("/", () => "Hello");
```

Output

```text
Middleware 1 Start

Middleware 2 Start

Hello

Middleware 2 End

Middleware 1 End
```

This demonstrates that middleware behaves like **nested layers** (often called the "onion model").

---

# Short-Circuiting the Pipeline

A middleware can stop the request.

```csharp
app.Use(async (context, next) =>
{
    await context.Response.WriteAsync("Pipeline Stopped");
});
```

Notice

No

```csharp
await next();
```

Therefore

```text
Browser

↓

Middleware

↓

Response

↓

Finished
```

The endpoint never executes.

---

# Complete Pipeline Example

```text
                     Browser
                        │
                 HTTP Request
                        │
                        ▼
                  Kestrel Server
                        │
                        ▼
┌──────────────────────────────────────────────┐
│ Exception Middleware                         │
└──────────────────────────────────────────────┘
                        │
                        ▼
┌──────────────────────────────────────────────┐
│ HTTPS Redirection                            │
└──────────────────────────────────────────────┘
                        │
                        ▼
┌──────────────────────────────────────────────┐
│ Static File Middleware                       │
└──────────────────────────────────────────────┘
                        │
                        ▼
┌──────────────────────────────────────────────┐
│ Routing Middleware                           │
└──────────────────────────────────────────────┘
                        │
                        ▼
┌──────────────────────────────────────────────┐
│ Authentication Middleware                    │
└──────────────────────────────────────────────┘
                        │
                        ▼
┌──────────────────────────────────────────────┐
│ Authorization Middleware                     │
└──────────────────────────────────────────────┘
                        │
                        ▼
┌──────────────────────────────────────────────┐
│ Minimal API / Controller / Razor Page        │
└──────────────────────────────────────────────┘
                        │
                 HTTP Response
                        │
                        ▼
                     Browser
```

# Middleware Summary

| Middleware                           | Purpose                                                                     |
| ------------------------------------ | --------------------------------------------------------------------------- |
| `UseExceptionHandler()`              | Catches unhandled exceptions and returns friendly error responses           |
| `UseHttpsRedirection()`              | Redirects HTTP requests to HTTPS                                            |
| `UseStaticFiles()`                   | Serves files from the `wwwroot` folder                                      |
| `UseRouting()`                       | Matches the incoming request to an endpoint                                 |
| `UseCors()`                          | Controls cross-origin requests from browsers                                |
| `UseAuthentication()`                | Identifies and validates the current user                                   |
| `UseAuthorization()`                 | Checks whether the authenticated user has permission to access the resource |
| `UseSession()`                       | Enables server-side session storage                                         |
| `MapGet()`, `MapPost()`, Controllers | Execute the endpoint logic and generate the response                        |

## Key Interview Points

* A **middleware** is a component in the ASP.NET Core request pipeline that processes HTTP requests and responses.
* Middleware executes **in the order it is registered** in `Program.cs`.
* Each middleware should perform **one specific task**, such as HTTPS redirection, serving static files, authentication, or authorization.
* Middleware can either call `await next()` to pass the request to the next component or **short-circuit** the pipeline by generating a response directly.
* The request flows **forward** through the pipeline, while the response flows **backward** through the same middleware components, allowing each middleware to inspect or modify both the request and the response.
