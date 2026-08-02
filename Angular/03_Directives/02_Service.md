> **Important Note**
>
> Angular 22 has introduced **new conventions**. Throughout this course, I'll teach:
>
> * ✅ **Angular 22 way** (what you're using)
> * ✅ **Enterprise/Older Angular way** (what you'll see in most companies)

---

# Lesson 10: Services & Dependency Injection (Angular 22)

## Learning Objectives

By the end of this lesson, you will understand:

* What is a Service?
* Why do we need Services?
* Component vs Service
* Creating a Service
* `@Service()`
* Dependency Injection (DI)
* Injecting a Service into a Component
* Preparing for ASP.NET Core Web API integration

---

# What is a Service?

A **Service** is a TypeScript class that contains **business logic**.

Examples:

* Call ASP.NET Core APIs
* Calculate Salary
* Authentication
* Logging
* Validation
* Shared Data

Unlike Components,

Services

❌ have no HTML

❌ have no CSS

✅ only contain TypeScript code.

---

# Why Do We Need Services?

Imagine this component.

```typescript
export class Employee {

    saveEmployee() {

    }

    deleteEmployee() {

    }

    updateEmployee() {

    }

    calculateSalary() {

    }

    generateReport() {

    }

}
```

After a few months

```text
Employee Component

↓

4000 Lines

↓

Impossible to Maintain
```

Instead

Move business logic to a Service.

---

# Enterprise Architecture

```text
Browser

↓

Employee Component

↓

Employee Service

↓

ASP.NET Core Web API

↓

SQL Server
```

Notice

Component

↓

Service

↓

API

This architecture is used almost everywhere.

---

# Component vs Service

| Component        | Service        |
| ---------------- | -------------- |
| UI               | Business Logic |
| HTML             | No HTML        |
| CSS              | No CSS         |
| User Interaction | API Calls      |
| Displays Data    | Retrieves Data |

---

# Creating a Service

Angular CLI

```bash
ng generate service employee
```

or

```bash
ng g s employee
```

---

# Angular 22 Output

Angular creates

```text
src

└── app

    employee.ts

    employee.spec.ts
```

Notice

Angular 22 **does not create**

```text
employee.service.ts
```

Instead

it creates

```text
employee.ts
```

This is expected behavior in Angular 22.

---

# Your Project Structure

Your project currently looks like

```text
src

└── app

    employee

        employee.ts

        employee.html

        employee.css

        employee.spec.ts

    employee.ts

    employee.spec.ts
```

The difference is

### Component

```text
employee/

    employee.ts
```

because it is inside a folder.

### Service

```text
employee.ts
```

because it is directly inside

```text
app
```

---

# Generated Service

Angular 22 generates something similar to

```typescript
import { Service } from '@angular/core';

@Service()
export class Employee {

}
```

> **Note:** If you're working on older Angular versions or existing enterprise projects, you'll often see `@Injectable()` and filenames like `employee.service.ts`. We'll discuss those differences whenever they're relevant.

---

# What is @Service()?

```typescript
@Service()
```

tells Angular

This class is managed by Angular's

Dependency Injection System.

Angular can automatically create

and inject this class.

---

# Dependency Injection

Suppose

Employee Component

needs

Employee Service.

Instead of writing

```typescript
const service = new Employee();
```

Angular automatically creates

the service.

We simply ask for it.

---

# Visual Representation

```text
Employee Component

↓

Needs Employee Service

↓

Angular

↓

Creates Service

↓

Injects Service
```

---

# Creating a Method

employee.ts

(Service)

```typescript
import { Service } from '@angular/core';

@Service()
export class Employee {

    getCompanyName() {

        return "KMIT Solutions";

    }

}
```

---

# Injecting Service

Component

```typescript
import { Component } from '@angular/core';

import { Employee } from '../employee';

@Component({

    selector:'app-employee',

    imports:[],

    templateUrl:'./employee.html',

    styleUrl:'./employee.css'

})

export class EmployeeComponent {

    company="";

    constructor(private employeeService:Employee){

        this.company=this.employeeService.getCompanyName();

    }

}
```

Notice

```typescript
constructor(private employeeService:Employee)
```

Angular automatically injects it.

---

# Browser

employee.html

```html
<h2>

{{ company }}

</h2>
```

Output

```text
KMIT Solutions
```

---

# Returning Multiple Employees

Service

```typescript
import { Service } from '@angular/core';

@Service()
export class Employee {

    getEmployees(){

        return [

            {

                id:1,

                name:"Raman"

            },

            {

                id:2,

                name:"John"

            },

            {

                id:3,

                name:"David"

            }

        ];

    }

}
```

---

# Component

```typescript
employees:any[]=[];

constructor(private employeeService:Employee){

    this.employees=this.employeeService.getEmployees();

}
```

---

# HTML

```html
<h2>Employees</h2>

@for(employee of employees;track employee.id){

    <p>

        {{employee.id}}

        -

        {{employee.name}}

    </p>

}
```

Browser

```text
1 - Raman

2 - John

3 - David
```

---

# Preparing for ASP.NET Core

Currently

Service returns

hardcoded data.

```typescript
getEmployees(){

    return [

        ...

    ];

}
```

Later

We'll replace it with

```typescript
getEmployees(){

    return this.http.get<Employee[]>(

        "https://localhost:5001/api/employees"

    );

}
```

Notice

The Component

does not change.

Only

the Service changes.

This is one of the biggest advantages of Services.

---

# Dependency Injection Flow

```text
Employee Component

↓

Needs Service

↓

Angular DI Container

↓

Creates Service

↓

Injects Service

↓

Component Uses Service
```

---

# Angular vs ASP.NET Core

## Angular

```typescript
constructor(private employeeService:Employee){

}
```

Angular creates

Employee Service.

---

## ASP.NET Core

```csharp
public EmployeeController(

EmployeeService service)

{

}
```

ASP.NET Core creates

EmployeeService.

Same concept.

---

# Complete Architecture

```text
Browser

↓

Employee Component

↓

Employee Service

↓

ASP.NET Core API

↓

SQL Server
```

Exactly

how enterprise applications work.

---

# Summary

| Component        | Service        |
| ---------------- | -------------- |
| UI               | Business Logic |
| HTML             | No HTML        |
| CSS              | No CSS         |
| Displays Data    | Retrieves Data |
| User Interaction | API Calls      |

---

# Best Practices

✅ Keep Components small.

✅ Put Business Logic inside Services.

✅ Put API calls inside Services.

✅ Reuse Services across multiple Components.

---

# Practice Exercises

### Exercise 1

Create

```bash
ng g s department
```

Observe

Angular creates

```text
department.ts
```

instead of

```text
department.service.ts
```

---

### Exercise 2

Create

```typescript
getCourses()
```

Return

```text
Angular

ASP.NET Core

Azure
```

Display them in a Component.

---

### Exercise 3

Create

```typescript
getCompanyName()
```

Return

```text
KMIT Solutions
```

Display it.

---

### Exercise 4

Explain

Why is Service better than putting everything inside the Component?

---

# Interview Questions

1. What is a Service?
2. Why do we use Services?
3. What is Dependency Injection?
4. What is the purpose of `@Service()`?
5. Why shouldn't Components call APIs directly?
6. What is the role of Angular's DI container?
7. How is Angular DI similar to ASP.NET Core DI?

---

# 🚨 Important Clarification

Before we continue with the course, **I need to correct one assumption** based on your environment.

Your Angular CLI generated:

```typescript
import { Service } from '@angular/core';

@Service()
export class Employee {}
```

This **does not match the official Angular 22 CLI** documentation that is publicly available today, which still documents services with `@Injectable`. Since your actual project is generating `@Service`, we'll treat **your generated code as the source of truth** for this course and adapt examples accordingly.

As we progress, if we encounter any differences between your installed tooling and the official documentation, I'll point them out so you understand both what your project is doing and what you'll commonly see in enterprise codebases. This way you'll be prepared for interviews and real-world projects alike.
