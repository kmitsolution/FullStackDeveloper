Routing is one of the **core features** of ASP.NET Core. It determines **which piece of code should execute** when a request arrives.

Without routing, the web server receives the request but doesn't know **which endpoint should handle it**.

---

# What is Routing?

**Routing** is the process of matching an incoming HTTP request (URL + HTTP Method) to the correct endpoint.

An endpoint can be:

* Minimal API
* Controller Action
* Razor Page
* SignalR Hub

Think of routing as a receptionist in an office.

---

# Real-Life Analogy

Suppose a company has different departments.

```text
Visitor
    │
    ▼
Reception
    │
    ├── HR Department
    │
    ├── Finance Department
    │
    ├── IT Department
    │
    └── Sales Department
```

The receptionist looks at your request and sends you to the correct department.

Routing does exactly the same.

---

# Without Routing

Imagine a browser sends:

```text
https://localhost:5001/products
```

The server receives the request.

```text
Browser

↓

Kestrel

↓

Application

↓

?????
```

Which code should execute?

Without routing,

ASP.NET Core doesn't know.

---

# With Routing

```text
Browser

↓

Kestrel

↓

Routing

↓

/products

↓

Products Endpoint

↓

Response
```

Routing decides which endpoint should execute.

---

# Request Flow

```text
Browser

↓

HTTP Request

↓

Kestrel

↓

Middleware Pipeline

↓

Routing

↓

Endpoint

↓

HTTP Response

↓

Browser
```

---

# Routing Matches Two Things

Routing looks at

1. HTTP Method
2. URL

Example

```http
GET /products
```

Routing checks

```text
Method

↓

GET
```

and

```text
URL

↓

/products
```

Then searches for a matching endpoint.

---

# Endpoint

An endpoint is simply the code that handles the request.

Example

```csharp
app.MapGet("/products", () =>
{
    return "Products";
});
```

Here,

```text
/products
```

is the route.

The lambda

```csharp
() => "Products"
```

is the endpoint.

---

# Minimal API Routing

Create an empty project.

Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", () => "Home Page");

app.MapGet("/about", () => "About Us");

app.MapGet("/contact", () => "Contact Us");

app.Run();
```

---

# Routing Table

Internally ASP.NET Core creates something similar to:

| HTTP Method | URL      | Endpoint   |
| ----------- | -------- | ---------- |
| GET         | /        | Home Page  |
| GET         | /about   | About Us   |
| GET         | /contact | Contact Us |

---

# Request 1

Browser

```text
GET /

```

Routing

```text
Match

/

↓

Home Endpoint
```

Response

```text
Home Page
```

---

# Request 2

Browser

```text
GET /about
```

Routing

```text
/about

↓

About Endpoint
```

Response

```text
About Us
```

---

# Request 3

```text
GET /contact
```

Routing

```text
/contact

↓

Contact Endpoint
```

Response

```text
Contact Us
```

---

# Visual Representation

```text
                Browser
                   │
                   ▼
           GET /about
                   │
                   ▼
             Kestrel
                   │
                   ▼
             Routing
                   │
        ┌──────────┼──────────┐
        │          │          │
        ▼          ▼          ▼
       /       /about    /contact
        │          │          │
        │          ▼          │
        │      About API      │
        │                     │
        └─────────┬───────────┘
                  ▼
             HTTP Response
                  │
                  ▼
               Browser
```

---

# HTTP Method Matters

Suppose

```csharp
app.MapGet("/student", () =>
{
    return "Student List";
});

app.MapPost("/student", () =>
{
    return "Student Created";
});
```

Same URL

```text
/student
```

Different HTTP methods.

---

### GET Request

```http
GET /student
```

Matches

```csharp
MapGet()
```

Output

```text
Student List
```

---

### POST Request

```http
POST /student
```

Matches

```csharp
MapPost()
```

Output

```text
Student Created
```

Same URL

Different Method

Different Endpoint

---

# Routing Decision

```text
Incoming Request

↓

Method = GET

↓

URL = /student

↓

Search Route Table

↓

MapGet("/student")

↓

Execute
```

---

# Available Map Methods

| Method        | Purpose        |
| ------------- | -------------- |
| `MapGet()`    | GET request    |
| `MapPost()`   | POST request   |
| `MapPut()`    | PUT request    |
| `MapDelete()` | DELETE request |
| `MapPatch()`  | PATCH request  |

---

Example

```csharp
app.MapGet("/products", () => "Get Products");

app.MapPost("/products", () => "Create Product");

app.MapPut("/products", () => "Update Product");

app.MapDelete("/products", () => "Delete Product");
```

---

# Route Parameters

Suppose

```text
/student/101
```

Routing

```csharp
app.MapGet("/student/{id}", (int id) =>
{
    return $"Student Id = {id}";
});
```

Open

```text
https://localhost:5001/student/101
```

Output

```text
Student Id = 101
```

Routing automatically extracts

```text
101
```

and passes it to

```csharp
(int id)
```

---

# Multiple Routes

```csharp
app.MapGet("/", () => "Home");

app.MapGet("/products", () => "Products");

app.MapGet("/orders", () => "Orders");

app.MapGet("/students", () => "Students");
```

Routing table

```text
/

↓

Home

-----------------

/products

↓

Products

-----------------

/orders

↓

Orders

-----------------

/students

↓

Students
```

---

# Browser Request Flow

```text
Browser

↓

GET /products

↓

Kestrel

↓

Routing

↓

MapGet("/products")

↓

Execute Lambda

↓

Return Response

↓

Browser
```

---

# What Happens When No Route Matches?

Suppose

```text
GET /employee
```

Application only has

```csharp
app.MapGet("/student", () => "Student");
```

Routing

```text
Search

↓

No Match

↓

404 Not Found
```

Browser

```text
404 Not Found
```

---

# Routing Pipeline

```text
                 Browser
                    │
              HTTP Request
                    │
                    ▼
             Kestrel Server
                    │
                    ▼
        Middleware Pipeline
                    │
                    ▼
               Routing
                    │
     Match Method + URL
                    │
       ┌────────────┼────────────┐
       │            │            │
       ▼            ▼            ▼
      /         /about     /products
       │            │            │
       ▼            ▼            ▼
 Home Endpoint About Endpoint Product Endpoint
       │            │            │
       └────────────┼────────────┘
                    ▼
              HTTP Response
                    │
                    ▼
                 Browser
```

---

# How Routing Works Internally

Suppose you define:

```csharp
app.MapGet("/", () => "Home");

app.MapGet("/products", () => "Products");

app.MapPost("/products", () => "Create Product");
```

ASP.NET Core internally builds a routing table similar to this:

```text
Method      Route          Endpoint

GET         /              Home

GET         /products      Products

POST        /products      Create Product
```

Every request is compared against this table.

---

# Complete Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", () => "Welcome");

app.MapGet("/students", () => "Student List");

app.MapGet("/courses", () => "Course List");

app.MapPost("/students", () => "Student Created");

app.Run();
```

### Test

| Request        | Response        |
| -------------- | --------------- |
| GET /          | Welcome         |
| GET /students  | Student List    |
| GET /courses   | Course List     |
| POST /students | Student Created |
| GET /unknown   | 404 Not Found   |

---

# ASP.NET Core 6 and Automatic Routing

One of the biggest improvements in **ASP.NET Core 6 Minimal APIs** is that **routing is configured automatically** for endpoint mapping methods like `MapGet()`, `MapPost()`, `MapPut()`, and `MapDelete()`.

In older ASP.NET Core versions (before the minimal hosting model), you typically wrote:

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
});
```

In ASP.NET Core 6 Minimal APIs, you simply write:

```csharp
app.MapGet("/", () => "Hello World");

app.MapGet("/products", () => "Products");

app.Run();
```

The framework automatically configures the routing middleware behind the scenes, making `Program.cs` much simpler.

---

# Routing Summary

| Concept           | Description                                                                  |
| ----------------- | ---------------------------------------------------------------------------- |
| Routing           | Matches an incoming HTTP request to an endpoint                              |
| Matching Criteria | HTTP Method + URL                                                            |
| Endpoint          | The code that handles the request                                            |
| `MapGet()`        | Handles GET requests                                                         |
| `MapPost()`       | Handles POST requests                                                        |
| `MapPut()`        | Handles PUT requests                                                         |
| `MapDelete()`     | Handles DELETE requests                                                      |
| Route Parameter   | Captures values from the URL, e.g., `/student/{id}`                          |
| No Matching Route | Returns **404 Not Found**                                                    |
| ASP.NET Core 6    | Minimal APIs automatically configure routing when you use `Map...()` methods |

---

# Interview Questions

### 1. What is routing?

**Answer:**

Routing is the process of matching an incoming HTTP request based on its **HTTP method** and **URL** to the correct endpoint in an ASP.NET Core application.

---

### 2. What is an endpoint?

**Answer:**

An endpoint is the code that executes when a route matches. It can be a Minimal API handler, a controller action, a Razor Page, or another request handler.

---

### 3. What does `MapGet()` do?

**Answer:**

`MapGet()` registers an endpoint that handles HTTP **GET** requests for a specified URL pattern.

Example:

```csharp
app.MapGet("/products", () => "Product List");
```

---

### 4. What happens if no route matches?

**Answer:**

ASP.NET Core returns an **HTTP 404 Not Found** response because it cannot find an endpoint that matches the requested URL and HTTP method.
