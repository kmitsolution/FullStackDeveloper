# Routing Constraints (MinLength, MaxLength, Length, Range, Alpha, Regex) in ASP.NET Core

Route constraints are used to **validate route parameters before the request reaches your endpoint**.

Instead of writing validation code inside your endpoint, you can let the **routing engine** decide whether a request is valid.

If the constraint fails:

* The route **does not match**
* ASP.NET Core tries another matching route
* If no route matches, **404 Not Found** (or `MapFallback()`) is returned

---

# Why Do We Need Constraints?

Suppose your Employee IDs always have **exactly 5 characters**.

Valid

```text
/employee/EMP01
```

Invalid

```text
/employee/E1
```

Instead of checking this inside the endpoint, Routing can do it automatically.

---

# Constraint Flow

```text
Browser

↓

Request URL

↓

Routing Engine

↓

Check Constraint

↓

Valid?

↓

Yes → Execute Endpoint

No  → Try Next Route → 404
```

---

# 1. MinLength Constraint

Ensures the parameter has **at least** a specified number of characters.

Syntax

```text
{name:minlength(3)}
```

---

## Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/employee/{name:minlength(3)}",
(string name) =>
{
    return $"Employee Name : {name}";
});

app.Run();
```

---

### Valid

```text
/employee/Raman
```

Output

```text
Employee Name : Raman
```

---

### Invalid

```text
/employee/Al
```

Output

```text
404 Not Found
```

---

# Flow

```text
/employee/Al

↓

Length = 2

↓

Minimum = 3

↓

Failed

↓

404
```

---

# 2. MaxLength Constraint

Ensures the parameter is **not longer** than the specified length.

Syntax

```text
{name:maxlength(8)}
```

---

## Example

```csharp
app.MapGet("/employee/{name:maxlength(8)}",
(string name) =>
{
    return $"Employee : {name}";
});
```

---

Valid

```text
/employee/Raman
```

Invalid

```text
/employee/Christopher
```

↓

404

---

# 3. Length Constraint

Requires the parameter length to be within a range.

Syntax

```text
{name:length(3,8)}
```

Meaning

```text
Minimum = 3

Maximum = 8
```

---

## Example

```csharp
app.MapGet("/employee/{name:length(3,8)}",
(string name) =>
{
    return $"Employee : {name}";
});
```

---

### Valid

```text
/employee/Raman
```

Length

```text
5
```

Matches

---

### Invalid

```text
/employee/Jo
```

Length

```text
2
```

↓

404

---

### Invalid

```text
/employee/Christopher
```

Length

```text
11
```

↓

404

---

# Visual Representation

```text
Browser

↓

employee/Raman

↓

Length

↓

Between 3 and 8 ?

↓

Yes

↓

Endpoint
```

---

# 4. Range Constraint

Used with numeric values.

Syntax

```text
{id:range(1,100)}
```

Only values between

```text
1

and

100
```

are accepted.

---

## Example

```csharp
app.MapGet("/employee/{id:range(1,100)}",
(int id) =>
{
    return $"Employee Id = {id}";
});
```

---

Valid

```text
/employee/55
```

Output

```text
Employee Id = 55
```

---

Invalid

```text
/employee/150
```

↓

404

---

Invalid

```text
/employee/-5
```

↓

404

---

# Flow

```text
Request

↓

55

↓

Range 1-100

↓

Accepted
```

---

```text
Request

↓

150

↓

Range 1-100

↓

Rejected
```

---

# 5. Alpha Constraint

Allows only alphabetic characters.

Syntax

```text
{name:alpha}
```

---

## Example

```csharp
app.MapGet("/employee/{name:alpha}",
(string name) =>
{
    return $"Employee Name = {name}";
});
```

---

Valid

```text
/employee/Raman
```

Output

```text
Employee Name = Raman
```

---

Invalid

```text
/employee/Raman123
```

↓

404

---

Invalid

```text
/employee/R@man
```

↓

404

---

# Visual

```text
Employee Name

↓

Letters Only?

↓

Yes

↓

Endpoint
```

---

# 6. Regex Constraint

The Regex constraint allows you to define your own validation rule using a regular expression.

Syntax

```text
{name:regex(pattern)}
```

---

## Example 1 – Employee Code

Suppose employee IDs must look like

```text
EMP001

EMP999
```

Pattern

```text
EMP followed by 3 digits
```

Program

```csharp
app.MapGet(
"/employee/{code:regex(^EMP[0-9]{3}$)}",
(string code) =>
{
    return $"Employee Code = {code}";
});
```

---

Valid

```text
/employee/EMP123
```

Output

```text
Employee Code = EMP123
```

---

Invalid

```text
/employee/ABC123
```

↓

404

---

Invalid

```text
/employee/EMP12
```

↓

404

---

# Regex Explanation

```text
^

Start

EMP

Fixed Text

[0-9]

Digits

{3}

Exactly 3 digits

$

End
```

---

# Example 2 – Sales Report

Suppose reports are stored by year.

Allowed

```text
2025

2026

2027
```

Route

```csharp
app.MapGet(
"/sales/{year:regex(^20[2-9][0-9]$)}",
(string year) =>
{
    return $"Sales Report {year}";
});
```

---

Valid

```text
/sales/2026
```

Output

```text
Sales Report 2026
```

---

Invalid

```text
/sales/1999
```

↓

404

---

Invalid

```text
/sales/ABC
```

↓

404

---

# Example 3 – Phone Number

```csharp
app.MapGet(
"/phone/{number:regex(^[0-9]{10}$)}",
(string number) =>
{
    return $"Phone = {number}";
});
```

---

Valid

```text
/phone/9876543210
```

Output

```text
Phone = 9876543210
```

---

Invalid

```text
/phone/12345
```

↓

404

---

# Combining Constraints

Suppose

Employee ID

must

* be integer
* between 1 and 500

```csharp
app.MapGet("/employee/{id:int:range(1,500)}",
(int id) =>
{
    return $"Employee Id = {id}";
});
```

---

Valid

```text
/employee/250
```

Accepted

---

Invalid

```text
/employee/900
```

Rejected

---

# Complete Example

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/employee/{name:minlength(3)}",
(string name) =>
{
    return $"Employee : {name}";
});

app.MapGet("/employeeid/{id:int:range(1,100)}",
(int id) =>
{
    return $"Employee Id : {id}";
});

app.MapGet("/sales/{year:regex(^20[2-9][0-9]$)}",
(string year) =>
{
    return $"Sales Report {year}";
});

app.Run();
```

---

# Testing

| URL               | Result    |
| ----------------- | --------- |
| `/employee/Raman` | ✅ Success |
| `/employee/Al`    | ❌ 404     |
| `/employeeid/55`  | ✅ Success |
| `/employeeid/150` | ❌ 404     |
| `/sales/2026`     | ✅ Success |
| `/sales/1999`     | ❌ 404     |
| `/sales/ABC`      | ❌ 404     |

---

# Complete Request Flow

```text
                    Browser
                       │
          GET /employee/Raman
                       │
                       ▼
                Routing Engine
                       │
          Route Template

/employee/{name:minlength(3)}

                       │
          Length >= 3 ?
               /        \
            Yes          No
             │            │
             ▼            ▼
      Execute Route      404
```

---

# Summary of Common Constraints

| Constraint        | Purpose           | Example                       | Valid    | Invalid             |
| ----------------- | ----------------- | ----------------------------- | -------- | ------------------- |
| `minlength(n)`    | Minimum length    | `{name:minlength(3)}`         | `Raman`  | `Al`                |
| `maxlength(n)`    | Maximum length    | `{name:maxlength(8)}`         | `Raman`  | `Christopher`       |
| `length(min,max)` | Length range      | `{name:length(3,8)}`          | `Raman`  | `Jo`, `Christopher` |
| `range(min,max)`  | Numeric range     | `{id:range(1,100)}`           | `55`     | `150`               |
| `alpha`           | Letters only      | `{name:alpha}`                | `Raman`  | `Raman123`          |
| `regex(pattern)`  | Custom validation | `{code:regex(^EMP[0-9]{3}$)}` | `EMP123` | `EMP12`, `ABC123`   |

---

# Best Practices

* Use **`int`**, **`guid`**, and **`datetime`** constraints for common data types.
* Use **`range`** for numeric validation instead of checking values inside the endpoint.
* Use **`minlength`**, **`maxlength`**, or **`length`** for simple string-length validation.
* Use **`alpha`** when only alphabetic characters are allowed.
* Use **`regex`** only when built-in constraints cannot express your validation, because regular expressions can become difficult to read and maintain.

---

# Interview Questions

### 1. What is the purpose of route constraints?

Route constraints validate route parameter values during route matching. If a parameter does not satisfy the constraint, the route is not matched and the endpoint is not executed.

---

### 2. What is the difference between `minlength`, `maxlength`, and `length`?

* `minlength(3)` → At least 3 characters.
* `maxlength(8)` → At most 8 characters.
* `length(3,8)` → Between 3 and 8 characters inclusive.

---

### 3. What does the `range` constraint do?

It restricts numeric values to a specified range.

Example:

```csharp
app.MapGet("/employee/{id:int:range(1,100)}", (int id) =>
{
    return $"Employee Id = {id}";
});
```

Only values from **1 to 100** match the route.

---

### 4. When should you use the `regex` constraint?

Use `regex` when you need to enforce a custom URL format that cannot be expressed using built-in constraints, such as employee codes (`EMP123`), phone numbers, invoice numbers, or year formats.
