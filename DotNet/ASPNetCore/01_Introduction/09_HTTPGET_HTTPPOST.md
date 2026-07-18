Understanding **GET** and **POST** is fundamental to web development and ASP.NET Core. They are the two most commonly used HTTP methods.

The main difference is:

* **GET** is used to **retrieve data**.
* **POST** is used to **send data** to the server, usually to create or process a resource.

---

# HTTP Methods Overview

```text
Browser
    │
    ├── GET  ─────────► Read Data
    │
    ├── POST ─────────► Send/Create Data
    │
    ├── PUT ──────────► Update Entire Resource
    │
    ├── PATCH ────────► Update Partial Resource
    │
    └── DELETE ───────► Delete Resource
```

---

# GET Request

A **GET** request asks the server to return information.

Think of it as asking a librarian:

> "Please show me the book."

The librarian shows the book but doesn't modify it.

---

## GET Example

Browser URL

```text
https://localhost:7001/student?id=101
```

HTTP Request

```http
GET /student?id=101 HTTP/1.1

Host: localhost:7001

User-Agent: Chrome

Accept: text/html
```

Notice:

* No request body
* Data travels in the URL

---

## GET Flow

```text
Browser

↓

GET /student?id=101

↓

Kestrel

↓

HttpContext.Request.Query

↓

ASP.NET Core

↓

Return Student Details
```

---

# ASP.NET Core GET Example

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

Open

```text
https://localhost:7001/student?id=101
```

Output

```text
Student Id = 101
```
## Example 2 (with HttpContext)
```
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", (HttpContext context) =>
{
    var id = context.Request.Query["id"];
    if (id == "1")
        return "All Well";
    else
        return "Not wel";
});
```
## Example 3 with HttpRequest which is HttpContext.HttpRequest
```
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", (HttpRequest request) =>
{
    var id = request.Query["id"];
    if (id == "1")
        return "All Well";
    else
        return "Not wel";
});

app.Run();
```
## Example 4 return all the query string keys and values
```
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", (HttpContext context) =>
{
    foreach (var item in context.Request.Query)
    {
        Console.WriteLine($"{item.Key} = {item.Value}");
    }

    return "Done";
});

app.Run();
```

## Example 5 with Multiple Get()
```
app.MapGet("/products", () => "All Products");

app.MapGet("/products/{id}", (int id) =>
{
    return $"Product Id = {id}";
});
```
## Example 6
```
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", () => "Home");

app.MapGet("/about", () => "About");

app.MapGet("/products", async (
    HttpRequest request,
    HttpResponse response) =>
{
    string id = request.Query["id"];

    if (id == "1")
    {
        await response.WriteAsync("Product Found");
    }
    else
    {
        await response.WriteAsync("Product Not Found");
    }
});

app.Run();
---
```
# POST Request

A **POST** request sends data to the server.

Think of filling out an admission form.

Instead of asking for information, you're **submitting** information.

---

Example

```text
Create Student

↓

POST

↓

Server stores data
```

---

# POST Flow

```text
Browser

↓

POST /student

↓

Request Body

↓

ASP.NET Core

↓

Save Student

↓

Return Success
```

---

# POST Request Structure

Unlike GET, POST has a **Request Body**.

```http
POST /student HTTP/1.1

Host: localhost:7001

Content-Type: application/json

Content-Length: 45

{
    "id":101,
    "name":"Raman"
}
```

Notice

The data is **inside the request body**, not in the URL.

---

# GET vs POST Visualization

## GET

```text
Browser

↓

URL

/student?id=101

↓

Server
```

Data travels in the URL.

---

## POST

```text
Browser

↓

POST /student

↓

Request Body

↓

{
   "id":101,
   "name":"Raman"
}

↓

Server
```

---

# Reading POST Body in ASP.NET Core

```csharp
app.MapPost("/student", async (HttpContext context) =>
{
    using var reader = new StreamReader(context.Request.Body);

    var body = await reader.ReadToEndAsync();

    return $"Received:\n{body}";
});
```

---

# Sending JSON

Using Postman

```json
{
    "id":101,
    "name":"Raman"
}
```

Response

```text
Received:

{
   "id":101,
   "name":"Raman"
}
```

---

# Different Types of POST Request Body

POST can send data in multiple formats.

```text
POST

│

├── JSON

├── XML

├── Form Data

├── x-www-form-urlencoded

└── Binary Files
```

---

# JSON Example

Headers

```http
Content-Type: application/json
```

Body

```json
{
    "id":101,
    "name":"Raman",
    "city":"Bangalore"
}
```

Most common format for REST APIs.

---

# XML Example

Headers

```http
Content-Type: application/xml
```

Body

```xml
<Student>

    <Id>101</Id>

    <Name>Raman</Name>

</Student>
```

Older enterprise applications commonly use XML.

---

# Form Data Example

Suppose a registration form.

```text
Name

Email

Password
```

Browser sends

```http
Content-Type: multipart/form-data
```

Request

```text
Name = Raman

Email = abc@gmail.com

Password = 12345
```

Common when uploading files.

---

# URL Encoded Form

```http
Content-Type: application/x-www-form-urlencoded
```

Body

```text
name=Raman&city=Bangalore
```

Frequently used by traditional HTML forms.

---

# Browser Example

Imagine a login page.

```text
Username

Password

[ Login ]
```

Browser sends

```http
POST /login HTTP/1.1

Content-Type: application/x-www-form-urlencoded

username=raman

password=123
```

---

# Chrome Developer Tools

Open

```text
F12

↓

Network
```

Submit a form.

Click the request.

You'll see

```text
Headers

Payload

Response
```

For GET

```text
Query String Parameters

id = 101
```

For POST

```text
Request Payload

{
   "id":101,

   "name":"Raman"
}
```

Notice

GET shows **Query String Parameters**

POST shows **Request Payload**

---

# Complete Demo Project

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student", (HttpContext context) =>
{
    var id = context.Request.Query["id"];

    return $"GET Student Id = {id}";
});

app.MapPost("/student", async (HttpContext context) =>
{
    using var reader = new StreamReader(context.Request.Body);

    var body = await reader.ReadToEndAsync();

    return $"POST Body:\n\n{body}";
});

app.Run();
```

---

## GET Request

Open

```text
https://localhost:7001/student?id=101
```

Output

```text
GET Student Id = 101
```

---

## POST Request

Using Postman

```
POST

https://localhost:7001/student
```

Body

```json
{
   "id":101,

   "name":"Raman"
}
```

Output

```text
POST Body

{
   "id":101,

   "name":"Raman"
}
```

---

# Chrome Network Comparison

## GET

```http
GET /student?id=101 HTTP/1.1

Host: localhost
```

No request body.

Network Tab

```text
Headers

↓

Query String Parameters

id = 101
```

---

## POST

```http
POST /student HTTP/1.1

Content-Type: application/json

{
   "id":101,

   "name":"Raman"
}
```

Network Tab

```text
Headers

↓

Request Payload

{

"id":101,

"name":"Raman"

}
```

---

# GET vs POST Comparison

| Feature             | GET                                       | POST                                                            |
| ------------------- | ----------------------------------------- | --------------------------------------------------------------- |
| Purpose             | Retrieve data                             | Send or create data                                             |
| Request Body        | ❌ No                                      | ✅ Yes                                                           |
| Data Location       | URL (Query String)                        | Request Body                                                    |
| Data Visible in URL | ✅ Yes                                     | ❌ No                                                            |
| Maximum Data Size   | Limited by URL length                     | Much larger (server limits apply)                               |
| Can Send JSON       | ❌ No                                      | ✅ Yes                                                           |
| Can Send XML        | ❌ No                                      | ✅ Yes                                                           |
| Can Send Form Data  | ❌ No                                      | ✅ Yes                                                           |
| Can Upload Files    | ❌ No                                      | ✅ Yes (`multipart/form-data`)                                   |
| Can Be Bookmarked   | ✅ Yes                                     | ❌ No                                                            |
| Browser Caching     | Usually cacheable                         | Typically not cached                                            |
| Typical Use Cases   | Search, filters, product details, reports | Login, registration, create records, file uploads, API requests |

---

# HTTP Message Comparison

## GET

```text
GET /student?id=101 HTTP/1.1
Host: localhost

(No Body)
```

## POST

```text
POST /student HTTP/1.1
Host: localhost
Content-Type: application/json

{
   "id":101,
   "name":"Raman"
}
```

---

# Complete Request Flow

```text
                Browser
                   │
        ┌──────────┴──────────┐
        │                     │
        ▼                     ▼
   GET Request           POST Request
        │                     │
        │                     │
 URL + Query String      Request Body
?id=101                  JSON/XML/Form Data
        │                     │
        └──────────┬──────────┘
                   ▼
              Kestrel Server
                   │
             Creates HttpContext
                   │
        ┌──────────┴──────────┐
        │                     │
        ▼                     ▼
Request.Query          Request.Body
        │                     │
        └──────────┬──────────┘
                   ▼
          ASP.NET Core Application
                   │
                   ▼
             HTTP Response
                   │
                   ▼
                Browser
```

## Interview Summary

* **GET** is used to **retrieve** data. Parameters are usually sent in the URL as a **query string** and are available in ASP.NET Core through `HttpContext.Request.Query`.
* **POST** is used to **send** data to the server. The data is placed in the **request body**, which can contain JSON, XML, form data (`multipart/form-data`), or URL-encoded form data (`application/x-www-form-urlencoded`).
* In ASP.NET Core, the POST body can be accessed through `HttpContext.Request.Body` or, more commonly in APIs, by model binding to C# objects.
