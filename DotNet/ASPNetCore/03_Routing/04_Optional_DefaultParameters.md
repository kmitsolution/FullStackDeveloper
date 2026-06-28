Default and Optional Route Parameters make your routes **more flexible**. They allow a URL to work even when some values are not provided by the user.

There are two concepts:

1. **Optional Parameters (`?`)** – The parameter may or may not be supplied.
2. **Default Parameters (`=`)** – If the parameter is not supplied, ASP.NET Core automatically uses a default value.

---

# Why Do We Need Optional Parameters?

Suppose we have a route

```text
/student/{id}
```

It requires an `id`.

Valid URL

```text
/student/101
```

Invalid URL

```text
/student
```

Result

```text
404 Not Found
```

But what if we want both URLs to work?

```text
/student

/student/101
```

That's where **optional parameters** help.

---

# Optional Route Parameter

Syntax

```text
{id?}
```

Notice the question mark (`?`).

---

## Example 1

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student/{id?}", (int? id) =>
{
    if (id == null)
        return "All Students";

    return $"Student Id = {id}";
});

app.Run();
```

---

## Test 1

Open

```text
https://localhost:5001/student
```

Output

```text
All Students
```

---

## Test 2

Open

```text
https://localhost:5001/student/101
```

Output

```text
Student Id = 101
```

---

# How It Works

Route Template

```text
/student/{id?}
```

Possible URLs

```text
/student

/student/101

/student/200
```

Routing

```text
/student

↓

id = null
```

---

```text
/student/101

↓

id = 101
```

---

# Visual Representation

```text
               Browser
                  │
                  ▼
          /student/101
                  │
                  ▼
        Route Template
     /student/{id?}
                  │
                  ▼
          id = 101
                  │
                  ▼
            Endpoint
```

---

# Nullable Parameter

Since the value may not exist

Use

```csharp
int? id
```

instead of

```csharp
int id
```

because

```text
/student
```

has no id.

---

# Example 2 – Optional Name

```csharp
app.MapGet("/welcome/{name?}",
(string? name) =>
{
    if (string.IsNullOrEmpty(name))
        return "Welcome Guest";

    return $"Welcome {name}";
});
```

---

Request

```text
/welcome
```

Output

```text
Welcome Guest
```

---

Request

```text
/welcome/Raman
```

Output

```text
Welcome Raman
```

---

# Default Route Parameter

Instead of returning null,

we can assign a default value.

Syntax

```text
{name=Guest}
```

---

## Example

```csharp
app.MapGet("/welcome/{name=Guest}",
(string name) =>
{
    return $"Welcome {name}";
});
```

---

Request

```text
/welcome
```

Output

```text
Welcome Guest
```

---

Request

```text
/welcome/Raman
```

Output

```text
Welcome Raman
```

---

# How Default Values Work

Route Template

```text
/welcome/{name=Guest}
```

Browser

```text
/welcome
```

Routing

```text
name = Guest
```

Browser

```text
/welcome/Raman
```

Routing

```text
name = Raman
```

---

# Visual Representation

```text
                Browser
                   │
           /welcome
                   │
                   ▼
      Route Template

 /welcome/{name=Guest}

                   │
                   ▼
          name = Guest

                   │
                   ▼
            Endpoint
```

---

# Example 3 – Product Category

```csharp
app.MapGet("/products/{category=All}",
(string category) =>
{
    return $"Category = {category}";
});
```

---

Request

```text
/products
```

Output

```text
Category = All
```

---

Request

```text
/products/Laptop
```

Output

```text
Category = Laptop
```

---

# Multiple Optional Parameters

```csharp
app.MapGet("/student/{id?}/{name?}",
(int? id, string? name) =>
{
    return $"""

Id : {id}

Name : {name}

""";
});
```

---

Request

```text
/student
```

Output

```text
Id :

Name :
```

---

Request

```text
/student/101
```

Output

```text
Id : 101

Name :
```

---

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

# Multiple Default Parameters

```csharp
app.MapGet("/student/{id=100}/{name=Guest}",
(int id, string name) =>
{
    return $"""

Id : {id}

Name : {name}

""";
});
```

---

Request

```text
/student
```

Output

```text
Id : 100

Name : Guest
```

---

Request

```text
/student/101
```

Output

```text
Id : 101

Name : Guest
```

---

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

# Optional vs Default

| Optional             | Default                         |
| -------------------- | ------------------------------- |
| Value may be missing | Value is automatically supplied |
| Returns `null`       | Returns a predefined value      |
| Uses `?`             | Uses `=`                        |

Example

Optional

```text
/student/{id?}
```

Default

```text
/student/{id=100}
```

---

# Real-Life Example

Imagine an e-commerce website.

Search URL

```text
/products/{category?}
```

User opens

```text
/products
```

Application

```text
All Products
```

User opens

```text
/products/Laptop
```

Application

```text
Laptop Products
```

---

Default example

```text
/products/{category=All}
```

If no category is provided

Application automatically uses

```text
All
```

---

# Request Flow

```text
                    Browser
                       │
              /products
                       │
                       ▼
                Routing
                       │
        /products/{category=All}
                       │
                       ▼
             category = All
                       │
                       ▼
                Endpoint
                       │
                       ▼
                HTTP Response
```

---

# Complete Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/employee/{id=1}/{name=Guest}",
(int id, string name) =>
{
    return Results.Text($"""

Employee Id   : {id}

Employee Name : {name}

""");
});

app.Run();
```

---

## Test

### Request

```text
/employee
```

Output

```text
Employee Id   : 1

Employee Name : Guest
```

---

### Request

```text
/employee/10
```

Output

```text
Employee Id   : 10

Employee Name : Guest
```

---

### Request

```text
/employee/10/Raman
```

Output

```text
Employee Id   : 10

Employee Name : Raman
```

---

# Complete Routing Flow

```text
                 Browser
                    │
            GET /employee
                    │
                    ▼
             Route Template

/employee/{id=1}/{name=Guest}

                    │
                    ▼
       id = 1
       name = Guest
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

# Summary

| Route Template                  | Request URL          | Values Received             |
| ------------------------------- | -------------------- | --------------------------- |
| `/student/{id?}`                | `/student`           | `id = null`                 |
| `/student/{id?}`                | `/student/101`       | `id = 101`                  |
| `/welcome/{name=Guest}`         | `/welcome`           | `name = "Guest"`            |
| `/welcome/{name=Guest}`         | `/welcome/Raman`     | `name = "Raman"`            |
| `/employee/{id=1}/{name=Guest}` | `/employee`          | `id = 1`, `name = "Guest"`  |
| `/employee/{id=1}/{name=Guest}` | `/employee/10`       | `id = 10`, `name = "Guest"` |
| `/employee/{id=1}/{name=Guest}` | `/employee/10/Raman` | `id = 10`, `name = "Raman"` |

---

# Interview Questions

### What is an optional route parameter?

An optional route parameter allows a URL segment to be omitted. It is defined using a **question mark (`?`)**.

Example:

```csharp
app.MapGet("/student/{id?}", (int? id) =>
{
    return id is null ? "All Students" : $"Student Id = {id}";
});
```

---

### What is a default route parameter?

A default route parameter provides a value automatically when the URL does not include that parameter.

Example:

```csharp
app.MapGet("/welcome/{name=Guest}", (string name) =>
{
    return $"Welcome {name}";
});
```

If the user requests `/welcome`, the value of `name` is `"Guest"`.

---

### Difference Between Optional and Default Parameters

| Feature           | Optional Parameter                    | Default Parameter                              |
| ----------------- | ------------------------------------- | ---------------------------------------------- |
| Syntax            | `{id?}`                               | `{id=100}`                                     |
| Value if omitted  | `null` (or default for nullable type) | Assigned default value (e.g., `100`)           |
| Handler parameter | Usually nullable (`int?`, `string?`)  | Usually non-nullable (`int`, `string`)         |
| Typical use       | Value is truly optional               | A sensible default should be used when omitted |
