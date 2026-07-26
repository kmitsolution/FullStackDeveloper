. **Basic Authentication** is actually the easiest way to understand authentication because there is **no JWT, no Claims yet**. It simply sends a **Base64 encoded username and password** in the `Authorization` header.

> **Important:** Base64 is **not encryption**. It is only an encoding. Anyone can decode it easily. Therefore, Basic Authentication should only be used over **HTTPS**.

Let's build a complete example.

---

# Step 1: Client Sends Request

Suppose the username is:

```text
raman
```

Password:

```text
password123
```

The client combines them:

```text
raman:password123
```

Then Base64 encodes it.

```
cmFtYW46cGFzc3dvcmQxMjM=
```

Finally, the HTTP request becomes:

```http
GET /profile HTTP/1.1
Host: localhost:5220

Authorization: Basic cmFtYW46cGFzc3dvcmQxMjM=
```

Notice that **Basic** is followed by the Base64 string.

---

# Step 2: ASP.NET Core Receives the Request

The authentication middleware reads the header.

```
Authorization

↓

Basic cmFtYW46cGFzc3dvcmQxMjM=
```

It removes **Basic**

```
cmFtYW46cGFzc3dvcmQxMjM=
```

Then decodes it.

```
raman:password123
```

It splits the string at `:`.

```
Username = raman

Password = password123
```

Now it checks whether these credentials are valid.

---

# Step 3: Create a Custom Basic Authentication Handler

Register authentication:

```csharp
builder.Services.AddAuthentication("Basic")
    .AddScheme<AuthenticationSchemeOptions,
               BasicAuthenticationHandler>(
               "Basic", null);

builder.Services.AddAuthorization();
```

Then:

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

---

# Step 4: Authentication Handler

```csharp
using Microsoft.AspNetCore.Authentication;
using Microsoft.Extensions.Options;
using System.Security.Claims;
using System.Text;
using System.Text.Encodings.Web;

public class BasicAuthenticationHandler
    : AuthenticationHandler<AuthenticationSchemeOptions>
{
    public BasicAuthenticationHandler(
        IOptionsMonitor<AuthenticationSchemeOptions> options,
        ILoggerFactory logger,
        UrlEncoder encoder)
        : base(options, logger, encoder)
    {
    }

    protected override Task<AuthenticateResult>
        HandleAuthenticateAsync()
    {
        if (!Request.Headers.ContainsKey("Authorization"))
        {
            return Task.FromResult(
                AuthenticateResult.Fail("Header Missing"));
        }

        var header = Request.Headers["Authorization"].ToString();

        if (!header.StartsWith("Basic "))
        {
            return Task.FromResult(
                AuthenticateResult.Fail("Invalid Scheme"));
        }

        var encoded =
            header.Substring("Basic ".Length);

        var decoded =
            Encoding.UTF8.GetString(
                Convert.FromBase64String(encoded));

        var values = decoded.Split(':');

        string username = values[0];
        string password = values[1];

        // Validate credentials
        if (username != "raman" ||
            password != "password123")
        {
            return Task.FromResult(
                AuthenticateResult.Fail(
                    "Invalid Credentials"));
        }

        var claims = new[]
        {
            new Claim(ClaimTypes.Name, username),
            new Claim(ClaimTypes.Role, "Admin")
        };

        var identity =
            new ClaimsIdentity(claims, Scheme.Name);

        var principal =
            new ClaimsPrincipal(identity);

        var ticket =
            new AuthenticationTicket(
                principal,
                Scheme.Name);

        return Task.FromResult(
            AuthenticateResult.Success(ticket));
    }
}
```

---

# Step 5: Protected Endpoint

```csharp
app.MapGet("/profile",
(HttpContext context) =>
{
    return $"Welcome {context.User.Identity?.Name}";
})
.RequireAuthorization();
```

---

# Step 6: Request Without Header

```
GET /profile
```

Pipeline

```
Authentication

↓

Authorization header exists?

↓

No

↓

Authentication Failed

↓

401 Unauthorized
```

The endpoint is never executed.

---

# Step 7: Wrong Password

Request

```http
Authorization:
Basic cmFtYW46MTIzNDU=
```

Decoded

```
raman:12345
```

Authentication

```
Username OK

Password Wrong

↓

401 Unauthorized
```

---

# Step 8: Correct Password

Request

```http
Authorization:
Basic cmFtYW46cGFzc3dvcmQxMjM=
```

Decoded

```
raman:password123
```

Authentication

```
Credentials Valid

↓

Claims Created

↓

HttpContext.User

↓

Authorization

↓

MapGet()

↓

Welcome raman
```

Response

```
Welcome raman
```

---

# Where Do Claims Come In?

This is the most important point.

Authentication only proves:

```
Yes

This user is Raman.
```

After authentication succeeds, ASP.NET Core creates claims.

```csharp
new Claim(ClaimTypes.Name, username)

new Claim(ClaimTypes.Role, "Admin")
```

These claims are stored inside:

```csharp
HttpContext.User
```

Now your endpoint can access them.

```csharp
var name =
    context.User.Identity?.Name;
```

returns

```
raman
```

---

# Complete Flow

```
Client
   │
   │ Authorization:
   │ Basic cmFtYW46cGFzc3dvcmQxMjM=
   ▼
Authentication Middleware
   │
Read Header
   │
Base64 Decode
   │
raman:password123
   │
Validate Credentials
   │
Create ClaimsPrincipal
   │
Store in HttpContext.User
   ▼
Authorization Middleware
   │
Authenticated?
   ▼
Yes
   │
MapGet("/profile")
   │
Read Claims
   ▼
Welcome raman
```

## Why is Base64 used?

HTTP headers are text-based. Usernames and passwords can contain characters that are inconvenient or unsafe to send directly in headers. Base64 converts the `username:password` string into a transport-friendly ASCII string.

For example:

```
raman:password123
```

becomes

```
cmFtYW46cGFzc3dvcmQxMjM=
```

The server simply decodes it back. Since anyone can decode Base64, **Basic Authentication must always be used with HTTPS**, which encrypts the entire HTTP request (including the headers) while it travels over the network.

Once you're comfortable with this Basic Authentication flow, you'll find JWT authentication much easier to understand because the overall middleware pipeline is the same—the difference is that instead of sending `username:password` on every request, the client sends a signed JWT that the server validates before creating the `ClaimsPrincipal`.
