This is one of the first middleware concepts you'll learn in ASP.NET Core.

The `app.Run()` middleware is called **Terminal Middleware** because it **terminates the request pipeline**. Once execution reaches `app.Run()`, the request ends there, and no further middleware is executed.

---

# What is `app.Run()`?

`app.Run()` has **two different meanings** in ASP.NET Core:

### 1. Start the Web Server

```csharp
app.Run();
```

Starts the application and begins listening for HTTP requests.

---

### 2. Register Terminal Middleware

```csharp
app.Run(async (HttpContext context) =>
{
    await context.Response.WriteAsync("Hello");
});
```

Registers a middleware that handles every request and ends the pipeline.

---

# Creating an Empty ASP.NET Core Project

## Using .NET CLI

```bash
dotnet new web -n MiddlewareDemo
```

Move to the project folder:

```bash
cd MiddlewareDemo
```

Run the application:

```bash
dotnet run
```

---

# Default Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Run();
```

Here,

```csharp
app.Run();
```

starts the Kestrel web server.

---

# Implementing a Terminal Middleware

Replace `Program.cs` with:

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Run(async (HttpContext context) =>
{
    await context.Response.WriteAsync("Hello ASP.NET Core");
});

app.Run();
```

Open

```text
https://localhost:7001
```

Output

```text
Hello ASP.NET Core
```

---

# Understanding the Code

## Create Builder

```csharp
var builder = WebApplication.CreateBuilder(args);
```

Creates the application builder and configures services.

---

## Build Application

```csharp
var app = builder.Build();
```

Creates the middleware pipeline.

---

## Register Terminal Middleware

```csharp
app.Run(async (HttpContext context) =>
{
    await context.Response.WriteAsync("Hello ASP.NET Core");
});
```

Let's understand it line by line.

---

### app.Run()

Registers a middleware.

Unlike `Use()`, it **does not call the next middleware**.

Pipeline stops here.

---

### async

```csharp
async
```

The middleware performs asynchronous work.

`WriteAsync()` is asynchronous, so the lambda must be marked `async`.

---

### HttpContext context

```csharp
(HttpContext context)
```

ASP.NET Core automatically provides the current request's `HttpContext`.

The `HttpContext` contains:

```text
Request

Response

Headers

Cookies

User

Session

Connection
```

---

### WriteAsync()

```csharp
await context.Response.WriteAsync("Hello ASP.NET Core");
```

Writes the response body to the client.

Equivalent HTTP response:

```http
HTTP/1.1 200 OK

Content-Type: text/plain

Hello ASP.NET Core
```

---

# Request Flow

```text
Browser

↓

HTTP Request

↓

Kestrel

↓

app.Run()

↓

WriteAsync()

↓

HTTP Response

↓

Browser
```

---

# What Happens Internally?

Suppose the browser requests

```text
https://localhost:7001
```

ASP.NET Core executes:

```text
Browser

↓

Kestrel

↓

HttpContext Created

↓

app.Run Middleware

↓

context.Response.WriteAsync()

↓

Response Sent

↓

Request Ends
```

---

# Why is it called Terminal Middleware?

Because there is **no next middleware**.

Unlike `Use()`, `Run()` doesn't have a `next` parameter.

```csharp
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello");
});
```

Execution stops here.

---

# Difference Between `Use()` and `Run()`

### `Use()`

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Before");

    await next();

    Console.WriteLine("After");
});
```

Has access to

```csharp
next
```

and can continue the pipeline.

---

### `Run()`

```csharp
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello");
});
```

No

```csharp
next
```

The pipeline ends immediately after this middleware completes.

---

# Example 1

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Run(async context =>
{
    await context.Response.WriteAsync("Welcome to ASP.NET Core");
});

app.Run();
```

Output

```text
Welcome to ASP.NET Core
```

---

# Example 2 – Display Request Information

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Run(async context =>
{
    await context.Response.WriteAsync($"""

Method : {context.Request.Method}

Path : {context.Request.Path}

Host : {context.Request.Host}

""");
});

app.Run();
```

Open

```text
https://localhost:7001/student
```

Output

```text
Method : GET

Path : /student

Host : localhost:7001
```

Notice how `HttpContext` gives access to both the request and the response.

---

# Example 3 – Set Headers

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Run(async context =>
{
    context.Response.Headers["Company"] = "KMIT";
    context.Response.Headers["Trainer"] = "Raman Sharma";

    await context.Response.WriteAsync("Middleware Demo");
});

app.Run();
```

Open Chrome Developer Tools (**F12 → Network**) and inspect the response.

You'll find:

```text
Response Headers

Company : KMIT

Trainer : Raman Sharma
```

---

# Example 4 – Return HTML

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Run(async context =>
{
    context.Response.ContentType = "text/html";

    await context.Response.WriteAsync("""
        <html>
            <body>
                <h1>Welcome</h1>
                <h2>ASP.NET Core Middleware</h2>
            </body>
        </html>
        """);
});

app.Run();
```

Browser

```text
Welcome

ASP.NET Core Middleware
```

---

# Complete Request Pipeline

```text
                Browser
                   │
                   │ HTTP Request
                   ▼
             Kestrel Server
                   │
        Creates HttpContext
                   │
                   ▼
        app.Run() Middleware
                   │
       ┌───────────┴───────────┐
       │                       │
       ▼                       ▼
HttpContext.Request    HttpContext.Response
       │                       │
       │               WriteAsync("Hello")
       │                       │
       └───────────┬───────────┘
                   ▼
           HTTP Response Sent
                   │
                   ▼
                Browser
```

---

# `app.Run()` vs `MapGet()`

| `app.Run()`                                         | `app.MapGet()`                     |
| --------------------------------------------------- | ---------------------------------- |
| Terminal middleware                                 | Endpoint mapping                   |
| Handles every request                               | Handles only matching GET requests |
| No routing required                                 | Uses routing                       |
| No `next` delegate                                  | Executes after routing             |
| Commonly used for simple demos or fallback handlers | Preferred for building web APIs    |

Example:

```csharp
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello");
});
```

Every URL returns:

```text
Hello
```

Whereas:

```csharp
app.MapGet("/", () => "Home");

app.MapGet("/about", () => "About");
```

Produces:

```text
GET /

↓

Home

-------------------

GET /about

↓

About
```

---

# Summary

| Concept                             | Description                                                                               |
| ----------------------------------- | ----------------------------------------------------------------------------------------- |
| `app.Run(async context => { ... })` | Registers a **terminal middleware** that handles the request and ends the pipeline.       |
| `async`                             | Allows asynchronous operations such as `WriteAsync()`.                                    |
| `HttpContext`                       | Represents the current HTTP request and response.                                         |
| `context.Request`                   | Reads request data such as method, path, headers, and query string.                       |
| `context.Response`                  | Writes the response, sets headers, status code, and content type.                         |
| `WriteAsync()`                      | Writes the response body to the client.                                                   |
| Pipeline Behavior                   | Since `Run()` has no `next` delegate, the request processing stops after this middleware. |

## Interview Answer

> **`app.Run()` is used in two ways in ASP.NET Core. `app.Run()` without parameters starts the web application and begins listening for requests. `app.Run(async context => { ... })` registers a terminal middleware. A terminal middleware handles the incoming request, generates the response using `HttpContext.Response`, and ends the request pipeline because it does not call a `next` delegate.**
