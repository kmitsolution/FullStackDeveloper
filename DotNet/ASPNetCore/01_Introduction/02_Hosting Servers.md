When you create an **ASP.NET Core** application, your code does **not** communicate directly with the operating system or the browser. It runs inside a **web server** that receives HTTP requests and forwards them to your application.

Think of it like this:

```text
Browser
   │
HTTP Request
   │
Web Server
   │
ASP.NET Core Application
   │
Business Logic
   │
Database
```

A **hosting server** is responsible for:

* Listening for HTTP/HTTPS requests
* Managing network connections
* Passing requests to the ASP.NET Core application
* Sending responses back to clients

---

# Kestrel Server

Kestrel is the **default, cross-platform web server** for ASP.NET Core.

It is:

* Lightweight
* High performance
* Cross-platform
* Included with the .NET runtime

Architecture:

```text
Browser
    │
HTTP Request
    │
Kestrel
    │
ASP.NET Core Middleware
    │
Controllers / APIs
    │
Database
```

### Example

Run an application:

```bash
dotnet run
```

Output:

```text
Now listening on:

http://localhost:5000
https://localhost:5001
```

Here, **Kestrel** is listening on ports **5000** and **5001**.

---

# Why was Kestrel introduced?

The older .NET Framework depended mainly on **IIS**, which only runs on Windows.

With ASP.NET Core, Microsoft wanted applications to run on:

* Windows
* Linux
* macOS
* Docker
* Kubernetes

So they developed Kestrel as a built-in, cross-platform web server.

---

# Is Kestrel enough?

Yes, in many scenarios.

For example:

* Docker containers
* Kubernetes
* Azure App Service
* Linux servers
* Internal APIs
* Microservices

Kestrel alone is commonly used.

---

# Why use another web server in front of Kestrel?

In production, organizations often place a **reverse proxy** in front of Kestrel.

Benefits include:

* SSL/TLS termination
* Load balancing
* Compression
* Static file serving
* Request filtering
* Security
* Authentication integration

Architecture:

```text
Internet
    │
Reverse Proxy
(IIS/Nginx/Apache)
    │
Kestrel
    │
ASP.NET Core
```

---

# 1. IIS (Internet Information Services)

Internet Information Services is Microsoft's web server for Windows.

It provides:

* Windows Authentication
* SSL certificate management
* Logging
* Process management
* Application pools
* Reverse proxy support for Kestrel

Architecture:

```text
Browser
    │
IIS
    │
ASP.NET Core Module
    │
Kestrel
    │
Application
```

### Typical use case

* Windows Server
* Enterprise applications
* Active Directory integration

---

# 2. Nginx

Nginx is a popular web server and reverse proxy, especially on Linux.

Architecture:

```text
Browser
    │
Nginx
    │
Kestrel
    │
Application
```

Advantages:

* Very fast
* Low memory usage
* Reverse proxy
* Load balancing
* SSL termination

Common in:

* Ubuntu
* Red Hat
* Docker
* Kubernetes

---

# 3. Apache HTTP Server

Apache HTTP Server is another widely used web server.

Architecture:

```text
Browser
    │
Apache
    │
Kestrel
    │
Application
```

Used in organizations already running Apache infrastructure.

---

# 4. HTTP.sys

HTTP.sys is a Windows-only HTTP server built into the Windows kernel.

Unlike IIS, your ASP.NET Core application can host directly on HTTP.sys without Kestrel.

Architecture:

```text
Browser
    │
HTTP.sys
    │
ASP.NET Core
```

Features:

* Windows Authentication
* Kernel-mode request queue
* Port sharing
* Windows-only

---

# 5. Azure App Service

When deploying to Azure App Service:

```text
Internet
    │
Azure Front End
    │
Kestrel
    │
Application
```

Microsoft manages the front-end infrastructure, while your application runs on Kestrel behind it.

---

# 6. Docker

In Docker:

```text
Browser
    │
Docker Port
    │
Kestrel
    │
Application
```

Example:

```bash
docker run -p 8080:8080 myapp
```

Kestrel listens inside the container, and Docker maps the container port to the host.

---

# 7. Kubernetes

In Kubernetes:

```text
Internet
    │
Ingress Controller
    │
Service
    │
Pod
    │
Kestrel
    │
Application
```

Common ingress controllers include NGINX Ingress or Traefik, while Kestrel runs inside each application pod.

---

# Request Processing Flow

```text
Client
   │
HTTP Request
   │
Kestrel
   │
Middleware
   │
Routing
   │
Controller
   │
Business Logic
   │
Database
   │
Response
   │
Kestrel
   │
Client
```

---

# Kestrel Features

* Cross-platform
* High performance
* HTTP/1.1 support
* HTTP/2 support
* HTTP/3 support
* HTTPS support
* WebSockets
* gRPC support
* Asynchronous request processing

---

# Comparison of Hosting Servers

| Feature                  | Kestrel                                    | IIS                      | Nginx     | Apache    | HTTP.sys   |
| ------------------------ | ------------------------------------------ | ------------------------ | --------- | --------- | ---------- |
| Cross-platform           | ✅                                          | ❌                        | ✅         | ✅         | ❌          |
| Built into ASP.NET Core  | ✅                                          | ❌                        | ❌         | ❌         | ❌          |
| Reverse proxy            | Usually behind one, but can serve directly | Can act as reverse proxy | Yes       | Yes       | Not needed |
| Windows support          | ✅                                          | ✅                        | ✅         | ✅         | ✅          |
| Linux support            | ✅                                          | ❌                        | ✅         | ✅         | ❌          |
| High performance         | ✅                                          | ✅                        | ✅         | ✅         | ✅          |
| Load balancing           | ❌                                          | Basic                    | ✅         | ✅         | ❌          |
| SSL termination          | Basic                                      | ✅                        | ✅         | ✅         | ✅          |
| Static file optimization | Good                                       | Excellent                | Excellent | Excellent | Good       |

---

# Which server should you use?

| Environment            | Recommended Hosting                  |
| ---------------------- | ------------------------------------ |
| Local Development      | Kestrel                              |
| Windows Server         | IIS + Kestrel                        |
| Linux Server           | Nginx + Kestrel                      |
| Docker                 | Kestrel                              |
| Kubernetes             | Kestrel behind an Ingress Controller |
| Azure App Service      | Kestrel (managed by Azure)           |
| Internal Microservices | Kestrel directly                     |

### Summary

* **Kestrel** is the default web server for ASP.NET Core and is suitable for most scenarios.
* **IIS** is commonly used on Windows servers, usually as a reverse proxy in front of Kestrel.
* **Nginx** is the preferred reverse proxy on Linux deployments.
* **Apache** is an alternative reverse proxy for organizations already using Apache.
* **HTTP.sys** is a Windows-only server that can host ASP.NET Core applications directly without Kestrel.

For modern cloud-native applications, the most common deployment patterns are:

* **Windows:** IIS → Kestrel → ASP.NET Core App
* **Linux:** Nginx → Kestrel → ASP.NET Core App
* **Containers/Kubernetes:** Ingress Controller → Kestrel → ASP.NET Core App
