We have completed:

* ✅ SQL Server + EF Core
* ✅ Repository Pattern
* ✅ Product APIs
* ✅ DTOs
* ✅ Search / Filter / Sort / Pagination
* ✅ Category & Brand APIs
* ✅ ASP.NET Core Identity
* ✅ JWT Registration/Login
* ✅ Protected APIs
* ✅ SQL Server Shopping Cart
* ⏭️ **Redis skipped**

## Next: Step 13 — Orders & Checkout

Now we'll implement the important e-commerce flow:

**Cart → Checkout → Order → Order Items → Order History**

We'll keep it simple for the MVP:

```text
Customer
   │
   ├── Cart
   │     └── CartItems
   │
   └── Orders
          └── OrderItems
                 │
                 └── Product
```

### In this step we'll build

1. Checkout DTO
2. Create Order API
3. Convert CartItems → OrderItems
4. Calculate order total
5. Clear cart after successful order
6. Get customer's order history
7. Get order details

We'll **skip Stripe payment for now** and treat checkout as a successful order. Payment integration can be added later.

### First part

Let's create:

```text
DTOs
 └── CreateOrderDto.cs
```

with:

```csharp
namespace ShopSphere.API.DTOs;

public class CreateOrderDto
{
    public int AddressId { get; set; }
}
```

The idea is that the customer selects an address during checkout.

**Don't create anything else yet.**

Once you've created `CreateOrderDto.cs`, tell me **done**, and we'll implement the **Order API** next.
