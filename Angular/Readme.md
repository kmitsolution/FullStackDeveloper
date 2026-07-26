#  Angular Basics (Week 1)

## Install

* Node.js
* VS Code
* Angular CLI

Commands

```bash
npm install -g @angular/cli

ng new angular-demo

cd angular-demo

ng serve
```

Understand project structure

```
src

app

assets

public

angular.json

package.json

tsconfig.json
```

---

## Standalone Components

Create component

```bash
ng generate component home
```

Topics

* selector
* template
* styles
* imports
* standalone:true

---

## Data Binding

Very important

* Interpolation

```html
{{title}}
```

* Property Binding

```html
<img [src]="image">
```

* Event Binding

```html
<button (click)="save()">
```

* Two-way Binding

```html
<input [(ngModel)]="name">
```

---

## Directives

### @if

```html
@if(isAdmin){

}
```

### @for

```html
@for(emp of employees;track emp.id){

}
```

### @switch

---

## Pipes

* Uppercase
* Lowercase
* Currency
* Percent
* Date
* JSON

Custom Pipe

---

# Phase 3 - Components (Week 2)

Topics

Component Communication

Parent → Child

```typescript
@Input()
```

Child → Parent

```typescript
@Output()
```

EventEmitter

Component Lifecycle

* constructor
* ngOnInit
* ngOnChanges
* ngAfterViewInit
* ngOnDestroy

ViewChild

Content Projection

```
<ng-content>
```

---

# Phase 4 - Services and Dependency Injection

Create Service

```bash
ng g service services/product
```

Topics

* Injectable
* Singleton Service
* Dependency Injection
* Injection Tokens

---

# Phase 5 - Routing

Topics

Create Routes

Navigation

Route Parameters

```
products/:id
```

Query Parameters

Lazy Loading

Route Guards

404 Page

Nested Routes

---

# Phase 6 - Forms (Very Important)

Template Driven Forms

Reactive Forms

Validation

* Required
* Email
* Pattern
* MinLength

Custom Validators

Dynamic Forms

FormArray

FormGroup

---

# Phase 7 - HTTP Client

Topics

GET

POST

PUT

DELETE

PATCH

Headers

Authentication Token

Error Handling

Retry

Interceptors

Loading Spinner

---

# Phase 8 - RxJS

Angular heavily uses RxJS.

Topics

Observable

Observer

Subject

BehaviorSubject

ReplaySubject

Operators

* map
* filter
* tap
* mergeMap
* switchMap
* concatMap
* forkJoin
* combineLatest
* debounceTime
* catchError
* retry

---

# Phase 9 - Signals (Modern Angular)

Very Important

signal()

computed()

effect()

Writable Signals

Signal Inputs

Signal Outputs

Compare Signals vs RxJS

---

# Phase 10 - Angular UI

Learn one framework

Recommended

* Angular Material

Topics

Table

Dialog

Toolbar

Menu

Card

Paginator

Sorting

Snackbar

Progress Bar

Date Picker

Stepper

---

# Phase 11 - Authentication

JWT

Login

Logout

Role Based Authentication

Refresh Token

Route Guards

HTTP Interceptor

---

# Phase 12 - Angular + ASP.NET Core

This is where everything comes together.

Architecture

```
Angular

↓

HTTP

↓

ASP.NET Core API

↓

SQL Server
```

Learn

Calling APIs

CORS

JWT Authentication

File Upload

Download Files

Image Upload

Pagination

Filtering

Sorting

Search

Swagger

Error Handling

Validation

---

# Phase 13 - State Management

Start with

Services

BehaviorSubject

Signals

Then

NgRx

Only after becoming comfortable.

---

# Phase 14 - Unit Testing

Jasmine

Karma (or the newer alternatives used by Angular)

Component Testing

Service Testing

Mock HTTP

Code Coverage

---

# Phase 15 - Build & Deployment

Development Build

Production Build

```bash
ng build
```

Environment Files

Docker

Nginx

Azure App Service

Azure Static Web Apps

GitHub Actions

GitLab CI/CD

---

# Phase 16 - Capstone Project (3-4 Weeks)

Build a complete application using Angular + ASP.NET Core.

## Suggested Project: Employee Management System

### Angular

* Login
* Dashboard
* Employee List
* Employee Details
* Add Employee
* Edit Employee
* Delete Employee
* Search
* Pagination
* Sorting
* Charts
* File Upload
* Profile Picture
* Role Based UI

### ASP.NET Core

* JWT Authentication
* CRUD APIs
* SQL Server
* Entity Framework Core
* Repository Pattern
* Swagger
* Logging
* Validation
* File Upload

---

# Suggested Learning Timeline

| Week | Topics                                                     |
| ---- | ---------------------------------------------------------- |
| 1    | TypeScript Fundamentals                                    |
| 2    | Angular Basics, Components, Data Binding                   |
| 3    | Directives, Pipes, Services, Dependency Injection          |
| 4    | Routing and Forms                                          |
| 5    | HTTP Client, RxJS, Signals                                 |
| 6    | Angular Material and Authentication                        |
| 7    | Angular + ASP.NET Core Integration                         |
| 8    | Capstone Project (Employee Management System)              |
| 9    | Advanced Features (State Management, Testing, Performance) |
| 10   | Deployment with Docker, Azure, and GitLab CI/CD            |

## Final Project Ideas

After completing the roadmap, you should be able to build applications such as:

1. Employee Management System
2. E-Commerce Application
3. Banking Application
4. Hospital Management System
5. Student Management System
6. DevOps Pipeline Dashboard (Angular + ASP.NET Core + Azure)

Given your background, I'd also suggest turning this into a **YouTube playlist**. A **60-lecture series** (20–30 minutes per lecture) would take viewers from Angular fundamentals to a production-ready Angular + ASP.NET Core application with authentication, Docker, and GitLab CI/CD deployment, aligning well with the kind of technical training content you already create.
