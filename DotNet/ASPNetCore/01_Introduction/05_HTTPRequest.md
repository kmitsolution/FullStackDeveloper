This is one of the most important topics in ASP.NET Core because **every request passes through this lifecycle**. Once you understand the HTTP Request flow, concepts like middleware, routing, controllers, model binding, and APIs become much easier.

---

# HTTP Request Lifecycle

When you type a URL in your browser:

```text
https://localhost:7001/products?id=10
```

The following happens:

```text
+-------------------+
| Browser (Chrome)  |
+-------------------+
          |
          | 1. Creates HTTP Request
          |
          v
+-------------------+
| Internet / Network|
+-------------------+
          |
          v
+-------------------+
| Kestrel Web Server|
+-------------------+
          |
          | Creates HttpContext
          |
          v
+---------------------------+
| ASP.NET Core Middleware   |
+---------------------------+
          |
          v
+---------------------------+
| Routing                   |
+---------------------------+
          |
          v
+---------------------------+
| Controller / Minimal API  |
+---------------------------+
          |
          v
+---------------------------+
| HTTP Response             |
+---------------------------+
          |
          v
+-------------------+
| Browser           |
+-------------------+
```

---

# What does an HTTP Request contain?

An HTTP Request has **4 major parts**:

```text
HTTP Request
│
├── Start Line (Request Line)
├── URL
├── Headers
└── Body (Optional)
```

---

# Example HTTP Request

Suppose the browser requests

```text
https://localhost:7001/products?id=10
```

The actual request looks similar to:

```http
GET /products?id=10 HTTP/1.1
Host: localhost:7001
User-Agent: Chrome
Accept: text/html
Accept-Language: en-US
Accept-Encoding: gzip
Connection: keep-alive

<No Body>
```

Let's understand every part.

---

# 1. Start Line (Request Line)

The first line tells the server **what the client wants**.

```http
GET /products?id=10 HTTP/1.1
```

It consists of three parts:

```text
GET
│
├── HTTP Method

/products?id=10
│
├── URL

HTTP/1.1
│
└── HTTP Version
```

---

## HTTP Method

The HTTP Method tells the server **what action to perform**.

| Method | Purpose                   |
| ------ | ------------------------- |
| GET    | Read data                 |
| POST   | Create data               |
| PUT    | Update entire resource    |
| PATCH  | Update part of a resource |
| DELETE | Delete resource           |

Example

```http
GET /students
```

means

> Give me the list of students.

---

# Accessing Method in ASP.NET Core

```csharp
app.MapGet("/", (HttpContext context) =>
{
    return $"Method : {context.Request.Method}";
});
```

Output

```text
Method : GET
```

---

# 2. URL

The URL identifies **which resource** is requested.

Example

```text
https://localhost:7001/products?id=10
```

The URL consists of:

```text
https://localhost:7001/products?id=10

│       │         │       │
│       │         │       └── Query String
│       │         │
│       │         └──────── Path
│       │
│       └────────────────── Host
│
└─────────────────────────── Scheme
```

---

## Scheme

```text
https
```

Protocol used.

Possible values:

```text
http

https
```

Read in ASP.NET Core

```csharp
context.Request.Scheme
```

---

## Host

```text
localhost:7001
```

Read

```csharp
context.Request.Host
```

---

## Path

```text
/products
```

Read

```csharp
context.Request.Path
```

---

## Query String

```text
?id=10
```

Read

```csharp
context.Request.Query["id"]
```

Output

```text
10
```

---

# Demo Program

```csharp
app.MapGet("/", (HttpContext context) =>
{
    return $"""

    Scheme : {context.Request.Scheme}

    Host : {context.Request.Host}

    Path : {context.Request.Path}

    Query : {context.Request.QueryString}

    """;
});
```

---

Open

```text
https://localhost:7001/?id=100
```

Output

```text
Scheme : https

Host : localhost:7001

Path : /

Query : ?id=100
```

---

# 3. Request Headers

Headers contain **additional information about the request**.

Example

```http
Host: localhost:7001

User-Agent: Chrome

Accept: text/html

Accept-Language: en-US

Accept-Encoding: gzip
```

Think of headers as **metadata**.

---

## Common Headers

### Host

```http
Host: localhost:7001
```

Server knows which website is requested.

Read

```csharp
context.Request.Headers["Host"]
```

---

### User-Agent

```http
User-Agent: Chrome
```

Identifies the browser.

Read

```csharp
context.Request.Headers["User-Agent"]
```

Example Output

```text
Mozilla/5.0 Chrome
```

---

### Accept

```http
Accept: text/html
```

The browser says

> "I can understand HTML."

If calling an API

```http
Accept: application/json
```

---

### Accept-Language

```http
Accept-Language: en-US
```

Preferred language.

---

### Accept-Encoding

```http
Accept-Encoding: gzip
```

Browser supports compressed responses.

---

# Display All Headers

```csharp
app.MapGet("/", (HttpContext context) =>
{
    string result = "";
    foreach (var header in context.Request.Headers)
    {
        result += $"{header.Key} : {header.Value}\n";
    }
    return result;
});
app.Run();
```

Console

```text
Host : localhost:7001

User-Agent : Chrome

Accept : text/html

Accept-Encoding : gzip

Connection : keep-alive
```

---

# 4. Request Body

The Body contains the **actual data** sent by the client.

Usually used with

```text
POST

PUT

PATCH
```

Example

```http
POST /students HTTP/1.1

Content-Type: application/json

{
    "name":"Raman",
    "age":30
}
```

The JSON is the **Request Body**.

---

Read Body

```csharp
using var reader = new StreamReader(context.Request.Body);

string body = await reader.ReadToEndAsync();
```

Output

```text
{
   "name":"Raman",
   "age":30
}
```

---

# Request Flow Inside ASP.NET Core

```text
Browser

↓

HTTP Request

↓

Kestrel

↓

Creates HttpContext

↓

HttpContext.Request

↓

Middleware

↓

Routing

↓

Controller

↓

Response
```

The `HttpContext.Request` object contains everything from the incoming request.

---

# HttpContext.Request Properties

| Property        | Description             | Example            |
| --------------- | ----------------------- | ------------------ |
| `Method`        | HTTP Method             | GET                |
| `Scheme`        | http / https            | https              |
| `Host`          | Server Name             | localhost:7001     |
| `Path`          | URL Path                | /products          |
| `QueryString`   | Complete query          | ?id=10             |
| `Query`         | Individual query values | `Query["id"]`      |
| `Headers`       | All request headers     | User-Agent         |
| `Body`          | Request body stream     | JSON               |
| `ContentType`   | Body format             | application/json   |
| `ContentLength` | Body size               | 120 bytes          |
| `Cookies`       | Request cookies         | SessionId          |
| `Protocol`      | HTTP version            | HTTP/1.1 or HTTP/2 |

---

# Complete Demo

Replace your `Program.cs` with the following:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", (HttpContext context) =>
{
    return Results.Text($"""
Method         : {context.Request.Method}
Scheme         : {context.Request.Scheme}
Host           : {context.Request.Host}
Path           : {context.Request.Path}
Protocol       : {context.Request.Protocol}
Query String   : {context.Request.QueryString}
User-Agent     : {context.Request.Headers["User-Agent"]}
Accept         : {context.Request.Headers["Accept"]}
Accept-Language: {context.Request.Headers["Accept-Language"]}
""");
});

app.Run();
```

Now browse to:

```text
https://localhost:7001/?id=101&name=Raman
```

Sample output:

```text
Method         : GET
Scheme         : https
Host           : localhost:7001
Path           : /
Protocol       : HTTP/2
Query String   : ?id=101&name=Raman
User-Agent     : Mozilla/5.0 ...
Accept         : text/html,...
Accept-Language: en-US,en;q=0.9
```

---

# Complete Request Lifecycle Diagram

```text
┌──────────────────────────────────────────────┐
│              Google Chrome                   │
└──────────────────────────────────────────────┘
                │
                │ Type URL
                ▼
https://localhost:7001/products?id=10
                │
                ▼
Creates HTTP Request
                │
                ▼
GET /products?id=10 HTTP/1.1
Host: localhost:7001
User-Agent: Chrome
Accept: text/html
                │
                ▼
           Kestrel Server
                │
     Creates HttpContext
                │
                ▼
      HttpContext.Request
      ├── Method
      ├── Scheme
      ├── Host
      ├── Path
      ├── Query
      ├── Headers
      ├── Cookies
      └── Body
                │
                ▼
 ASP.NET Core Middleware Pipeline
                │
                ▼
      Routing → Endpoint
                │
                ▼
     Controller / Minimal API
                │
                ▼
       Generates Response
                │
                ▼
      HttpContext.Response
                │
                ▼
 Browser Receives HTTP Response
```

This demonstration is ideal for training because students can **run the application, open Chrome Developer Tools (F12 → Network), inspect the request**, and directly match what they see in the browser (request line, URL, headers, and query string) with the values available through `HttpContext.Request` in ASP.NET Core.
