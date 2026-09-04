Great. **Step 8 is complete**, so let's implement the actual authentication flow.

# Step 9 — Customer Registration & JWT Login

In this lesson we will create two APIs:

```text
POST /api/Account/register
POST /api/Account/login
```

The flow will be:

```text
                Customer
                   │
          ┌────────┴────────┐
          │                 │
       Register            Login
          │                 │
          ▼                 ▼
   ASP.NET Identity    ASP.NET Identity
          │                 │
          ▼                 ▼
     SQL Server          Validate
                            │
                            ▼
                       JWT Token
```

This is the authentication foundation we'll need later for cart, checkout, orders, and protected APIs. The course document also places Identity/JWT in the ShopSphere backend flow. 

---

# 9.1 Install JWT package

In **Package Manager Console**:

```powershell
Install-Package Microsoft.AspNetCore.Authentication.JwtBearer
```

For .NET 10, use the package version compatible with your project's .NET 10/ASP.NET Core version.

---

# 9.2 Create Account DTOs

Create:

```text
DTOs
├── ProductDto.cs
├── PagedResultDto.cs
├── RegisterDto.cs
└── LoginDto.cs
```

### `RegisterDto.cs`

```csharp
namespace ShopSphere.API.DTOs;

public class RegisterDto
{
    public string Name { get; set; } = string.Empty;

    public string Email { get; set; } = string.Empty;

    public string Password { get; set; } = string.Empty;
}
```

### `LoginDto.cs`

```csharp
namespace ShopSphere.API.DTOs;

public class LoginDto
{
    public string Email { get; set; } = string.Empty;

    public string Password { get; set; } = string.Empty;
}
```

---

# 9.3 Create AccountController

Create:

```text
Controllers
├── ProductsController.cs
├── CategoriesController.cs
├── BrandsController.cs
└── AccountController.cs
```

Add:

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;
using ShopSphere.API.DTOs;
using ShopSphere.API.Models;

namespace ShopSphere.API.Controllers;

[ApiController]
[Route("api/[controller]")]
public class AccountController : ControllerBase
{
    private readonly UserManager<Customer> _userManager;

    public AccountController(
        UserManager<Customer> userManager)
    {
        _userManager = userManager;
    }

    [HttpPost("register")]
    public async Task<IActionResult> Register(RegisterDto model)
    {
        var existingUser = await _userManager.FindByEmailAsync(model.Email);

        if (existingUser != null)
        {
            return BadRequest("Email already exists.");
        }

        var customer = new Customer
        {
            Name = model.Name,
            UserName = model.Email,
            Email = model.Email
        };

        var result = await _userManager.CreateAsync(
            customer,
            model.Password);

        if (!result.Succeeded)
        {
            return BadRequest(result.Errors);
        }

        return Ok(new
        {
            message = "Registration successful."
        });
    }
}
```

---

# 9.4 Test Registration

Start your API.

In Postman:

```text
POST
http://localhost:5292/api/Account/register
```

Select:

```text
Body → raw → JSON
```

Send:

```json
{
    "name": "Pradeep",
    "email": "pradeep@shopsphere.com",
    "password": "Password@123"
}
```

Expected:

```json
{
    "message": "Registration successful."
}
```

---

# 9.5 Check SQL Server

Open:

```text
ShopSphereDB
   ↓
AspNetUsers
```

You should find the newly registered user.

You should **not** see the plain-text password.

Instead, Identity stores a password hash.

This is one of the major advantages of using:

```text
ASP.NET Core Identity
```

rather than creating our own password system.

---

# 9.6 Add JWT Configuration

Now we'll prepare the application to generate JWT tokens.

Open:

```text
appsettings.json
```

Add:

```json
"Jwt": {
  "Key": "ShopSphere-SuperSecret-Key-For-Jwt-2026",
  "Issuer": "ShopSphere",
  "Audience": "ShopSphereUsers"
}
```

Your file will look approximately like:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=ShopSphereDB;Trusted_Connection=True;TrustServerCertificate=True;"
  },

  "Jwt": {
    "Key": "ShopSphere-SuperSecret-Key-For-Jwt-2026",
    "Issuer": "ShopSphere",
    "Audience": "ShopSphereUsers"
  }
}
```

### Important

For our local learning project this is fine.

In a real application, **don't commit the JWT secret to GitHub**. We'd eventually use environment variables, Azure Key Vault, or another secret-management mechanism.

---

# 9.7 Configure JWT Authentication

Open:

```text
Program.cs
```

Add:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.Text;
```

Then after your Identity configuration:

```csharp
var jwtKey = builder.Configuration["Jwt:Key"]
    ?? throw new InvalidOperationException("JWT Key is missing.");

builder.Services
    .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,

            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],

            IssuerSigningKey =
                new SymmetricSecurityKey(
                    Encoding.UTF8.GetBytes(jwtKey))
        };
    });

builder.Services.AddAuthorization();
```

Your service configuration should now conceptually be:

```text
AddControllers
      ↓
AddDbContext
      ↓
AddIdentity
      ↓
AddAuthentication(JWT)
      ↓
AddAuthorization
```

---

# 9.8 Add Authentication Middleware

Near the bottom of `Program.cs`, before:

```csharp
app.MapControllers();
```

add:

```csharp
app.UseAuthentication();

app.UseAuthorization();

app.MapControllers();
```

So:

```csharp
var app = builder.Build();

app.UseAuthentication();

app.UseAuthorization();

app.MapControllers();

app.Run();
```

### Why this order?

The request pipeline becomes:

```text
HTTP Request
     ↓
Authentication
     ↓
Authorization
     ↓
Controller
```

Authentication answers:

> Who is this user?

Authorization answers:

> Is this user allowed to access this resource?

---

# 9.9 Add JWT Generation

Now modify `AccountController`.

We need:

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Microsoft.IdentityModel.Tokens;
```

Inject `IConfiguration`:

```csharp
private readonly UserManager<Customer> _userManager;
private readonly IConfiguration _configuration;

public AccountController(
    UserManager<Customer> userManager,
    IConfiguration configuration)
{
    _userManager = userManager;
    _configuration = configuration;
}
```

Now add the login endpoint:

```csharp
[HttpPost("login")]
public async Task<IActionResult> Login(LoginDto model)
{
    var customer = await _userManager.FindByEmailAsync(model.Email);

    if (customer == null)
    {
        return Unauthorized("Invalid email or password.");
    }

    var passwordValid = await _userManager.CheckPasswordAsync(
        customer,
        model.Password);

    if (!passwordValid)
    {
        return Unauthorized("Invalid email or password.");
    }

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

    var key = new SymmetricSecurityKey(
        Encoding.UTF8.GetBytes(
            _configuration["Jwt:Key"]!));

    var credentials = new SigningCredentials(
        key,
        SecurityAlgorithms.HmacSha256);

    var token = new JwtSecurityToken(
        issuer: _configuration["Jwt:Issuer"],
        audience: _configuration["Jwt:Audience"],
        claims: claims,
        expires: DateTime.UtcNow.AddHours(2),
        signingCredentials: credentials);

    var tokenString = new JwtSecurityTokenHandler()
        .WriteToken(token);

    return Ok(new
    {
        token = tokenString
    });
}
```

---

# 9.10 Test Login

First register a customer:

```text
POST /api/Account/register
```

Then:

```text
POST /api/Account/login
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

That long string is the **JWT token**.

---

# 9.11 What is inside the JWT?

Conceptually:

```text
JWT
│
├── Header
│
├── Payload
│     ├── User ID
│     ├── Name
│     └── Email
│
└── Signature
```

We created these claims:

```csharp
new Claim(
    ClaimTypes.NameIdentifier,
    customer.Id.ToString())
```

and:

```csharp
new Claim(
    ClaimTypes.Name,
    customer.Name)
```

and:

```csharp
new Claim(
    ClaimTypes.Email,
    customer.Email!)
```

So later, our APIs can identify the logged-in customer.

---

# 9.12 Don't test protected APIs yet

We haven't created a protected endpoint yet.

The next step will demonstrate the complete JWT process:

```text
Register
   ↓
Login
   ↓
JWT
   ↓
Send JWT in Authorization header
   ↓
[Authorize]
   ↓
Protected API
```

We'll also use:

```text
User.FindFirst(...)
```

to retrieve the customer ID from the JWT.

---

## Step 9 checkpoint

For this lesson, verify these two endpoints:

### Register

```text
POST http://localhost:5292/api/Account/register
```

```json
{
    "name": "Pradeep",
    "email": "pradeep@shopsphere.com",
    "password": "Password@123"
}
```

### Login

```text
POST http://localhost:5292/api/Account/login
```

```json
{
    "email": "pradeep@shopsphere.com",
    "password": "Password@123"
}
```

The login response should contain:

```json
{
    "token": "eyJ..."
}
```

**Stop here once registration and login work.**

Next we'll do **Step 10 — `[Authorize]`, JWT Bearer token in Postman, Claims, and a protected customer endpoint**. This is the practical part where you'll see exactly how JWT authentication works end-to-end.
