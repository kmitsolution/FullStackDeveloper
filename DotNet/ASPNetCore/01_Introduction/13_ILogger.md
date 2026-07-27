`ILogger` is ASP.NET Core's built-in logging framework. Instead of using `Console.WriteLine()`, you use `ILogger` to write logs with different levels like Information, Warning, Error, etc.

Think of it this way:

* **Console.WriteLine()** → Simple messages for learning/debugging.
* **ILogger** → Professional logging used in production applications.

---

# Why not Console.WriteLine()?

Example:

```csharp
Console.WriteLine("Student Saved");
```

Problems:

* No log levels
* Cannot easily write to files, databases, Azure, etc.
* Difficult to filter messages

With `ILogger`:

```csharp
logger.LogInformation("Student Saved");
```

Now ASP.NET Core knows this is an **Information** log.

---

# Example 1: Simple Logging

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", (ILogger<Program> logger) =>
{
    logger.LogInformation("Home page visited.");

    return "Hello World";
});

app.Run();
```

Browse:

```text
http://localhost:5220/
```

Output:

```text
info: Program[0]
      Home page visited.
```

---

# Example 2: Logging Query String

```csharp
app.MapGet("/student", (HttpRequest request,
                        ILogger<Program> logger) =>
{
    string id = request.Query["id"];

    logger.LogInformation("Student Id = {Id}", id);

    return $"Student Id = {id}";
});
```

Request:

```text
GET /student?id=101
```

Console:

```text
info: Program[0]
      Student Id = 101
```

Notice the placeholder `{Id}`. This is called **structured logging**. It lets logging systems treat `Id` as a searchable field instead of just text.

---

# Example 3: Different Log Levels

```csharp
app.MapGet("/", (ILogger<Program> logger) =>
{
    logger.LogTrace("Trace Message");

    logger.LogDebug("Debug Message");

    logger.LogInformation("Information Message");

    logger.LogWarning("Warning Message");

    logger.LogError("Error Message");

    logger.LogCritical("Critical Message");

    return "Done";
});
```

Output (depending on your logging configuration):

```text
info: Program[0]
      Information Message

warn: Program[0]
      Warning Message

fail: Program[0]
      Error Message

crit: Program[0]
      Critical Message
```

By default, `Trace` and `Debug` are often not shown because the default minimum log level is `Information`.

---

# Example 4: Logging Inside Try-Catch

```csharp
app.MapGet("/", (ILogger<Program> logger) =>
{
    try
    {
        int number = 10 / 0;

        return number;
    }
    catch (Exception ex)
    {
        logger.LogError(ex, "An exception occurred.");

        return "Error";
    }
});
```

Console:

```text
fail: Program[0]
      An exception occurred.
System.DivideByZeroException...
```

Passing the exception (`ex`) records the stack trace as well.

---

# Example 5: Logging Before and After Work

```csharp
app.MapGet("/save", async (ILogger<Program> logger) =>
{
    logger.LogInformation("Saving student...");

    await Task.Delay(2000);

    logger.LogInformation("Student saved successfully.");

    return "Saved";
});
```

Console:

```text
info: Program[0]
      Saving student...

(wait 2 seconds)

info: Program[0]
      Student saved successfully.
```

---

# Logging in a Controller

```csharp
[ApiController]
[Route("api/[controller]")]
public class StudentController : ControllerBase
{
    private readonly ILogger<StudentController> _logger;

    public StudentController(
        ILogger<StudentController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public IActionResult Get()
    {
        _logger.LogInformation("Getting students.");

        return Ok();
    }
}
```

ASP.NET Core injects `ILogger<StudentController>` using Dependency Injection.

---

# Logging Levels

| Method             | When to Use                                             |
| ------------------ | ------------------------------------------------------- |
| `LogTrace()`       | Very detailed debugging information                     |
| `LogDebug()`       | Information useful during development                   |
| `LogInformation()` | Normal application flow (login, save, request received) |
| `LogWarning()`     | Something unexpected, but the app can continue          |
| `LogError()`       | An operation failed or an exception occurred            |
| `LogCritical()`    | Serious failure that may stop the application           |

---

# Request Flow

```text
Client
   │
   ▼
MapGet("/student")
   │
   ▼
logger.LogInformation("Request Received")
   │
   ▼
Business Logic
   │
   ▼
logger.LogInformation("Data Returned")
   │
   ▼
Response
```

---

# Real-World Example

Suppose a user logs in.

```csharp
_logger.LogInformation(
    "User {UserName} logged in at {Time}",
    username,
    DateTime.Now);
```

Console:

```text
info: StudentController[0]
      User Raman logged in at 7/27/2026 8:15:32 PM
```

Using placeholders (`{UserName}`, `{Time}`) is recommended over string concatenation because it supports structured logging and makes it easier to search and analyze logs.

---

## Interview Definition

> `ILogger` is the built-in logging abstraction in ASP.NET Core. It allows applications to record messages at different log levels (Trace, Debug, Information, Warning, Error, and Critical). It integrates with Dependency Injection and can write logs to multiple providers such as the console, files (through third-party providers), Azure Application Insights, Event Log, or other logging systems.
