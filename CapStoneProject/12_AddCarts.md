# Step 11 — Shopping Cart

Now we start building the actual e-commerce functionality.

Our flow becomes:

```text
Customer
   │
   ▼
Login
   │
   ▼
JWT
   │
   ▼
Browse Products
   │
   ▼
Add to Cart
   │
   ▼
Cart
```

For this step, we'll first implement the cart using **SQL Server**. After it works, we'll introduce **Redis** as a separate step rather than mixing two technologies at once.

---

# 11.1 Cart Database Design

We need two new tables:

```text
Cart
----------------
Id
CustomerId
```

and:

```text
CartItem
----------------
Id
CartId
ProductId
Quantity
Price
```

Relationship:

```text
Customer
   │
   │ 1
   ▼
 Cart
   │
   │ 1
   ▼
CartItem
   │
   │ many-to-1
   ▼
Product
```

A customer's cart might look like:

```text
Customer 1
     │
     ▼
   Cart
     │
     ├── iPhone 17
     │     Quantity = 2
     │     Price = 79999
     │
     └── AirPods
           Quantity = 1
           Price = 19999
```

---

# 11.2 Create `Cart` Model

Create:

```text
Models/Cart.cs
```

```csharp
namespace ShopSphere.API.Models;

public class Cart
{
    public int Id { get; set; }

    public int CustomerId { get; set; }

    public Customer Customer { get; set; } = null!;

    public ICollection<CartItem> Items { get; set; }
        = new List<CartItem>();
}
```

---

# 11.3 Create `CartItem` Model

Create:

```text
Models/CartItem.cs
```

```csharp
namespace ShopSphere.API.Models;

public class CartItem
{
    public int Id { get; set; }

    public int CartId { get; set; }

    public Cart Cart { get; set; } = null!;

    public int ProductId { get; set; }

    public Product Product { get; set; } = null!;

    public int Quantity { get; set; }

    public decimal Price { get; set; }
}
```

### Why do we store `Price`?

Suppose the customer adds:

```text
iPhone 17
Price = ₹79,999
```

to the cart.

We store:

```text
Quantity = 1
Price = 79999
```

However, when we eventually create an order, we will **revalidate the current product price** rather than blindly trusting the cart price.

---

# 11.4 Add DbSets

Open:

```text
Data/ShopSphereDbContext.cs
```

Add:

```csharp
public DbSet<Cart> Carts { get; set; }

public DbSet<CartItem> CartItems { get; set; }
```

So this section becomes:

```csharp
public DbSet<Category> Categories { get; set; }

public DbSet<Brand> Brands { get; set; }

public DbSet<Product> Products { get; set; }

public DbSet<Customer> Customers { get; set; }

public DbSet<Address> Addresses { get; set; }

public DbSet<Order> Orders { get; set; }

public DbSet<OrderItem> OrderItems { get; set; }

public DbSet<Cart> Carts { get; set; }

public DbSet<CartItem> CartItems { get; set; }
```

---

# 11.5 Configure the Relationships

Inside `OnModelCreating()`, after:

```csharp
base.OnModelCreating(modelBuilder);
```

add:

```csharp
modelBuilder.Entity<Cart>()
    .HasOne(c => c.Customer)
    .WithOne()
    .HasForeignKey<Cart>(c => c.CustomerId)
    .OnDelete(DeleteBehavior.Cascade);

modelBuilder.Entity<Cart>()
    .HasIndex(c => c.CustomerId)
    .IsUnique();

modelBuilder.Entity<CartItem>()
    .HasOne(ci => ci.Cart)
    .WithMany(c => c.Items)
    .HasForeignKey(ci => ci.CartId)
    .OnDelete(DeleteBehavior.Cascade);

modelBuilder.Entity<CartItem>()
    .HasOne(ci => ci.Product)
    .WithMany()
    .HasForeignKey(ci => ci.ProductId)
    .OnDelete(DeleteBehavior.Restrict);
```

The important part is:

```csharp
.HasIndex(c => c.CustomerId)
.IsUnique();
```

This means:

> One customer can have only one cart.

So:

```text
Customer 1 → Cart 1
Customer 2 → Cart 2
Customer 3 → Cart 3
```

---

# 11.6 Create Migration

Open:

**Package Manager Console**

Run:

```powershell
Add-Migration AddShoppingCart
```

Then:

```powershell
Update-Database
```

Check SQL Server.

You should now see:

```text
Carts
CartItems
```

---

# 11.7 Create Cart DTOs

Create:

```text
DTOs/AddCartItemDto.cs
```

```csharp
namespace ShopSphere.API.DTOs;

public class AddCartItemDto
{
    public int ProductId { get; set; }

    public int Quantity { get; set; }
}
```

Now create:

```text
DTOs/CartDto.cs
```

```csharp
namespace ShopSphere.API.DTOs;

public class CartDto
{
    public int Id { get; set; }

    public List<CartItemDto> Items { get; set; }
        = new();
}
```

And:

```text
DTOs/CartItemDto.cs
```

```csharp
namespace ShopSphere.API.DTOs;

public class CartItemDto
{
    public int ProductId { get; set; }

    public string ProductName { get; set; } = string.Empty;

    public string PictureUrl { get; set; } = string.Empty;

    public decimal Price { get; set; }

    public int Quantity { get; set; }

    public decimal Total { get; set; }
}
```

---

# 11.8 Create `CartController`

Create:

```text
Controllers/CartController.cs
```

Use:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Data;
using ShopSphere.API.DTOs;
using ShopSphere.API.Models;
using System.Security.Claims;

namespace ShopSphere.API.Controllers;

[Authorize]
[ApiController]
[Route("api/[controller]")]
public class CartController : ControllerBase
{
    private readonly ShopSphereDbContext _context;

    public CartController(ShopSphereDbContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<IActionResult> GetCart()
    {
        var customerId = GetCustomerId();

        if (customerId == null)
        {
            return Unauthorized();
        }

        var cart = await _context.Carts
            .Include(c => c.Items)
            .ThenInclude(i => i.Product)
            .FirstOrDefaultAsync(c =>
                c.CustomerId == customerId.Value);

        if (cart == null)
        {
            return Ok(new CartDto());
        }

        var result = new CartDto
        {
            Id = cart.Id,

            Items = cart.Items.Select(i => new CartItemDto
            {
                ProductId = i.ProductId,
                ProductName = i.Product.Name,
                PictureUrl = i.Product.PictureUrl,
                Price = i.Price,
                Quantity = i.Quantity,
                Total = i.Price * i.Quantity
            }).ToList()
        };

        return Ok(result);
    }

    [HttpPost]
    public async Task<IActionResult> AddToCart(
        AddCartItemDto model)
    {
        var customerId = GetCustomerId();

        if (customerId == null)
        {
            return Unauthorized();
        }

        if (model.Quantity <= 0)
        {
            return BadRequest("Quantity must be greater than zero.");
        }

        var product = await _context.Products
            .FirstOrDefaultAsync(p =>
                p.Id == model.ProductId);

        if (product == null)
        {
            return NotFound("Product not found.");
        }

        var cart = await _context.Carts
            .Include(c => c.Items)
            .FirstOrDefaultAsync(c =>
                c.CustomerId == customerId.Value);

        if (cart == null)
        {
            cart = new Cart
            {
                CustomerId = customerId.Value
            };

            _context.Carts.Add(cart);
        }

        var cartItem = cart.Items
            .FirstOrDefault(i =>
                i.ProductId == model.ProductId);

        if (cartItem == null)
        {
            cartItem = new CartItem
            {
                ProductId = product.Id,
                Quantity = model.Quantity,
                Price = product.Price
            };

            cart.Items.Add(cartItem);
        }
        else
        {
            cartItem.Quantity += model.Quantity;
        }

        await _context.SaveChangesAsync();

        return Ok(new
        {
            message = "Product added to cart."
        });
    }

    [HttpDelete("{productId}")]
    public async Task<IActionResult> RemoveFromCart(
        int productId)
    {
        var customerId = GetCustomerId();

        if (customerId == null)
        {
            return Unauthorized();
        }

        var cart = await _context.Carts
            .Include(c => c.Items)
            .FirstOrDefaultAsync(c =>
                c.CustomerId == customerId.Value);

        if (cart == null)
        {
            return NotFound("Cart not found.");
        }

        var item = cart.Items
            .FirstOrDefault(i =>
                i.ProductId == productId);

        if (item == null)
        {
            return NotFound("Product is not in the cart.");
        }

        _context.CartItems.Remove(item);

        await _context.SaveChangesAsync();

        return Ok(new
        {
            message = "Product removed from cart."
        });
    }

    private int? GetCustomerId()
    {
        var value = User.FindFirst(
            ClaimTypes.NameIdentifier)?.Value;

        if (int.TryParse(value, out var customerId))
        {
            return customerId;
        }

        return null;
    }
}
```

---

# 11.9 Understand `GetCustomerId()`

This is one of the most important pieces.

We get the customer ID from the JWT:

```csharp
var value = User.FindFirst(
    ClaimTypes.NameIdentifier)?.Value;
```

Remember when we created the JWT?

We added:

```csharp
new Claim(
    ClaimTypes.NameIdentifier,
    customer.Id.ToString())
```

So:

```text
JWT
 │
 └── NameIdentifier = 1
                       ↓
                  CustomerId
```

This means a customer **cannot simply send another customer's ID** to access their cart.

The server gets the identity from the authenticated JWT.

---

# 11.10 Test the Cart

## First: Login

```text
POST /api/Account/login
```

Get your JWT.

Then in Postman select:

```text
Authorization
    ↓
Bearer Token
    ↓
<your JWT>
```

---

## Test 1 — Empty Cart

Call:

```text
GET http://localhost:5292/api/Cart
```

For a new customer:

```json
{
    "id": 0,
    "items": []
}
```

---

# 11.11 Add Product to Cart

Call:

```text
POST http://localhost:5292/api/Cart
```

Body:

```json
{
    "productId": 1,
    "quantity": 2
}
```

Expected:

```json
{
    "message": "Product added to cart."
}
```

---

# 11.12 Get Cart

Now:

```text
GET http://localhost:5292/api/Cart
```

You should get something like:

```json
{
    "id": 1,
    "items": [
        {
            "productId": 1,
            "productName": "iPhone 17",
            "pictureUrl": "images/iphone17.jpg",
            "price": 79999,
            "quantity": 2,
            "total": 159998
        }
    ]
}
```

---

# 11.13 Add the Same Product Again

Call:

```text
POST /api/Cart
```

```json
{
    "productId": 1,
    "quantity": 1
}
```

Now the quantity should become:

```text
Quantity = 3
```

rather than creating another cart item.

So:

```text
Before:
iPhone 17 → Quantity 2

Add 1

After:
iPhone 17 → Quantity 3
```

---

# 11.14 Add Another Product

Call:

```text
POST /api/Cart
```

```json
{
    "productId": 7,
    "quantity": 1
}
```

Now:

```text
Cart
 │
 ├── iPhone 17
 │      Quantity = 3
 │
 └── Apple AirPods
        Quantity = 1
```

---

# 11.15 Remove Product

Call:

```text
DELETE http://localhost:5292/api/Cart/7
```

Expected:

```json
{
    "message": "Product removed from cart."
}
```

---

# 11.16 Final Cart API

We now have:

| HTTP   | Endpoint                | Purpose                     |
| ------ | ----------------------- | --------------------------- |
| GET    | `/api/Cart`             | Get current customer's cart |
| POST   | `/api/Cart`             | Add product                 |
| DELETE | `/api/Cart/{productId}` | Remove product              |

All three require:

```csharp
[Authorize]
```

So the API is:

```text
Customer
   │
   ▼
JWT
   │
   ▼
[Authorize]
   │
   ▼
CartController
   │
   ▼
CustomerId from JWT
   │
   ▼
Cart
   │
   └── CartItems
          │
          ▼
       Products
```

---

## Important checkpoint

For this lesson, don't add Redis yet.

First make sure these work in Postman:

```text
GET    /api/Cart

POST   /api/Cart

GET    /api/Cart

DELETE /api/Cart/{productId}
```

using your **Bearer JWT**.

Once this works, we'll do **Step 12 — Redis Cart Storage/Caching**. There we'll take the SQL-based cart we just built and understand why an e-commerce application can use Redis for fast cart access, without losing our SQL data model.
