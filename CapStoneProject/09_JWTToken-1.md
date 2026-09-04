Excellent. **Step 7 is complete.**

# Step 8 — Customer Registration & Authentication Foundation

Now we move from the **catalog side** into the **customer side** of ShopSphere.

The goal of this step is to establish:

```text
Customer
   ↓
Register
   ↓
Login
   ↓
JWT Token
   ↓
Authenticated APIs
```

For the full ShopSphere project, the course includes **Identity/JWT authentication**, so we'll use **ASP.NET Core Identity** rather than building password handling ourselves. 

## What we'll do in Step 8

We'll keep this step focused. **Today we'll only set up ASP.NET Core Identity and the database.**

We are **not doing JWT login yet**. We'll do that in the next step after Identity is working.

---

# 8.1 Install ASP.NET Core Identity packages

In Visual Studio:

**Tools → NuGet Package Manager → Package Manager Console**

Install:

```powershell
Install-Package Microsoft.AspNetCore.Identity.EntityFrameworkCore
```

Because you're using **.NET 10**, make sure Visual Studio installs the version compatible with your .NET 10 project.

You should now have:

```text
Microsoft.AspNetCore.Identity.EntityFrameworkCore
```

in your project's NuGet packages.

---

# 8.2 Why ASP.NET Core Identity?

Without Identity, we might create something like:

```text
Customers
----------------
Id
Name
Email
Password
```

But storing passwords ourselves is a bad idea.

Identity manages things such as:

```text
Users
Passwords
Password hashing
Roles
Claims
Authentication-related data
```

It also creates the required tables in SQL Server.

So instead of building authentication from scratch:

```text
Our Code
   ↓
Identity
   ↓
SQL Server
```

---

# 8.3 Modify `Customer`

We currently have a `Customer` model.

Open:

```text
Models/Customer.cs
```

For now, **do not delete your Customer model**.

Change it so that it inherits from `IdentityUser<int>`:

```csharp
using Microsoft.AspNetCore.Identity;

namespace ShopSphere.API.Models;

public class Customer : IdentityUser<int>
{
    public string Name { get; set; } = string.Empty;

    public ICollection<Address> Addresses { get; set; }
        = new List<Address>();

    public ICollection<Order> Orders { get; set; }
        = new List<Order>();
}
```

### Why `IdentityUser<int>`?

The default Identity user uses a string ID.

We're choosing:

```csharp
IdentityUser<int>
```

so that our customer ID remains an integer:

```text
Customer
   ↓
Id = 1
```

This also works nicely with our existing:

```text
Orders.CustomerId
Addresses.CustomerId
```

relationships.

---

# 8.4 Modify `ShopSphereDbContext`

Open:

```text
Data/ShopSphereDbContext.cs
```

Currently it inherits from:

```csharp
DbContext
```

Change:

```csharp
public class ShopSphereDbContext : DbContext
```

to:

```csharp
public class ShopSphereDbContext
    : IdentityDbContext<Customer, IdentityRole<int>, int>
```

You also need these imports:

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Models;
```

So the beginning becomes:

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Models;

namespace ShopSphere.API.Data;

public class ShopSphereDbContext
    : IdentityDbContext<Customer, IdentityRole<int>, int>
{
    public ShopSphereDbContext(
        DbContextOptions<ShopSphereDbContext> options)
        : base(options)
    {
    }

    public DbSet<Category> Categories { get; set; }

    public DbSet<Brand> Brands { get; set; }

    public DbSet<Product> Products { get; set; }

    public DbSet<Customer> Customers { get; set; }

    public DbSet<Address> Addresses { get; set; }

    public DbSet<Order> Orders { get; set; }

    public DbSet<OrderItem> OrderItems { get; set; }

    // existing OnModelCreating...
}
```

### Important

Keep your existing `OnModelCreating()` and your existing seed data.

But because Identity requires its own model configuration, add this as the **first line** inside `OnModelCreating()`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    // Your existing seed data...
}
```

The:

```csharp
base.OnModelCreating(modelBuilder);
```

is important because it tells Identity to configure its tables.

---

# 8.5 Register Identity in `Program.cs`

Open:

```text
Program.cs
```

Add:

```csharp
using Microsoft.AspNetCore.Identity;
using ShopSphere.API.Models;
```

Then after your `AddDbContext`:

```csharp
builder.Services
    .AddIdentityCore<Customer>()
    .AddRoles<IdentityRole<int>>()
    .AddEntityFrameworkStores<ShopSphereDbContext>();
```

So the relevant part becomes:

```csharp
builder.Services.AddControllers();

builder.Services.AddDbContext<ShopSphereDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services
    .AddIdentityCore<Customer>()
    .AddRoles<IdentityRole<int>>()
    .AddEntityFrameworkStores<ShopSphereDbContext>();

builder.Services.AddScoped<IProductRepository, ProductRepository>();
```

We're registering Identity with:

```text
Customer
   ↓
ShopSphereDbContext
   ↓
SQL Server
```

---

# 8.6 Create Identity migration

Now open:

**Tools → NuGet Package Manager → Package Manager Console**

Run:

```powershell
Add-Migration AddIdentity
```

If successful, you'll see a new migration under:

```text
Migrations
```

Something similar to:

```text
20260904xxxxxx_AddIdentity.cs
```

Then run:

```powershell
Update-Database
```

---

# 8.7 Check SQL Server

Open SQL Server Management Studio and refresh:

```text
ShopSphereDB
   └── Tables
```

You should now see Identity tables such as:

```text
AspNetRoles
AspNetRoleClaims
AspNetUserClaims
AspNetUserLogins
AspNetUserRoles
AspNetUsers
AspNetUserTokens
```

along with our existing tables:

```text
Addresses
Brands
Categories
Customers
OrderItems
Orders
Products
__EFMigrationsHistory
```

The exact Identity table set depends on the Identity configuration/version, but `AspNetUsers` and the role-related tables should be part of the Identity schema.

---

# 8.8 Important: What happened to our `Customers` table?

This is an important architectural point.

We have chosen:

```csharp
Customer : IdentityUser<int>
```

Therefore our `Customer` entity **is the Identity user**.

So we don't want to maintain:

```text
Customer table
+
AspNetUsers table
```

as two separate user systems.

Instead:

```text
Customer
   ↓
IdentityUser
   ↓
AspNetUsers
```

Our additional property:

```csharp
Name
```

becomes an additional field on the Identity user entity.

---

# 8.9 One thing to watch for

Because we originally created:

```text
Customers
```

before introducing Identity, the migration may detect a model change involving that table.

**Do not manually delete the `Customers` table.**

If:

```powershell
Add-Migration AddIdentity
```

produces an error, stop there and send me the **complete migration error**.

Don't modify the database manually.

---

# Step 8 checkpoint

For now, we only want to achieve this:

```text
ShopSphere
     │
     ▼
ASP.NET Core Identity
     │
     ▼
Customer : IdentityUser<int>
     │
     ▼
ShopSphereDbContext
     │
     ▼
SQL Server
```

And SQL Server should contain the Identity tables.

### Don't create the registration controller yet.

Once `Add-Migration AddIdentity` and `Update-Database` complete successfully, tell me **"done"**.

Then we'll do **Step 9 — Customer Registration + JWT Login**, where we'll actually create:

```text
POST /api/Account/register
POST /api/Account/login
```

and see exactly how the JWT token is generated and used.
