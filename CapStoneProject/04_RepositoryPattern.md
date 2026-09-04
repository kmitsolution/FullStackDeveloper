
# Step 3 — Repository Pattern for Products

Before creating our first controller/API, I want you to understand and implement the **Repository Pattern**.

This matches the course sequence: after building the ASP.NET Core/EF Core foundation, the course introduces Repository Pattern, dependency injection, seeding, filtering and sorting. 

Our flow will become:

```text
                ProductController
                       │
                       ▼
                IProductRepository
                       │
                       ▼
                ProductRepository
                       │
                       ▼
                ShopSphereDbContext
                       │
                       ▼
                    EF Core
                       │
                       ▼
                  SQL Server
```

For **this step**, we will only build:

```text
IProductRepository
ProductRepository
```

We will **not create `ProductController` yet**.

---

# 3.1 Why do we need Repository Pattern?

Currently, EF Core gives us:

```csharp
_context.Products
```

We could directly use this in a controller.

For example:

```csharp
public class ProductController
{
    private readonly ShopSphereDbContext _context;

    public ProductController(ShopSphereDbContext context)
    {
        _context = context;
    }
}
```

But then the controller starts knowing too much about the database.

We want:

```text
Controller
     ↓
Repository
     ↓
DbContext
```

The controller should basically say:

> "Give me the products."

It shouldn't need to know **how SQL Server is queried**.

---

# 3.2 Create Repository Folder

In Visual Studio:

Right-click the project:

```text
ShopSphere.API
```

Select:

**Add → New Folder**

Name it:

```text
Repositories
```

Your structure becomes:

```text
ShopSphere.API
│
├── Data
│   └── ShopSphereDbContext.cs
│
├── Models
│   ├── Category.cs
│   ├── Brand.cs
│   ├── Product.cs
│   ├── Customer.cs
│   ├── Address.cs
│   ├── Order.cs
│   └── OrderItem.cs
│
├── Repositories
│
├── Migrations
│
├── Program.cs
└── appsettings.json
```

---

# 3.3 Create IProductRepository

Right-click:

```text
Repositories
```

Select:

**Add → Class**

Name:

```text
IProductRepository.cs
```

Add:

```csharp
using ShopSphere.API.Models;

namespace ShopSphere.API.Repositories;

public interface IProductRepository
{
    Task<IReadOnlyList<Product>> GetProductsAsync();

    Task<Product?> GetProductByIdAsync(int id);
}
```

---

# 3.4 Understand the Interface

We have defined two operations:

```csharp
Task<IReadOnlyList<Product>> GetProductsAsync();
```

Means:

> Give me all products.

And:

```csharp
Task<Product?> GetProductByIdAsync(int id);
```

Means:

> Give me one product based on its ID.

Notice that we haven't written **any SQL**.

The interface only defines **what we want to do**.

---

# 3.5 Create ProductRepository

Right-click:

```text
Repositories
```

Select:

**Add → Class**

Name:

```text
ProductRepository.cs
```

Write:

```csharp
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Data;
using ShopSphere.API.Models;

namespace ShopSphere.API.Repositories;

public class ProductRepository : IProductRepository
{
    private readonly ShopSphereDbContext _context;

    public ProductRepository(ShopSphereDbContext context)
    {
        _context = context;
    }

    public async Task<IReadOnlyList<Product>> GetProductsAsync()
    {
        return await _context.Products
            .Include(p => p.Category)
            .Include(p => p.Brand)
            .ToListAsync();
    }

    public async Task<Product?> GetProductByIdAsync(int id)
    {
        return await _context.Products
            .Include(p => p.Category)
            .Include(p => p.Brand)
            .FirstOrDefaultAsync(p => p.Id == id);
    }
}
```

---

# 3.6 What Is Happening Here?

This:

```csharp
_context.Products
```

represents:

```text
Products table
```

Then:

```csharp
.Include(p => p.Category)
```

means:

> Also load the related Category.

And:

```csharp
.Include(p => p.Brand)
```

means:

> Also load the related Brand.

So when we eventually ask for:

```text
iPhone 17
```

we can get:

```text
Product
-----------------
Name: iPhone 17
Price: 79999

Category:
Mobiles

Brand:
Apple
```

rather than just:

```text
Product
-----------------
Name: iPhone 17
Price: 79999
CategoryId: 1
BrandId: 1
```

This is EF Core's eager loading using `Include()`.

---

# 3.7 Why `async`?

Notice:

```csharp
public async Task<IReadOnlyList<Product>>
```

and:

```csharp
await _context.Products.ToListAsync();
```

Database operations can take time.

We don't want our application thread unnecessarily waiting while SQL Server is processing the request.

So we use asynchronous EF Core methods such as:

```text
ToListAsync()
FirstOrDefaultAsync()
SingleOrDefaultAsync()
```

This is the same async programming approach you'll use throughout the API.

---

# 3.8 Register Repository with Dependency Injection

Now we tell ASP.NET Core:

> When somebody asks for `IProductRepository`, create `ProductRepository`.

Open:

```text
Program.cs
```

Currently you have something similar to:

```csharp
builder.Services.AddDbContext<ShopSphereDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));
```

Add:

```csharp
builder.Services.AddScoped<IProductRepository, ProductRepository>();
```

You'll need:

```csharp
using ShopSphere.API.Repositories;
```

So:

```csharp
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Data;
using ShopSphere.API.Repositories;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<ShopSphereDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddScoped<IProductRepository, ProductRepository>();

var app = builder.Build();

app.Run();
```

---

# 3.9 What Does `AddScoped` Mean?

This line:

```csharp
builder.Services.AddScoped<IProductRepository, ProductRepository>();
```

means:

```text
IProductRepository
        ↓
ProductRepository
```

Whenever ASP.NET Core needs:

```csharp
IProductRepository
```

it will provide:

```csharp
ProductRepository
```

So later our controller can simply have:

```csharp
public ProductController(
    IProductRepository productRepository)
{
    ...
}
```

ASP.NET Core automatically provides the repository.

This is **Dependency Injection**.

---

# 3.10 Our Architecture Now

We have reached:

```text
┌────────────────────────────┐
│      Future Controller     │
└─────────────┬──────────────┘
              │
              ▼
     IProductRepository
              │
              ▼
     ProductRepository
              │
              ▼
     ShopSphereDbContext
              │
              ▼
         Entity Framework
              │
              ▼
          SQL Server
```

The important thing is that the future controller will depend on:

```text
IProductRepository
```

rather than directly depending on:

```text
ShopSphereDbContext
```

---

# 3.11 One Important Point

You may wonder:

> "Why create an interface when `ProductRepository` already exists?"

Because later we can change the implementation without changing the controller.

For example:

```text
IProductRepository
       │
       ├── ProductRepository
       │
       └── CachedProductRepository
```

The controller still works with:

```text
IProductRepository
```

This abstraction becomes particularly useful when we later introduce caching with Redis and more advanced querying.

---

# 3.12 Do We Need a Migration?

**No.**

We didn't change:

* Entity classes
* Database schema
* Relationships
* Columns

We only added:

```text
IProductRepository
ProductRepository
```

These are application classes, not database changes.

Therefore:

```text
❌ Add-Migration
❌ Update-Database
```

are **not required** for this step.

---

# 3.13 Current Project Structure

You should now have:

```text
ShopSphere.API
│
├── Data
│   └── ShopSphereDbContext.cs
│
├── Models
│   ├── Category.cs
│   ├── Brand.cs
│   ├── Product.cs
│   ├── Customer.cs
│   ├── Address.cs
│   ├── Order.cs
│   └── OrderItem.cs
│
├── Repositories
│   ├── IProductRepository.cs
│   └── ProductRepository.cs
│
├── Migrations
│   └── ...
│
├── Program.cs
├── appsettings.json
└── ShopSphere.API.csproj
```

---

# 3.14 Don't Create the Controller Yet

We are deliberately stopping here.

Our next step will be:

# Step 4 — First Product API

We'll create:

```text
ProductsController
```

and expose:

```http
GET /api/products
```

and:

```http
GET /api/products/{id}
```

The important learning will be seeing the complete journey:

```text
HTTP Request
     ↓
ProductsController
     ↓
IProductRepository
     ↓
ProductRepository
     ↓
ShopSphereDbContext
     ↓
EF Core
     ↓
SQL Server
     ↓
Product
     ↓
JSON Response
```

Then we'll test it using **Swagger/Postman**.

**For now, implement only this Repository step and make sure the project builds without errors.** Once it builds, we'll move to the first controller.
