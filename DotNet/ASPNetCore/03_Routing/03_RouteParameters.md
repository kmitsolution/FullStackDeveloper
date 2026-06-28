Route Parameters are one of the most frequently used features in ASP.NET Core because they allow values to be passed **as part of the URL path** instead of the query string.

For example:

Instead of

```text
https://localhost:5001/student?id=101
```

we can create a cleaner URL:

```text
https://localhost:5001/student/101
```

Here,

```text
101
```

is a **Route Parameter**.

---

# What is a Route Parameter?

A route parameter is a **variable part of a URL** enclosed in **curly braces `{}`**.

Example

```text
/student/{id}
```

Here,

```text
{id}
```

is a route parameter.

When a request arrives,

```text
/student/101
```

ASP.NET Core automatically extracts

```text
id = 101
```

---

# Route Template

A route consists of:

* Fixed (Literal) segments
* Variable (Parameter) segments

Example

```text
/student/{id}
```

Breakdown

```text
student
   │
   ├──────── Fixed Literal

{id}
   │
   └──────── Route Parameter
```

---

# Request Flow

```text
Browser

↓

GET /student/101

↓

Routing

↓

Route Template

/student/{id}

↓

id = 101

↓

Endpoint

↓

Response
```

---

# Example 1 - Single Route Parameter

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student/{id}", (int id) =>
{
    return $"Student Id = {id}";
});

app.Run();
```

---

## Test

Open

```text
https://localhost:5001/student/101
```

Output

```text
Student Id = 101
```

---

## What Happened?

Incoming request

```text
/student/101
```

Routing compares

```text
/student/{id}
```

Match

```text
student

↓

Literal Match

101

↓

{id}
```

Routing extracts

```text
id = 101
```

and passes it to

```csharp
(int id)
```

---

# Visual Representation

```text
Browser

↓

/student/101

↓

Routing

↓

student

↓

{id}

↓

id = 101

↓

Endpoint
```

---

# Example 2 - Multiple Route Parameters

```csharp
app.MapGet("/student/{id}/{name}",
    (int id, string name) =>
{
    return $"Id = {id}\nName = {name}";
});
```

---

Open

```text
https://localhost:5001/student/101/Raman
```

Output

```text
Id = 101

Name = Raman
```

---

Routing

```text
/student/101/Raman

↓

student

↓

{id}

↓

{name}
```

---

# Example 3 - Product Details

```csharp
app.MapGet("/products/{id}",
(int id) =>
{
    return $"Product Id = {id}";
});
```

Open

```text
/products/500
```

Output

```text
Product Id = 500
```

---

# Fixed Literals vs Route Parameters

Suppose

```text
/orders/{id}
```

Breakdown

```text
orders

↓

Fixed Literal

{id}

↓

Variable
```

Request

```text
/orders/900
```

Match

```text
orders

↓

Literal

900

↓

{id}
```

---

# Example 4 - File Route

Suppose files are accessed like

```text
/files/report.pdf
```

Route

```csharp
app.MapGet("/files/{filename}.{ext}",
(string filename,
 string ext) =>
{
    return $"""

Filename = {filename}

Extension = {ext}

""";
});
```

---

Request

```text
/files/report.pdf
```

Output

```text
Filename = report

Extension = pdf
```

---

Request

```text
/files/photo.png
```

Output

```text
Filename = photo

Extension = png
```

---

Visual

```text
/files/report.pdf

↓

files

↓

{filename}

↓

report

↓

{ext}

↓

pdf
```

---

# Example 5 - Three Parameters

```csharp
app.MapGet(
"/country/{country}/state/{state}/city/{city}",
(string country,
 string state,
 string city) =>
{
    return $"""

Country : {country}

State : {state}

City : {city}

""";
});
```

Open

```text
/country/India/state/Karnataka/city/Bangalore
```

Output

```text
Country : India

State : Karnataka

City : Bangalore
```

---

# Reading Route Values Using HttpContext

Instead of parameters,

you can access

```text
Route Values
```

Program

```csharp
app.MapGet("/student/{id}",
(HttpContext context) =>
{
    var id =
        context.Request.RouteValues["id"];

    return $"Student Id = {id}";
});
```

Open

```text
/student/101
```

Output

```text
Student Id = 101
```

---

# Multiple Route Values

```csharp
app.MapGet("/student/{id}/{name}",
(HttpContext context) =>
{
    var id =
        context.Request.RouteValues["id"];

    var name =
        context.Request.RouteValues["name"];

    return $"""

Id : {id}

Name : {name}

""";
});
```

Request

```text
/student/101/Raman
```

Output

```text
Id : 101

Name : Raman
```

---

# Route Values Dictionary

Internally

```text
/student/101/Raman
```

becomes

```text
Key            Value

id             101

name           Raman
```

Exactly like

```text
Dictionary
```

---

# Route Parameters vs Query String

### Route Parameter

```text
/student/101
```

Read using

```csharp
context.Request.RouteValues["id"]
```

---

### Query String

```text
/student?id=101
```

Read using

```csharp
context.Request.Query["id"]
```

---

Comparison

| Route Parameter                | Query String                           |
| ------------------------------ | -------------------------------------- |
| `/student/101`                 | `/student?id=101`                      |
| Part of URL path               | Appears after `?`                      |
| `RouteValues`                  | `Query`                                |
| Used for identifying resources | Used for filtering, searching, sorting |

---

# Complete Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/files/{filename}.{ext}",
(HttpContext context) =>
{
    var filename =
        context.Request.RouteValues["filename"];

    var extension =
        context.Request.RouteValues["ext"];

    return Results.Text($"""

File Name : {filename}

Extension : {extension}

""");
});

app.Run();
```

---

Open

```text
/files/report.pdf
```

Output

```text
File Name : report

Extension : pdf
```

---

# Route Matching Process

```text
Browser

↓

GET /files/report.pdf

↓

Routing

↓

files

↓

{filename}

↓

report

↓

{ext}

↓

pdf

↓

RouteValues

↓

filename = report

ext = pdf

↓

Endpoint

↓

Response
```

---

# Complete Flow Diagram

```text
                 Browser
                    │
      GET /files/report.pdf
                    │
                    ▼
             Kestrel Server
                    │
                    ▼
                Routing
                    │
      Match Route Template
/files/{filename}.{ext}
                    │
        ┌───────────┴───────────┐
        │                       │
        ▼                       ▼
 filename = report        ext = pdf
        │                       │
        └───────────┬───────────┘
                    ▼
        Request.RouteValues
                    │
                    ▼
               Endpoint
                    │
                    ▼
             HTTP Response
                    │
                    ▼
                 Browser
```

---

---

# Summary

| Route Template                     | URL                              | Extracted Values                       |
| ---------------------------------- | -------------------------------- | -------------------------------------- |
| `/student/{id}`                    | `/student/101`                   | `id = 101`                             |
| `/student/{id}/{name}`             | `/student/101/Raman`             | `id = 101`, `name = Raman`             |
| `/files/{filename}.{ext}`          | `/files/report.pdf`              | `filename = report`, `ext = pdf`       |
| `/country/{country}/state/{state}` | `/country/India/state/Karnataka` | `country = India`, `state = Karnataka` |

---

# Interview Questions

### 1. What is a route parameter?

A route parameter is a variable part of a URL enclosed in curly braces `{}`. ASP.NET Core extracts its value from the URL and makes it available to the endpoint.

Example:

```text
/student/{id}
```

---

### 2. How do you read route parameters?

There are two common ways:

**Method parameters (recommended):**

```csharp
app.MapGet("/student/{id}", (int id) =>
{
    return $"Id = {id}";
});
```

**Using `HttpContext`:**

```csharp
var id = context.Request.RouteValues["id"];
```

---

### 3. What is the difference between a route parameter and a query string?

| Route Parameter                         | Query String                                                   |
| --------------------------------------- | -------------------------------------------------------------- |
| `/student/101`                          | `/student?id=101`                                              |
| Part of the URL path                    | Comes after `?`                                                |
| Accessed using `RouteValues`            | Accessed using `Request.Query`                                 |
| Commonly identifies a specific resource | Commonly used for filtering, sorting, searching, or pagination |
