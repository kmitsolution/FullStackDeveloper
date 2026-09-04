# Step 6 — Proper Pagination Response

Currently:

```text
GET /api/Products?pageNumber=1&pageSize=3
```

returns only the products:

```json
[
  {
    "id": 1,
    "name": "iPhone 17"
  },
  ...
]
```

The Angular application will eventually need to know:

* Which page is this?
* How many products are on each page?
* How many products exist in total?
* How many pages are available?

So we will change the response to:

```json
{
  "pageNumber": 1,
  "pageSize": 3,
  "totalCount": 7,
  "totalPages": 3,
  "data": [
    {
      "id": 1,
      "name": "iPhone 17",
      "price": 79999,
      "category": "Mobiles",
      "brand": "Apple"
    }
  ]
}
```

This is much more useful for our future Angular application.

---

## 6.1 Create `PagedResultDto`

Create a new file:

```text
DTOs/PagedResultDto.cs
```

Add:

```csharp
namespace ShopSphere.API.DTOs;

public class PagedResultDto<T>
{
    public int PageNumber { get; set; }

    public int PageSize { get; set; }

    public int TotalCount { get; set; }

    public int TotalPages { get; set; }

    public IReadOnlyList<T> Data { get; set; } = [];
}
```

We are using `<T>` because this is a **generic class**.

For example:

```text
PagedResultDto<ProductDto>
```

means:

```text
Data = ProductDto objects
```

Later we could also use:

```text
PagedResultDto<OrderDto>
```

for orders.

---

# 6.2 Modify the Repository Interface

Open:

```text
Repositories/IProductRepository.cs
```

Our current method:

```csharp
Task<IReadOnlyList<Product>> GetProductsAsync(
    string? search,
    int? categoryId,
    int? brandId,
    string? sort,
    int pageNumber,
    int pageSize);
```

will be changed.

We need the repository to return both:

```text
Products
+
Total count
```

A simple way for our MVP is to return a tuple.

Change it to:

```csharp
Task<(IReadOnlyList<Product> Products, int TotalCount)> GetProductsAsync(
    string? search,
    int? categoryId,
    int? brandId,
    string? sort,
    int pageNumber,
    int pageSize);
```

---

# 6.3 Modify `ProductRepository`

Open:

```text
Repositories/ProductRepository.cs
```

Replace the filtering/pagination method with:

```csharp
public async Task<(IReadOnlyList<Product> Products, int TotalCount)> GetProductsAsync(
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
        query = query.Where(p =>
            p.CategoryId == categoryId.Value);
    }

    // Brand filter
    if (brandId.HasValue)
    {
        query = query.Where(p =>
            p.BrandId == brandId.Value);
    }

    // Total count BEFORE pagination
    var totalCount = await query.CountAsync();

    // Sorting
    query = sort?.ToLower() switch
    {
        "priceasc" =>
            query.OrderBy(p => p.Price),

        "pricedesc" =>
            query.OrderByDescending(p => p.Price),

        "nameasc" =>
            query.OrderBy(p => p.Name),

        "namedesc" =>
            query.OrderByDescending(p => p.Name),

        _ =>
            query.OrderBy(p => p.Name)
    };

    // Pagination
    query = query
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize);

    var products = await query.ToListAsync();

    return (products, totalCount);
}
```

### Important change

Notice this happens **before** `Skip()` and `Take()`:

```csharp
var totalCount = await query.CountAsync();
```

That's very important.

Suppose we have 7 products and request:

```text
pageSize = 3
```

After pagination we only get 3 products.

But we need:

```text
totalCount = 7
```

not:

```text
totalCount = 3
```

---

# 6.4 Modify `ProductsController`

Open:

```text
Controllers/ProductsController.cs
```

Change the `GetProducts()` method to:

```csharp
[HttpGet]
public async Task<ActionResult<PagedResultDto<ProductDto>>> GetProducts(
    string? search,
    int? categoryId,
    int? brandId,
    string? sort,
    int pageNumber = 1,
    int pageSize = 10)
{
    var result = await _productRepository.GetProductsAsync(
        search,
        categoryId,
        brandId,
        sort,
        pageNumber,
        pageSize);

    var products = result.Products;

    var productDtos = products.Select(p => new ProductDto
    {
        Id = p.Id,
        Name = p.Name,
        Description = p.Description,
        Price = p.Price,
        PictureUrl = p.PictureUrl,
        Category = p.Category?.Name ?? "",
        Brand = p.Brand?.Name ?? ""
    }).ToList();

    var totalPages = (int)Math.Ceiling(
        result.TotalCount / (double)pageSize);

    var response = new PagedResultDto<ProductDto>
    {
        PageNumber = pageNumber,
        PageSize = pageSize,
        TotalCount = result.TotalCount,
        TotalPages = totalPages,
        Data = productDtos
    };

    return Ok(response);
}
```

---

# 6.5 Test the API

Run your application.

Call:

```text
GET http://localhost:5292/api/Products?pageNumber=1&pageSize=3
```

You should now get something similar to:

```json
{
  "pageNumber": 1,
  "pageSize": 3,
  "totalCount": 7,
  "totalPages": 3,
  "data": [
    {
      "id": 1,
      "name": "iPhone 17",
      "description": "Apple smartphone",
      "price": 79999,
      "pictureUrl": "images/iphone17.jpg",
      "category": "Mobiles",
      "brand": "Apple"
    },
    {
      "id": 2,
      "name": "Galaxy S26",
      "description": "Samsung smartphone",
      "price": 69999,
      "pictureUrl": "images/galaxy-s26.jpg",
      "category": "Mobiles",
      "brand": "Samsung"
    },
    {
      "id": 3,
      "name": "Dell Inspiron",
      "description": "Dell laptop",
      "price": 65000,
      "pictureUrl": "images/dell-inspiron.jpg",
      "category": "Laptops",
      "brand": "Dell"
    }
  ]
}
```

The exact order will depend on the default sorting in your repository.

---

# 6.6 Test Page 2

Now:

```text
GET http://localhost:5292/api/Products?pageNumber=2&pageSize=3
```

You should get:

```json
{
  "pageNumber": 2,
  "pageSize": 3,
  "totalCount": 7,
  "totalPages": 3,
  "data": [
    ...
  ]
}
```

Notice:

```text
pageNumber = 2
pageSize = 3
totalCount = 7
totalPages = 3
```

The `data` array contains the second page.

---

# 6.7 Filtering + Pagination

This also works with our previous filtering.

For example:

```text
GET /api/Products?categoryId=2&pageNumber=1&pageSize=2
```

If there are 3 laptops:

```text
totalCount = 3
totalPages = 2
```

The response tells Angular exactly how many pages to display.

---

# 6.8 Why This Is Important for Angular

Eventually Angular can do something like:

```text
Page 1   2   3   Next
```

Angular can calculate this from:

```json
{
  "pageNumber": 1,
  "pageSize": 3,
  "totalCount": 7,
  "totalPages": 3
}
```

So our backend is now becoming ready for the frontend.

---

## Step 6 checkpoint

At this point your backend should support:

```text
GET /api/Products

GET /api/Products/1

GET /api/Products?search=Apple

GET /api/Products?categoryId=2

GET /api/Products?brandId=2

GET /api/Products?sort=priceAsc

GET /api/Products?pageNumber=1&pageSize=3

GET /api/Products?search=Samsung&sort=priceDesc&pageNumber=1&pageSize=5
```

and the collection endpoint returns:

```text
PagedResult
   ├── PageNumber
   ├── PageSize
   ├── TotalCount
   ├── TotalPages
   └── Data
```

**Don't move to Angular yet.** We still have a few important backend pieces to build first: categories/brands APIs and then the remaining e-commerce functionality.

Once Step 6 works, tell me **"done"**, and we'll take Step 7.
