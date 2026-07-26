Let's build a **simple authentication and authorization example** without using JWT or ASP.NET Identity. This will help you understand **how the middleware pipeline works** before learning real-world authentication.

---

# Step 1: Create a New ASP.NET Core Project

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddAuthentication("MyAuth")
    .AddScheme<AuthenticationSchemeOptions, MyAuthenticationHandler>(
        "MyAuth", null);

builder.Services.AddAuthorization();

var app = builder.Build();

app.UseAuthentication();

app.UseAuthorization();

app.MapGet("/", () => "Welcome Guest");

app.MapGet("/profile", (HttpContext context) =>
{
    return $"Welcome {context.User.Identity?.Name}";
}).RequireAuthorization();

app.Run();
```

Notice the order:

```text
UseAuthentication()

↓

UseAuthorization()

↓

MapGet()
```

---

# Step 2: Create Authentication Handler

Create a file named **MyAuthenticationHandler.cs**

```csharp
using System.Security.Claims;
using System.Text.Encodings.Web;
using Microsoft.AspNetCore.Authentication;
using Microsoft.Extensions.Options;

public class MyAuthenticationHandler :
AuthenticationHandler<AuthenticationSchemeOptions>
{
    public MyAuthenticationHandler(
        IOptionsMonitor<AuthenticationSchemeOptions> options,
        ILoggerFactory logger,
        UrlEncoder encoder)
        : base(options, logger, encoder)
    {
    }

    protected override Task<AuthenticateResult>
        HandleAuthenticateAsync()
    {
        if (!Request.Headers.ContainsKey("Username"))
        {
            return Task.FromResult(
                AuthenticateResult.Fail("Username Missing"));
        }

        string username = Request.Headers["Username"]!;

        var claims = new[]
        {
            new Claim(ClaimTypes.Name, username)
        };

        var identity =
            new ClaimsIdentity(claims, Scheme.Name);

        var principal =
            new ClaimsPrincipal(identity);

        var ticket =
            new AuthenticationTicket(principal,
                                     Scheme.Name);

        return Task.FromResult(
            AuthenticateResult.Success(ticket));
    }
}
```

Don't worry about every line just yet. The key part is that this handler:

* Reads the `Username` header.
* If present, it creates a user (`ClaimsPrincipal`).
* If not present, authentication fails.

---

# What happens now?

Suppose the client sends:

```http
GET /profile
```

without any headers.

Pipeline:

```text
Request

↓

Authentication Middleware

↓

Username header?

↓

No

↓

Authentication Failed

↓

Authorization Middleware

↓

User authenticated?

↓

No

↓

401 Unauthorized
```

The request never reaches `MapGet("/profile")`.

---

Now send:

```http
GET /profile

Username: Raman
```

Pipeline:

```text
Request

↓

Authentication Middleware

↓

Username Found

↓

Create User

↓

HttpContext.User

↓

Authorization Middleware

↓

User Authenticated

↓

MapGet("/profile")

↓

Welcome Raman
```

Output:

```text
Welcome Raman
```

---

# What does `UseAuthentication()` do?

Every request executes:

```text
Incoming Request

↓

Read Header

↓

Create User

↓

Store in

HttpContext.User
```

---

# What does `UseAuthorization()` do?

It checks whether the endpoint requires authorization.

```text
MapGet("/profile")
        │
RequireAuthorization()
```

If yes, it asks:

```text
Is HttpContext.User authenticated?
```

If:

```text
Yes
```

Continue.

If:

```text
No
```

Return

```text
401 Unauthorized
```

---

# Flow Diagram

```text
Client
   │
   │ GET /profile
   ▼
Authentication Middleware
   │
   │ Read Username Header
   ▼
Create ClaimsPrincipal
   │
   ▼
HttpContext.User
   │
   ▼
Authorization Middleware
   │
   │ RequireAuthorization() ?
   ▼
Yes
   │
User Authenticated?
   │
 ┌───────┴────────┐
 │                │
Yes              No
 │                │
 ▼                ▼
MapGet()      401 Unauthorized
```

---

# Adding Roles (Authorization Example)

Suppose you want only **Admins** to access `/admin`.

Update the claims:

```csharp
var claims = new[]
{
    new Claim(ClaimTypes.Name, username),
    new Claim(ClaimTypes.Role, "Admin")
};
```

Now create an endpoint:

```csharp
app.MapGet("/admin", (HttpContext context) =>
{
    return "Admin Dashboard";
})
.RequireAuthorization(policy =>
{
    policy.RequireRole("Admin");
});
```

If the authenticated user has:

```text
Role = Admin
```

Response:

```text
Admin Dashboard
```

Otherwise:

```text
403 Forbidden
```

---

# Authentication vs Authorization

| Authentication                 | Authorization                     |
| ------------------------------ | --------------------------------- |
| Who are you?                   | What are you allowed to do?       |
| Verifies identity              | Verifies permissions              |
| Creates `HttpContext.User`     | Checks `HttpContext.User`         |
| `UseAuthentication()`          | `UseAuthorization()`              |
| Example: Username, JWT, Cookie | Example: Admin role, Manager role |

---

# Real-World JWT Flow

In production, you won't usually write your own authentication handler. Instead, you'll configure JWT Bearer authentication:

```text
Client
   │
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
   ▼
JWT Authentication Middleware
   │
Validate Token
   │
Extract Claims
   ▼
HttpContext.User
   ▼
Authorization Middleware
   ▼
MapGet()
```

The authentication middleware validates the JWT, builds the `ClaimsPrincipal`, and stores it in `HttpContext.User`. The authorization middleware then decides whether the authenticated user has permission to access the endpoint.

---

This custom example is intentionally simple because it demonstrates the **responsibilities** of each middleware without the added complexity of JWT configuration. Once you're comfortable with this flow, moving to JWT Bearer authentication becomes much easier because the overall pipeline remains the same—the only difference is how the user's identity is established.
