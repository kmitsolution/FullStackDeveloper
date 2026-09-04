
# ShopSphere — Project Development Document

## Part 1: ASP.NET Core + Entity Framework Core

### Project Name

**ShopSphere – E-Commerce Web Application**

### Technology Stack

For the backend, we will use:

| Technology            | Purpose                 |
| --------------------- | ----------------------- |
| ASP.NET Core Web API  | Build REST APIs         |
| C#                    | Backend programming     |
| Entity Framework Core | Database access         |
| SQL Server            | Relational database     |
| LINQ                  | Querying data           |
| Repository Pattern    | Data access abstraction |
| Dependency Injection  | Manage dependencies     |
| JWT                   | Authentication later    |
| Redis                 | Shopping cart later     |
| SignalR               | Real-time updates later |

The attached course specifically includes ASP.NET Core, C#, EF Core, SQL Server, REST APIs, Repository Pattern, Generic Repository, Specification Pattern, Unit of Work, authentication, Redis, SignalR and Azure. 

---

# 1. What Are We Building?

We are building an **online shopping application**.

A customer will eventually be able to:

```text
Browse Products
       ↓
Search Products
       ↓
Filter Products
       ↓
View Product Details
       ↓
Add to Cart
       ↓
Login/Register
       ↓
Checkout
       ↓
Place Order
       ↓
View Order
```

The administrator will eventually be able to:

```text
Admin Login
     ↓
Manage Products
     ↓
Manage Categories
     ↓
Manage Brands
     ↓
View Orders
     ↓
Update Order Status
     ↓
Process Refund
```

The course's Product Catalog, Shopping Cart, Authentication, Checkout, Order Management and Administration modules support this overall application flow. 

---

# 2. Overall Backend Architecture

Initially, don't worry about implementing every pattern.

We will gradually arrive at:

```text
                    ASP.NET Core Web API
                             │
                             ▼
                       Controllers
                             │
                             ▼
                     Business Logic
                             │
                             ▼
                       Repository
                             │
                             ▼
                    Entity Framework Core
                             │
                             ▼
                         DbContext
                             │
                             ▼
                         SQL Server
```

Later we will add:

```text
                 ┌───────────────┐
                 │ ASP.NET Core  │
                 └───────┬───────┘
                         │
          ┌──────────────┼───────────────┐
          ▼              ▼               ▼
       SQL Server      Redis           Stripe
          │              │               │
       Products        Cart            Payment
       Orders          Cache           Refunds
       Users
```

And later:

```text
                         ASP.NET Core
                              │
              ┌───────────────┼──────────────┐
              │               │              │
              ▼               ▼              ▼
          SQL Server        Redis         SignalR
                                             │
                                             ▼
                                        Angular Client
```

---

# 3. Database Design

For our **first working version**, we will use these tables:

```text
Categories
Brands
Products
Users
Addresses
Orders
OrderItems
```

Later, some additional tables will be introduced when we implement Identity and other features.

---

# 4. Category Table

A category groups similar products.

Examples:

```text
Electronics
Laptops
Mobiles
Accessories
Televisions
```

### Table: `Categories`

| Column | Type     | Description   |
| ------ | -------- | ------------- |
| Id     | int      | Primary Key   |
| Name   | nvarchar | Category name |

Example:

```text
Id    Name
--------------------
1     Electronics
2     Laptops
3     Mobiles
4     Accessories
```

Relationship:

```text
Category
   │
   └──────< Products
```

One category can contain many products.

---

# 5. Brand Table

A brand represents the manufacturer.

Examples:

```text
Apple
Samsung
Dell
HP
Lenovo
Sony
```

### Table: `Brands`

| Column | Type     | Description |
| ------ | -------- | ----------- |
| Id     | int      | Primary Key |
| Name   | nvarchar | Brand name  |

Example:

```text
Id    Name
----------------
1     Apple
2     Samsung
3     Dell
4     HP
```

Relationship:

```text
Brand
  │
  └──────< Products
```

---

# 6. Product Table

This is the most important table in our first phase.

### Table: `Products`

| Column      | Type     | Description         |
| ----------- | -------- | ------------------- |
| Id          | int      | Primary Key         |
| Name        | nvarchar | Product name        |
| Description | nvarchar | Product description |
| Price       | decimal  | Product price       |
| PictureUrl  | nvarchar | Product image       |
| CategoryId  | int      | Foreign Key         |
| BrandId     | int      | Foreign Key         |

Example:

```text
Id   Name          Price      CategoryId   BrandId
--------------------------------------------------
1    iPhone 17     79999         3           1
2    Galaxy S26    69999         3           2
3    Dell Laptop   65000         2           3
```

Relationship:

```text
Category ────────< Product >──────── Brand
```

---

# 7. User Table

Eventually we need customers.

However, **we will not manually create a basic `Users` table and then build authentication on top of it**.

When we reach authentication, we will introduce:

**ASP.NET Core Identity**

because the course specifically covers ASP.NET Core Identity and JWT authentication. 

Conceptually:

```text
User
 │
 ├── Login
 ├── Password
 ├── Email
 ├── Role
 └── Address
```

Identity will eventually create/manage several tables for us.

So for our first database step, **we will postpone the detailed User/Identity schema**.

---

# 8. Address

A customer can have an address used for delivery.

Conceptually:

### `Addresses`

| Column    | Type     |
| --------- | -------- |
| Id        | int      |
| FirstName | nvarchar |
| LastName  | nvarchar |
| Street    | nvarchar |
| City      | nvarchar |
| State     | nvarchar |
| ZipCode   | nvarchar |
| Country   | nvarchar |

Relationship:

```text
User
 │
 └──────< Address
```

We will implement this when we build authentication and checkout.

---

# 9. Order Table

An order represents a customer's purchase.

### `Orders`

| Column    | Type       | Description   |
| --------- | ---------- | ------------- |
| Id        | int        | Primary Key   |
| UserId    | string/int | Customer      |
| OrderDate | datetime   | Date of order |
| Total     | decimal    | Total amount  |
| Status    | nvarchar   | Order status  |

Example:

```text
Id     UserId     OrderDate       Total       Status
----------------------------------------------------------
1001   user1      2026-09-04      62500       Pending
1002   user2      2026-09-04      45000       Shipped
```

Possible statuses:

```text
Pending
PaymentReceived
Processing
Shipped
Delivered
Cancelled
```

---

# 10. OrderItems Table

An order can contain multiple products.

For example:

```text
Order #1001

Laptop × 1
Mouse  × 2
Keyboard × 1
```

We therefore need `OrderItems`.

### `OrderItems`

| Column    | Type    | Description        |
| --------- | ------- | ------------------ |
| Id        | int     | Primary Key        |
| OrderId   | int     | Foreign Key        |
| ProductId | int     | Foreign Key        |
| Quantity  | int     | Quantity purchased |
| Price     | decimal | Price at purchase  |

Example:

```text
Id   OrderId   ProductId   Quantity   Price
------------------------------------------------
1    1001      3           1          60000
2    1001      10          2           1000
```

Relationship:

```text
Order
  │
  └──────< OrderItems >────── Product
```

---

# 11. Complete Database Relationship

So our important relationships are:

```text
                    Category
                       │
                       │ 1
                       │
                       │ *
                    Product
                       │
                       │
                       │
                     Brand


Customer/User
      │
      │ 1
      │
      ├──────────< Address
      │
      │ 1
      │
      └──────────< Order
                     │
                     │ 1
                     │
                     │ *
                  OrderItem
                     │
                     │ *
                     │
                     ▼
                   Product
```

A simpler view:

```text
Category ────────< Product >──────── Brand
                     ▲
                     │
                     │
                 OrderItem
                     ▲
                     │
                   Order
                     ▲
                     │
                    User
```

---

# 12. Database We Will Initially Create

For the first part of development, our database will essentially start with:

```text
ShopSphereDb
│
├── Categories
│
├── Brands
│
└── Products
```

Then we'll add:

```text
ShopSphereDb
│
├── Categories
├── Brands
├── Products
│
├── Users / Identity
├── Addresses
│
├── Orders
└── OrderItems
```

Then Redis will sit **outside SQL Server**:

```text
                    ShopSphere
                        │
              ┌─────────┴─────────┐
              │                   │
          SQL Server             Redis
              │                   │
        Permanent Data        Temporary/Fast Data
              │                   │
        Products              Shopping Cart
        Users                 Cache
        Orders
```

---

# 13. Why Are We Separating SQL Server and Redis?

This is an important concept for the project.

### SQL Server

Used for data that must be permanently stored:

```text
Products
Users
Orders
OrderItems
Addresses
```

### Redis

Used for fast-access/temporary data:

```text
Shopping Cart
Cache
```

So:

```text
Customer adds laptop to cart
             ↓
           Redis

Customer places order
             ↓
         SQL Server
```

This gives us a practical reason for introducing Redis instead of simply using it because it is in the syllabus.

---

# 14. ASP.NET Core Project Structure

Since you're using **Visual Studio**, we will create the solution there.

Our eventual structure will be:

```text
ShopSphere
│
├── ShopSphere.API
│
├── ShopSphere.Core
│
└── ShopSphere.Infrastructure
```

### `ShopSphere.API`

Contains things related to the Web API:

```text
Controllers
Middleware
Program.cs
Configuration
```

### `ShopSphere.Core`

Contains our core/domain concepts:

```text
Entities
Interfaces
Specifications
DTOs
```

### `ShopSphere.Infrastructure`

Contains implementation details:

```text
DbContext
Entity Framework Core
Repositories
Migrations
Redis
Identity
```

We'll build this gradually rather than creating hundreds of files immediately.

---

# 15. Development Sequence

Now our ASP.NET development will follow this exact order:

### Part 1 — Solution

```text
Create Visual Studio solution
        ↓
Create API project
        ↓
Create Core project
        ↓
Create Infrastructure project
```

### Part 2 — Entity Framework Core

```text
Install EF Core
        ↓
Create Entities
        ↓
Create DbContext
        ↓
Configure SQL Server
```

### Part 3 — Database

```text
Migration
    ↓
SQL Server Database
    ↓
Tables
```

### Part 4 — Seed Data

```text
Categories
Brands
Products
       ↓
Initial data
```

### Part 5 — Product API

```text
GET Products
GET Product
POST Product
PUT Product
DELETE Product
```

### Part 6 — Repository

```text
Controller
    ↓
Repository
    ↓
DbContext
    ↓
SQL Server
```

### Part 7 — Search / Filtering / Pagination

```text
/api/products?search=laptop
/api/products?brandId=2
/api/products?categoryId=1
/api/products?pageIndex=1&pageSize=10
```

### Part 8 — Middleware

```text
Exception Handling
CORS
Validation
```

### Part 9 — Authentication

```text
ASP.NET Core Identity
        ↓
JWT
        ↓
Authorization
```

### Part 10 — Orders

```text
Cart
 ↓
Checkout
 ↓
Order
 ↓
OrderItems
 ↓
SQL Server
```

Then we move to Angular.

---

# 🎯 Our First Milestone

For now, **we don't need to think about Angular at all**.

Our first milestone is:

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
                    SQL Server
                        │
             ┌──────────┼──────────┐
             ▼          ▼          ▼
         Categories   Brands    Products
```

Once this works, we will have our first real backend foundation.

---

## Part 1 — What I want you to do now

Since you're using **Visual Studio**, let's start directly there.

Open **Visual Studio → Create a new project**.

Choose:

**ASP.NET Core Web API**

Set:

```text
Project Name: ShopSphere.API
Solution Name: ShopSphere
```

For the framework, use the **latest .NET version installed on your machine**.

For this first step, **don't create Core, Infrastructure, EF Core, entities, controllers, or database yet**.

Create only the **ASP.NET Core Web API project**.

Once you have created it and the default API runs successfully, tell me **"API created"**.

Then I'll give you **Part 2: create the Core and Infrastructure projects + add the correct project references**, and we'll proceed one step at a time.
