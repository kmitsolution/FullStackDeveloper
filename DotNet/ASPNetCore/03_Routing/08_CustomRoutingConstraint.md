# Custom Route Constraint Class in ASP.NET Core

ASP.NET Core provides many built-in route constraints such as:

* `int`
* `decimal`
* `guid`
* `datetime`
* `alpha`
* `range`
* `regex`

But sometimes these are not enough.

For example, suppose you want a route parameter that accepts **only month names**:

```text
January
February
March
...
December
```

If someone enters

```text
/expenses/Hello
```

the route should **not** match.

To solve this, we create a **Custom Route Constraint**.

---

# What is a Custom Route Constraint?

A Custom Route Constraint is a class that implements the **IRouteConstraint** interface.

It allows you to define your own validation logic for route parameters.

Think of it as creating your own routing rule.

---

# Real-Life Example

Suppose your company generates monthly reports.

Valid URLs

```text
/reports/January

/reports/February

/reports/December
```

Invalid URLs

```text
/reports/Test

/reports/ABC

/reports/Month13
```

A custom constraint ensures that only valid month names are accepted.

---

# How It Works

```text
Browser

↓

Request

↓

Routing Engine

↓

Custom Route Constraint

↓

Valid?

↓

Yes → Endpoint

No → Next Route / 404
```

---

# Step 1 - Create a Constraints Folder

Project Structure

```text
RouteConstraintDemo
│
├── Constraints
│      │
│      └── MonthsRouteConstraint.cs
│
├── Program.cs
```

---

# Step 2 - Create the Constraint Class

**Constraints/MonthsRouteConstraint.cs**

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Routing;
using System.Text.RegularExpressions;

namespace RouteConstraintDemo.Constraints;

public class MonthsRouteConstraint : IRouteConstraint
{
    public bool Match(
        HttpContext? httpContext,
        IRouter? route,
        string routeKey,
        RouteValueDictionary values,
        RouteDirection routeDirection)
    {
        if (!values.ContainsKey(routeKey))
            return false;

        string? month = values[routeKey]?.ToString();

        if (month == null)
            return false;

        return Regex.IsMatch(
            month,
            "^(January|February|March|April|May|June|July|August|September|October|November|December)$",
            RegexOptions.IgnoreCase);
    }
}
```

---

# Understanding the Match() Method

ASP.NET Core automatically calls

```csharp
Match()
```

whenever a route uses this constraint.

If

```csharp
return true;
```

↓

The route matches.

If

```csharp
return false;
```

↓

Routing ignores the route and looks for another one.

---

# Match() Parameters

```csharp
public bool Match(
    HttpContext? httpContext,
    IRouter? route,
    string routeKey,
    RouteValueDictionary values,
    RouteDirection routeDirection)
```

| Parameter        | Description                        |
| ---------------- | ---------------------------------- |
| `HttpContext`    | Current request                    |
| `route`          | Current route                      |
| `routeKey`       | Parameter name (e.g., `"month"`)   |
| `values`         | All extracted route values         |
| `routeDirection` | Incoming request or URL generation |

---

# Reading the Route Value

Suppose the request is

```text
/reports/January
```

Then

```csharp
string? month =
    values[routeKey]?.ToString();
```

returns

```text
January
```

---

# Regex Validation

```csharp
return Regex.IsMatch(
    month,
    "^(January|February|March|April|May|June|July|August|September|October|November|December)$",
    RegexOptions.IgnoreCase);
```

This regex allows only:

```text
January
February
March
April
May
June
July
August
September
October
November
December
```

Because of

```csharp
RegexOptions.IgnoreCase
```

all of these are valid:

```text
January

JANUARY

january

JaNuArY
```

---

# Step 3 - Register the Constraint

In **Program.cs**

```csharp
using RouteConstraintDemo.Constraints;

var builder = WebApplication.CreateBuilder(args);

builder.Services.Configure<RouteOptions>(options =>
{
    options.ConstraintMap.Add(
        "months",
        typeof(MonthsRouteConstraint));
});

var app = builder.Build();
```

---

# What Does ConstraintMap Do?

This line

```csharp
options.ConstraintMap.Add(
    "months",
    typeof(MonthsRouteConstraint));
```

means

```text
months

↓

MonthsRouteConstraint
```

Now ASP.NET Core recognizes

```text
{month:months}
```

---

# Step 4 - Use the Constraint

```csharp
app.MapGet("/reports/{month:months}",
(string month) =>
{
    return $"Monthly Report : {month}";
});

app.Run();
```

Notice

```text
{month:months}
```

Here

```text
months
```

is our custom constraint.

---

# Testing

### Valid

```text
/reports/January
```

Output

```text
Monthly Report : January
```

---

### Valid

```text
/reports/December
```

Output

```text
Monthly Report : December
```

---

### Invalid

```text
/reports/Hello
```

Output

```text
404 Not Found
```

---

### Invalid

```text
/reports/Test
```

Output

```text
404 Not Found
```

---

# Complete Program.cs

```csharp
using RouteConstraintDemo.Constraints;

var builder = WebApplication.CreateBuilder(args);

builder.Services.Configure<RouteOptions>(options =>
{
    options.ConstraintMap.Add(
        "months",
        typeof(MonthsRouteConstraint));
});

var app = builder.Build();

app.MapGet("/reports/{month:months}",
(string month) =>
{
    return $"Monthly Report : {month}";
});

app.MapFallback(() =>
{
    return "Invalid Month";
});

app.Run();
```

---

# Request Flow

```text
Browser

↓

GET /reports/January

↓

Routing

↓

month = January

↓

MonthsRouteConstraint

↓

Regex Match

↓

True

↓

Endpoint

↓

Response
```

---

# Invalid Request Flow

```text
Browser

↓

GET /reports/Test

↓

Routing

↓

MonthsRouteConstraint

↓

Regex Match

↓

False

↓

Try Next Route

↓

MapFallback()

↓

Invalid Month
```

---

# Visual Representation

```text
                    Browser
                       │
        GET /reports/January
                       │
                       ▼
               Routing Engine
                       │
        Route Template

/reports/{month:months}

                       │
                       ▼
       MonthsRouteConstraint
                       │
             Match()
                       │
             Regex Check
                       │
          Is Valid Month?
            /          \
         Yes            No
          │              │
          ▼              ▼
     Execute Route   MapFallback()
```

---

# Another Example - Weekdays Constraint

Instead of months, suppose you allow only weekdays.

```csharp
return Regex.IsMatch(
    day,
    "^(Monday|Tuesday|Wednesday|Thursday|Friday)$",
    RegexOptions.IgnoreCase);
```

Then

```text
/schedule/Monday
```

works

while

```text
/schedule/Sunday
```

returns **404**.

---

# Why Not Use the Built-in Regex Constraint?

You could write:

```csharp
app.MapGet(
"/reports/{month:regex(^January|February|March$)}",
...
);
```

But imagine all 12 months:

```text
January
February
March
April
May
June
July
August
September
October
November
December
```

The route becomes difficult to read.

Instead,

```text
{month:months}
```

is much cleaner and easier to maintain.

---

# Complete Pipeline

```text
Browser
   │
   ▼
HTTP Request
   │
   ▼
Routing Engine
   │
   ▼
ConstraintMap
   │
   ▼
MonthsRouteConstraint
   │
   ▼
Match()
   │
   ▼
Regex Validation
   │
   ├──────── True ───────► Endpoint
   │
   └──────── False ──────► Next Route / 404 / MapFallback
```

---

# Summary

| Component              | Purpose                                           |
| ---------------------- | ------------------------------------------------- |
| `IRouteConstraint`     | Interface for creating custom route constraints   |
| `Match()`              | Called by ASP.NET Core during route matching      |
| `RouteValueDictionary` | Contains route parameter values                   |
| `ConstraintMap`        | Registers a custom constraint name                |
| `{month:months}`       | Uses the custom constraint in a route             |
| `Regex`                | Validates the month value                         |
| `MapFallback()`        | Handles requests when the custom constraint fails |

---

# Interview Questions

### 1. What is a custom route constraint?

A custom route constraint is a class that implements `IRouteConstraint` and provides custom validation logic for route parameters during route matching.

---

### 2. What is the purpose of the `Match()` method?

`Match()` is invoked by the ASP.NET Core routing engine. It examines the route parameter and returns:

* `true` if the value is valid and the route should match.
* `false` if the value is invalid, causing the routing engine to skip the route.

---

### 3. Why register a custom constraint in `ConstraintMap`?

The routing system needs to know which class should handle a custom constraint name.

```csharp
builder.Services.Configure<RouteOptions>(options =>
{
    options.ConstraintMap.Add(
        "months",
        typeof(MonthsRouteConstraint));
});
```

This registration allows you to use:

```text
/reports/{month:months}
```

where `months` maps to the `MonthsRouteConstraint` class.

---

### 4. When should you create a custom route constraint?

Create a custom route constraint when the built-in constraints (`int`, `guid`, `datetime`, `regex`, etc.) are not sufficient or when you want reusable, readable validation logic for domain-specific values such as month names, weekdays, product codes, or business identifiers.
