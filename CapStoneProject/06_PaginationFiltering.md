Great. We’ll continue **one step at a time**.

# Step 5 — Product Filtering, Sorting, Searching & Pagination

Our current API is:

```text
GET /api/Products
GET /api/Products/{id}
```

Now we want the product catalog API to support things an e-commerce application actually needs:

```text
Search
Filter
Sort
Pagination
```

For example:

```text
GET /api/Products?search=iphone
GET /api/Products?categoryId=1
GET /api/Products?brandId=2
GET /api/Products?sort=priceAsc
GET /api/Products?pageNumber=1&pageSize=3
```

This fits the course's later product-catalog requirements around pagination, filtering, and search. 

---

## 5.1 First, understand the requirement

Suppose we have these products:

```text
1  iPhone 17          79999   Mobiles       Apple
2  Galaxy S26         69999   Mobiles       Samsung
3  Dell Inspiron      65000   Laptops       Dell
4  HP Pavilion        60000   Laptops       HP
5  Lenovo ThinkPad    85000   Laptops       Lenovo
6  Samsung 55 inch TV 55000   Televisions   Samsung
7  Apple AirPods      19999   Accessories   Apple
```

A customer might ask:

### Search

```text
search=Apple
```

Result:

```text
iPhone 17
Apple AirPods
```

### Category filter

```text
categoryId=2
```

Result:

```text
Dell Inspiron
HP Pavilion
Lenovo ThinkPad
```

### Brand filter

```text
brandId=2
```

Result:

```text
Galaxy S26
Samsung 55 inch TV
```

### Sort by price

```text
sort=priceAsc
```

Result:

```text
Apple AirPods       19999
Samsung TV          55000
HP Pavilion         60000
...
```

### Pagination

```text
pageNumber=1&pageSize=3
```

Result:

```text
Products 1 - 3
```

and:

```text
pageNumber=2&pageSize=3
```

returns:

```text
Products 4 - 6
```

---

# 5.2 Where should this logic go?

We don't want all the database logic inside the controller.

Eventually we'll have:

```text
ProductsController
       ↓
ProductRepository
       ↓
EF Core
       ↓
SQL Server
```

The controller should mainly deal with the HTTP request.

The repository should deal with querying the database.

So we'll add the functionality to our repository.

---

# 5.3 Modify `IProductRepository`

Open:

```text
Repositories/IProductRepository.cs
```

Change it to:

```csharp
using ShopSphere.API.Models;

namespace ShopSphere.API.Repositories;

public interface IProductRepository
{
    Task<IReadOnlyList<Product>> GetProductsAsync();

    Task<Product?> GetProductByIdAsync(int id);

    Task<IReadOnlyList<Product>> GetProductsAsync(
        string? search,
        int? categoryId,
        int? brandId,
        string? sort,
        int pageNumber,
        int pageSize);
}
```

We are adding parameters for:

```text
search
categoryId
brandId
sort
pageNumber
pageSize
```

---

# 5.4 Modify `ProductRepository`

Open:

```text
Repositories/ProductRepository.cs
```

Add this method:

```csharp
public async Task<IReadOnlyList<Product>> GetProductsAsync(
    string? search,
    int? categoryId,
    int? brandId,
    string? sort,
    int pageNumber,
    int pageSize)
{
    var query = _context.Products
        .Include(p => p.Category)
        .Include(p => p.Brand)
        .AsQueryable();

    // Search
    if (!string.IsNullOrWhiteSpace(search))
    {
        query = query.Where(p =>
            p.Name.Contains(search) ||
            p.Description.Contains(search));
    }

    // Category filter
    if (categoryId.HasValue)
    {
        query = query.Where(p => p.CategoryId == categoryId.Value);
    }

    // Brand filter
    if (brandId.HasValue)
    {
        query = query.Where(p => p.BrandId == brandId.Value);
    }

    // Sorting
    query = sort?.ToLower() switch
    {
        "priceasc" => query.OrderBy(p => p.Price),

        "pricedesc" => query.OrderByDescending(p => p.Price),

        "nameasc" => query.OrderBy(p => p.Name),

        "namedesc" => query.OrderByDescending(p => p.Name),

        _ => query.OrderBy(p => p.Name)
    };

    // Pagination
    query = query
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize);

    return await query.ToListAsync();
}
```

---

# 5.5 Understand `IQueryable`

This line is important:

```csharp
var query = _context.Products
    .Include(p => p.Category)
    .Include(p => p.Brand)
    .AsQueryable();
```

At this point, EF Core has **not necessarily retrieved all products into memory**.

We're building a query.

For example:

```csharp
query = query.Where(p => p.Price > 50000);
```

EF Core can translate this into SQL.

Conceptually:

```sql
SELECT ...
FROM Products
WHERE Price > 50000
```

This is much better than retrieving every product and filtering in C#.

---

# 5.6 Search

We have:

```csharp
if (!string.IsNullOrWhiteSpace(search))
{
    query = query.Where(p =>
        p.Name.Contains(search) ||
        p.Description.Contains(search));
}
```

So:

```text
/api/Products?search=Apple
```

searches:

```text
Name
Description
```

For example:

```text
Apple AirPods
```

matches because the name contains `Apple`.

---

# 5.7 Category Filter

We have:

```csharp
if (categoryId.HasValue)
{
    query = query.Where(p => p.CategoryId == categoryId.Value);
}
```

Therefore:

```text
/api/Products?categoryId=2
```

returns products whose:

```text
CategoryId = 2
```

which is our **Laptops** category.

---

# 5.8 Brand Filter

Similarly:

```csharp
if (brandId.HasValue)
{
    query = query.Where(p => p.BrandId == brandId.Value);
}
```

Therefore:

```text
/api/Products?brandId=2
```

returns Samsung products.

---

# 5.9 Sorting

We use:

```csharp
query = sort?.ToLower() switch
{
    "priceasc" => query.OrderBy(p => p.Price),

    "pricedesc" => query.OrderByDescending(p => p.Price),

    "nameasc" => query.OrderBy(p => p.Name),

    "namedesc" => query.OrderByDescending(p => p.Name),

    _ => query.OrderBy(p => p.Name)
};
```

So we can call:

```text
/api/Products?sort=priceAsc
```

or:

```text
/api/Products?sort=priceDesc
```

or:

```text
/api/Products?sort=nameAsc
```

or:

```text
/api/Products?sort=nameDesc
```

---

# 5.10 Pagination

This is the important formula:

```csharp
.Skip((pageNumber - 1) * pageSize)
.Take(pageSize);
```

Suppose:

```text
pageNumber = 1
pageSize = 3
```

Then:

```text
Skip((1 - 1) * 3)
Skip(0)
Take(3)
```

We get:

```text
Products 1, 2, 3
```

For:

```text
pageNumber = 2
pageSize = 3
```

we get:

```text
Skip((2 - 1) * 3)
Skip(3)
Take(3)
```

Therefore:

```text
Products 4, 5, 6
```

---

# 5.11 Now Modify the Controller

Open:

```text
Controllers/ProductsController.cs
```

Change the `GetProducts()` method to:

```csharp
[HttpGet]
public async Task<ActionResult<IReadOnlyList<ProductDto>>> GetProducts(
    string? search,
    int? categoryId,
    int? brandId,
    string? sort,
    int pageNumber = 1,
    int pageSize = 10)
{
    var products = await _productRepository.GetProductsAsync(
        search,
        categoryId,
        brandId,
        sort,
        pageNumber,
        pageSize);

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
```

Our existing:

```text
GET /api/Products/{id}
```

doesn't need to change.

---

# 5.12 Test in Postman

Run the application.

### Test 1 — All products

```text
GET
http://localhost:5292/api/Products
```

---

### Test 2 — Search

```text
GET
http://localhost:5292/api/Products?search=Apple
```

Expected:

```text
iPhone 17
Apple AirPods
```

---

### Test 3 — Category

```text
GET
http://localhost:5292/api/Products?categoryId=2
```

Expected:

```text
Dell Inspiron
HP Pavilion
Lenovo ThinkPad
```

---

### Test 4 — Brand

```text
GET
http://localhost:5292/api/Products?brandId=2
```

Expected:

```text
Galaxy S26
Samsung 55 inch TV
```

---

### Test 5 — Price ascending

```text
GET
http://localhost:5292/api/Products?sort=priceAsc
```

Expected first product:

```text
Apple AirPods
19999
```

---

### Test 6 — Price descending

```text
GET
http://localhost:5292/api/Products?sort=priceDesc
```

Expected first product:

```text
Lenovo ThinkPad
85000
```

---

### Test 7 — Pagination

```text
GET
http://localhost:5292/api/Products?pageNumber=1&pageSize=3
```

Then:

```text
GET
http://localhost:5292/api/Products?pageNumber=2&pageSize=3
```

---

# 5.13 Combining Everything

This is where it becomes useful.

We can combine parameters:

```text
GET /api/Products?search=Samsung&brandId=2&sort=priceDesc&pageNumber=1&pageSize=5
```

Conceptually:

```text
Products
   ↓
Search Samsung
   ↓
Brand = Samsung
   ↓
Sort price descending
   ↓
Page 1
   ↓
5 products
```

That's the kind of API our Angular product catalog will eventually consume.

---

# Important limitation of this step

There is one thing we haven't implemented yet:

**Total number of products / total pages.**

For a real Angular pagination component, we eventually want a response like:

```json
{
    "pageNumber": 1,
    "pageSize": 3,
    "totalCount": 7,
    "totalPages": 3,
    "data": [
        ...
    ]
}
```

But **don't implement that yet**.

Let's first make sure the basic filtering, sorting and pagination works.

### Your task now

Implement **Step 5** and test these five URLs:

```text
/api/Products
/api/Products?search=Apple
/api/Products?categoryId=2
/api/Products?sort=priceAsc
/api/Products?pageNumber=2&pageSize=3
```

