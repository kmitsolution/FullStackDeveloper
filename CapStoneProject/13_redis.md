
# Step 12 — Redis for Shopping Cart

We already have a working cart in SQL Server.

For example:

```text
SQL Server
   │
   └── Cart
        └── CartItems
```

We're now going to introduce **Redis** to make cart retrieval faster.

The important architectural decision for our MVP is:

> **SQL Server remains our persistent database, while Redis acts as a fast cache for the cart.**

We will **not delete our SQL cart implementation**.

This gives us:

```text
                 Cart Request
                      │
                      ▼
                    Redis
                 ┌────┴────┐
                 │         │
              Found      Not Found
                 │         │
                 ▼         ▼
              Return    SQL Server
                           │
                           ▼
                        Redis
                           │
                           ▼
                         Return
```

This is a very useful real-world caching pattern.

---

# 12.1 Start Redis

Since you're working locally, the easiest approach is Docker.

Run:

```powershell
docker run -d --name shopsphere-redis -p 6379:6379 redis
```

Check:

```powershell
docker ps
```

You should see something similar to:

```text
shopsphere-redis
```

on:

```text
6379
```

So:

```text
ASP.NET Core
      │
      ▼
localhost:6379
      │
      ▼
Redis
```

---

# 12.2 Install Redis Package

In Visual Studio:

**Tools → NuGet Package Manager → Package Manager Console**

Install:

```powershell
Install-Package StackExchange.Redis
```

This is the .NET Redis client we'll use.

---

# 12.3 Configure Redis

Open:

```text
appsettings.json
```

Add:

```json
"Redis": {
  "ConnectionString": "localhost:6379"
}
```

Your configuration will now contain approximately:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=ShopSphereDB;Trusted_Connection=True;TrustServerCertificate=True;"
  },

  "Jwt": {
    "Key": "ShopSphere-SuperSecret-Key-For-Jwt-2026",
    "Issuer": "ShopSphere",
    "Audience": "ShopSphereUsers"
  },

  "Redis": {
    "ConnectionString": "localhost:6379"
  }
}
```

---

# 12.4 Register Redis in `Program.cs`

Add:

```csharp
using StackExchange.Redis;
```

Then before:

```csharp
var app = builder.Build();
```

add:

```csharp
builder.Services.AddSingleton<IConnectionMultiplexer>(
    ConnectionMultiplexer.Connect(
        builder.Configuration["Redis:ConnectionString"]!));
```

So conceptually:

```text
Services
   │
   ├── ShopSphereDbContext
   │
   ├── Identity
   │
   ├── JWT Authentication
   │
   ├── ProductRepository
   │
   └── Redis Connection
```

---

# 12.5 What is `IConnectionMultiplexer`?

This object manages the connection between our ASP.NET Core application and Redis.

Think of it as:

```text
ASP.NET Core
      │
      │ IConnectionMultiplexer
      ▼
    Redis
```

We register it as:

```csharp
AddSingleton
```

because we want to reuse the Redis connection rather than creating a new connection for every HTTP request.

---

# 12.6 Test Redis Connection

Before modifying our Cart API, let's make sure Redis is working.

Create:

```text
Services
    └── RedisService.cs
```

Create the `Services` folder first.

Add:

```csharp
using StackExchange.Redis;

namespace ShopSphere.API.Services;

public class RedisService
{
    private readonly IConnectionMultiplexer _redis;

    public RedisService(IConnectionMultiplexer redis)
    {
        _redis = redis;
    }

    public async Task SetAsync(
    string key,
    string value,
    TimeSpan? expiry = null)
{
    var database = _redis.GetDatabase();

    await database.StringSetAsync(
        key,
        value,
        expiry ?? TimeSpan.FromMinutes(30));
}
    public async Task<string?> GetAsync(string key)
    {
        var database = _redis.GetDatabase();

        return await database.StringGetAsync(key);
    }

    public async Task DeleteAsync(string key)
    {
        var database = _redis.GetDatabase();

        await database.KeyDeleteAsync(key);
    }
}
```

---

# 12.7 Register `RedisService`

In `Program.cs`:

```csharp
builder.Services.AddSingleton<RedisService>();
```

So now:

```csharp
builder.Services.AddSingleton<IConnectionMultiplexer>(
    ConnectionMultiplexer.Connect(
        builder.Configuration["Redis:ConnectionString"]!));

builder.Services.AddSingleton<RedisService>();
```

---

# 12.8 Create a Simple Redis Test Endpoint

We don't want to modify the cart yet.

Create:

```text
Controllers
   └── RedisController.cs
```

Add:

```csharp
using Microsoft.AspNetCore.Mvc;
using ShopSphere.API.Services;

namespace ShopSphere.API.Controllers;

[ApiController]
[Route("api/[controller]")]
public class RedisController : ControllerBase
{
    private readonly RedisService _redisService;

    public RedisController(RedisService redisService)
    {
        _redisService = redisService;
    }

    [HttpGet("test")]
    public async Task<IActionResult> TestRedis()
    {
        await _redisService.SetAsync(
            "shopsphere:test",
            "Redis is working!");

        var value = await _redisService.GetAsync(
            "shopsphere:test");

        return Ok(new
        {
            value
        });
    }
}
```

---

# 12.9 Test Redis

Start your ASP.NET Core application.

Call:

```text
GET http://localhost:5292/api/Redis/test
```

Expected:

```json
{
    "value": "Redis is working!"
}
```

If you get this response:

**Redis is successfully connected to ASP.NET Core.**

---

# 12.10 Understand What Just Happened

When you called:

```text
GET /api/Redis/test
```

ASP.NET Core executed:

```csharp
await _redisService.SetAsync(
    "shopsphere:test",
    "Redis is working!");
```

Redis now contains approximately:

```text
Key:
shopsphere:test

Value:
Redis is working!
```

Then:

```csharp
await _redisService.GetAsync(
    "shopsphere:test");
```

retrieved it.

The flow was:

```text
Postman
   │
   ▼
RedisController
   │
   ▼
RedisService
   │
   ▼
StackExchange.Redis
   │
   ▼
Redis
```

---

# 12.11 Why Redis for Cart?

Imagine ShopSphere has:

```text
100,000 customers
```

and many customers frequently open their carts.

Cart operations are high-frequency:

```text
GET cart
Add item
Update quantity
Remove item
GET cart
GET cart
GET cart
```

We don't necessarily want every read to go:

```text
ASP.NET Core
      ↓
SQL Server
      ↓
Disk/database processing
```

Redis is an in-memory data store, so it can provide very fast access.

We can use:

```text
ASP.NET Core
      ↓
Redis
```

for frequently accessed cart information.

---

# 12.12 Our Architecture

After this lesson:

```text
                    ShopSphere
                        │
                ASP.NET Core API
                        │
              ┌─────────┴─────────┐
              │                   │
              ▼                   ▼
          SQL Server            Redis
              │                   │
        Persistent Data       Fast Cache
```

For the cart specifically:

```text
                    Cart
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
      SQL Server              Redis
      Permanent              Fast access
      storage                / caching
```

---

# 12.13 Why aren't we moving the whole cart to Redis yet?

Because I want you to understand an important production concept:

**Redis and SQL Server have different responsibilities.**

SQL Server:

```text
Permanent
Transactional
Relational
```

Redis:

```text
Fast
In-memory
Cache
```

For our ShopSphere MVP, we'll use:

```text
SQL Server = source of truth
Redis = cached cart
```

That also means if Redis is restarted, we don't permanently lose the customer's cart.

---

# Step 12 Checkpoint

For now, **don't modify `CartController`**.

Just make sure these work:

### 1. Redis container

```powershell
docker ps
```

You should see:

```text
shopsphere-redis
```

### 2. Redis API

```text
GET http://localhost:5292/api/Redis/test
```

Expected:

```json
{
    "value": "Redis is working!"
}
```

Once this works, **Step 12 is complete**.

Then we'll do **Step 13 — integrate Redis with our actual Cart API** so that:

```text
GET /api/Cart
```

first checks Redis, and if the cart isn't cached, retrieves it from SQL Server and places it into Redis.
