Excellent! 🎉

Now you've learned:

* ✅ Angular calling ASP.NET Core
* ✅ Receiving a JSON object
* ✅ Displaying object properties

Now we'll move to the most common scenario in web applications.

> **Displaying a list of data from ASP.NET Core**

Almost every enterprise application has pages like:

* Employee List
* Student List
* Product List
* Customer List

All of these return an **array of JSON objects**.

---

# Lesson 13: Displaying a List of Employees from ASP.NET Core

## Learning Objectives

By the end of this lesson, you will understand:

* Returning a JSON array from ASP.NET Core
* Arrays in TypeScript
* `Employee[]`
* Displaying data using `@for`
* Creating an HTML table
* Difference between an Object and an Array

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

JSON Array

↓

Employee[]

↓

@for

↓

HTML Table
```

---

# Step 1 - Update ASP.NET Core API

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

app.MapGet("/", () =>
{
    return new[]
    {
        new
        {
            Id=101,
            Name="Raman",
            Salary=50000
        },

        new
        {
            Id=102,
            Name="John",
            Salary=60000
        },

        new
        {
            Id=103,
            Name="David",
            Salary=70000
        }
    };
});

app.Run();
```

---

# Browser Output

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

Notice

Earlier

we returned

```json
{
    "id":101,
    "name":"Raman"
}
```

Now

we return

```json
[
   {...},
   {...},
   {...}
]
```

This is an **array**.

---

# Step 2 - Interface

We already have

```typescript
export interface Employee {

    id:number;

    name:string;

    salary:number;

}
```

No changes needed.

---

# Step 3 - Service

Update

```typescript
getEmployees() {

    return this.http.get<Employee[]>(this.apiUrl);

}
```

Notice

Earlier

```typescript
Employee
```

Now

```typescript
Employee[]
```

because

the API returns

multiple employees.

---

# Step 4 - Component

employee.ts

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

    employees:Employee[]=[];

    constructor(private employeeService:EmployeeService){

    }

    ngOnInit(){

        this.loadEmployees();

    }

    loadEmployees(){

        this.employeeService
            .getEmployees()
            .subscribe({

                next:(data)=>{

                    console.log(data);

                    this.employees=data;

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
employees:Employee[]=[]
```

An empty array.

---

# Step 5 - HTML

```html
<h2>Employee List</h2>

<table border="1">

    <tr>

        <th>Id</th>

        <th>Name</th>

        <th>Salary</th>

    </tr>

    @for(employee of employees; track employee.id){

        <tr>

            <td>{{employee.id}}</td>

            <td>{{employee.name}}</td>

            <td>{{employee.salary}}</td>

        </tr>

    }

</table>
```

---

# Browser Output

| Id  | Name  | Salary |
| --- | ----- | ------ |
| 101 | Raman | 50000  |
| 102 | John  | 60000  |
| 103 | David | 70000  |

---

# What is Happening?

```text
Angular Starts

↓

Employee Component

↓

loadEmployees()

↓

EmployeeService

↓

HttpClient

↓

GET /

↓

ASP.NET Core

↓

JSON Array

↓

Employee[]

↓

employees[]

↓

@for

↓

HTML Table
```

---

# Object vs Array

### Object

```typescript
employee:Employee;
```

Contains

```text
One Employee
```

Example

```json
{
    "id":101,
    "name":"Raman"
}
```

---

### Array

```typescript
employees:Employee[];
```

Contains

```text
Many Employees
```

Example

```json
[
    {
        "id":101
    },
    {
        "id":102
    }
]
```

---

# Using @empty

Suppose

the API returns

```json
[]
```

Instead of an empty table

we can show

```html
<h2>Employee List</h2>

@if (employees.length > 0) {

<table border="1">

<tr>

<th>Id</th>

<th>Name</th>

<th>Salary</th>

</tr>

@for(employee of employees;track employee.id){

<tr>

<td>{{employee.id}}</td>

<td>{{employee.name}}</td>

<td>{{employee.salary}}</td>

</tr>

}

</table>

}
@else {

<p>No Employees Found</p>

}
```

Output

```text
No Employees Found
```

This provides a better user experience.

---

# Why Track by Id?

```html
@for(employee of employees; track employee.id)
```

Angular uses `employee.id` to identify each row.

If one employee changes,

Angular updates only that row instead of rebuilding the entire table.

This improves performance.

---

# Best Practices

✅ Always use interfaces.

```typescript
Employee[]
```

instead of

```typescript
any[]
```

---

✅ Use

```html
track employee.id
```

for better rendering performance.

---

✅ Put API calls inside Services.

---

# Summary

| Concept    | Description                    |
| ---------- | ------------------------------ |
| JSON Array | Collection of objects          |
| Employee[] | Array of employees             |
| @for       | Displays multiple items        |
| track      | Improves rendering performance |
| HTML Table | Displays structured data       |

---

# Practice Exercises

## Exercise 1

Modify the API to return **5 employees**.

Verify that Angular displays all five.

---

## Exercise 2

Add a new property

```json
Department
```

Display it as a new table column.

---

## Exercise 3

Add

```json
City
```

Display it.

---

## Exercise 4

If the API returns an empty array, display:

```text
No Employees Found
```

---

# Interview Questions

### 1. What is the difference between `Employee` and `Employee[]`?

`Employee` represents a single object.

`Employee[]` represents a collection of Employee objects.

---

### 2. Why do we use `@for`?

To iterate over a collection and render HTML for each item.

---

### 3. What is the purpose of `track employee.id`?

To uniquely identify items so Angular can efficiently update only the rows that change.

---

### 4. Why do we create an Interface?

To provide type safety, IntelliSense, and compile-time checking.

---

# What We'll Build Next

So far we have only displayed data.

A real Employee Management application also lets users **add** new employees.

In **Lesson 14**, we'll build our first **Employee Entry Form** using:

* Two-way data binding with `[(ngModel)]`
* A Save button
* Calling an ASP.NET Core **POST** API
* Refreshing the employee list after saving

This is the beginning of a complete CRUD (Create, Read, Update, Delete) application.
