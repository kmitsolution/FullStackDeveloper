# Routing Constraints in ASP.NET Core

Route Constraints are used to **restrict route parameters** so that only values matching a specific rule are accepted.

Without constraints, any value can match a route.

For example:

```text
/student/100
```

and

```text
/student/abc
```

both match

```text
/student/{id}
```

But if `id` should always be a number, we can enforce that using a route constraint.

---

# What is a Route Constraint?

A **Route Constraint** tells ASP.NET Core:

> "Only match this route if the parameter satisfies a specific condition."

Example:

```text
/student/{id:int}
```

Here,

```text
{id:int}
```

means

> The `id` parameter **must be an integer**.

---

# Why Do We Need Route Constraints?

Suppose we have

```csharp
app.MapGet("/student/{id}", (string id) =>
{
    return $"Student Id = {id}";
});
```

These URLs all match:

```text
/student/101

/student/ABC

/student/xyz

/student/123abc
```

Sometimes this is not what we want.

Instead,

we want only

```text
/student/101
```

to be valid.

---

# Without Constraint

Route

```text
/student/{id}
```

Matches

```text
/student/100
```

✅

Matches

```text
/student/abc
```

✅

Matches

```text
/student/xyz
```

✅

---

# With Constraint

Route

```text
/student/{id:int}
```

Matches

```text
/student/100
```

✅

Matches

```text
/student/abc
```

❌

Returns

```text
404 Not Found
```

---

# Visual Representation

```text
Browser

↓

GET /student/100

↓

Routing

↓

{id:int}

↓

100 is Integer

↓

Endpoint
```

---

```text
Browser

↓

GET /student/abc

↓

Routing

↓

{id:int}

↓

abc is NOT Integer

↓

404 Not Found
```

---

# Example 1 - Integer Constraint

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student/{id:int}",
(int id) =>
{
    return $"Student Id = {id}";
});

app.Run();
```

---

## Test 1

Open

```text
https://localhost:5001/student/101
```

Output

```text
Student Id = 101
```

---

## Test 2

Open

```text
https://localhost:5001/student/abc
```

Output

```text
404 Not Found
```

---

# Request Flow

```text
Request

↓

/student/101

↓

Is 101 Integer?

↓

Yes

↓

Execute Endpoint
```

---

```text
Request

↓

/student/ABC

↓

Is ABC Integer?

↓

No

↓

404
```

---

# Example 2 - DateTime Constraint

Suppose your application accepts only dates.

```csharp
app.MapGet("/report/{date:datetime}",
(DateTime date) =>
{
    return $"Report Date = {date:d}";
});
```

---

Request

```text
/report/2026-06-28
```

Output

```text
Report Date = 28-06-2026
```

---

Request

```text
/report/Hello
```

Output

```text
404 Not Found
```

---

# Visual

```text
/report/2026-06-28

↓

datetime

↓

Valid

↓

Endpoint
```

---

```text
/report/ABC

↓

datetime

↓

Invalid

↓

404
```

---

# Example 3 - Guid Constraint

```csharp
app.MapGet("/user/{id:guid}",
(Guid id) =>
{
    return $"User Id = {id}";
});
```

Valid

```text
/user/7b9d8c0b-5e0d-4a5e-a75f-5f80b5f26e4d
```

Invalid

```text
/user/100
```

↓

404

---

# Example 4 - Decimal Constraint

```csharp
app.MapGet("/price/{amount:decimal}",
(decimal amount) =>
{
    return $"Price = {amount}";
});
```

Valid

```text
/price/250.75
```

Output

```text
Price = 250.75
```

Invalid

```text
/price/ABC
```

↓

404

---

# Example 5 - Alpha Constraint

Only letters allowed.

```csharp
app.MapGet("/employee/{name:alpha}",
(string name) =>
{
    return $"Employee = {name}";
});
```

Valid

```text
/employee/Raman
```

Output

```text
Employee = Raman
```

Invalid

```text
/employee/Raman123
```

↓

404

---

# Example 6 - Bool Constraint

```csharp
app.MapGet("/status/{active:bool}",
(bool active) =>
{
    return $"Status = {active}";
});
```

Valid

```text
/status/true
```

Output

```text
Status = True
```

---

Invalid

```text
/status/yes
```

↓

404

---

# Multiple Constraints

```csharp
app.MapGet("/employee/{id:int}/{joiningDate:datetime}",
(int id,
 DateTime joiningDate) =>
{
    return $"""

Employee Id : {id}

Joining Date : {joiningDate:d}

""";
});
```

Request

```text
/employee/100/2026-06-28
```

Output

```text
Employee Id : 100

Joining Date : 28-06-2026
```

---

# Daily Digest Example (Fallback)

Imagine a news application.

We want URLs like:

```text
/dailydigest/2026-06-28
```

Only valid dates should match.

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/dailydigest/{date:datetime}",
(DateTime date) =>
{
    return $"Daily Digest for {date:dd-MM-yyyy}";
});

app.MapFallback(() =>
{
    return Results.Text("Invalid URL or Invalid Date");
});

app.Run();
```

---

### Test 1

Open

```text
/dailydigest/2026-06-28
```

Output

```text
Daily Digest for 28-06-2026
```

---

### Test 2

Open

```text
/dailydigest/today
```

The `datetime` constraint fails, so the route doesn't match.

ASP.NET Core then checks for another matching endpoint.

Since no route matches, it executes:

```csharp
app.MapFallback(...)
```

Output

```text
Invalid URL or Invalid Date
```

---

# Flow Diagram

```text
                 Browser
                    │
     /dailydigest/2026-06-28
                    │
                    ▼
             Routing Engine
                    │
      {date:datetime} Constraint
                    │
            Is Valid Date?
             /           \
          Yes             No
          │               │
          ▼               ▼
   Daily Digest      MapFallback()
      Endpoint        "Invalid URL"
```

---

# Common Route Constraints

| Constraint | Description                 | Example            |
| ---------- | --------------------------- | ------------------ |
| `int`      | Integer value               | `{id:int}`         |
| `long`     | Long integer                | `{id:long}`        |
| `float`    | Floating-point number       | `{price:float}`    |
| `double`   | Double value                | `{value:double}`   |
| `decimal`  | Decimal value               | `{amount:decimal}` |
| `bool`     | Boolean (`true` or `false`) | `{active:bool}`    |
| `datetime` | Date and time               | `{date:datetime}`  |
| `guid`     | GUID                        | `{id:guid}`        |
| `alpha`    | Letters only                | `{name:alpha}`     |

---

# Complete Routing Flow

```text
                   Browser
                      │
          GET /student/ABC
                      │
                      ▼
              Routing Engine
                      │
             Route Template

          /student/{id:int}

                      │
          Is "ABC" an Integer?
                /          \
             Yes            No
             │              │
             ▼              ▼
        Execute Route   Try Next Route
                             │
                             ▼
                     MapFallback()
                             │
                             ▼
                      404 / Custom Page
```

---

# Complete Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/student/{id:int}", (int id) =>
{
    return $"Student Id : {id}";
});

app.MapGet("/report/{date:datetime}", (DateTime date) =>
{
    return $"Report Date : {date:dd-MM-yyyy}";
});

app.MapFallback(() =>
{
    return Results.Text("Route not found or invalid parameter.");
});

app.Run();
```

### Test URLs

| URL                  | Result                     |
| -------------------- | -------------------------- |
| `/student/101`       | ✅ Student Id : 101         |
| `/student/abc`       | ❌ Fallback executes        |
| `/report/2026-06-28` | ✅ Report Date : 28-06-2026 |
| `/report/today`      | ❌ Fallback executes        |

---

# Interview Questions

### 1. What is a route constraint?

A route constraint restricts the values that a route parameter can accept. If the value doesn't satisfy the constraint, the route is not considered a match, and ASP.NET Core continues searching for another matching route or executes a fallback endpoint if one exists.

---

### 2. How do you define an integer constraint?

```csharp
app.MapGet("/student/{id:int}", (int id) =>
{
    return $"Student Id = {id}";
});
```

Only URLs with an integer `id` will match.

---

### 3. What happens if a route constraint fails?

If a constraint fails:

* The route **does not match**.
* ASP.NET Core looks for another matching route.
* If no route matches, it returns **404 Not Found** or executes `MapFallback()` if one is configured.

---

### 4. What are some commonly used route constraints?

* `int`
* `long`
* `decimal`
* `bool`
* `datetime`
* `guid`
* `alpha`

These constraints help ensure that URLs contain values of the expected type before the endpoint is executed.
