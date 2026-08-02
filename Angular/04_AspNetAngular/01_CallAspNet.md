
# Lesson 11: Calling a Simple ASP.NET Core API from Angular

## Objective

In this lesson, we will learn how Angular communicates with an ASP.NET Core API.

Instead of returning JSON, we'll first return a simple text message.

ASP.NET Core

```text
Welcome Guest
```

Angular will display

```text
Welcome Guest
```

This is the simplest way to understand Angular + ASP.NET Core communication.

---

# Architecture

```text
Angular Browser

↓

Employee Component

↓

Employee Service

↓

HttpClient

↓

ASP.NET Core API

↓

"Welcome Guest"

↓

Angular Component

↓

Browser
```

---

# Step 1 - Create ASP.NET Core API

Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddCors(options =>
{
    options.AddPolicy("AngularPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:4200")
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});

var app = builder.Build();

app.UseCors("AngularPolicy");

app.MapGet("/", () => "Welcome Guest");

app.Run();
```

---

# Why CORS is Required

Angular runs on

```text
http://localhost:4200
```

ASP.NET Core runs on

```text
http://localhost:5010
```

Since both applications are running on different ports,

the browser considers them

**Different Origins**

Therefore,

ASP.NET Core must explicitly allow Angular.

Without CORS

Angular shows

```text
Access to fetch has been blocked by CORS policy.
```

Postman works because

Postman ignores browser CORS rules.

---

# Step 2 - Register HttpClient

Open

```text
app.config.ts
```

```typescript
import { provideHttpClient } from '@angular/common/http';

export const appConfig: ApplicationConfig = {

  providers: [

      provideHttpClient()

  ]

};
```

Without

```typescript
provideHttpClient()
```

Angular cannot call Web APIs.

---

# Step 3 - Create Employee Service

employee.ts

```typescript
import { Service, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Service()
export class EmployeeService {

    private http = inject(HttpClient);

    private apiUrl = "http://localhost:5010/";

    getEmployees() {

        return this.http.get(
            this.apiUrl,
            {
                responseType: 'text'
            });

    }

    getCompanyName() {

        return "KMIT Solutions";

    }

}
```

---

# Why responseType:'text' ?

Normally

```typescript
this.http.get(...)
```

expects

JSON.

Our API returns

```text
Welcome Guest
```

which is

Plain Text.

Therefore

```typescript
responseType:'text'
```

is mandatory.

---

# Step 4 - Call Service from Component

employee/employee.ts

```typescript
import { Component } from '@angular/core';
import { EmployeeService } from '../employee';

@Component({
    selector: 'app-employee',
    imports: [],
    templateUrl: './employee.html',
    styleUrl: './employee.css'
})
export class Employee {

    message = "";

    constructor(private employeeService: EmployeeService) {

    }

    ngOnInit() {

        console.log("Employee component initialized");

        this.employeeService
            .getEmployees()
            .subscribe({

                next: (data) => {

                    console.log(data);

                    this.message = data;

                },

                error: (error) => {

                    console.log(error);

                }

            });

    }

}
```

---

# Step 5 - Display Data

employee.html

```html
<h2>{{ message }}</h2>
```

Browser Output

```text
Welcome Guest
```

---

# Complete Flow

```text
Angular Starts

↓

Employee Component Created

↓

ngOnInit()

↓

EmployeeService.getEmployees()

↓

HttpClient

↓

GET http://localhost:5010/

↓

ASP.NET Core

↓

Returns

"Welcome Guest"

↓

subscribe()

↓

message = "Welcome Guest"

↓

Angular Updates HTML

↓

Browser
```

---

# Understanding subscribe()

This is one of the most important concepts.

Service

```typescript
return this.http.get(...)
```

Only creates

the HTTP request.

Nothing happens yet.

When Component executes

```typescript
.subscribe(...)
```

Angular actually sends

the request.

Flow

```text
Observable

↓

subscribe()

↓

Request Sent

↓

Response Received

↓

next()

↓

UI Updated
```

---

# Why Didn't It Work Initially?

Initially

Angular displayed

```text
Failed to Fetch
```

Reason

No CORS.

Browser Error

```text
Access to fetch has been blocked by CORS policy

No Access-Control-Allow-Origin header
```

Solution

```csharp
builder.Services.AddCors(...);

app.UseCors(...);
```

---

# Why Postman Worked

Postman

```text
Postman

↓

ASP.NET Core
```

No browser involved.

No CORS.

Angular

```text
Angular

↓

Browser

↓

CORS Check

↓

ASP.NET Core
```

Browser blocks the request

if CORS isn't configured.

---

# Debugging Technique

During development

always use

```typescript
console.log(data);
```

Example

```typescript
.subscribe({

    next:(data)=>{

        console.log(data);

    }

});
```

If

```text
Welcome Guest
```

appears in the console

then

✔ API works

✔ HttpClient works

✔ Service works

If it doesn't appear on the webpage, the next place to inspect is the HTML template.

---

# Summary

| Angular             | Purpose                               |
| ------------------- | ------------------------------------- |
| HttpClient          | Calls Web API                         |
| Service             | Contains API code                     |
| Component           | Uses Service                          |
| subscribe()         | Executes HTTP Request                 |
| responseType:'text' | Receive plain text                    |
| provideHttpClient() | Registers HttpClient                  |
| CORS                | Allows Angular to access ASP.NET Core |

---

# Interview Questions

### 1. Why do we use HttpClient?

To communicate with Web APIs.

---

### 2. Why is provideHttpClient() required?

It registers HttpClient with Angular's Dependency Injection container.

---

### 3. Why is responseType:'text' needed?

Because the API returns plain text instead of JSON.

---

### 4. Why is subscribe() required?

An HTTP request is represented as an Observable. The request is sent only when the Observable is subscribed to.

---

### 5. Why does Postman work while Angular shows a CORS error?

Postman does not enforce CORS. Browsers do.

---

### 6. Why do we keep HttpClient inside a Service?

To separate business logic from the UI and make the code reusable and maintainable.

---

## Next Lesson

Now that you know how Angular communicates with ASP.NET Core using a simple text response, the next lesson will return **JSON** instead of plain text.

We'll learn:

* Returning an Employee object
* Returning a list of employees
* Creating a TypeScript interface (model)
* Displaying API data using `@for`

This is the foundation for building a complete Angular + ASP.NET Core CRUD application.
