`HttpContext` is one of the **most important classes in ASP.NET Core**. Every HTTP request that reaches your application gets its own `HttpContext` object.

You can think of it as a **container that holds all the information about the current HTTP request and response**.

---

# Real-Life Analogy

Imagine you order food online.

When you place an order, the restaurant receives a packet containing:

* Your name
* Delivery address
* Phone number
* Items ordered
* Payment method

The restaurant prepares the food and sends back:

* Order confirmation
* Invoice
* Food

Similarly, in ASP.NET Core:

```text
Browser

↓

HTTP Request

↓

HttpContext

↓

ASP.NET Core Application

↓

HTTP Response

↓

Browser
```

The `HttpContext` acts like the packet that carries everything related to that request.

---

# Why is it called HttpContext?

Break the name into two parts:

**HTTP**

It refers to the **HyperText Transfer Protocol**, which browsers and servers use to communicate.

Example:

```http
GET /products HTTP/1.1
Host: localhost:5000
```

Everything sent between the client and server uses HTTP (or HTTPS).

---

**Context**

"Context" means **all the information available about the current operation**.

For an HTTP request, that includes:

* Who made the request
* Which URL was requested
* Which HTTP method was used
* Request headers
* Cookies
* Query string
* Route values
* Request body
* User identity
* Session
* Response object

So:

```text
HTTP + Context

↓

All information related to the current HTTP request and response
```

---

# Lifecycle of HttpContext

For every incoming request:

```text
Client Request
        │
        ▼
Kestrel receives request
        │
Creates HttpContext
        │
Middleware
        │
Controller / Minimal API
        │
Response generated
        │
HttpContext destroyed
```

A new `HttpContext` is created for each request and discarded after the response is sent.

---

# What's Inside HttpContext?

Some commonly used properties are:

```text
HttpContext
│
├── Request
├── Response
├── User
├── Session
├── Connection
├── RequestServices
├── TraceIdentifier
├── Items
└── WebSockets
```

Let's look at the important ones.

---

# 1. Request

Contains everything the client sent.

```csharp
app.MapGet("/", (HttpContext context) =>
{
    return context.Request.Method;
});
```

Output:

```text
GET
```

---

You can also access the URL:

```csharp
context.Request.Path
```

Example:

```text
/products
```

---

HTTP Method:

```csharp
context.Request.Method
```

Example:

```text
GET
POST
PUT
DELETE
```

---

Query String:

URL

```text
/products?id=10
```

Code:

```csharp
context.Request.Query["id"]
```

Output

```text
10
```

---

Headers:

```csharp
context.Request.Headers
```

Example:

```text
User-Agent

Accept

Authorization
```

---

Cookies:

```csharp
context.Request.Cookies
```

---

# 2. Response

Used to send data back to the client.

Example:

```csharp
app.MapGet("/", async (HttpContext context) =>
{
    await context.Response.WriteAsync("Welcome");
});
```

Browser:

```text
Welcome
```

---

Set Status Code

```csharp
context.Response.StatusCode = 404;
```

---

Set Header

```csharp
context.Response.Headers["Company"] = "KMIT";
```

---

# 3. User

Contains authenticated user information.

```csharp
context.User.Identity?.Name
```

Example:

```text
Raman
```

---

Check Authentication

```csharp
context.User.Identity?.IsAuthenticated
```

Output:

```text
True
```

---

# 4. Session

Stores user-specific data between requests.

Example:

```csharp
context.Session.SetString("Name", "Raman");
```

Retrieve:

```csharp
context.Session.GetString("Name");
```

---

# 5. Connection

Contains client connection information.

Example:

```csharp
context.Connection.RemoteIpAddress
```

Output:

```text
192.168.1.20
```

---

# 6. Items

A temporary dictionary for sharing data during a single request.

```csharp
context.Items["Company"] = "KMIT";
```

Another middleware or controller can access it later in the same request.

After the request completes, the data is removed.

---

# Example Project

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", (HttpContext context) =>
{
    return $"Method : {context.Request.Method}";
});

app.Run();
```

Browser:

```text
Method : GET
```

---

Another Example

```csharp
app.MapGet("/", (HttpContext context) =>
{
    return $"Path : {context.Request.Path}";
});
```

Open

```text
http://localhost:5000
```

Output

```text
Path : /
```

---

Example with Query String

URL

```text
http://localhost:5000?id=101
```

Code

```csharp
app.MapGet("/", (HttpContext context) =>
{
    return $"Id = {context.Request.Query["id"]}";
});
```

Output

```text
Id = 101
```

---

# How Does HttpContext Reach Your Code?

When a request arrives:

```text
Browser

↓

Kestrel

↓

Creates HttpContext

↓

Middleware

↓

Controller

↓

Minimal API

↓

Response
```

ASP.NET Core automatically passes the current `HttpContext` to your middleware, controllers, or minimal API handlers.

For example:

```csharp
app.MapGet("/", (HttpContext context) =>
{
    return "Hello";
});
```

You don't create the `HttpContext` yourself—ASP.NET Core creates it and injects it into your code.

---

# Is HttpContext Shared?

No.

Suppose three users access your application simultaneously:

```text
User A

↓

HttpContext A

----------------------

User B

↓

HttpContext B

----------------------

User C

↓

HttpContext C
```

Each request gets its own `HttpContext`, so data from one user's request isn't mixed with another's.

---

# Summary

| Term            | Meaning                                                                                                                                       |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **HTTP**        | The protocol used for communication between client and server                                                                                 |
| **Context**     | All the information related to the current request and response                                                                               |
| **HttpContext** | An object that contains the current request, response, user, connection, session, headers, cookies, services, and other request-specific data |
| **Created By**  | ASP.NET Core (via Kestrel) for every incoming request                                                                                         |
| **Lifetime**    | Exists only for the duration of a single HTTP request                                                                                         |
| **Common Uses** | Reading request data, writing responses, checking authentication, accessing headers, cookies, session, and client connection details          |

### Simple definition for interviews

> **HttpContext is an ASP.NET Core object that represents the current HTTP request and response. It provides access to request information (such as headers, query strings, cookies, and the request body), response information (status codes and headers), the authenticated user, session data, and other request-specific information. A new `HttpContext` is created for every incoming request and is disposed after the response is sent.**
