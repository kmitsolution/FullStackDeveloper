We will make **Step 1 completely about ASP.NET Core + Entity Framework Core + SQL Server database creation**.

We will **not create any Angular code, Razor/MVC pages, or API controllers yet**.

At the end of this step, running the EF migration will create:

```text
ShopSphereDB
│
├── Categories
├── Brands
├── Products
├── Customers
├── Addresses
├── Orders
└── OrderItems
```

This is the database foundation for the e-commerce project described in your course, which covers EF Core, SQL Server, product catalog, authentication, addresses, orders and order management. 

---

# STEP 1 — Create ShopSphere Database Using EF Core

## 1. What we are going to do

Our flow is:

```text
Visual Studio
     │
     ▼
ASP.NET Core Web API Project
     │
     ▼
Install EF Core
     │
     ▼
Create Entity Classes
     │
     ▼
Create DbContext
     │
     ▼
Configure Connection String
     │
     ▼
Create Migration
     │
     ▼
Update Database
     │
     ▼
SQL Server
     │
     ▼
ShopSphereDB
```

We are **not writing controllers yet**.

---

# 2. Create ASP.NET Core Project in Visual Studio

Open **Visual Studio**.

Select:

> **Create a new project**

Search:

```text
ASP.NET Core Web API
```

Select:

**ASP.NET Core Web API**

Click **Next**.

---

## Project configuration

Use:

```text
Project name:

ShopSphere.API
```

For the solution:

```text
Solution name:

ShopSphere
```

Choose your desired location.

Click **Next**.

---

## Framework

Select the latest .NET version you have installed.

For example, if you have:

```text
.NET 8
```

use .NET 8.

If you have .NET 9 or .NET 10 installed, you can use that instead.

For this project, the exact version is not important as long as your EF Core packages use the **same major version** as your .NET/EF setup.

---

## Additional Information

For our project, you can leave the standard Web API options.

You may see:

```text
Authentication type: None
Configure for HTTPS: checked
Enable OpenAPI support: checked
```

For now:

```text
Authentication: None
HTTPS: Yes
OpenAPI: Yes
```

Click **Create**.

---

# 3. Important — We Don't Need the Default API

Visual Studio may create something like:

```text
Controllers
    WeatherForecastController.cs

WeatherForecast.cs
```

We don't need those for this step.

You can delete:

```text
Controllers
WeatherForecast.cs
```

We are **not creating any API controller yet**.

Our project will initially look like:

```text
ShopSphere
│
└── ShopSphere.API
    │
    ├── Data
    │
    ├── Models
    │
    ├── Program.cs
    ├── appsettings.json
    └── ShopSphere.API.csproj
```

We'll create the folders next.

---

# 4. Install Entity Framework Core

Right-click the project:

```text
ShopSphere.API
```

Select:

> **Manage NuGet Packages**

Go to **Browse**.

Install these packages.

## Package 1 — SQL Server

Search:

```text
Microsoft.EntityFrameworkCore.SqlServer
```

Install the version corresponding to your .NET/EF Core version.

For example:

```text
Microsoft.EntityFrameworkCore.SqlServer 8.x
```

if you are using .NET 8.

---

## Package 2 — EF Core Tools

Search:

```text
Microsoft.EntityFrameworkCore.Tools
```

Install the matching version.

This gives us commands such as:

```powershell
Add-Migration
Update-Database
```

inside Visual Studio's **Package Manager Console**.

---

## Package 3 — EF Core Design

Also install:

```text
Microsoft.EntityFrameworkCore.Design
```

Again, use the same major version.

So we should have:

```text
Microsoft.EntityFrameworkCore.SqlServer
Microsoft.EntityFrameworkCore.Tools
Microsoft.EntityFrameworkCore.Design
```

### Why three packages?

```text
SqlServer
    ↓
Allows EF Core to communicate with SQL Server

Design
    ↓
Provides design-time functionality used by migrations

Tools
    ↓
Provides Visual Studio EF commands
```

---

# 5. Check EF Tools

In Visual Studio:

> Tools → NuGet Package Manager → Package Manager Console

You will see:

```text
PM>
```

Run:

```powershell
Get-Help EntityFrameworkCore
```

If the tools are installed correctly, EF Core commands should be available.

Later we'll use:

```powershell
Add-Migration InitialCreate
```

and:

```powershell
Update-Database
```

---

# 6. Create Models Folder

Right-click:

```text
ShopSphere.API
```

Select:

> Add → New Folder

Name it:

```text
Models
```

Our structure:

```text
ShopSphere.API
│
├── Models
│
├── Data
│
├── Program.cs
└── appsettings.json
```

Create another folder:

```text
Data
```

---

# 7. Design Our Database Before Writing Classes

This is important.

We don't randomly create classes.

Our business requirement is:

```text
A customer can purchase products.
Products belong to categories and brands.
A customer has addresses.
A customer can place orders.
An order contains multiple products.
```

Therefore our database relationships are:

```text
Category
   │
   │ 1
   │
   │ *
   ▼
Product
   ▲
   │ *
   │
   │ 1
Brand


Customer
   │
   ├──────────────< Address
   │
   └──────────────< Order
                         │
                         │ 1
                         ▼
                    OrderItem
                         │
                         │ *
                         ▼
                      Product
```

---

# 8. Table 1 — Categories

Create:

```text
Models
   └── Category.cs
```

Code:

```csharp
namespace ShopSphere.API.Models;

public class Category
{
    public int Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public ICollection<Product> Products { get; set; }
        = new List<Product>();
}
```

### Database table

EF Core will create approximately:

```text
Categories
--------------------------------
Id          int        PK
Name        nvarchar
```

### Relationship

```text
Category 1 ─────────── * Product
```

Meaning:

> One category can have many products.

Example:

```text
Electronics
    │
    ├── iPhone
    ├── Samsung Galaxy
    └── Dell Laptop
```

---

# 9. Table 2 — Brands

Create:

```text
Models
   └── Brand.cs
```

```csharp
namespace ShopSphere.API.Models;

public class Brand
{
    public int Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public ICollection<Product> Products { get; set; }
        = new List<Product>();
}
```

Database:

```text
Brands
---------------------------
Id       int       PK
Name     nvarchar
```

Relationship:

```text
Brand 1 ─────────── * Product
```

Example:

```text
Dell
 │
 ├── Dell Laptop
 └── Dell Monitor
```

---

# 10. Table 3 — Products

Create:

```text
Models
   └── Product.cs
```

```csharp
namespace ShopSphere.API.Models;

public class Product
{
    public int Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public string Description { get; set; } = string.Empty;

    public decimal Price { get; set; }

    public string PictureUrl { get; set; } = string.Empty;

    public int CategoryId { get; set; }

    public Category Category { get; set; } = null!;

    public int BrandId { get; set; }

    public Brand Brand { get; set; } = null!;

    public ICollection<OrderItem> OrderItems { get; set; }
        = new List<OrderItem>();
}
```

Database:

```text
Products
------------------------------------------------
Id             int             PK
Name           nvarchar
Description    nvarchar
Price          decimal
PictureUrl     nvarchar
CategoryId     int             FK
BrandId        int             FK
```

Relationships:

```text
Category 1 ───────── * Product

Brand    1 ───────── * Product
```

So a product belongs to:

```text
one Category
one Brand
```

---

# 11. Table 4 — Customers

For the first database version, we'll keep the business customer table simple.

Create:

```text
Models
   └── Customer.cs
```

```csharp
namespace ShopSphere.API.Models;

public class Customer
{
    public int Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public string Email { get; set; } = string.Empty;

    public ICollection<Address> Addresses { get; set; }
        = new List<Address>();

    public ICollection<Order> Orders { get; set; }
        = new List<Order>();
}
```

Database:

```text
Customers
--------------------------------
Id          int          PK
Name        nvarchar
Email       nvarchar
```

Relationship:

```text
Customer 1 ───────── * Address

Customer 1 ───────── * Order
```

### Important

We are **not implementing ASP.NET Core Identity yet**.

Later, when we reach authentication, we'll introduce Identity + JWT as specified in the course. 

At that point we'll decide whether to integrate Identity with this customer model or replace it with an Identity-based user model.

For this first database milestone, this keeps the schema and EF learning straightforward.

---

# 12. Table 5 — Addresses

Create:

```text
Models
   └── Address.cs
```

```csharp
namespace ShopSphere.API.Models;

public class Address
{
    public int Id { get; set; }

    public string FirstName { get; set; } = string.Empty;

    public string LastName { get; set; } = string.Empty;

    public string Street { get; set; } = string.Empty;

    public string City { get; set; } = string.Empty;

    public string State { get; set; } = string.Empty;

    public string ZipCode { get; set; } = string.Empty;

    public string Country { get; set; } = string.Empty;

    public int CustomerId { get; set; }

    public Customer Customer { get; set; } = null!;
}
```

Database:

```text
Addresses
------------------------------------------------
Id             int          PK
FirstName      nvarchar
LastName       nvarchar
Street         nvarchar
City           nvarchar
State          nvarchar
ZipCode        nvarchar
Country        nvarchar
CustomerId     int          FK
```

Relationship:

```text
Customer 1 ───────── * Address
```

One customer can have:

```text
Home Address
Office Address
Delivery Address
```

---

# 13. Table 6 — Orders

Create:

```text
Models
   └── Order.cs
```

```csharp
namespace ShopSphere.API.Models;

public class Order
{
    public int Id { get; set; }

    public DateTime OrderDate { get; set; }

    public decimal Total { get; set; }

    public string Status { get; set; } = "Pending";

    public int CustomerId { get; set; }

    public Customer Customer { get; set; } = null!;

    public ICollection<OrderItem> OrderItems { get; set; }
        = new List<OrderItem>();
}
```

Database:

```text
Orders
------------------------------------------------
Id             int          PK
OrderDate      datetime
Total          decimal
Status         nvarchar
CustomerId     int          FK
```

Relationship:

```text
Customer 1 ───────── * Order
```

For example:

```text
Customer: Raman

Orders:
   #1001
   #1002
   #1003
```

---

# 14. Table 7 — OrderItems

Create:

```text
Models
   └── OrderItem.cs
```

```csharp
namespace ShopSphere.API.Models;

public class OrderItem
{
    public int Id { get; set; }

    public int Quantity { get; set; }

    public decimal Price { get; set; }

    public int OrderId { get; set; }

    public Order Order { get; set; } = null!;

    public int ProductId { get; set; }

    public Product Product { get; set; } = null!;
}
```

Database:

```text
OrderItems
--------------------------------------------
Id          int          PK
Quantity    int
Price       decimal
OrderId     int          FK
ProductId   int          FK
```

Relationships:

```text
Order 1 ───────── * OrderItem

Product 1 ─────── * OrderItem
```

---

# 15. Why Do We Need OrderItem?

This is a very important database design concept.

Suppose an order contains:

```text
Order #1001

Laptop       Quantity 1
Mouse        Quantity 2
Keyboard     Quantity 1
```

We cannot simply put:

```text
Order
--------------------
ProductId
Quantity
```

because one order has multiple products.

Therefore:

```text
Order
  │
  └──────< OrderItem
              │
              └──── Product
```

`OrderItem` acts as the connection between `Order` and `Product`.

---

# 16. Final Database Relationship

Our complete model is:

```text
                         ┌──────────────┐
                         │  Categories  │
                         └──────┬───────┘
                                │ 1
                                │
                                │ *
                         ┌──────▼───────┐
                         │   Products   │
                         └──────▲───────┘
                                │ *
                                │
                         ┌──────┴───────┐
                         │    Brands    │
                         └──────────────┘


┌──────────────┐
│  Customers   │
└──────┬───────┘
       │
       ├─────────────── * ────── Addresses
       │
       │ 1
       │
       │ *
       ▼
┌──────────────┐
│    Orders    │
└──────┬───────┘
       │ 1
       │
       │ *
       ▼
┌──────────────┐
│  OrderItems  │
└──────┬───────┘
       │ *
       │
       │ 1
       ▼
┌──────────────┐
│   Products   │
└──────────────┘
```

---

# 17. Create DbContext

Now EF Core needs a class that represents our database.

Inside:

```text
Data
```

create:

```text
ShopSphereDbContext.cs
```

Code:

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
}
```

This is one of the most important classes in our application.

Think of:

```text
ShopSphereDbContext
```

as:

> The bridge between our C# objects and SQL Server.

---

# 18. Understand DbSet

When we write:

```csharp
public DbSet<Product> Products { get; set; }
```

EF Core understands:

```text
Product class
      ↓
Products table
```

Similarly:

```csharp
public DbSet<Category> Categories { get; set; }
```

means:

```text
Category class
      ↓
Categories table
```

So:

```text
DbContext
   │
   ├── DbSet<Category>
   ├── DbSet<Brand>
   ├── DbSet<Product>
   ├── DbSet<Customer>
   ├── DbSet<Address>
   ├── DbSet<Order>
   └── DbSet<OrderItem>
```

represents our database tables.

---

# 19. Add SQL Server Connection String

Open:

```text
appsettings.json
```

Add:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=ShopSphereDB;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

This is the connection string you specified.

It means:

```text
Server=.
```

Connect to the SQL Server instance on the local machine.

```text
Database=ShopSphereDB
```

Use/create a database called:

```text
ShopSphereDB
```

```text
Trusted_Connection=True
```

Use Windows authentication.

```text
TrustServerCertificate=True
```

Trust the SQL Server certificate for this local development setup.

---

# 20. Configure DbContext in Program.cs

We aren't creating any API endpoints.

But `Program.cs` still needs to tell ASP.NET Core:

> "Use SQL Server and this DbContext."

Open:

```text
Program.cs
```

Add:

```csharp
using Microsoft.EntityFrameworkCore;
using ShopSphere.API.Data;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<ShopSphereDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

app.Run();
```

That's enough for this stage.

We don't need:

```text
Controllers
Endpoints
Pages
```

yet.

---

# 21. What Does This Configuration Do?

This:

```csharp
builder.Services.AddDbContext<ShopSphereDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));
```

connects these pieces:

```text
appsettings.json
       │
       │ Connection String
       ▼
Program.cs
       │
       ▼
ShopSphereDbContext
       │
       ▼
Entity Framework Core
       │
       ▼
SQL Server
```

---

# 22. Create EF Migration

Now comes the important part.

Open:

> Tools → NuGet Package Manager → Package Manager Console

At the bottom:

```text
PM>
```

Make sure **Default project** is:

```text
ShopSphere.API
```

Run:

```powershell
Add-Migration InitialCreate
```

EF Core examines:

```text
Category
Brand
Product
Customer
Address
Order
OrderItem
```

and generates a migration describing how to create the database schema.

You should see a new folder:

```text
Migrations
```

Something like:

```text
Migrations
│
├── 202609040xxxxx_InitialCreate.cs
├── 202609040xxxxx_InitialCreate.Designer.cs
└── ShopSphereDbContextModelSnapshot.cs
```

---

# 23. What Did Add-Migration Do?

This command:

```powershell
Add-Migration InitialCreate
```

**does NOT create the database yet.**

It creates instructions for EF Core:

```text
Create Categories table
Create Brands table
Create Products table
Create Customers table
Create Addresses table
Create Orders table
Create OrderItems table

Create Primary Keys
Create Foreign Keys
Create Relationships
```

Think:

```text
C# Classes
     ↓
Add-Migration
     ↓
Migration instructions
```

---

# 24. Create the Actual Database

Now run:

```powershell
Update-Database
```

EF Core will use:

```text
DefaultConnection
```

and connect to SQL Server.

Then:

```text
ShopSphereDB
```

will be created.

The process is:

```text
C# Entity Classes
       ↓
DbContext
       ↓
Add-Migration
       ↓
Migration
       ↓
Update-Database
       ↓
SQL Server
       ↓
ShopSphereDB
```

---

# 25. Check SQL Server

Open:

**SQL Server Management Studio**

Connect to your local SQL Server.

Expand:

```text
Databases
```

You should see:

```text
ShopSphereDB
```

Expand:

```text
ShopSphereDB
   ↓
Tables
```

You should find our tables, along with EF Core's migration history table:

```text
dbo.Addresses
dbo.Brands
dbo.Categories
dbo.Customers
dbo.OrderItems
dbo.Orders
dbo.Products
dbo.__EFMigrationsHistory
```

`__EFMigrationsHistory` is created by EF Core to track which migrations have already been applied.

---

# 26. Expected SQL Database

At this stage:

```text
ShopSphereDB
│
├── dbo.Addresses
│
├── dbo.Brands
│
├── dbo.Categories
│
├── dbo.Customers
│
├── dbo.OrderItems
│
├── dbo.Orders
│
├── dbo.Products
│
└── dbo.__EFMigrationsHistory
```

---

# 27. Expected Foreign Keys

The database should contain relationships similar to:

### Products

```text
Products.CategoryId
       ↓
Categories.Id
```

```text
Products.BrandId
       ↓
Brands.Id
```

### Addresses

```text
Addresses.CustomerId
       ↓
Customers.Id
```

### Orders

```text
Orders.CustomerId
       ↓
Customers.Id
```

### OrderItems

```text
OrderItems.OrderId
       ↓
Orders.Id
```

and:

```text
OrderItems.ProductId
       ↓
Products.Id
```

---

# 28. The Important Concept — EF Core Is Doing the Work

Notice what we did **not** do.

We did not manually write:

```sql
CREATE TABLE Products ...
```

Instead:

```text
C# Classes
     ↓
EF Core
     ↓
Migration
     ↓
SQL Server Tables
```

This is the core idea of **Code First Entity Framework Core**.

Our C# classes are the starting point for the database model.

---

# 29. Our Current Project Structure

After completing this step, you should have:

```text
ShopSphere
│
└── ShopSphere.API
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
    ├── Migrations
    │   ├── InitialCreate.cs
    │   ├── InitialCreate.Designer.cs
    │   └── ShopSphereDbContextModelSnapshot.cs
    │
    ├── Program.cs
    ├── appsettings.json
    └── ShopSphere.API.csproj
```

No controllers.

No Angular.

No pages.

No Redis.

No authentication.

**Just our database foundation.**

---

# 30. What We Have Completed

At the end of Step 1:

```text
                 ShopSphere
                     │
                     ▼
             ASP.NET Core API
                     │
                     ▼
             Entity Framework Core
                     │
                     ▼
                DbContext
                     │
                     ▼
                SQL Server
                     │
                     ▼
              ShopSphereDB
                     │
       ┌─────────────┼─────────────┐
       ▼             ▼             ▼
   Categories      Brands       Products
                                   │
                                   │
                              OrderItems
                                   │
                                   ▼
                                Orders
                                   ▲
                                   │
                               Customers
                                   │
                                   ▼
                               Addresses
```

### Don't move to controllers yet.

**First complete exactly this database step and verify that `ShopSphereDB` and the seven tables appear in SQL Server.**

Once that is working, our **next step** will be **EF Core data seeding**: we'll put realistic Categories, Brands and Products into the database and learn how EF Core creates initial data. After that we'll build the Product API.
