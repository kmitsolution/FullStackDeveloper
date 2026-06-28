# Routing Constraints with Decimal and GUID in ASP.NET Core

In the previous lesson, you learned about the `int` and `datetime` route constraints. ASP.NET Core also supports constraints such as **decimal** and **GUID**.

These constraints ensure that only valid values match the route.

For example,

```text
/products/100.50
```

should match a decimal route,

whereas

```text
/products/abc
```

should not.

Similarly,

```text
/cities/550e8400-e29b-41d4-a716-446655440000
```

should match a GUID route,

but

```text
/cities/123
```

should not.

---

# What is a Decimal Constraint?

A decimal constraint ensures that the route parameter contains a valid decimal number.

Syntax

```text
{price:decimal}
```

---

# Example 1 – Decimal Route Constraint

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/products/{price:decimal}", (decimal price) =>
{
    return $"Product Price = {price}";
});

app.Run();
```

---

## Test 1

Open

```text
https://localhost:5001/products/250.75
```

Output

```text
Product Price = 250.75
```

---

## Test 2

Open

```text
https://localhost:5001/products/ABC
```

Output

```text
404 Not Found
```

---

# Request Flow

```text
Browser

↓

GET /products/250.75

↓

Routing

↓

{price:decimal}

↓

Valid Decimal

↓

Endpoint

↓

Response
```

---

# Visual Representation

```text
/products/250.75

↓

price = 250.75

↓

Decimal Constraint

↓

Endpoint
```

---

# What is a GUID Constraint?

A GUID (Globally Unique Identifier) is a 128-bit unique identifier.

Example

```text
550e8400-e29b-41d4-a716-446655440000
```

Many applications use GUIDs as IDs instead of integers.

Example:

```text
/users/550e8400-e29b-41d4-a716-446655440000
```

---

# GUID Route Constraint

Syntax

```text
{id:guid}
```

---

# Example 2 – GUID Constraint

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/cities/{cityId:guid}", (Guid cityId) =>
{
    return $"City Id = {cityId}";
});

app.Run();
```

---

## Test

Open

```text
https://localhost:5001/cities/550e8400-e29b-41d4-a716-446655440000
```

Output

```text
City Id = 550e8400-e29b-41d4-a716-446655440000
```

---

## Invalid Request

```text
https://localhost:5001/cities/100
```

Output

```text
404 Not Found
```

---

# Request Flow

```text
Browser

↓

GET /cities/{cityId}

↓

Routing

↓

Is cityId a GUID?

↓

Yes

↓

Endpoint
```

---

```text
Browser

↓

GET /cities/123

↓

Routing

↓

GUID?

↓

No

↓

404
```

---

# Reading Route Values Using HttpContext

Instead of receiving the parameter directly, you can read it from `Request.RouteValues`.

Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/cities/{cityId:guid}", (HttpContext context) =>
{
    var cityId = context.Request.RouteValues["cityId"];

    return $"City Id = {cityId}";
});

app.Run();
```

---

Open

```text
/cities/550e8400-e29b-41d4-a716-446655440000
```

Output

```text
City Id = 550e8400-e29b-41d4-a716-446655440000
```

---

# RouteValues Dictionary

Internally

```text
/cities/550e8400-e29b-41d4-a716-446655440000
```

becomes

```text
Key          Value

cityId       550e8400-e29b-41d4-a716-446655440000
```

---

# Converting Route Value to Guid

Since `RouteValues` returns an object, we normally convert it into a `Guid`.

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/cities/{cityId:guid}", (HttpContext context) =>
{
    string cityIdString =
        context.Request.RouteValues["cityId"]!.ToString()!;

    Guid cityId = Guid.Parse(cityIdString);

    return Results.Text($"""

City Id : {cityId}

Type : {cityId.GetType().Name}

""");
});

app.Run();
```

---

## Test

Open

```text
/cities/550e8400-e29b-41d4-a716-446655440000
```

Output

```text
City Id :
550e8400-e29b-41d4-a716-446655440000

Type :
Guid
```

---

# Safer Conversion Using TryParse

Instead of `Guid.Parse()`, use `Guid.TryParse()` to avoid exceptions.

```csharp
app.MapGet("/cities/{cityId:guid}", (HttpContext context) =>
{
    string? value =
        context.Request.RouteValues["cityId"]?.ToString();

    if (Guid.TryParse(value, out Guid cityId))
    {
        return Results.Text($"City Id = {cityId}");
    }

    return Results.BadRequest("Invalid GUID");
});
```

Although the `:guid` constraint already guarantees the value is a valid GUID, `TryParse()` is a good practice when parsing values from strings.

---

# Complete Example (Decimal + GUID)

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

// Decimal Route
app.MapGet("/products/{price:decimal}",
(decimal price) =>
{
    return $"Price = {price}";
});

// GUID Route
app.MapGet("/cities/{cityId:guid}",
(HttpContext context) =>
{
    string cityIdString =
        context.Request.RouteValues["cityId"]!.ToString()!;

    Guid cityId = Guid.Parse(cityIdString);

    return $"City Id = {cityId}";
});

app.Run();
```

---

# Testing

### Decimal

Request

```text
/products/999.99
```

Output

```text
Price = 999.99
```

---

Request

```text
/products/ABC
```

Output

```text
404 Not Found
```

---

### GUID

Request

```text
/cities/550e8400-e29b-41d4-a716-446655440000
```

Output

```text
City Id = 550e8400-e29b-41d4-a716-446655440000
```

---

Request

```text
/cities/12345
```

Output

```text
404 Not Found
```

---

# Complete Request Flow

```text
                 Browser
                    │
      GET /cities/{cityId}
                    │
                    ▼
             Routing Engine
                    │
       Route Template

/cities/{cityId:guid}

                    │
          Is cityId a GUID?
             /           \
          Yes             No
          │               │
          ▼               ▼
Read RouteValues     Route Doesn't Match
          │               │
Convert to Guid          404
          │
          ▼
      Endpoint
          │
          ▼
    HTTP Response
```

---

# Commonly Used Route Constraints

| Constraint | Example           | Valid Example                                  |
| ---------- | ----------------- | ---------------------------------------------- |
| `int`      | `{id:int}`        | `/students/101`                                |
| `decimal`  | `{price:decimal}` | `/products/99.95`                              |
| `guid`     | `{cityId:guid}`   | `/cities/550e8400-e29b-41d4-a716-446655440000` |
| `datetime` | `{date:datetime}` | `/reports/2026-06-28`                          |
| `bool`     | `{active:bool}`   | `/status/true`                                 |
| `alpha`    | `{name:alpha}`    | `/employee/Raman`                              |

---

# Best Practice

If you're using Minimal APIs, the simplest approach is to let ASP.NET Core perform the conversion automatically:

```csharp
app.MapGet("/cities/{cityId:guid}", (Guid cityId) =>
{
    return $"City Id = {cityId}";
});
```

Use `Request.RouteValues` when:

* You want to understand how routing stores parameter values internally.
* You are writing custom middleware.
* You need to inspect or manipulate route values before model binding.

---

# Interview Questions

### 1. What is the purpose of a GUID route constraint?

A GUID route constraint (`{id:guid}`) ensures that the route only matches if the parameter is a valid GUID. Invalid values cause the route not to match, resulting in another matching route being tried or a `404 Not Found` response.

---

### 2. How do you read a GUID route parameter from `HttpContext`?

```csharp
var value = context.Request.RouteValues["cityId"]?.ToString();
Guid cityId = Guid.Parse(value!);
```

or more safely:

```csharp
Guid.TryParse(value, out Guid cityId);
```

---

### 3. What happens if a decimal or GUID constraint fails?

The route is **not matched**. ASP.NET Core continues looking for another matching route. If none exists, it returns **404 Not Found** (or executes a `MapFallback()` endpoint if one is configured).
