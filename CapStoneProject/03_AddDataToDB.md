
# Step 2 — Add Initial Product Data Using EF Core Seeding

Our goal now is:

```text
C# Entities
     ↓
EF Core
     ↓
Migration
     ↓
SQL Server
     ↓
ShopSphereDB
     ↓
Seed initial data
```

We will put some realistic data into:

* `Categories`
* `Brands`
* `Products`

This is useful because when we build the API later, we can immediately retrieve real products instead of an empty database.

The course also specifically includes **seeding initial data** in its Repository Pattern module. 

---

## 2.1 What data are we going to insert?

### Categories

```text
1  Mobiles
2  Laptops
3  Accessories
4  Televisions
```

### Brands

```text
1  Apple
2  Samsung
3  Dell
4  HP
5  Lenovo
```

### Products

For example:

```text
iPhone 17
Galaxy S26
Dell Inspiron
HP Pavilion
Lenovo ThinkPad
Samsung TV
Apple AirPods
```

The relationships will be:

```text
Category
   │
   └──────< Product >────── Brand
```

For example:

```text
Mobiles
   │
   ├── iPhone 17 ───── Apple
   │
   └── Galaxy S26 ──── Samsung
```

---

# 2.2 Where should we seed the data?

Open:

```text
ShopSphere.API
   └── Data
       └── ShopSphereDbContext.cs
```

We currently have something similar to:

```csharp
public class ShopSphereDbContext : DbContext
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
}
```

We're going to add EF Core's `OnModelCreating()` method.

---

# 2.3 Add Category Seed Data

Modify the `ShopSphereDbContext`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Category>().HasData(
        new Category
        {
            Id = 1,
            Name = "Mobiles"
        },
        new Category
        {
            Id = 2,
            Name = "Laptops"
        },
        new Category
        {
            Id = 3,
            Name = "Accessories"
        },
        new Category
        {
            Id = 4,
            Name = "Televisions"
        }
    );
}
```

So your complete `DbContext` becomes:

```csharp
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Models;

namespace ShopSphere.API.Data;

public class ShopSphereDbContext : DbContext
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

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Category>().HasData(
            new Category
            {
                Id = 1,
                Name = "Mobiles"
            },
            new Category
            {
                Id = 2,
                Name = "Laptops"
            },
            new Category
            {
                Id = 3,
                Name = "Accessories"
            },
            new Category
            {
                Id = 4,
                Name = "Televisions"
            }
        );
    }
}
```

---

# 2.4 Add Brand Seed Data

Inside the same `OnModelCreating()` method, add:

```csharp
modelBuilder.Entity<Brand>().HasData(
    new Brand
    {
        Id = 1,
        Name = "Apple"
    },
    new Brand
    {
        Id = 2,
        Name = "Samsung"
    },
    new Brand
    {
        Id = 3,
        Name = "Dell"
    },
    new Brand
    {
        Id = 4,
        Name = "HP"
    },
    new Brand
    {
        Id = 5,
        Name = "Lenovo"
    }
);
```

So:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Category>().HasData(
        new Category { Id = 1, Name = "Mobiles" },
        new Category { Id = 2, Name = "Laptops" },
        new Category { Id = 3, Name = "Accessories" },
        new Category { Id = 4, Name = "Televisions" }
    );

    modelBuilder.Entity<Brand>().HasData(
        new Brand { Id = 1, Name = "Apple" },
        new Brand { Id = 2, Name = "Samsung" },
        new Brand { Id = 3, Name = "Dell" },
        new Brand { Id = 4, Name = "HP" },
        new Brand { Id = 5, Name = "Lenovo" }
    );
}
```

---

# 2.5 Add Product Seed Data

Now the important part.

Add:

```csharp
modelBuilder.Entity<Product>().HasData(
    new Product
    {
        Id = 1,
        Name = "iPhone 17",
        Description = "Apple smartphone",
        Price = 79999,
        PictureUrl = "images/iphone17.jpg",
        CategoryId = 1,
        BrandId = 1
    },

    new Product
    {
        Id = 2,
        Name = "Galaxy S26",
        Description = "Samsung smartphone",
        Price = 69999,
        PictureUrl = "images/galaxy-s26.jpg",
        CategoryId = 1,
        BrandId = 2
    },

    new Product
    {
        Id = 3,
        Name = "Dell Inspiron",
        Description = "Dell laptop",
        Price = 65000,
        PictureUrl = "images/dell-inspiron.jpg",
        CategoryId = 2,
        BrandId = 3
    },

    new Product
    {
        Id = 4,
        Name = "HP Pavilion",
        Description = "HP laptop",
        Price = 60000,
        PictureUrl = "images/hp-pavilion.jpg",
        CategoryId = 2,
        BrandId = 4
    },

    new Product
    {
        Id = 5,
        Name = "Lenovo ThinkPad",
        Description = "Business laptop",
        Price = 85000,
        PictureUrl = "images/thinkpad.jpg",
        CategoryId = 2,
        BrandId = 5
    },

    new Product
    {
        Id = 6,
        Name = "Samsung 55 inch TV",
        Description = "Samsung Smart TV",
        Price = 55000,
        PictureUrl = "images/samsung-tv.jpg",
        CategoryId = 4,
        BrandId = 2
    },

    new Product
    {
        Id = 7,
        Name = "Apple AirPods",
        Description = "Wireless earbuds",
        Price = 19999,
        PictureUrl = "images/airpods.jpg",
        CategoryId = 3,
        BrandId = 1
    }
);
```

---

# 2.6 Why CategoryId and BrandId?

Look at:

```csharp
CategoryId = 1,
BrandId = 1
```

This means:

```text
Product
iPhone 17
   │
   ├── CategoryId = 1
   │       ↓
   │    Mobiles
   │
   └── BrandId = 1
           ↓
         Apple
```

So our relational database is doing the relationship.

---

# 2.7 Complete OnModelCreating

At this point:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Category>().HasData(
        new Category { Id = 1, Name = "Mobiles" },
        new Category { Id = 2, Name = "Laptops" },
        new Category { Id = 3, Name = "Accessories" },
        new Category { Id = 4, Name = "Televisions" }
    );

    modelBuilder.Entity<Brand>().HasData(
        new Brand { Id = 1, Name = "Apple" },
        new Brand { Id = 2, Name = "Samsung" },
        new Brand { Id = 3, Name = "Dell" },
        new Brand { Id = 4, Name = "HP" },
        new Brand { Id = 5, Name = "Lenovo" }
    );

    modelBuilder.Entity<Product>().HasData(
        new Product
        {
            Id = 1,
            Name = "iPhone 17",
            Description = "Apple smartphone",
            Price = 79999,
            PictureUrl = "images/iphone17.jpg",
            CategoryId = 1,
            BrandId = 1
        },
        new Product
        {
            Id = 2,
            Name = "Galaxy S26",
            Description = "Samsung smartphone",
            Price = 69999,
            PictureUrl = "images/galaxy-s26.jpg",
            CategoryId = 1,
            BrandId = 2
        },
        new Product
        {
            Id = 3,
            Name = "Dell Inspiron",
            Description = "Dell laptop",
            Price = 65000,
            PictureUrl = "images/dell-inspiron.jpg",
            CategoryId = 2,
            BrandId = 3
        },
        new Product
        {
            Id = 4,
            Name = "HP Pavilion",
            Description = "HP laptop",
            Price = 60000,
            PictureUrl = "images/hp-pavilion.jpg",
            CategoryId = 2,
            BrandId = 4
        },
        new Product
        {
            Id = 5,
            Name = "Lenovo ThinkPad",
            Description = "Business laptop",
            Price = 85000,
            PictureUrl = "images/thinkpad.jpg",
            CategoryId = 2,
            BrandId = 5
        },
        new Product
        {
            Id = 6,
            Name = "Samsung 55 inch TV",
            Description = "Samsung Smart TV",
            Price = 55000,
            PictureUrl = "images/samsung-tv.jpg",
            CategoryId = 4,
            BrandId = 2
        },
        new Product
        {
            Id = 7,
            Name = "Apple AirPods",
            Description = "Wireless earbuds",
            Price = 19999,
            PictureUrl = "images/airpods.jpg",
            CategoryId = 3,
            BrandId = 1
        }
    );
}
```

---

# 2.8 Create New Migration

Because we changed our EF model by adding seed data, we need a **new migration**.

Open:

**Tools → NuGet Package Manager → Package Manager Console**

Make sure:

```text
Default project = ShopSphere.API
```

Run:

```powershell
Add-Migration SeedProductData
```

You should get a new migration.

Notice something important:

We **don't modify `InitialCreate`**.

Instead:

```text
InitialCreate
      ↓
Created database/tables

SeedProductData
      ↓
Adds initial records
```

This is the correct way to evolve an EF Core database.

---

# 2.9 Apply Migration

Run:

```powershell
Update-Database
```

EF Core will execute the new migration.

---

# 2.10 Verify in SQL Server

Open:

```text
ShopSphereDB
   ↓
Tables
   ↓
dbo.Categories
```

You should see:

| Id | Name        |
| -: | ----------- |
|  1 | Mobiles     |
|  2 | Laptops     |
|  3 | Accessories |
|  4 | Televisions |

---

Check:

```text
dbo.Brands
```

You should see:

| Id | Name    |
| -: | ------- |
|  1 | Apple   |
|  2 | Samsung |
|  3 | Dell    |
|  4 | HP      |
|  5 | Lenovo  |

---

And:

```text
dbo.Products
```

You should see:

| Id | Name               | Price | CategoryId | BrandId |
| -: | ------------------ | ----: | ---------: | ------: |
|  1 | iPhone 17          | 79999 |          1 |       1 |
|  2 | Galaxy S26         | 69999 |          1 |       2 |
|  3 | Dell Inspiron      | 65000 |          2 |       3 |
|  4 | HP Pavilion        | 60000 |          2 |       4 |
|  5 | Lenovo ThinkPad    | 85000 |          2 |       5 |
|  6 | Samsung 55 inch TV | 55000 |          4 |       2 |
|  7 | Apple AirPods      | 19999 |          3 |       1 |

---

# 2.11 What We Learned in This Step

The important concept is:

```text
HasData()
```

EF Core can use:

```csharp
modelBuilder.Entity<Product>().HasData(...)
```

to define initial data as part of the EF model.

Then:

```text
C# Seed Data
       ↓
Add-Migration
       ↓
Migration
       ↓
Update-Database
       ↓
SQL Server
```

---

# ✅ Current Project Status

We have now completed:

```text
                    ShopSphere
                        │
                        ▼
                ASP.NET Core API
                        │
                        ▼
                  EF Core
                        │
                        ▼
                   DbContext
                        │
                        ▼
                  ShopSphereDB
                        │
       ┌────────────────┼────────────────┐
       ▼                ▼                ▼
  Categories         Brands           Products
       │                │                │
       └────────────────┴────────────────┘
                        │
                   Relationships
```

And the database now contains **real product data**.

### Don't create controllers yet.

Once you confirm that the **seeded Categories, Brands and Products are visible in SQL Server**, our **next step will be Step 3: build the first Product Repository and understand why we use Repository Pattern before creating the Product API**. That will connect nicely to the Repository Pattern covered in your course. 
