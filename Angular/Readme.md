Perfect. Based on what we've already completed and your goals, we'll **not learn Angular as an isolated framework**. Instead, we'll learn it exactly the way it's used in real companies—with an **ASP.NET Core Web API backend**.

Since you already know ASP.NET Core, we'll treat it as the backend and focus on becoming productive in Angular.

---

# Angular + ASP.NET Core Learning Roadmap

## Phase 1: Angular Fundamentals

**Goal:** Understand how Angular works before connecting it to an API.

### Module 1: Introduction to Angular

* What is Angular?
* Why Angular?
* Angular vs React vs Vue
* SPA (Single Page Application)
* Angular Architecture
* Angular CLI
* Create your first Angular application
* Angular project structure
* Build & Run Angular application

**Mini Project**

* Simple Welcome Page

---

### Module 2: Components

Components are the heart of Angular.

Topics:

* What is a Component?
* Component Architecture
* Component Lifecycle
* Inline Template
* External Template
* Inline CSS
* External CSS
* Creating Components
* Nested Components
* Parent & Child Components
* Component Communication

**Mini Project**

Employee Dashboard

---

### Module 3: Templates & Data Binding

One of the most important modules.

Topics:

* String Interpolation
* Property Binding
* Event Binding
* Two-way Binding
* Template Variables
* Template Expressions

Example

```html
<input [(ngModel)]="employee.name">
```

Mini Project

Employee Form

---

### Module 4: Directives

Topics:

* ngIf
* ngFor
* ngSwitch
* ngClass
* ngStyle
* Custom Directives

Mini Project

Employee List

---

### Module 5: Pipes

Topics:

* Date Pipe
* Currency Pipe
* Decimal Pipe
* Uppercase
* Lowercase
* Slice Pipe
* JSON Pipe
* Async Pipe
* Custom Pipe

Mini Project

Employee Salary Report

---

## Phase 2: Angular Architecture

Now we'll learn how enterprise Angular applications are organized.

### Module 6: Services

Topics

* Why Services?
* Creating Services
* Singleton Services
* Dependency Injection
* Service Lifetime

Mini Project

EmployeeService

---

### Module 7: Routing

Topics

* Angular Routing
* Route Parameters
* Child Routes
* Lazy Loading
* Route Guards

Mini Project

```text
Login

↓

Dashboard

↓

Employees

↓

Employee Details
```

---

### Module 8: Forms

Topics

Template Driven Forms

Reactive Forms

Validation

Custom Validation

Form Arrays

Dynamic Forms

Mini Project

Employee Registration

---

## Phase 3: Angular + ASP.NET Core Integration ⭐

Now Angular becomes interesting.

### Module 9: HttpClient

Topics

* GET
* POST
* PUT
* DELETE
* PATCH
* Headers
* Query Parameters
* Error Handling

Backend

ASP.NET Core API

Example

```text
Angular

↓

GET

↓

/api/employees

↓

ASP.NET Core

↓

SQL Server
```

Mini Project

Employee CRUD

---

### Module 10: Models

Topics

Create

```text
Employee.ts
```

matching

```csharp
Employee.cs
```

Example

ASP.NET Core

```csharp
public class Employee
{
    public int Id {get;set;}
    public string Name {get;set;}
}
```

Angular

```typescript
export interface Employee
{
    id:number;
    name:string;
}
```

---

### Module 11: RxJS

Topics

Observable

Subscribe

Operators

map

filter

tap

switchMap

catchError

forkJoin

combineLatest

Subjects

BehaviorSubject

ReplaySubject

Angular uses RxJS everywhere.

---

### Module 12: Authentication

Backend

ASP.NET Core JWT

Frontend

Angular

Topics

* Login
* Logout
* JWT Token
* Store Token
* HttpInterceptor
* Authorization Header
* Route Guard
* Refresh Token (concept)

Mini Project

Secure Employee Portal

---

### Module 13: HttpInterceptor

Topics

Automatically add

```text
Authorization:
Bearer Token
```

to every API request.

Very common in enterprise applications.

---

### Module 14: Error Handling

Topics

Global Error Handler

API Error Messages

Loading Spinner

Retry Logic

Toast Notifications

---

## Phase 4: Advanced Angular

Topics

Signals

Standalone Components

Lazy Loading

Performance Optimization

Environment Files

Configuration

Reusable Components

Reusable Services

Deployment

---

## Phase 5: Complete Enterprise Project ⭐⭐⭐⭐⭐

We'll build one complete application from scratch.

### Employee Management System

### Backend (ASP.NET Core)

* Web API
* Entity Framework Core
* SQL Server
* JWT Authentication
* Repository Pattern
* Dependency Injection
* Swagger

---

### Frontend (Angular)

* Login
* Dashboard
* Employee CRUD
* Department CRUD
* Search
* Pagination
* Sorting
* Forms
* Validation
* Authentication
* Authorization
* Route Guards
* Services
* HttpClient
* RxJS
* Interceptors
* Responsive UI

---

### Deployment

Docker

Angular

↓

Nginx

ASP.NET Core

↓

Kestrel

↓

SQL Server

(Optional later, since you already have Docker knowledge.)

---

# Folder Structure We'll Follow

```text
EmployeeManagement

├── EmployeeManagement.Api        (ASP.NET Core)

├── EmployeeManagement.UI         (Angular)

└── README.md
```

