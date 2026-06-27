**HTTP Response** is just as important as understanding the HTTP Request. Every time the browser sends a request, the server returns an HTTP response that tells the browser:

* Was the request successful?
* What data should be displayed?
* What is the content type?
* Should the browser cache it?
* Should cookies be stored?

In ASP.NET Core, all of this is represented by:

```csharp
HttpContext.Response
```

---

# HTTP Response Lifecycle

When a user opens:

```text
https://localhost:7001/
```

The flow is:

```text
Browser
   │
   │ HTTP Request
   ▼
Kestrel Server
   │
   ▼
ASP.NET Core Application
   │
   │ Creates HTTP Response
   ▼
Browser receives response
```

The response consists of **4 parts**:

```text
HTTP Response
│
├── Start Line (Status Line)
├── Response Headers
├── Blank Line
└── Response Body
```

---

# Structure of an HTTP Response

Example:

```http
HTTP/1.1 200 OK
Date: Fri, 27 Jun 2026 10:15:30 GMT
Server: Kestrel
Content-Type: text/html; charset=utf-8
Content-Length: 145
Company: KMIT Solutions

<html>
    <body>
        <h1>Welcome to ASP.NET Core</h1>
    </body>
</html>
```

Let's understand each part.

---

# 1. Status Line (Start Line)

The first line is called the **Status Line**.

```http
HTTP/1.1 200 OK
```

It contains three parts:

```text
HTTP/1.1
│
├── HTTP Version

200
│
├── Status Code

OK
│
└── Reason Phrase
```

---

## HTTP Version

```text
HTTP/1.1
```

or

```text
HTTP/2
```

or

```text
HTTP/3
```

Tells the browser which HTTP protocol version was used.

---

## Status Code

Example

```text
200
```

means

> Request processed successfully.

Some common codes:

| Code | Meaning               |
| ---- | --------------------- |
| 200  | OK                    |
| 201  | Created               |
| 204  | No Content            |
| 301  | Redirect              |
| 400  | Bad Request           |
| 401  | Unauthorized          |
| 403  | Forbidden             |
| 404  | Not Found             |
| 500  | Internal Server Error |
| 503  | Service Unavailable   |

---

## Reason Phrase

Examples

```text
OK

Created

Not Found

Bad Request
```

This is a human-readable description of the status code.

---

# Setting Status Code in ASP.NET Core

```csharp
app.MapGet("/", async (HttpContext context) =>
{
    context.Response.StatusCode = 200;

    await context.Response.WriteAsync("Success");
});
```

---

404 Example

```csharp
context.Response.StatusCode = 404;
```

Browser Network Tab

```text
404 Not Found
```

---

500 Example

```csharp
context.Response.StatusCode = 500;
```

Browser

```text
500 Internal Server Error
```

---

# 2. Response Headers

Headers provide metadata about the response.

Example

```http
Content-Type: text/html

Content-Length: 120

Server: Kestrel

Date: Fri

Set-Cookie: Student=John

Company: KMIT
```

---

## Common Response Headers

### Server

```http
Server: Kestrel
```

Indicates which web server processed the request.

---

### Date

```http
Date:
Fri, 27 Jun 2026
```

The time when the response was generated.

---

### Content-Type

```http
Content-Type: text/html
```

The browser now knows:

> Render this as HTML.

Other examples

```text
application/json

text/plain

application/xml

image/png
```

---

ASP.NET Core

```csharp
context.Response.ContentType = "text/html";
```

---

### Content-Length

```http
Content-Length: 220
```

Number of bytes in the response body.

Usually generated automatically.

---

### Set-Cookie

```http
Set-Cookie:

Student=John
```

Browser stores this cookie.

---

### Cache-Control

```http
Cache-Control: no-cache
```

Controls browser caching.

---

### Custom Headers

Example

```http
Company: KMIT

Trainer: Raman Sharma
```

Add programmatically

```csharp
context.Response.Headers["Company"] = "KMIT";
context.Response.Headers["Trainer"] = "Raman Sharma";
```

---

# 3. Blank Line

HTTP requires a blank line after the headers.

```http
HTTP/1.1 200 OK

Content-Type: text/html

Server: Kestrel

<Blank Line>

<html>
```

This blank line separates:

```text
Headers

↓

Body
```

---

# 4. Response Body

The body contains the actual data returned to the client.

HTML example

```html
<html>
<body>

<h1>Welcome</h1>

</body>
</html>
```

JSON example

```json
{
  "id":101,
  "name":"Raman"
}
```

Plain Text

```text
Hello World
```

Image

```text
PNG Binary Data
```

PDF

```text
PDF Binary Data
```

---

# ASP.NET Core Demo

Replace your `Program.cs` with:

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", async (HttpContext context) =>
{
    // Status Code
    context.Response.StatusCode = 200;

    // Content Type
    context.Response.ContentType = "text/html";

    // Custom Headers
    context.Response.Headers["Company"] = "KMIT Solutions";
    context.Response.Headers["Trainer"] = "Raman Sharma";
    context.Response.Headers["Course"] = "ASP.NET Core";

    // Cookie
    context.Response.Cookies.Append("Student", "John");

    await context.Response.WriteAsync("""
        <html>
            <body>
                <h1>HTTP Response Demo</h1>
                <p>Open Chrome Developer Tools → Network → Refresh the page.</p>
            </body>
        </html>
        """);
});

app.Run();
```

---

# Open Browser

```text
https://localhost:7001
```

Press

```text
F12
```

Open

```text
Network
```

Refresh the page.

Click the request.

---

# What You'll See

## General

```text
Request URL

https://localhost:7001
```

```text
Request Method

GET
```

```text
Status Code

200 OK
```

```text
Remote Address

127.0.0.1
```

---

## Response Headers

```text
Content-Type

text/html
```

Because we set

```csharp
context.Response.ContentType = "text/html";
```

---

```text
Server

Kestrel
```

Added automatically by Kestrel.

---

```text
Date

Fri, 27 Jun
```

Generated automatically.

---

```text
Content-Length

185
```

Calculated automatically.

---

```text
Company

KMIT Solutions
```

Added from

```csharp
context.Response.Headers["Company"] = "KMIT Solutions";
```

---

```text
Trainer

Raman Sharma
```

Added programmatically.

---

```text
Course

ASP.NET Core
```

Added programmatically.

---

```text
Set-Cookie

Student=John
```

Created using

```csharp
context.Response.Cookies.Append("Student","John");
```

---

# Response Tab

Click

```text
Response
```

You'll see

```html
<html>
<body>

<h1>HTTP Response Demo</h1>

<p>Open Chrome Developer Tools</p>

</body>
</html>
```

This is the **Response Body** returned by ASP.NET Core.

---

# Preview Tab

Chrome renders the HTML:

```text
HTTP Response Demo

Open Chrome Developer Tools
```

This is exactly what the browser displays on the page.

---

# Mapping Network Tab to ASP.NET Core

| Chrome Network Tab | ASP.NET Core Code                                      |
| ------------------ | ------------------------------------------------------ |
| Status Code        | `context.Response.StatusCode`                          |
| Content-Type       | `context.Response.ContentType`                         |
| Response Headers   | `context.Response.Headers`                             |
| Set-Cookie         | `context.Response.Cookies.Append()`                    |
| Response Body      | `context.Response.WriteAsync()` or `Results.Content()` |
| Server             | Added automatically by Kestrel                         |
| Date               | Added automatically by the server                      |
| Content-Length     | Calculated automatically based on the response body    |

---

# Complete HTTP Response Flow

```text
                 ASP.NET Core Application
                          │
                          │
                          ▼
                 HttpContext.Response
                          │
        ┌─────────────────┼──────────────────┐
        │                 │                  │
        ▼                 ▼                  ▼
  Status Code      Response Headers     Response Body
     200 OK        Content-Type         HTML
                   Server               JSON
                   Company              Text
                   Trainer              XML
                   Set-Cookie
                          │
                          ▼
                     Kestrel Server
                          │
            Creates HTTP Response Message
                          │
                          ▼
HTTP/1.1 200 OK
Content-Type: text/html
Server: Kestrel
Company: KMIT Solutions

<html>...</html>
                          │
                          ▼
                    Browser (Chrome)
                          │
                          ▼
                Developer Tools → Network
        ┌─────────────┬──────────────┬──────────────┐
        │ General     │ Headers      │ Response     │
        │ 200 OK      │ Metadata     │ HTML Content │
        └─────────────┴──────────────┴──────────────┘
```

### Key Interview Points

* **Status Line** contains the HTTP version, status code, and reason phrase (e.g., `HTTP/1.1 200 OK`).
* **Response Headers** provide metadata about the response, such as `Content-Type`, `Content-Length`, `Server`, `Date`, and any custom headers.
* **Blank Line** separates the headers from the body.
* **Response Body** contains the actual content returned to the client (HTML, JSON, XML, images, etc.).
* In ASP.NET Core, you work with the response using `HttpContext.Response`, while Kestrel converts your settings into a valid HTTP response before sending it back to the browser.
