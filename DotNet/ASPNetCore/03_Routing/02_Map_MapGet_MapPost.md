This is an important topic because many beginners get confused between **Map()**, **MapGet()**, **MapPost()**, and **MapFallback()**.

The easiest way to understand them is:

* **Map()** → Match a URL path and create a **pipeline (branch)**.
* **MapGet()** → Match a **GET** request to an endpoint.
* **MapPost()** → Match a **POST** request to an endpoint.
* **MapFallback()** → Execute only when **no other route matches**.

---

# Overview of the Map Family

```text
Incoming Request
       │
       ▼
  ASP.NET Core Routing
       │
 ┌─────┼───────────────┬─────────────┐
 │     │               │             │
 ▼     ▼               ▼             ▼
Map()  MapGet()    MapPost()   MapFallback()
 │        │             │             │
Pipeline Endpoint   Endpoint   Default Handler
```

---

# 1. Map()

## What is Map()?

`Map()` creates a **branch in the middleware pipeline** based on the URL path.

Think of it as saying:

> "If the URL starts with this path, execute this branch of middleware."

---

## Syntax

```csharp
app.Map("/admin", adminApp =>
{
    adminApp.Run(async context =>
    {
        await context.Response.WriteAsync("Welcome to Admin");
    });
});
```

---

## Request Flow

```text
Browser

↓

/admin

↓

Map("/admin")

↓

Admin Pipeline

↓

Response
```

---

## Complete Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.Map("/admin", adminApp =>
{
    adminApp.Run(async context =>
    {
        await context.Response.WriteAsync("Welcome Admin");
    });
});

app.Run(async context =>
{
    await context.Response.WriteAsync("Home Page");
});

app.Run();
```

---

### Test

Open

```text
https://localhost:5001/admin
```

Output

```text
Welcome Admin
```

---

Open

```text
https://localhost:5001/
```

Output

```text
Home Page
```

---

# Visual Representation

```text
Browser
   │
   ▼
Is URL /admin ?

      │
 Yes──┘

↓

Admin Pipeline

↓

Welcome Admin
```

Otherwise

```text
↓

Home Page
```

---

# 2. MapGet()

`MapGet()` registers an endpoint for **HTTP GET**.

```csharp
app.MapGet("/products", () =>
{
    return "Product List";
});
```

---

## Request

```http
GET /products
```

Output

```text
Product List
```

---

### Flow

```text
GET /products

↓

Routing

↓

MapGet()

↓

Response
```

---

# 3. MapPost()

Handles only **HTTP POST** requests.

```csharp
app.MapPost("/products", () =>
{
    return "Product Created";
});
```

---

## Request

```http
POST /products
```

Output

```text
Product Created
```

---

Notice

Same URL

```text
/products
```

Different Method

Different Endpoint

---

# GET vs POST

```text
GET /products

↓

MapGet()

↓

Product List

-----------------------

POST /products

↓

MapPost()

↓

Product Created
```

---

# Complete Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student", () =>
{
    return "Student List";
});

app.MapPost("/student", () =>
{
    return "Student Created";
});

app.Run();
```

---

### Test

GET

```text
/student
```

Response

```text
Student List
```

POST

```text
/student
```

Response

```text
Student Created
```

---

# 4. MapFallback()

Suppose the application contains

```text
/

/about

/contact
```

User opens

```text
/xyz
```

No route matches.

Instead of

```text
404
```

you can define a fallback.

---

## Syntax

```csharp
app.MapFallback(() =>
{
    return "Page Not Found";
});
```

---

Complete example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", () => "Home");

app.MapGet("/about", () => "About");

app.MapFallback(() => "Custom 404 Page");

app.Run();
```

---

### Test

Open

```text
/unknown
```

Output

```text
Custom 404 Page
```

---

# Visual Representation

```text
Browser

↓

Routing

↓

Route Found?

      │

Yes───┘

↓

Execute Endpoint

--------------------

No

↓

MapFallback()

↓

Custom Page
```

---

# Route to Different Pages (HTML)

Let's create a small website with different pages.

---

## Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", async context =>
{
    context.Response.ContentType = "text/html";

    await context.Response.WriteAsync("""
        <h1>Home Page</h1>

        <a href="/about">About</a><br>

        <a href="/contact">Contact</a>
        """);
});

app.MapGet("/about", async context =>
{
    context.Response.ContentType = "text/html";

    await context.Response.WriteAsync("""
        <h1>About Us</h1>

        <a href="/">Home</a>
        """);
});

app.MapGet("/contact", async context =>
{
    context.Response.ContentType = "text/html";

    await context.Response.WriteAsync("""
        <h1>Contact Us</h1>

        <a href="/">Home</a>
        """);
});

app.MapFallback(async context =>
{
    context.Response.ContentType = "text/html";

    await context.Response.WriteAsync("""
        <h1>404 - Page Not Found</h1>

        <a href="/">Go Home</a>
        """);
});

app.Run();
```

---

### Browser Navigation

Open

```text
https://localhost:5001/
```

Output

```text
Home Page

About

Contact
```

Click

```text
About
```

URL changes

```text
/about
```

Output

```text
About Us
```

Click

```text
Home
```

Returns to

```text
/
```

---

If you type

```text
/xyz
```

Output

```text
404 - Page Not Found

Go Home
```

---

# Request Flow

```text
                    Browser
                       │
                  GET /about
                       │
                       ▼
                  Routing
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
       /          /about        /contact
        │              │              │
        │              ▼              │
        │        About Endpoint       │
        │                             │
        └──────────────┬──────────────┘
                       ▼
                 HTML Response
                       │
                       ▼
                    Browser
```

---

# Difference Between Map(), MapGet(), MapPost(), and MapFallback()

| Method          | Purpose                                     | Matches          |
| --------------- | ------------------------------------------- | ---------------- |
| `Map()`         | Creates a branch (mini middleware pipeline) | URL Path         |
| `MapGet()`      | Registers a GET endpoint                    | GET + URL        |
| `MapPost()`     | Registers a POST endpoint                   | POST + URL       |
| `MapPut()`      | Registers a PUT endpoint                    | PUT + URL        |
| `MapDelete()`   | Registers a DELETE endpoint                 | DELETE + URL     |
| `MapFallback()` | Handles unmatched requests                  | No route matched |

---

# When Should You Use Each?

### Use `Map()`

When you want to create a **separate middleware pipeline**.

Example:

```csharp
app.Map("/admin", admin =>
{
    admin.Use(async (context, next) =>
    {
        Console.WriteLine("Admin Request");
        await next();
    });

    admin.Run(async context =>
    {
        await context.Response.WriteAsync("Admin Area");
    });
});
```

---

### Use `MapGet()`

When creating a **GET API**.

```csharp
app.MapGet("/products", () => "Products");
```

---

### Use `MapPost()`

When creating a **POST API**.

```csharp
app.MapPost("/products", () => "Product Created");
```

---

### Use `MapFallback()`

When you want a custom response for invalid URLs.

```csharp
app.MapFallback(() => "Sorry, this page does not exist.");
```

---

# Complete Flow Diagram

```text
                    Browser
                       │
                  HTTP Request
                       │
                       ▼
                  Routing Engine
                       │
      ┌────────────────┼─────────────────┐
      │                │                 │
      ▼                ▼                 ▼
   Map()          MapGet()          MapPost()
      │                │                 │
      │                │                 │
      └────────────┬───┴─────────────────┘
                   │
            Route Found?
          ┌────────┴────────┐
          │                 │
         Yes               No
          │                 │
          ▼                 ▼
     Execute Endpoint   MapFallback()
          │                 │
          └────────┬────────┘
                   ▼
             HTTP Response
                   │
                   ▼
                Browser
```

## Interview Questions

### What is the difference between `Map()` and `MapGet()`?

* **`Map()`** creates a **branch of the middleware pipeline** based on a URL path. Inside that branch you can register middleware (`Use`) and a terminal handler (`Run`).
* **`MapGet()`** registers a **GET endpoint**. It directly maps a GET request for a specific route to an endpoint.

### Can the same URL have both `MapGet()` and `MapPost()`?

Yes.

```csharp
app.MapGet("/student", () => "Get Student");

app.MapPost("/student", () => "Create Student");
```

The routing system distinguishes them by the **HTTP method**. A `GET /student` request executes the first endpoint, while a `POST /student` request executes the second.
