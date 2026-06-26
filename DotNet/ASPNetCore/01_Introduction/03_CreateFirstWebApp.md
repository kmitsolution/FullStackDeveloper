# Creating an ASP.NET Core Empty Project

There are two common ways to create an ASP.NET Core Empty project:

1. Using the **.NET CLI (Command Line)**
2. Using **Visual Studio**

---

# Method 1: Create Project using .NET CLI

## Step 1: Verify .NET SDK Installation

Open Command Prompt or Terminal.

```bash
dotnet --version
```

Example output:

```text
9.0.100
```

If a version is displayed, the SDK is installed.

---

## Step 2: View Available Templates

```bash
dotnet new list
```

You will see templates such as:

```text
web
webapi
mvc
razor
console
classlib
```

---

## Step 3: Create an Empty ASP.NET Core Project

```bash
dotnet new web -n FirstWebApp
```

Explanation:

* `dotnet new` → Create a new project
* `web` → ASP.NET Core Empty template
* `-n` → Project name
* `FirstWebApp` → Project name

Output:

```text
The template "ASP.NET Core Empty" was created successfully.
```

---

## Step 4: Navigate to the Project

```bash
cd FirstWebApp
```

---

## Step 5: Project Structure

```text
FirstWebApp
│
├── Program.cs
├── appsettings.json
├── appsettings.Development.json
├── FirstWebApp.csproj
├── Properties
│      └── launchSettings.json
│
├── obj
└── bin
```

---

## Step 6: Run the Application

```bash
dotnet run
```

Output:

```text
Building...

Now listening on:

https://localhost:7243

http://localhost:5234
```

Open:

```
https://localhost:7243
```

Browser displays:

```text
Hello World!
```

---

# Method 2: Create Project using Visual Studio

### Step 1

Open **Visual Studio 2022**.

---

### Step 2

Select:

```
Create a new project
```

---

### Step 3

Search for:

```
ASP.NET Core Empty
```

Choose:

```
ASP.NET Core Empty
```

Click **Next**.

---

### Step 4

Provide:

```
Project Name:
FirstWebApp

Location:
C:\Projects

Solution Name:
FirstWebApp
```

Click **Next**.

---

### Step 5

Choose:

```
.NET Version:
.NET 8 / .NET 9

Authentication:
None

Configure for HTTPS:
✔ Checked

Enable OpenAPI:
Unchecked

Use Controllers:
Unchecked
```

Click **Create**.

Visual Studio creates the project.

---

# Default Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", () => "Hello World!");

app.Run();
```

---

# Understanding the Code

## Create Builder

```csharp
var builder = WebApplication.CreateBuilder(args);
```

This creates:

* Dependency Injection container
* Configuration
* Logging
* Hosting environment
* Web server (Kestrel)

---

## Build Application

```csharp
var app = builder.Build();
```

Creates the application pipeline.

---

## Define Endpoint

```csharp
app.MapGet("/", () => "Hello World!");
```

Meaning:

```
When user visits "/"

↓

Return

Hello World!
```

---

## Start Web Server

```csharp
app.Run();
```

Starts Kestrel and begins listening for incoming HTTP requests.

---

# What is `launchSettings.json`?

It is a **development-only configuration file** used by Visual Studio and the `dotnet run` command to define how the application should be launched during development.

Location:

```text
Properties
    launchSettings.json
```

It is **not used in production**.

---

# Typical `launchSettings.json`

```json
{
  "$schema": "https://json.schemastore.org/launchsettings.json",

  "profiles": {

    "http": {
      "commandName": "Project",
      "launchBrowser": true,
      "applicationUrl": "http://localhost:5234",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },

    "https": {
      "commandName": "Project",
      "launchBrowser": true,
      "applicationUrl": "https://localhost:7243;http://localhost:5234",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

---

# Understanding Each Property

## `$schema`

```json
"$schema": "https://json.schemastore.org/launchsettings.json"
```

Provides IntelliSense and validation support in Visual Studio.

---

## `profiles`

A project can have multiple launch profiles.

Example:

```text
http

https

Docker

IIS Express
```

You can choose which profile to use when running the application.

---

## `commandName`

```json
"commandName": "Project"
```

Tells Visual Studio to launch the project using the built-in Kestrel server.

Other possible values include:

* `Project` → Run with Kestrel
* `IISExpress` → Run using IIS Express (Visual Studio only)
* `Executable` → Launch an external executable

---

## `launchBrowser`

```json
"launchBrowser": true
```

If `true`, your default browser opens automatically after the application starts.

If `false`, the application runs without opening a browser.

---

## `applicationUrl`

```json
"applicationUrl": "https://localhost:7243;http://localhost:5234"
```

Defines the URLs and ports Kestrel listens on during development.

This example enables both:

* HTTPS on port `7243`
* HTTP on port `5234`

You can change the ports if needed.

---

## `environmentVariables`

```json
"environmentVariables": {
    "ASPNETCORE_ENVIRONMENT": "Development"
}
```

Sets environment variables for the selected launch profile.

The most important one is:

```text
ASPNETCORE_ENVIRONMENT
```

Common values:

* `Development`
* `Staging`
* `Production`

---

# How `ASPNETCORE_ENVIRONMENT` Works

Suppose you have:

```text
appsettings.json
```

and

```text
appsettings.Development.json
```

If:

```text
ASPNETCORE_ENVIRONMENT = Development
```

ASP.NET Core loads:

1. `appsettings.json`
2. `appsettings.Development.json` (overrides matching values)

If:

```text
ASPNETCORE_ENVIRONMENT = Production
```

It loads:

1. `appsettings.json`
2. `appsettings.Production.json` (if present)

This allows you to have different settings (connection strings, logging, API keys, etc.) for each environment.

---

# Request Flow During Development

```text
Visual Studio / dotnet run
          │
          ▼
Reads launchSettings.json
          │
          ▼
Sets Environment Variables
          │
          ▼
Starts Kestrel
          │
          ▼
Listens on Configured URLs
          │
          ▼
Opens Browser (if launchBrowser = true)
          │
          ▼
Processes Requests
```

---

# Summary

| Component                | Purpose                                                                                                                |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------- |
| `dotnet new web`         | Creates an ASP.NET Core Empty project                                                                                  |
| `Program.cs`             | Entry point that configures and starts the application                                                                 |
| `launchSettings.json`    | Development-only launch configuration                                                                                  |
| `profiles`               | Defines one or more ways to start the application                                                                      |
| `commandName`            | Specifies how the app is launched (e.g., Kestrel or IIS Express)                                                       |
| `applicationUrl`         | Specifies the HTTP/HTTPS URLs and ports used during development                                                        |
| `launchBrowser`          | Controls whether a browser opens automatically                                                                         |
| `ASPNETCORE_ENVIRONMENT` | Selects the current environment (Development, Staging, Production) and influences which configuration files are loaded |

> **Note:** `launchSettings.json` is intended only for local development. When you publish and deploy an ASP.NET Core application (to IIS, Linux, Docker, Kubernetes, Azure, etc.), the hosting environment provides the URLs and environment variables instead of this file.
