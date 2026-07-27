`HttpClient` is one of the most commonly used classes in ASP.NET Core.

## What is HttpClient?

`HttpClient` is a .NET class used to **send HTTP requests to another web server or API and receive the response**.

Think of it as a **web browser inside your code**.

For example:

* Your ASP.NET Core API wants weather data from a Weather API.
* Your application wants to call another microservice.
* Your application wants to call the GitHub API.

Instead of opening Chrome and visiting a URL, your code uses `HttpClient`.

---

# Real-Life Example

Imagine you are ordering food.

```text
You (Customer)
      │
      ▼
Waiter (HttpClient)
      │
      ▼
Kitchen (Another API)
      │
      ▼
Food
      │
      ▼
Waiter
      │
      ▼
You
```

* Your application = Customer
* HttpClient = Waiter
* Another API = Kitchen

The waiter takes your request, brings back the response.

---

# Without HttpClient

Suppose your application needs weather information.

Normally, you would open Chrome.

```text
https://weatherapi.com/current
```

Browser sends request.

Server returns response.

---

# With HttpClient

Your code becomes the browser.

```text
ASP.NET Core App

↓

HttpClient

↓

Weather API

↓

JSON Response

↓

Your Application
```

---

# Simple GET Example

```csharp
using System.Net.Http;

HttpClient client = new HttpClient();

string data =
    await client.GetStringAsync(
        "https://jsonplaceholder.typicode.com/posts/1");

Console.WriteLine(data);
```

Output

```json
{
  "userId": 1,
  "id": 1,
  "title": "...",
  "body": "..."
}
```

Notice:

```csharp
GetStringAsync()
```

downloads the response as a string.

---

# Example in ASP.NET Core

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", async () =>
{
    HttpClient client = new HttpClient();

    var data =
        await client.GetStringAsync(
        "https://jsonplaceholder.typicode.com/posts/1");

    return data;
});

app.Run();
```

Request

```text
GET /
```

Your API internally calls another API and returns its response.

---

# GET Request Flow

```text
Browser

↓

Your ASP.NET Core API

↓

HttpClient

↓

https://jsonplaceholder.typicode.com

↓

JSON

↓

HttpClient

↓

Your API

↓

Browser
```

---

# Reading JSON as Object

Suppose API returns

```json
{
   "id":1,
   "title":"Learning ASP.NET Core"
}
```

Create Model

```csharp
public class Post
{
    public int Id { get; set; }

    public string Title { get; set; } = "";
}
```

Now

```csharp
HttpClient client = new HttpClient();

Post? post =
await client.GetFromJsonAsync<Post>(
"https://jsonplaceholder.typicode.com/posts/1");
```

Now

```csharp
Console.WriteLine(post.Title);
```

Output

```text
Learning ASP.NET Core
```

`GetFromJsonAsync<T>()` automatically converts the JSON into a C# object.

---

# POST Request Example

Suppose another API expects

```json
{
    "name":"Raman",
    "age":30
}
```

Model

```csharp
public class Student
{
    public string Name { get; set; } = "";

    public int Age { get; set; }
}
```

Sending POST

```csharp
HttpClient client = new HttpClient();

Student student = new Student
{
    Name = "Raman",
    Age = 30
};

var response =
await client.PostAsJsonAsync(
"https://localhost:5001/student",
student);
```

`PostAsJsonAsync()`:

* Converts object to JSON
* Sets `Content-Type: application/json`
* Sends POST request

---

# Calling Your Own API

Suppose your API has

```csharp
app.MapGet("/student", () =>
{
    return "Student Details";
});
```

Another application can call it.

```csharp
HttpClient client = new HttpClient();

string data =
await client.GetStringAsync(
"http://localhost:5220/student");

Console.WriteLine(data);
```

Output

```text
Student Details
```

---

# Reading Status Code

```csharp
HttpClient client = new HttpClient();

HttpResponseMessage response =
await client.GetAsync(
"https://jsonplaceholder.typicode.com/posts/1");

Console.WriteLine(response.StatusCode);
```

Output

```text
200 OK
```

You can also check:

```csharp
if(response.IsSuccessStatusCode)
{
    Console.WriteLine("Success");
}
```

---

# Reading Headers

```csharp
HttpClient client = new HttpClient();

HttpResponseMessage response =
await client.GetAsync(url);

foreach(var header in response.Headers)
{
    Console.WriteLine(header.Key);
}
```

---

# Sending Authorization Header

Suppose API requires JWT.

```csharp
client.DefaultRequestHeaders.Authorization =
new AuthenticationHeaderValue(
"Bearer",
token);
```

Now every request sends

```http
Authorization: Bearer eyJhb...
```

---

# Why async?

Network calls take time.

Without async

```csharp
var data =
client.GetStringAsync(url).Result;
```

Thread is blocked.

Better

```csharp
var data =
await client.GetStringAsync(url);
```

The thread is free while waiting for the server.

---

# Should We Create HttpClient Like This?

Many beginners do:

```csharp
HttpClient client = new HttpClient();
```

This works for learning, but **it's not recommended in production**.

Why?

Every `HttpClient` creates and manages network connections. Creating a new one for every request can lead to **socket exhaustion** and poor performance.

Instead, ASP.NET Core provides **`IHttpClientFactory`**.

Register it:

```csharp
builder.Services.AddHttpClient();
```

Use it:

```csharp
app.MapGet("/", async (IHttpClientFactory factory) =>
{
    var client = factory.CreateClient();

    var data = await client.GetStringAsync(
        "https://jsonplaceholder.typicode.com/posts/1");

    return data;
});
```

`IHttpClientFactory`:

* Reuses underlying connections efficiently.
* Manages the lifetime of `HttpClient`.
* Makes configuration and testing easier.

---

# Request Flow

```text
Browser

↓

Your ASP.NET Core API

↓

HttpClient

↓

Weather API

↓

JSON Response

↓

Convert JSON

↓

Return to Browser
```

---

# Real-World Examples

| Scenario                  | HttpClient Use                 |
| ------------------------- | ------------------------------ |
| Call Weather API          | `GetAsync()`                   |
| Call Payment Gateway      | `PostAsync()`                  |
| Call Azure REST API       | `GetAsync()` with Bearer Token |
| Call GitHub API           | `GetAsync()`                   |
| Call another Microservice | `GetFromJsonAsync()`           |

---

## Interview Definition

> `HttpClient` is a .NET class used to send HTTP requests and receive HTTP responses from web servers or APIs. It supports all HTTP methods (GET, POST, PUT, DELETE, PATCH), works asynchronously, and is commonly used in ASP.NET Core to communicate with external services and microservices. In production applications, `IHttpClientFactory` is the recommended way to create and manage `HttpClient` instances.
