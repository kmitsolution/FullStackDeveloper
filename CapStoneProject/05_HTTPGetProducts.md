
# ShopSphere — Step 4: Product API and DTOs

## 1. Objective

In this step, we expose the `Product` data through an ASP.NET Core Web API.

We will:

* Create the `ProductsController`
* Use the Repository Pattern to retrieve products
* Create API endpoints
* Understand routing
* Introduce DTOs
* Avoid exposing EF Core entities directly
* Solve circular-reference problems during JSON serialization

At the end of this step, we will have:

```text
GET /api/Products
GET /api/Products/{id}
```

---

# 2. Current Architecture

Our backend currently looks like this:

```text
Client
  │
  │ HTTP Request
  ▼
ProductsController
  │
  │ calls
  ▼
IProductRepository
  │
  ▼
ProductRepository
  │
  │ EF Core
  ▼
ShopSphereDbContext
  │
  ▼
SQL Server
```

The important point is that the **Controller does not directly access the database**.

Instead:

```text
Controller → Repository → EF Core → SQL Server
```

This keeps our application organized.

---

# 3. Create ProductsController

Create:

```text
Controllers
    └── ProductsController.cs
```

Code:

```csharp
using Microsoft.AspNetCore.Mvc;
using ShopSphere.API.DTOs;
using ShopSphere.API.Repositories;

namespace ShopSphere.API.Controllers;

[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IProductRepository _productRepository;

    public ProductsController(IProductRepository productRepository)
    {
        _productRepository = productRepository;
    }

    [HttpGet]
    public async Task<ActionResult<IReadOnlyList<ProductDto>>> GetProducts()
    {
        var products = await _productRepository.GetProductsAsync();

        var result = products.Select(p => new ProductDto
        {
            Id = p.Id,
            Name = p.Name,
            Description = p.Description,
            Price = p.Price,
            PictureUrl = p.PictureUrl,
            Category = p.Category?.Name ?? "",
            Brand = p.Brand?.Name ?? ""
        }).ToList();

        return Ok(result);
    }

    [HttpGet("{id}")]
    public async Task<ActionResult<ProductDto>> GetProduct(int id)
    {
        var product = await _productRepository.GetProductByIdAsync(id);

        if (product == null)
        {
            return NotFound();
        }

        var result = new ProductDto
        {
            Id = product.Id,
            Name = product.Name,
            Description = product.Description,
            Price = product.Price,
            PictureUrl = product.PictureUrl,
            Category = product.Category?.Name ?? "",
            Brand = product.Brand?.Name ?? ""
        };

        return Ok(result);
    }
}
```

---

# 4. Understanding `[ApiController]`

We use:

```csharp
[ApiController]
```

This tells ASP.NET Core that this class is an API controller.

It provides features such as:

* API-specific behavior
* Automatic model validation
* Binding request data
* Returning appropriate HTTP responses

Our controller inherits from:

```csharp
ControllerBase
```

because this is a Web API.

We are **not** using:

```csharp
Controller
```

because we are not creating MVC/Razor pages.

---

# 5. Understanding `[Route("api/[controller]")]`

We have:

```csharp
[Route("api/[controller]")]
public class ProductsController : ControllerBase
```

ASP.NET Core replaces:

```text
[controller]
```

with the controller name without `Controller`.

Therefore:

```text
ProductsController
```

becomes:

```text
Products
```

So the base URL becomes:

```text
/api/Products
```

---

# 6. Understanding `[HttpGet]`

Our first method:

```csharp
[HttpGet]
public async Task<ActionResult<IReadOnlyList<ProductDto>>> GetProducts()
```

responds to:

```text
GET /api/Products
```

For example:

```text
http://localhost:5292/api/Products
```

The flow is:

```text
GET /api/Products
       │
       ▼
ProductsController
       │
       ▼
GetProducts()
       │
       ▼
IProductRepository
       │
       ▼
SQL Server
```

---

# 7. Getting a Single Product

Our second endpoint is:

```csharp
[HttpGet("{id}")]
```

This creates:

```text
GET /api/Products/{id}
```

For example:

```text
GET /api/Products/1
```

ASP.NET Core takes:

```text
1
```

and passes it to:

```csharp
GetProduct(int id)
```

So:

```text
/api/Products/1
```

means:

```text
id = 1
```

---

# 8. Returning 404 When Product Doesn't Exist

We use:

```csharp
if (product == null)
{
    return NotFound();
}
```

Therefore:

```text
GET /api/Products/999
```

if product 999 doesn't exist, returns:

```text
HTTP 404 Not Found
```

This is better than returning an empty object.

---

# 9. Why We Need DTOs

Initially, we returned the EF Core `Product` entity directly:

```csharp
return Ok(products);
```

This caused a JSON serialization error:

```text
A possible object cycle was detected.
```

The reason was our navigation properties.

For example:

```text
Product
   │
   └── Category
          │
          └── Products
                 │
                 └── Category
                        │
                        └── Products
```

The serializer could continue indefinitely:

```text
Product
 → Category
 → Products
 → Category
 → Products
 → Category
 → ...
```

Therefore, directly returning EF Core entities is not a good approach for our API.

---

# 10. Create DTO Folder

Create:

```text
DTOs
```

Our project now looks like:

```text
ShopSphere.API
│
├── Controllers
│   └── ProductsController.cs
│
├── Data
│   └── ShopSphereDbContext.cs
│
├── DTOs
│   └── ProductDto.cs
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
└── Program.cs
```

---

# 11. ProductDto

Create:

```text
DTOs/ProductDto.cs
```

Code:

```csharp
namespace ShopSphere.API.DTOs;

public class ProductDto
{
    public int Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public string Description { get; set; } = string.Empty;

    public decimal Price { get; set; }

    public string PictureUrl { get; set; } = string.Empty;

    public string Category { get; set; } = string.Empty;

    public string Brand { get; set; } = string.Empty;
}
```

---

# 12. What Is a DTO?

DTO means:

> **Data Transfer Object**

A DTO defines exactly what data we want to send to the client.

Our database entity might contain:

```text
Product
 ├── Id
 ├── Name
 ├── Description
 ├── Price
 ├── PictureUrl
 ├── CategoryId
 ├── Category
 └── Brand
```

But our API may only need to send:

```text
ProductDto
 ├── Id
 ├── Name
 ├── Description
 ├── Price
 ├── PictureUrl
 ├── Category
 └── Brand
```

For example:

```json
{
    "id": 1,
    "name": "iPhone 17",
    "description": "Apple smartphone",
    "price": 79999,
    "pictureUrl": "images/iphone17.jpg",
    "category": "Mobiles",
    "brand": "Apple"
}
```

---

# 13. Entity vs DTO

Think of it this way:

### Entity

Represents the **database**.

```text
Product
    ↓
Database structure
```

### DTO

Represents the **API response**.

```text
ProductDto
    ↓
Data sent to Angular/client
```

So we have:

```text
SQL Server
    ↓
EF Core Entity
    ↓
Repository
    ↓
Controller
    ↓
DTO
    ↓
JSON
    ↓
Angular
```

This separation becomes very important as ShopSphere grows.

---

# 14. Mapping Entity to DTO

We convert:

```csharp
Product
```

into:

```csharp
ProductDto
```

using:

```csharp
var result = products.Select(p => new ProductDto
{
    Id = p.Id,
    Name = p.Name,
    Description = p.Description,
    Price = p.Price,
    PictureUrl = p.PictureUrl,
    Category = p.Category?.Name ?? "",
    Brand = p.Brand?.Name ?? ""
}).ToList();
```

For example:

```text
Product
----------------
Id = 1
Name = iPhone 17
Price = 79999
CategoryId = 1
BrandId = 1
```

EF Core also loaded:

```text
Category
----------------
Id = 1
Name = Mobiles
```

and:

```text
Brand
----------------
Id = 1
Name = Apple
```

The DTO becomes:

```text
ProductDto
----------------
Id = 1
Name = iPhone 17
Price = 79999
Category = Mobiles
Brand = Apple
```

---

# 15. Why `Category?.Name`

We use:

```csharp
p.Category?.Name ?? ""
```

The `?.` is the null-conditional operator.

It means:

> If Category exists, get its Name. Otherwise don't throw an exception.

And:

```csharp
?? ""
```

means:

> If the result is null, use an empty string.

The same applies to:

```csharp
p.Brand?.Name ?? ""
```

---

# 16. Repository Responsibility

Our repository still contains:

```csharp
public async Task<IReadOnlyList<Product>> GetProductsAsync()
{
    return await _context.Products
        .Include(p => p.Category)
        .Include(p => p.Brand)
        .ToListAsync();
}
```

The repository is responsible for retrieving the data.

The controller is responsible for converting it to the API format.

Therefore:

```text
Repository
    ↓
Gets Product entities

Controller
    ↓
Converts Product → ProductDto

API
    ↓
Returns JSON
```

---

# 17. Program.cs

Because we are using controllers, `Program.cs` must contain:

```csharp
builder.Services.AddControllers();
```

and:

```csharp
app.MapControllers();
```

Complete relevant configuration:

```csharp
builder.Services.AddControllers();

builder.Services.AddDbContext<ShopSphereDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddScoped<IProductRepository, ProductRepository>();

var app = builder.Build();

app.MapControllers();

app.Run();
```

Without:

```csharp
app.MapControllers();
```

our `/api/Products` route would return:

```text
404 Not Found
```

---

# 18. Testing the API

Start the ASP.NET Core application.

### Get all products

```text
GET http://localhost:5292/api/Products
```

Expected:

```json
[
    {
        "id": 1,
        "name": "iPhone 17",
        "description": "Apple smartphone",
        "price": 79999,
        "pictureUrl": "images/iphone17.jpg",
        "category": "Mobiles",
        "brand": "Apple"
    }
]
```

### Get one product

```text
GET http://localhost:5292/api/Products/1
```

### Non-existing product

```text
GET http://localhost:5292/api/Products/999
```

Expected:

```text
404 Not Found
```

---

# 19. Step 4 Architecture

At the end of Step 4:

```text
                    ShopSphere API
                         │
                         ▼
                ProductsController
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
                     SQL Server
```

And the response path is:

```text
SQL Server
    ↓
EF Core
    ↓
Product Entity
    ↓
Repository
    ↓
Controller
    ↓
ProductDto
    ↓
JSON Response
```

---

# 20. Key Concepts Learned

By completing Step 4, we have covered:

| Concept               | Purpose                                    |
| --------------------- | ------------------------------------------ |
| `[ApiController]`     | Marks class as API controller              |
| `[Route]`             | Defines API URL                            |
| `[HttpGet]`           | Handles GET requests                       |
| `[HttpGet("{id}")]`   | Handles GET by ID                          |
| `ActionResult`        | Represents HTTP/API response               |
| `Ok()`                | HTTP 200                                   |
| `NotFound()`          | HTTP 404                                   |
| DTO                   | Controls API data                          |
| Navigation properties | Represent EF Core relationships            |
| JSON cycle            | Circular object graph during serialization |
| Repository            | Separates database access from controller  |

