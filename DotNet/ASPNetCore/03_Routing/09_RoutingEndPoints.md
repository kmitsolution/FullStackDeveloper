# Endpoint Selection Order in ASP.NET Core

When multiple routes **could match the same request**, ASP.NET Core uses an **endpoint selection algorithm** to decide **which endpoint should execute**.

This process is called **Endpoint Selection** or **Route Precedence**.

Understanding this is important because in a real application you may have many routes such as:

```text
/products/new

/products/{id}

/products/{category}

/products/{*path}
```

Which one should ASP.NET Core choose?

It follows a set of precedence rules.

---

# What is Endpoint Selection?

Endpoint Selection is the process of choosing **the best matching route** from all available routes.

```text
Browser

↓

Routing Engine

↓

Many Routes Match

↓

Endpoint Selection Rules

↓

Best Match

↓

Execute Endpoint
```

---

# The Four Endpoint Selection Rules

ASP.NET Core selects the endpoint using these rules (highest priority first):

| Priority | Rule                                      | Preference      |
| -------- | ----------------------------------------- | --------------- |
| 1        | More URL Segments                         | Higher priority |
| 2        | Literal Text over Parameters              | Higher priority |
| 3        | Constrained Parameters over Unconstrained | Higher priority |
| 4        | Catch-All Parameters                      | Lowest priority |

A simple way to remember them is:

```text
More Specific Route

↓

Literal Route

↓

Constrained Route

↓

Catch-All Route
```

---

# Rule 1 – More URL Segments Win

Routes with **more path segments** are considered more specific.

Example

```csharp
app.MapGet("/products", () => "All Products");

app.MapGet("/products/electronics", () => "Electronics");
```

---

## Request

```text
/products/electronics
```

Possible Matches

```text
/products

/products/electronics
```

Which is selected?

```text
/products/electronics
```

because it contains **more URL segments**.

---

## Visual

```text
Request

/products/electronics

↓

Route 1

/products

(1 segment)

↓

Route 2

/products/electronics

(2 segments)

↓

Winner
```

---

# Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/products", () => "All Products");

app.MapGet("/products/electronics", () => "Electronics Products");

app.Run();
```

---

Request

```text
/products/electronics
```

Output

```text
Electronics Products
```

---

# Rule 2 – Literal Text Wins Over Parameters

Literal text is more specific than a parameter.

Example

```csharp
app.MapGet("/products/new", () => "Create Product");

app.MapGet("/products/{id}", (string id) =>
{
    return $"Product {id}";
});
```

---

## Request

```text
/products/new
```

Both routes could match.

Route 1

```text
/products/new
```

Literal

Route 2

```text
/products/{id}
```

Parameter

Routing chooses

```text
/products/new
```

because literal text has higher precedence.

---

## Visual

```text
Request

/products/new

↓

Literal Route

/products/new

↓

Parameter Route

/products/{id}

↓

Literal Wins
```

---

# Example

```csharp
app.MapGet("/products/new",
() => "New Product Page");

app.MapGet("/products/{id}",
(string id) => $"Product : {id}");
```

---

Request

```text
/products/new
```

Output

```text
New Product Page
```

---

Request

```text
/products/101
```

Output

```text
Product : 101
```

---

# Rule 3 – Constrained Routes Win

Suppose

```csharp
app.MapGet("/employee/{id}",
(string id) =>
{
    return $"Employee : {id}";
});

app.MapGet("/employee/{id:int}",
(int id) =>
{
    return $"Employee Id : {id}";
});
```

---

Request

```text
/employee/100
```

Both routes match.

However

```text
{id:int}
```

is **more specific** than

```text
{id}
```

Therefore

```text
{id:int}
```

wins.

---

## Visual

```text
Request

/employee/100

↓

{id}

↓

{id:int}

↓

Constraint Wins
```

---

### Output

```text
Employee Id : 100
```

---

Request

```text
/employee/Raman
```

Only

```text
{id}
```

matches.

Output

```text
Employee : Raman
```

---

# Rule 4 – Catch-All Routes Are Least Preferred

Catch-all parameters are written using

```text
{*path}
```

or

```text
{**path}
```

Example

```csharp
app.MapGet("/files/{filename}",
(string filename) =>
{
    return $"File : {filename}";
});

app.MapGet("/files/{*path}",
(string path) =>
{
    return $"Catch All : {path}";
});
```

---

Request

```text
/files/report.pdf
```

Both routes match.

Winner

```text
/files/{filename}
```

because it is more specific.

---

Request

```text
/files/reports/2026/january.pdf
```

Only

```text
/files/{*path}
```

matches.

Output

```text
Catch All :
reports/2026/january.pdf
```

---

# Visual

```text
Request

/files/report.pdf

↓

{filename}

↓

{*path}

↓

Normal Parameter Wins
```

---

# Complete Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

// Literal
app.MapGet("/products/new",
() => "Create Product");

// Parameter
app.MapGet("/products/{id}",
(string id) => $"Product {id}");

// Constrained
app.MapGet("/employee/{id:int}",
(int id) => $"Employee Id {id}");

// Catch All
app.MapGet("/files/{*path}",
(string path) => $"Path : {path}");

app.Run();
```

---

# Testing

### Request

```text
/products/new
```

Output

```text
Create Product
```

---

### Request

```text
/products/101
```

Output

```text
Product 101
```

---

### Request

```text
/employee/200
```

Output

```text
Employee Id 200
```

---

### Request

```text
/files/report.pdf
```

Output

```text
Path : report.pdf
```

---

### Request

```text
/files/reports/2026/report.pdf
```

Output

```text
Path :
reports/2026/report.pdf
```

---

# Complete Endpoint Selection Flow

```text
                   Browser
                      │
                HTTP Request
                      │
                      ▼
              Routing Engine
                      │
          Find Matching Routes
                      │
                      ▼
      Apply Selection Rules
                      │
     ┌─────────────────────────────────┐
     │ 1. More URL Segments            │
     │ 2. Literal over Parameter       │
     │ 3. Constrained over Normal      │
     │ 4. Catch-All is Least Preferred │
     └─────────────────────────────────┘
                      │
                      ▼
             Best Matching Route
                      │
                      ▼
               Execute Endpoint
                      │
                      ▼
                 HTTP Response
```

---

# Real-Life Analogy

Imagine a courier delivering a package.

Address:

```text
Building A

Floor 2

Room 205
```

Possible matches:

```text
Building A

Building A → Floor 2

Building A → Floor 2 → Room 205
```

The courier chooses the **most specific address**.

ASP.NET Core routing behaves the same way.

---

# Summary Table

| Rule                           | Example                                | Selected Route          |
| ------------------------------ | -------------------------------------- | ----------------------- |
| More URL Segments              | `/products/electronics` vs `/products` | `/products/electronics` |
| Literal over Parameter         | `/products/new` vs `/products/{id}`    | `/products/new`         |
| Constrained over Unconstrained | `{id:int}` vs `{id}`                   | `{id:int}`              |
| Catch-All Least Preferred      | `{filename}` vs `{*path}`              | `{filename}`            |

---

# Priority Diagram

```text
Highest Priority
        │
        ▼
More URL Segments
        │
        ▼
Literal Text
        │
        ▼
Constrained Parameters
        │
        ▼
Normal Parameters
        │
        ▼
Catch-All Parameters
        │
Lowest Priority
```

---

# Best Practices

* Create **specific routes before generic ones** to make your routing intent clear.
* Use **literal routes** (for example, `/products/new`) for special pages instead of relying on parameter routes.
* Apply **route constraints** (`int`, `guid`, `datetime`, etc.) whenever you know the expected data type.
* Use **catch-all routes** only when you intentionally want to capture the remainder of the URL, such as for file systems or fallback scenarios.

---

# Interview Questions

### 1. What is endpoint selection in ASP.NET Core?

Endpoint selection is the process by which the ASP.NET Core routing system chooses the **best matching endpoint** when multiple routes could match the same incoming request.

---

### 2. What are the four endpoint selection rules?

1. Routes with **more URL segments** are preferred.
2. **Literal text** has higher precedence than route parameters.
3. **Constrained route parameters** have higher precedence than unconstrained parameters.
4. **Catch-all parameters** (`{*path}`) have the lowest precedence.

---

### 3. Why does `/products/new` match the literal route instead of `/products/{id}`?

Because **literal route segments are considered more specific** than parameter segments. Even though `"new"` could be treated as the value of `{id}`, ASP.NET Core gives higher priority to the literal route.

---

### 4. Why are catch-all routes evaluated last?

Catch-all routes are intentionally very broad because they can match many URLs. If they were evaluated first, they would prevent more specific routes from ever being selected. Therefore, ASP.NET Core treats them as the **least preferred** route type.
