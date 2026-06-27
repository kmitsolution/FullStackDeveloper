This is an excellent exercise because it helps you understand what actually happens when a browser communicates with an ASP.NET Core application. We'll create a simple project, inspect the request and response in the browser's **Developer Tools**, and then modify headers programmatically.

---

# Step 1: Create an Empty ASP.NET Core Application

```bash
dotnet new web -n HttpContextDemo
```

Go to the project folder:

```bash
cd HttpContextDemo
```

Run the application:

```bash
dotnet run
```

Suppose it runs on:

```
https://localhost:7208
```

---

# Step 2: Replace Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", (HttpContext context) =>
{
    // Custom Response Headers
    context.Response.Headers["Company"] = "KMIT Solutions";
    context.Response.Headers["Trainer"] = "Raman Sharma";
    context.Response.Headers["Course"] = "ASP.NET Core";

    // Response Content Type
    context.Response.ContentType = "text/html";

    return Results.Content("""
        <html>
        <body>
            <h1>Welcome to ASP.NET Core</h1>
            <h2>HTTP Context Demo</h2>
            <p>Open Developer Tools (F12) and inspect the Network tab.</p>
        </body>
        </html>
        """, "text/html");
});

app.Run();
```

Run the application.

---

# Step 3: Open Browser

```
https://localhost:7208
```

You will see

```
Welcome to ASP.NET Core

HTTP Context Demo

Open Developer Tools (F12)
```

---

# Step 4: Open Developer Tools

Chrome

```
F12
```

or

```
Right Click

Inspect
```

Open

```
Network
```

Refresh the page.

You'll see something like

```
Name        Status      Type

/           200         document
```

Click the request.

Now several tabs appear

```
Headers
Preview
Response
Cookies
Timing
Initiator
```

---

# Understanding the Network Tab

```
Browser

↓

HTTP Request

↓

Kestrel Server

↓

ASP.NET Core

↓

HTTP Response

↓

Browser
```

The Network tab displays the complete request and response exchanged between the browser and the server.

---

# General Section

```
Request URL
```

Example

```
https://localhost:7208/
```

The page requested by the browser.

---

```
Request Method
```

```
GET
```

Since we opened the page, the browser issued a GET request.

---

```
Status Code
```

```
200 OK
```

Meaning

```
Server successfully processed the request.
```

---

```
Remote Address
```

Example

```
127.0.0.1:7208
```

The IP address and port of the server.

---

# Request Headers

These are sent **from the browser to the server**.

Example

```
GET / HTTP/1.1

Host: localhost:7208

User-Agent: Chrome

Accept: text/html

Accept-Encoding: gzip

Accept-Language: en-US
```

---

## Host Header

```
Host

localhost:7208
```

Tells the server which website is being requested.

---

## User-Agent

Example

```
Mozilla/5.0
Chrome/138
```

Identifies the browser.

In ASP.NET Core

```csharp
context.Request.Headers["User-Agent"]
```

---

## Accept

```
Accept

text/html
```

The browser tells the server:

> I can accept HTML.

If calling an API

```
Accept

application/json
```

---

## Accept-Encoding

```
gzip
```

The browser can accept compressed responses.

---

## Accept-Language

```
en-US
```

Preferred language.

---

# Response Headers

These are sent **from the server back to the browser**.

Example

```
HTTP/1.1 200 OK

Content-Type: text/html

Date: Tue

Server: Kestrel

Company: KMIT Solutions

Trainer: Raman Sharma
```

---

# Server Header

```
Server

Kestrel
```

Indicates which web server processed the request.

ASP.NET Core uses Kestrel by default.

---

# Content-Type

```
text/html
```

The browser knows to render HTML.

If

```
application/json
```

it interprets the response as JSON.

---

# Date

```
Date

Fri, 26 Jun 2026
```

The time the server generated the response.

---

# Custom Headers

Our application added

```
Company

KMIT Solutions
```

```
Trainer

Raman Sharma
```

```
Course

ASP.NET Core
```

These appear because we set them in our code.

---

# Response Body

Click

```
Response
```

You'll see

```html
<html>

<body>

<h1>Welcome to ASP.NET Core</h1>

<h2>HTTP Context Demo</h2>

</body>

</html>
```

This is the content returned by the server.

---

# Setting Headers Programmatically

```csharp
app.MapGet("/", (HttpContext context) =>
{
    context.Response.Headers["Company"] = "KMIT";

    return "Hello";
});
```

Browser

```
Response Headers

Company: KMIT
```

---

Multiple headers

```csharp
context.Response.Headers["Company"] = "KMIT";

context.Response.Headers["Trainer"] = "Raman";

context.Response.Headers["Version"] = "1.0";
```

---

# Reading Request Headers

```csharp
app.MapGet("/", (HttpContext context) =>
{
    var browser = context.Request.Headers["User-Agent"];

    return $"Browser = {browser}";
});
```

Output

```
Browser = Mozilla/5.0 Chrome...
```

---

# Reading Host Header

```csharp
app.MapGet("/", (HttpContext context) =>
{
    return context.Request.Headers["Host"];
});
```

Output

```
localhost:7208
```

---

# Setting Response Content Type

HTML

```csharp
context.Response.ContentType = "text/html";
```

JSON

```csharp
context.Response.ContentType = "application/json";
```

Plain Text

```csharp
context.Response.ContentType = "text/plain";
```

XML

```csharp
context.Response.ContentType = "application/xml";
```

---

# Setting Status Code

```csharp
context.Response.StatusCode = 404;

await context.Response.WriteAsync("Page Not Found");
```

Browser

```
404 Not Found
```

---

# Setting Cookies

```csharp
context.Response.Cookies.Append("Company", "KMIT");
```

Browser

```
Response Headers

Set-Cookie

Company=KMIT
```

Next request

```
Request Headers

Cookie

Company=KMIT
```

---

# Complete Demo

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", async (HttpContext context) =>
{
    // Read Request Information
    var browser = context.Request.Headers["User-Agent"];
    var host = context.Request.Headers["Host"];
    var method = context.Request.Method;

    // Configure Response
    context.Response.StatusCode = 200;
    context.Response.ContentType = "text/html";

    // Custom Headers
    context.Response.Headers["Company"] = "KMIT";
    context.Response.Headers["Trainer"] = "Raman Sharma";
    context.Response.Headers["Course"] = "ASP.NET Core";

    // Cookie
    context.Response.Cookies.Append("Student", "John");

    await context.Response.WriteAsync($"""
        <html>
        <body>
            <h1>HTTP Context Demo</h1>

            <p><b>Method:</b> {method}</p>
            <p><b>Host:</b> {host}</p>
            <p><b>Browser:</b> {browser}</p>

            <hr>

            <p>Open Developer Tools (F12) → Network → Refresh the page and inspect:</p>

            <ul>
                <li>General</li>
                <li>Request Headers</li>
                <li>Response Headers</li>
                <li>Response</li>
                <li>Cookies</li>
            </ul>
        </body>
        </html>
        """);
});

app.Run();
```

## HTTP Request/Response Flow

```text
                Browser
                   │
                   │ 1. HTTP GET /
                   ▼
            Request Headers
      (Host, User-Agent, Accept...)
                   │
                   ▼
              Kestrel Server
                   │
                   ▼
            ASP.NET Core App
                   │
        Reads HttpContext.Request
                   │
        Sets HttpContext.Response
      - StatusCode = 200
      - ContentType = text/html
      - Headers (Company, Trainer)
      - Cookies
                   │
                   ▼
          HTTP Response Sent
                   │
                   ▼
               Browser
                   │
     Developer Tools → Network
                   │
    ┌──────────────┼──────────────┐
    │              │              │
 General      Request Headers  Response Headers
                                   │
                                Response Body
```

### What to inspect in Chrome Developer Tools

1. **General**

   * Request URL
   * Request Method (`GET`)
   * Status Code (`200 OK`)
   * Remote Address

2. **Request Headers**

   * `Host`
   * `User-Agent`
   * `Accept`
   * `Accept-Encoding`
   * `Accept-Language`
   * `Cookie` (after refreshing once, since the server sets one)

3. **Response Headers**

   * `Content-Type`
   * `Content-Length`
   * `Date`
   * `Server`
   * `Company`
   * `Trainer`
   * `Course`
   * `Set-Cookie`

4. **Response**

   * The HTML generated by your ASP.NET Core application.

This simple application is ideal for a classroom or YouTube lecture because students can immediately correlate the `HttpContext.Request` and `HttpContext.Response` objects in code with the actual HTTP traffic visible in the browser's Network tab.
