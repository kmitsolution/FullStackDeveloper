Now that you've successfully connected Angular to ASP.NET Core and displayed a simple text response, it's time to move to the next step.

In real-world applications, APIs rarely return plain text—they return **JSON**.

From this lesson onward, we'll start building a real **Employee Management System**.

---

# Lesson 12: Receiving JSON Data from ASP.NET Core

## Learning Objectives

By the end of this lesson, you will understand:

* What is JSON?
* Why Web APIs return JSON
* Returning an object from ASP.NET Core
* Creating a TypeScript Interface
* Receiving JSON in Angular
* Displaying Object Properties
* Understanding Type Safety

---

# What is JSON?

JSON stands for

> **JavaScript Object Notation**

It is the standard format used to exchange data between applications.

For example

```json
{
    "id": 101,
    "name": "Raman",
    "salary": 50000
}
```

Instead of returning

```text
Welcome Guest
```

our API will return an Employee object.

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

JSON Object

↓

Angular Component

↓

Browser
```

---

# Step 1 - ASP.NET Core API

Update Program.cs

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

app.MapGet("/", () =>
{
    return new
    {
        Id = 101,
        Name = "Raman",
        Salary = 50000
    };
});

app.Run();
```

---

# Browser Output

Open

```
http://localhost:5010/
```

Output

```json
{
    "id":101,
    "name":"Raman",
    "salary":50000
}
```

Notice

This is JSON.

---

# Step 2 - Create Interface

Create

```
src/app/models/employee.ts
```

```typescript
export interface Employee {

    id:number;

    name:string;

    salary:number;

}
```

---

# Why Interface?

Without Interface

```typescript
employee:any;
```

Angular doesn't know

* id
* name
* salary

With Interface

```typescript
employee:Employee;
```

Angular knows every property.

You also get IntelliSense and compile-time type checking.

---

# Step 3 - Service

employee.ts (Service)

```typescript
import { Service, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Employee } from './models/employee';

@Service()
export class EmployeeService {

    private http = inject(HttpClient);

    private apiUrl="http://localhost:5010/";

    getEmployee(){

        return this.http.get<Employee>(this.apiUrl);

    }

}
```

Notice

No

```typescript
responseType:'text'
```

because now

the API returns JSON.

---

# Step 4 - Component

employee/employee.ts

```typescript
import { Component } from '@angular/core';
import { EmployeeService } from '../employee';
import { Employee } from '../models/employee';

@Component({
  selector:'app-employee',
  imports:[],
  templateUrl:'./employee.html',
  styleUrl:'./employee.css'
})
export class EmployeeComponent {

    employee!:Employee;

    constructor(private employeeService:EmployeeService){

    }

    ngOnInit(){

        this.employeeService
            .getEmployee()
            .subscribe({

                next:(data)=>{

                    console.log(data);

                    this.employee=data;

                },

                error:(err)=>{

                    console.log(err);

                }

            });

    }

}
```

Notice

```typescript
employee!:Employee;
```

The `!` tells TypeScript:

> "Angular will assign a value before I use it."

---

# Step 5 - HTML

employee.html

```html
<h2>Employee Details</h2>

<p>Id : {{employee.id}}</p>

<p>Name : {{employee.name}}</p>

<p>Salary : {{employee.salary}}</p>
```

---

# Browser Output

```
Employee Details

Id : 101

Name : Raman

Salary : 50000
```

---

# Complete Flow

```text
Angular Starts

↓

Employee Component

↓

EmployeeService.getEmployee()

↓

HttpClient

↓

GET http://localhost:5010/

↓

ASP.NET Core

↓

JSON

↓

Observable

↓

subscribe()

↓

employee

↓

HTML

↓

Browser
```

---

# Why Use Interfaces?

Without Interface

```typescript
employee:any;
```

No IntelliSense.

No compile-time checking.

---

With Interface

```typescript
employee:Employee;
```

Visual Studio Code suggests

```
employee.id

employee.name

employee.salary
```

This helps prevent mistakes.

---

# What Happens Internally?

ASP.NET Core returns

```json
{
    "id":101,
    "name":"Raman",
    "salary":50000
}
```

Angular automatically converts it into

```typescript
Employee
```

because of

```typescript
this.http.get<Employee>()
```

No manual conversion is needed.

---

# Best Practices

✅ Always return JSON from Web APIs.

✅ Create a TypeScript interface for every API response.

✅ Avoid using `any` unless absolutely necessary.

✅ Keep API calls inside Services.

---

# Summary

| Concept                | Description                   |
| ---------------------- | ----------------------------- |
| JSON                   | Standard data exchange format |
| Interface              | Defines the structure of data |
| `http.get<Employee>()` | Returns an Employee object    |
| `employee!`            | Definite assignment assertion |
| Interpolation          | Displays object properties    |

---

# Practice Exercises

### Exercise 1

Modify the API to return

```json
{
    "id":102,
    "name":"John",
    "salary":65000
}
```

Verify Angular displays the new values.

---

### Exercise 2

Add another property

```json
Department : "IT"
```

Update the interface and display it.

---

### Exercise 3

Add

```json
City : "Bangalore"
```

Display it in Angular.

---

# Interview Questions

### 1. What is JSON?

A lightweight data format used to exchange data between applications.

---

### 2. Why do we use Interfaces?

To define the structure of data and enable type checking.

---

### 3. What is the purpose of `http.get<Employee>()`?

It tells Angular to deserialize the JSON response into an `Employee` object.

---

### 4. Why don't we use `responseType: 'text'` anymore?

Because the API now returns JSON instead of plain text.

---

## Next Lesson (Lesson 13)

We'll move from **one employee** to **multiple employees**.

ASP.NET Core will return:

```json
[
    {
        "id":101,
        "name":"Raman",
        "salary":50000
    },
    {
        "id":102,
        "name":"John",
        "salary":60000
    },
    {
        "id":103,
        "name":"David",
        "salary":70000
    }
]
```

You'll learn:

* Returning a JSON array
* `Employee[]`
* Displaying data using `@for`
* Creating a professional employee table
* The foundation for full CRUD operations

This is the point where your Angular application starts looking like a real enterprise application.
