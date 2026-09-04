Excellent. **Step 9 is complete.**

# Step 10 — JWT Authentication in Practice

Now we'll actually use the JWT we generated.

The goal is to understand this complete flow:

```text
Register
   ↓
Login
   ↓
JWT Token
   ↓
Client stores token
   ↓
Authorization: Bearer <token>
   ↓
ASP.NET Core Authentication
   ↓
[Authorize]
   ↓
Protected API
```

This is an important step because later we'll protect **cart, checkout, orders, and customer APIs**.

---

## 10.1 Create a Protected Customer Endpoint

Open:

```text
Controllers/AccountController.cs
```

Add this namespace:

```csharp
using Microsoft.AspNetCore.Authorization;
```

Then add this method:

```csharp
[Authorize]
[HttpGet("me")]
public async Task<IActionResult> GetCurrentCustomer()
{
    var customerId = User.FindFirst(
        System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;

    if (customerId == null)
    {
        return Unauthorized();
    }

    var customer = await _userManager.FindByIdAsync(customerId);

    if (customer == null)
    {
        return NotFound();
    }

    return Ok(new
    {
        id = customer.Id,
        name = customer.Name,
        email = customer.Email
    });
}
```

So your AccountController now has:

```text
POST /api/Account/register
POST /api/Account/login
GET  /api/Account/me
```

The important difference is:

```csharp
[Authorize]
```

---

# 10.2 What does `[Authorize]` mean?

When we write:

```csharp
[Authorize]
```

we are telling ASP.NET Core:

> Only an authenticated user can execute this endpoint.

Without a valid JWT:

```text
GET /api/Account/me
```

should fail.

With a valid JWT:

```text
GET /api/Account/me
```

should succeed.

---

# 10.3 Test Without JWT

In Postman:

```text
GET http://localhost:5292/api/Account/me
```

Don't add an Authorization header.

Send the request.

You should get:

```text
401 Unauthorized
```

This is exactly what we want.

---

# 10.4 Login and Get the JWT

Now call:

```text
POST http://localhost:5292/api/Account/login
```

Body:

```json
{
    "email": "pradeep@shopsphere.com",
    "password": "Password@123"
}
```

You should receive:

```json
{
    "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

Copy the entire token.

---

# 10.5 Send JWT from Postman

Now open:

```text
GET http://localhost:5292/api/Account/me
```

In Postman:

**Authorization → Type → Bearer Token**

Paste the JWT into:

```text
Token
```

Postman will send:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

Now click **Send**.

You should get something similar to:

```json
{
    "id": 1,
    "name": "Pradeep",
    "email": "pradeep@shopsphere.com"
}
```

🎉 You have now successfully implemented JWT authentication end-to-end.

---

# 10.6 Understand What Happened

The request was:

```text
GET /api/Account/me
```

with:

```http
Authorization: Bearer eyJ...
```

ASP.NET Core's authentication middleware reads the token.

```text
HTTP Request
      │
      ▼
UseAuthentication()
      │
      ▼
Read JWT
      │
      ▼
Validate signature
      │
      ▼
Validate issuer
      │
      ▼
Validate audience
      │
      ▼
Validate expiration
      │
      ▼
Create User
      │
      ▼
[Authorize]
      │
      ▼
AccountController
```

---

# 10.7 Where Does `User` Come From?

This code:

```csharp
User
```

comes from:

```csharp
ControllerBase.User
```

ASP.NET Core creates the authenticated user's `ClaimsPrincipal`.

Our JWT contains the claims we created during login:

```csharp
var claims = new List<Claim>
{
    new Claim(
        ClaimTypes.NameIdentifier,
        customer.Id.ToString()),

    new Claim(
        ClaimTypes.Name,
        customer.Name),

    new Claim(
        ClaimTypes.Email,
        customer.Email!)
};
```

Therefore:

```csharp
User.FindFirst(
    ClaimTypes.NameIdentifier)
```

allows us to retrieve the customer ID.

---

# 10.8 Why Customer ID in the JWT Is Important

This becomes extremely important for ShopSphere.

Suppose customer 1 places an order.

We don't want the client to say:

```text
Give me customer 5's orders
```

Instead, we get the customer ID from the authenticated JWT:

```text
JWT
 │
 └── NameIdentifier = 1
                    ↓
               Customer ID
                    ↓
              Customer Orders
```

So later our order API can do something like:

```csharp
var customerId = User.FindFirst(
    ClaimTypes.NameIdentifier)?.Value;
```

and retrieve **only that customer's orders**.

This is an important security principle.

---

# 10.9 Authentication vs Authorization

You have now seen both concepts practically.

### Authentication

> Who are you?

JWT answers:

```text
Customer ID = 1
Name = Pradeep
Email = pradeep@shopsphere.com
```

### Authorization

> Are you allowed to access this API?

That's:

```csharp
[Authorize]
```

So:

```text
JWT
 ↓
Authentication
 ↓
Who is the user?
 ↓
Authorization
 ↓
Is the user allowed?
```

---

# 10.10 A Useful Experiment

Change:

```csharp
[Authorize]
```

temporarily to:

```csharp
[Authorize]
```

No change is required.

Instead, try three scenarios.

### Scenario 1

No token:

```text
GET /api/Account/me
```

Result:

```text
401 Unauthorized
```

### Scenario 2

Invalid token:

```text
Authorization: Bearer abc123
```

Result:

```text
401 Unauthorized
```

### Scenario 3

Valid JWT:

```text
Authorization: Bearer eyJ...
```

Result:

```text
200 OK
```

This is the best way to understand JWT practically.

---

# 10.11 Our Current API

We now have:

```text
ACCOUNT

POST /api/Account/register
POST /api/Account/login
GET  /api/Account/me       🔒
```

And:

```text
PRODUCTS

GET /api/Products
GET /api/Products/{id}
```

```text
CATEGORIES

GET /api/Categories
```

```text
BRANDS

GET /api/Brands
```

The 🔒 endpoint requires authentication.

---

# Step 10 Checkpoint

Make sure these three tests work:

### 1. Login

```text
POST /api/Account/login
```

Returns JWT.

### 2. Without JWT

```text
GET /api/Account/me
```

Returns:

```text
401 Unauthorized
```

### 3. With JWT

```text
GET /api/Account/me
Authorization: Bearer <your-token>
```

Returns the logged-in customer's details.

---

## Next Step

Once this works, **Step 11 will be the Cart**.

We'll introduce:

```text
Customer
   ↓
Cart
   ├── Product
   ├── Quantity
   └── Price
```

and implement:

```text
POST /api/Cart
GET  /api/Cart
DELETE /api/Cart/{productId}
```

For our MVP, we'll first implement the cart using **SQL Server**, and then introduce **Redis caching**, which is part of the planned ShopSphere architecture. 
