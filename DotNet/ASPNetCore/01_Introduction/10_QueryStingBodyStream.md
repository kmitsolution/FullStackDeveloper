This topic is very important because it teaches how ASP.NET Core receives data sent by the client. Before using model binding (`Student student`), it's useful to understand what happens internally.

In ASP.NET Core, the request body is available as a **Stream**, so you read it using a `StreamReader`.

---

# HTTP Request Body

When the client sends a POST request, the data is placed in the **Request Body**.

Example:

```http
POST /student?id=101&name=Raman HTTP/1.1

Host: localhost:7001

Content-Type: application/json

{
    "course":"ASP.NET Core",
    "duration":"40 Hours"
}
```

Notice there are **two types of data**:

1. Query String

```text
?id=101&name=Raman
```

2. Request Body

```json
{
    "course":"ASP.NET Core",
    "duration":"40 Hours"
}
```

---

# Request Flow

```text
             Browser

                 │

POST /student?id=101&name=Raman

                 │

          Request Body

                 │

                 ▼

           Kestrel Server

                 │

        Creates HttpContext

                 │

        ┌────────┴─────────┐
        │                  │
        ▼                  ▼

 Request.Query        Request.Body

        │                  │

        └────────┬─────────┘

                 ▼

        ASP.NET Core Application
```

---

# Why is Request.Body a Stream?

The request body can contain:

* Small JSON (100 bytes)
* Large JSON (10 MB)
* File Upload (500 MB)
* Video Upload (2 GB)

Keeping everything in memory is inefficient, so ASP.NET Core exposes the body as a **Stream**.

```text
Request.Body

↓

Stream
```

A stream reads data sequentially.

---

# Reading the Request Body

Create an empty project.

Replace `Program.cs` with:

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapPost("/student", async (HttpContext context) =>
{
    using var reader = new StreamReader(context.Request.Body);

    string body = await reader.ReadToEndAsync();

    return Results.Text(body);
});

app.Run();
```

---

# Understanding the Code

## Step 1

```csharp
context.Request.Body
```

Returns

```text
Stream
```

---

## Step 2

```csharp
new StreamReader(context.Request.Body)
```

Wraps the stream inside a `StreamReader` so it can be read as text.

---

## Step 3

```csharp
await reader.ReadToEndAsync();
```

Reads the complete stream asynchronously.

Example

```json
{
   "id":101,
   "name":"Raman"
}
```

---

# Testing with Postman

Method

```text
POST
```

URL

```text
https://localhost:7001/student
```

Headers

```text
Content-Type

application/json
```

Body

```json
{
    "id":101,
    "name":"Raman"
}
```

Response

```json
{
    "id":101,
    "name":"Raman"
}
```

The application simply echoes the request body.

---

# Browser Network Tab

Open

```text
F12

↓

Network
```

Submit the request.

You'll see

```text
Request Payload

{

"id":101,

"name":"Raman"

}
```

This is exactly what

```csharp
ReadToEndAsync()
```

reads.

---

# Reading the Query String

Suppose the request is

```text
POST

https://localhost:7001/student?id=101&name=Raman
```

The body contains

```json
{
   "course":"ASP.NET Core"
}
```

The URL still contains

```text
?id=101&name=Raman
```

You can read it like this:

```csharp
var id = context.Request.Query["id"];

var name = context.Request.Query["name"];
```

Output

```text
101

Raman
```

---

# Reading Both Query String and Body

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapPost("/student", async (HttpContext context) =>
{
    var id = context.Request.Query["id"];
    var name = context.Request.Query["name"];

    using var reader = new StreamReader(context.Request.Body);

    var body = await reader.ReadToEndAsync();

    return Results.Text($"""

Id : {id}

Name : {name}

Body

{body}

""");
});

app.Run();
```

Request

```text
POST

https://localhost:7001/student?id=101&name=Raman
```

Body

```json
{
    "course":"ASP.NET Core",
    "duration":"40 Hours"
}
```

Response

```text
Id : 101

Name : Raman

Body

{
   "course":"ASP.NET Core",
   "duration":"40 Hours"
}
```

---

# Parsing a Query String with `QueryHelpers`

Normally, you use:

```csharp
context.Request.Query
```

However, sometimes you have a raw query string and want to parse it manually.

ASP.NET Core provides the `QueryHelpers` class.

Namespace:

```csharp
using Microsoft.AspNetCore.WebUtilities;
```

Example:

```csharp
using Microsoft.AspNetCore.WebUtilities;

string query = "?id=101&name=Raman";

var values = QueryHelpers.ParseQuery(query);

foreach (var item in values)
{
    Console.WriteLine($"{item.Key} = {item.Value}");
}
```

Output

```text
id = 101

name = Raman
```

---

# Using `QueryHelpers` with the Current Request

```csharp
using Microsoft.AspNetCore.WebUtilities;

app.MapPost("/student", async (HttpContext context) =>
{
    var queryDictionary =
        QueryHelpers.ParseQuery(context.Request.QueryString.ToString());

    var id = queryDictionary["id"];

    var name = queryDictionary["name"];

    using var reader =
        new StreamReader(context.Request.Body);

    var body =
        await reader.ReadToEndAsync();

    return Results.Text($"""

Id : {id}

Name : {name}

Body

{body}

""");
});
```

---

# What Does `ParseQuery()` Return?

It returns a dictionary-like collection.

```text
Dictionary

↓

Key

↓

Value
```

Example

```text
?id=101&name=Raman&city=Bangalore
```

becomes

```text
Key         Value

id          101

name        Raman

city        Bangalore
```

---

# Accessing Values

```csharp
queryDictionary["id"]
```

returns

```text
101
```

---

```csharp
queryDictionary["name"]
```

returns

```text
Raman
```

---

# Complete Example

```csharp
using Microsoft.AspNetCore.WebUtilities;

var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapPost("/student", async (HttpContext context) =>
{
    // Parse Query String
    var query =
        QueryHelpers.ParseQuery(
            context.Request.QueryString.ToString());

    // Read Body
    using var reader =
        new StreamReader(context.Request.Body);

    var body =
        await reader.ReadToEndAsync();

    return Results.Text($"""

Method : {context.Request.Method}

Id : {query["id"]}

Name : {query["name"]}

Body

{body}

""");
});

app.Run();
```

---

# Test in Postman

URL

```text
POST

https://localhost:7001/student?id=101&name=Raman
```

Body

```json
{
    "course":"ASP.NET Core",
    "fees":15000
}
```

Output

```text
Method : POST

Id : 101

Name : Raman

Body

{
   "course":"ASP.NET Core",
   "fees":15000
}
```

---

# Complete Flow

```text
                     Browser
                        │
POST /student?id=101&name=Raman
                        │
                        │
                Request Body
                        │
                        ▼
                  Kestrel Server
                        │
                 Creates HttpContext
                        │
         ┌──────────────┴──────────────┐
         │                             │
         ▼                             ▼
Request.QueryString              Request.Body(Stream)
         │                             │
         ▼                             ▼
QueryHelpers.ParseQuery()      StreamReader
         │                             │
         ▼                             ▼
 Dictionary<Key,Value>         ReadToEndAsync()
         │                             │
         └──────────────┬──────────────┘
                        ▼
              ASP.NET Core Application
                        │
                        ▼
                 HTTP Response
                        │
                        ▼
                     Browser
```

# Best Practice

For learning purposes, reading the body manually with:

```csharp
using var reader = new StreamReader(context.Request.Body);
var body = await reader.ReadToEndAsync();
```

is excellent because it shows how HTTP data reaches your application.

However, in real-world ASP.NET Core applications, you will usually let **model binding** do this work for you:

```csharp
app.MapPost("/student", (Student student) =>
{
    return Results.Ok(student);
});
```

ASP.NET Core automatically reads the JSON request body, deserializes it into a `Student` object, validates it (if configured), and makes it available to your code. This is the recommended approach for production applications, while manual stream reading is primarily useful for understanding the underlying HTTP pipeline or implementing custom processing.
