`async` and `await` are used to perform **non-blocking operations**. They allow your application to continue processing other requests while waiting for a long-running operation (database call, API call, file read, etc.) to complete.

---

# Problem Without `async/await`

Suppose your API needs 5 seconds to get data.

```csharp
app.MapGet("/", () =>
{
    Thread.Sleep(5000);

    return "Hello World";
});
```

### What happens?

1. Request comes in.
2. Current thread sleeps for 5 seconds.
3. During those 5 seconds, that thread cannot do anything else.
4. If many users come simultaneously, threads get blocked and application performance decreases.

---

# Using `async/await`

```csharp
app.MapGet("/", async () =>
{
    await Task.Delay(5000);

    return "Hello World";
});
```

### What happens?

1. Request comes in.
2. `Task.Delay(5000)` starts.
3. Current thread is returned to the Thread Pool.
4. Thread can serve other requests.
5. After 5 seconds, another available thread resumes execution.

This is much more scalable.

---

# Understanding `async`

`async` means:

> This method contains asynchronous operations and may pause execution.

Example:

```csharp
async Task MyMethod()
{
}
```

Without `async`, you cannot use `await`.

---

# Understanding `await`

`await` means:

> Pause this method until the task completes, but do NOT block the thread.

Example:

```csharp
await Task.Delay(5000);
```

Think of it like:

```text
Start operation
↓
Release thread
↓
Operation completes
↓
Resume execution
```

---

# Real Example

### Without Async

```csharp
app.MapGet("/", () =>
{
    Console.WriteLine("1");

    Thread.Sleep(5000);

    Console.WriteLine("2");

    return "Done";
});
```

Thread is blocked for 5 seconds.

---

### With Async

```csharp
app.MapGet("/", async () =>
{
    Console.WriteLine("1");

    await Task.Delay(5000);

    Console.WriteLine("2");

    return "Done";
});
```

Output:

```text
1
(wait 5 seconds)
2
```

But during the waiting period, ASP.NET Core can process other requests.

---

# Database Example

```csharp
app.MapGet("/", async () =>
{
    var customers =
        await dbContext.Customers.ToListAsync();

    return customers;
});
```

While SQL Server is executing the query:

* ASP.NET Core thread is free.
* It can handle other users.

---

# External API Example

```csharp
app.MapGet("/", async () =>
{
    HttpClient client = new HttpClient();

    string data =
        await client.GetStringAsync(
            "https://api.github.com");

    return data;
});
```

Again, thread is not blocked while waiting for GitHub response.

---

# Execution Flow

```text
Request Arrives
       ↓
Method Starts
       ↓
await SomeOperation()
       ↓
Thread Returned To Pool
       ↓
Operation Completes
       ↓
Another Thread Continues Execution
       ↓
Response Sent
```

---

# Why ASP.NET Core uses Async extensively?

Because web applications spend most of their time waiting for:

* Database
* REST APIs
* File System
* Redis
* Message Queues

Instead of blocking threads, ASP.NET Core uses async programming to support thousands of concurrent requests.

---

# Example inside `MapGet`

```csharp
app.MapGet("/", async (
    HttpRequest request,
    HttpResponse response) =>
{
    string id = request.Query["id"];

    await Task.Delay(3000);

    await response.WriteAsync(
        $"Hello {id}");
});
```

---

# Important Rules

### Rule 1

If you use `await`, method must be `async`.

❌ Invalid:

```csharp
app.MapGet("/", () =>
{
    await Task.Delay(1000);
});
```

✅ Valid:

```csharp
app.MapGet("/", async () =>
{
    await Task.Delay(1000);
});
```

---

### Rule 2

Avoid using `.Result` or `.Wait()`

❌

```csharp
var data =
    client.GetStringAsync(url).Result;
```

This blocks the thread.

✅

```csharp
var data =
    await client.GetStringAsync(url);
```

---

# Analogy

Imagine ordering tea in a restaurant.

### Synchronous (`Thread.Sleep`)

```text
Order Tea
Sit idle and wait
Drink Tea
```

You cannot do anything else.

### Asynchronous (`await`)

```text
Order Tea
Go talk to friends
Tea arrives
Drink Tea
```

You utilized your waiting time efficiently.

---

# Interview Definition

**async**

> Indicates that a method contains asynchronous operations.

**await**

> Suspends execution of the method until the awaited task completes without blocking the current thread.

---

### Most Common ASP.NET Core Async Methods

```csharp
await context.Response.WriteAsync();

await dbContext.SaveChangesAsync();

await dbContext.Customers.ToListAsync();

await File.ReadAllTextAsync();

await httpClient.GetAsync();
```

Almost all I/O operations in ASP.NET Core have an `Async` version and should generally be preferred in web applications.
