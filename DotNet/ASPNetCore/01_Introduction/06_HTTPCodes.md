HTTP Status Codes are **3-digit numbers** returned by the web server to indicate the result of an HTTP request.

For example, when you request:

```text
https://localhost:5001/
```

The browser sends an HTTP request, and the server responds with a status code.

```text
Browser
   │
   │ GET /
   ▼
ASP.NET Core Server
   │
   │ HTTP Response
   ▼
Status Code: 200 OK
```

---

# Structure of HTTP Status Codes

The first digit determines the category:

| Range   | Category      | Meaning                                |
| ------- | ------------- | -------------------------------------- |
| **1xx** | Informational | Request received, processing continues |
| **2xx** | Success       | Request completed successfully         |
| **3xx** | Redirection   | Client needs to take additional action |
| **4xx** | Client Error  | Problem with the client's request      |
| **5xx** | Server Error  | Problem occurred on the server         |

---

# 1xx - Informational Responses

These codes indicate that the request has been received and processing is continuing.

| Code    | Meaning             | Description                                             |
| ------- | ------------------- | ------------------------------------------------------- |
| **100** | Continue            | Client can continue sending the request body            |
| **101** | Switching Protocols | Server is switching protocols (e.g., HTTP to WebSocket) |
| **102** | Processing          | Server is processing the request (less common)          |
| **103** | Early Hints         | Server provides preliminary response headers            |

Example:

```text
100 Continue
```

The server says:

> "I've received your request headers. Go ahead and send the request body."

---

# 2xx - Success Responses

The request was successful.

| Code    | Meaning         | Description                           |
| ------- | --------------- | ------------------------------------- |
| **200** | OK              | Request succeeded                     |
| **201** | Created         | A new resource was created            |
| **202** | Accepted        | Request accepted for processing       |
| **204** | No Content      | Success, but nothing is returned      |
| **206** | Partial Content | Only part of the resource is returned |

---

## 200 OK

Most common response.

```text
GET /products

↓

200 OK
```

Meaning:

```text
Everything worked successfully.
```

ASP.NET Core example:

```csharp
app.MapGet("/", () =>
{
    return "Welcome";
});
```

Response:

```text
200 OK
```

---

## 201 Created

Common in REST APIs.

```text
POST /students
```

Response:

```text
201 Created
```

Meaning:

A new student record has been created.

---

## 202 Accepted

Example:

```text
Upload Video
```

Server responds:

```text
202 Accepted
```

Meaning:

> "I've accepted the request and will process it later."

---

## 204 No Content

Useful when deleting data.

```text
DELETE /student/10
```

Response:

```text
204 No Content
```

Meaning:

The operation succeeded, but there is no response body.

---

# 3xx - Redirection

The client must perform another action.

| Code    | Meaning            | Description                              |
| ------- | ------------------ | ---------------------------------------- |
| **301** | Moved Permanently  | Resource has permanently moved           |
| **302** | Found              | Temporary redirect                       |
| **303** | See Other          | Redirect using GET                       |
| **304** | Not Modified       | Use cached version                       |
| **307** | Temporary Redirect | Method preserved during redirect         |
| **308** | Permanent Redirect | Permanent redirect with method preserved |

---

## 301 Moved Permanently

Example:

```text
http://abc.com
```

Redirects to:

```text
https://abc.com
```

Browser automatically follows the new URL.

---

## 302 Found

Temporary redirect.

Example:

```text
Login Page

↓

Dashboard
```

---

## 304 Not Modified

Browser already has the latest version cached.

```text
304 Not Modified
```

Meaning:

Use your cached copy.

---

# 4xx - Client Errors

The problem is with the request sent by the client.

| Code    | Meaning                | Description                          |
| ------- | ---------------------- | ------------------------------------ |
| **400** | Bad Request            | Invalid request                      |
| **401** | Unauthorized           | Authentication required              |
| **403** | Forbidden              | Access denied                        |
| **404** | Not Found              | Resource not found                   |
| **405** | Method Not Allowed     | HTTP method not supported            |
| **406** | Not Acceptable         | Requested format not available       |
| **408** | Request Timeout        | Client took too long                 |
| **409** | Conflict               | Request conflicts with current state |
| **410** | Gone                   | Resource permanently removed         |
| **415** | Unsupported Media Type | Invalid content type                 |
| **429** | Too Many Requests      | Rate limit exceeded                  |

---

## 400 Bad Request

Example:

```text
GET /student?id=abc
```

Expected:

```text
Integer
```

Received:

```text
abc
```

Response:

```text
400 Bad Request
```

---

## 401 Unauthorized

Example:

```text
GET /api/orders
```

Without a valid authentication token:

```text
401 Unauthorized
```

---

## 403 Forbidden

Authenticated user lacks permission.

Example:

```text
Student trying to access Admin page.
```

Response:

```text
403 Forbidden
```

---

## 404 Not Found

Very common.

Example:

```text
https://localhost:5001/test
```

No endpoint exists.

Response:

```text
404 Not Found
```

---

## 405 Method Not Allowed

Example:

Endpoint:

```text
GET /products
```

Client sends:

```text
POST /products
```

Response:

```text
405 Method Not Allowed
```

---

## 409 Conflict

Example:

Trying to create a user with an email address that already exists.

---

## 415 Unsupported Media Type

Example:

API expects:

```text
application/json
```

Client sends:

```text
text/plain
```

Response:

```text
415 Unsupported Media Type
```

---

## 429 Too Many Requests

Common in APIs with rate limiting.

Example:

```text
1000 requests per minute
```

Client exceeds the limit.

Response:

```text
429 Too Many Requests
```

---

# 5xx - Server Errors

These indicate the request was valid, but the server failed while processing it.

| Code    | Meaning                    | Description                           |
| ------- | -------------------------- | ------------------------------------- |
| **500** | Internal Server Error      | Unexpected server error               |
| **501** | Not Implemented            | Feature not implemented               |
| **502** | Bad Gateway                | Invalid response from upstream server |
| **503** | Service Unavailable        | Server temporarily unavailable        |
| **504** | Gateway Timeout            | Upstream server timed out             |
| **505** | HTTP Version Not Supported | Unsupported HTTP version              |

> **Note:** It's **500**, not **5000**. HTTP status codes are always three digits.

---

## 500 Internal Server Error

Most common server error.

Example:

```csharp
int x = 10;
int y = 0;

int result = x / y;
```

The application throws an exception.

Response:

```text
500 Internal Server Error
```

---

## 502 Bad Gateway

Example:

```text
Browser

↓

Nginx

↓

ASP.NET Core
```

If the ASP.NET Core application is down or returns an invalid response, Nginx may return:

```text
502 Bad Gateway
```

---

## 503 Service Unavailable

Example:

Server is:

* Under maintenance
* Overloaded
* Restarting

Response:

```text
503 Service Unavailable
```

---

## 504 Gateway Timeout

Example:

```text
Browser

↓

Nginx

↓

Backend API
```

If the backend API doesn't respond in time:

```text
504 Gateway Timeout
```

---

# HTTP Status Code Summary

| Code | Name                       | Category      |
| ---- | -------------------------- | ------------- |
| 100  | Continue                   | Informational |
| 101  | Switching Protocols        | Informational |
| 200  | OK                         | Success       |
| 201  | Created                    | Success       |
| 202  | Accepted                   | Success       |
| 204  | No Content                 | Success       |
| 301  | Moved Permanently          | Redirection   |
| 302  | Found                      | Redirection   |
| 304  | Not Modified               | Redirection   |
| 307  | Temporary Redirect         | Redirection   |
| 308  | Permanent Redirect         | Redirection   |
| 400  | Bad Request                | Client Error  |
| 401  | Unauthorized               | Client Error  |
| 403  | Forbidden                  | Client Error  |
| 404  | Not Found                  | Client Error  |
| 405  | Method Not Allowed         | Client Error  |
| 408  | Request Timeout            | Client Error  |
| 409  | Conflict                   | Client Error  |
| 415  | Unsupported Media Type     | Client Error  |
| 429  | Too Many Requests          | Client Error  |
| 500  | Internal Server Error      | Server Error  |
| 501  | Not Implemented            | Server Error  |
| 502  | Bad Gateway                | Server Error  |
| 503  | Service Unavailable        | Server Error  |
| 504  | Gateway Timeout            | Server Error  |
| 505  | HTTP Version Not Supported | Server Error  |

## Common ASP.NET Core Examples

| Scenario                                | Status Code                   |
| --------------------------------------- | ----------------------------- |
| Home page loads successfully            | **200 OK**                    |
| New user created via POST               | **201 Created**               |
| Record deleted successfully             | **204 No Content**            |
| Invalid input or model validation fails | **400 Bad Request**           |
| User not logged in                      | **401 Unauthorized**          |
| User logged in but lacks permission     | **403 Forbidden**             |
| Endpoint does not exist                 | **404 Not Found**             |
| Wrong HTTP method used                  | **405 Method Not Allowed**    |
| Too many API requests                   | **429 Too Many Requests**     |
| Unhandled exception in application      | **500 Internal Server Error** |
| Backend service unavailable             | **503 Service Unavailable**   |

### Interview Tip

A simple way to remember the categories is:

* **1xx** – "Please wait, I'm processing."
* **2xx** – "Everything worked."
* **3xx** – "Go somewhere else."
* **4xx** – "The client made a mistake."
* **5xx** – "The server encountered a problem."
