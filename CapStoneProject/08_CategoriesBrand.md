# Step 7 — Categories & Brands API

Before we move into customers, authentication, cart, and orders, our Angular product catalog needs to be able to retrieve the available **categories and brands**.

Currently we have:

```text
GET /api/Products
GET /api/Products/{id}
```

We will now add:

```text
GET /api/Categories
GET /api/Brands
```

This allows the future Angular UI to show filters such as:

```text
Categories
----------------
Mobiles
Laptops
Accessories
Televisions

Brands
----------------
Apple
Samsung
Dell
HP
Lenovo
```

---

## 7.1 Create `CategoriesController`

Create:

```text
Controllers
   └── CategoriesController.cs
```

Add:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Data;

namespace ShopSphere.API.Controllers;

[ApiController]
[Route("api/[controller]")]
public class CategoriesController : ControllerBase
{
    private readonly ShopSphereDbContext _context;

    public CategoriesController(ShopSphereDbContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<IActionResult> GetCategories()
    {
        var categories = await _context.Categories
            .Select(c => new
            {
                c.Id,
                c.Name
            })
            .ToListAsync();

        return Ok(categories);
    }
}
```

### What are we doing here?

We're directly querying `Categories` because this is a very simple lookup.

The query:

```csharp
.Select(c => new
{
    c.Id,
    c.Name
})
```

means we only return:

```json
[
  {
    "id": 1,
    "name": "Mobiles"
  },
  {
    "id": 2,
    "name": "Laptops"
  }
]
```

We don't need to return the `Products` navigation property.

---

# 7.2 Create `BrandsController`

Create:

```text
Controllers
   └── BrandsController.cs
```

Add:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Data;

namespace ShopSphere.API.Controllers;

[ApiController]
[Route("api/[controller]")]
public class BrandsController : ControllerBase
{
    private readonly ShopSphereDbContext _context;

    public BrandsController(ShopSphereDbContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<IActionResult> GetBrands()
    {
        var brands = await _context.Brands
            .Select(b => new
            {
                b.Id,
                b.Name
            })
            .ToListAsync();

        return Ok(brands);
    }
}
```

---

# 7.3 Test Categories

Run the application and call:

```text
GET http://localhost:5292/api/Categories
```

Expected:

```json
[
  {
    "id": 1,
    "name": "Mobiles"
  },
  {
    "id": 2,
    "name": "Laptops"
  },
  {
    "id": 3,
    "name": "Accessories"
  },
  {
    "id": 4,
    "name": "Televisions"
  }
]
```

---

# 7.4 Test Brands

Call:

```text
GET http://localhost:5292/api/Brands
```

Expected:

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Samsung"
  },
  {
    "id": 3,
    "name": "Dell"
  },
  {
    "id": 4,
    "name": "HP"
  },
  {
    "id": 5,
    "name": "Lenovo"
  }
]
```

---

# 7.5 Why aren't we using repositories here?

You may notice that `CategoriesController` uses:

```csharp
private readonly ShopSphereDbContext _context;
```

instead of:

```text
CategoryRepository
```

For our current MVP, these are simple read-only lookup endpoints.

However, **architecturally**, we could create:

```text
ICategoryRepository
CategoryRepository
IBrandRepository
BrandRepository
```

later.

For our **3-hour MVP**, I would avoid adding unnecessary layers at this point.

The important repository work is around the more complex product queries.

---

# 7.6 Current Backend Architecture

We now have:

```text
                    ShopSphere API
                          │
        ┌─────────────────┼──────────────────┐
        │                 │                  │
        ▼                 ▼                  ▼
 ProductsController  CategoriesController  BrandsController
        │                 │                  │
        ▼                 │                  │
ProductRepository         │                  │
        │                 │                  │
        └─────────────────┼──────────────────┘
                          ▼
                  ShopSphereDbContext
                          │
                          ▼
                      SQL Server
```

And our APIs are:

| HTTP | Endpoint             | Purpose         |
| ---- | -------------------- | --------------- |
| GET  | `/api/Products`      | Product list    |
| GET  | `/api/Products/{id}` | Product details |
| GET  | `/api/Categories`    | Category list   |
| GET  | `/api/Brands`        | Brand list      |

---

## Step 7 checkpoint

Please test:

```text
http://localhost:5292/api/Categories
```

and:

```text
http://localhost:5292/api/Brands
```

Once both work, **Step 7 is complete**.

Then we'll move to **Step 8 — Customer registration and ASP.NET Core Identity/JWT authentication**. This is where the ShopSphere backend starts becoming a real e-commerce application rather than just a product catalog.
