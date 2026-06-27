The **Query String** is one of the easiest and most common ways to send small pieces of data from the browser to the server.

It is widely used in:

* Search pages
* Product filtering
* Pagination
* Sorting
* Reporting
* REST APIs

In ASP.NET Core, query string values are available through:

```csharp
HttpContext.Request.Query
```

---

# What is a Query String?

A query string is the part of a URL that comes **after the `?` (question mark)**.

Example:

```text
https://localhost:7001/student?id=101&name=Raman
```

Let's break it down:

```text
https://localhost:7001/student?id=101&name=Raman
                           │
                           ▼
                     Query String
```

---

# Structure of a Query String

```text
https://localhost:7001/student?id=101&name=Raman
```

```text
/student
    │
    ├──────── Path

?id=101&name=Raman
│
├──────── Query String

id=101
│
├──────── Key = id
│         Value = 101

name=Raman
│
├──────── Key = name
│         Value = Raman
```

---

# How Query String Travels

Suppose the user types

```text
https://localhost:7001/student?id=101&name=Raman
```

The browser creates an HTTP request.

```http
GET /student?id=101&name=Raman HTTP/1.1
Host: localhost:7001
```

Notice

```text
?id=101&name=Raman
```

is part of the URL.

The server receives this request and creates

```text
HttpContext.Request
```

Inside it

```text
HttpContext.Request.Query
```

contains

```text
id = 101

name = Raman
```

---

# Request Flow

```text
Browser
     │
     │
https://localhost:7001/student?id=101&name=Raman
     │
     ▼
HTTP Request

GET /student?id=101&name=Raman
     │
     ▼
Kestrel
     │
Creates HttpContext
     │
     ▼
HttpContext.Request.Query
     │
     ▼
id = 101

name = Raman
```

---

# Example 1 – Read a Single Query String Value

Replace your `Program.cs` with:

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student", (HttpContext context) =>
{
    var id = context.Request.Query["id"];

    return $"Student Id = {id}";
});

app.Run();
```

Run the application.

Open

```text
https://localhost:7001/student?id=101
```

Output

```text
Student Id = 101
```

---

# What Happened?

Browser sends

```http
GET /student?id=101
```

ASP.NET Core creates

```text
HttpContext.Request.Query
```

which contains

```text
id = 101
```

Your code

```csharp
context.Request.Query["id"]
```

returns

```text
101
```

---

# Example 2 – Read Multiple Values

Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student", (HttpContext context) =>
{
    var id = context.Request.Query["id"];
    var name = context.Request.Query["name"];

    return $"""

Student Id : {id}

Student Name : {name}

""";
});

app.Run();
```

Open

```text
https://localhost:7001/student?id=101&name=Raman
```

Output

```text
Student Id : 101

Student Name : Raman
```

---

# Browser → Server

Browser URL

```text
https://localhost:7001/student?id=101&name=Raman
```

Browser sends

```http
GET /student?id=101&name=Raman HTTP/1.1
```

Server receives

```text
HttpContext.Request.Query
```

Which contains

```text
id

↓

101

------------------

name

↓

Raman
```

---

# Example 3 – Display Complete Query String

```csharp
app.MapGet("/", (HttpContext context) =>
{
    return context.Request.QueryString.ToString();
});
```

Open

```text
https://localhost:7001/?id=101&name=Raman
```

Output

```text
?id=101&name=Raman
```

Notice

```csharp
QueryString
```

returns the **entire query string**, while

```csharp
Query["id"]
```

returns the value of a specific parameter.

---

# Example 4 – Display All Query Parameters

```csharp
app.MapGet("/", (HttpContext context) =>
{
    string result = "";

    foreach (var item in context.Request.Query)
    {
        result += $"{item.Key} = {item.Value}\n";
    }

    return result;
});
```

Open

```text
https://localhost:7001/?id=101&name=Raman&city=Bangalore
```

Output

```text
id = 101

name = Raman

city = Bangalore
```

---

# Example 5 – Product Search

Suppose an e-commerce website.

User opens

```text
https://localhost:7001/products?category=Laptop&brand=Dell
```

Program

```csharp
app.MapGet("/products", (HttpContext context) =>
{
    var category = context.Request.Query["category"];

    var brand = context.Request.Query["brand"];

    return $"Category = {category}\nBrand = {brand}";
});
```

Output

```text
Category = Laptop

Brand = Dell
```

---

# Example 6 – Pagination

URL

```text
https://localhost:7001/products?page=3&pagesize=20
```

Code

```csharp
app.MapGet("/products", (HttpContext context) =>
{
    var page = context.Request.Query["page"];

    var pageSize = context.Request.Query["pagesize"];

    return $"Page = {page}\nPage Size = {pageSize}";
});
```

Output

```text
Page = 3

Page Size = 20
```

---

# What Happens in Chrome Developer Tools?

Open

```text
F12

↓

Network

↓

Refresh
```

Click the request.

Under **Headers** you'll see

```text
General

Request URL

https://localhost:7001/student?id=101&name=Raman
```

The browser is sending

```http
GET /student?id=101&name=Raman
```

Notice

```text
?id=101&name=Raman
```

This is exactly what ASP.NET Core reads using

```csharp
context.Request.Query
```

---

# Query vs QueryString

| Property              | Returns                         |
| --------------------- | ------------------------------- |
| `Request.Query`       | Individual key/value parameters |
| `Request.QueryString` | Complete query string           |

Example

```text
?id=101&name=Raman
```

```csharp
context.Request.QueryString
```

Output

```text
?id=101&name=Raman
```

Whereas

```csharp
context.Request.Query["id"]
```

returns

```text
101
```

---

# Complete Demo Program

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student", (HttpContext context) =>
{
    var id = context.Request.Query["id"];
    var name = context.Request.Query["name"];

    return Results.Text($"""

HTTP Query String Demo

-----------------------

Complete Query String : {context.Request.QueryString}

Student Id           : {id}

Student Name         : {name}

""");
});

app.Run();
```

Open

```text
https://localhost:7001/student?id=101&name=Raman
```

Output

```text
HTTP Query String Demo

-----------------------

Complete Query String : ?id=101&name=Raman

Student Id           : 101

Student Name         : Raman
```

---

# Complete Query String Flow

```text
                  Browser
                     │
                     │
https://localhost:7001/student?id=101&name=Raman
                     │
                     ▼
Creates HTTP Request

GET /student?id=101&name=Raman HTTP/1.1
                     │
                     ▼
              Kestrel Server
                     │
                     ▼
         Creates HttpContext
                     │
                     ▼
         HttpContext.Request
                     │
                     ▼
              Request.Query
             ┌──────────────┐
             │ id   = 101   │
             │ name = Raman │
             └──────────────┘
                     │
                     ▼
          ASP.NET Core Application
                     │
                     ▼
Reads:

context.Request.Query["id"]

context.Request.Query["name"]
                     │
                     ▼
Generates Response
                     │
                     ▼
Browser Displays Result
```

# Interview Points

* A **query string** is the part of the URL that appears after the `?` character.
* Multiple parameters are separated using the `&` character.
* Query string data is sent as part of the URL, so it is visible in the browser address bar.
* In ASP.NET Core, query string values are accessed using `HttpContext.Request.Query`.
* `Request.Query` lets you read individual parameters such as `id` or `name`, while `Request.QueryString` returns the complete query string exactly as it appeared in the URL.
* Query strings are ideal for optional parameters such as search text, filters, sorting, and pagination. They should **not** be used to send sensitive information like passwords because they are visible in the URL and may be logged or stored in browser history.
