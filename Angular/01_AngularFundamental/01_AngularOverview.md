Excellent! Welcome to **Angular**. 🎉

From now on, think like a **Full Stack Developer**.

You already know:

* ✅ C#
* ✅ ASP.NET Core
* ✅ REST APIs
* ✅ TypeScript
* ✅ OOP

Now we'll learn how Angular fits into the complete application.

---

# Lesson 1: Introduction to Angular

## Learning Objectives

By the end of this lesson, you will understand:

* What is Angular?
* Why Angular was created
* What problems Angular solves
* SPA (Single Page Application)
* Angular Architecture
* Angular + ASP.NET Core Architecture
* Angular CLI
* Angular Project Life Cycle
* Why Companies Use Angular

---

# What is Angular?

Angular is an **open-source front-end framework** developed and maintained by **Google**.

It is used to build:

* Enterprise Web Applications
* Single Page Applications (SPA)
* Dashboards
* Admin Portals
* CRM Applications
* Banking Applications
* ERP Systems

Angular is written using:

* HTML
* CSS
* TypeScript

---

# What Does Angular Do?

Angular creates the **User Interface (UI)**.

Example

```text
Employee Management System

------------------------------------

Employees

------------------------------------

ID    Name      Salary

1     Raman     50000

2     John      60000

------------------------------------

[ Add Employee ]
```

Everything the user sees is built using Angular.

---

# What Does ASP.NET Core Do?

ASP.NET Core handles

* Authentication
* Authorization
* Business Logic
* Database Access
* API Development

Example

```http
GET /api/employees
```

Returns

```json
[
  {
    "id":1,
    "name":"Raman",
    "salary":50000
  }
]
```

Angular displays this data.

---

# Complete Architecture

```text
+--------------------------------------+
|          Web Browser                 |
|                                      |
|          Angular Application         |
|                                      |
+--------------------------------------+
                |
                | HTTP / HTTPS
                |
+--------------------------------------+
|       ASP.NET Core Web API           |
|                                      |
| Authentication                       |
| Business Logic                       |
| Entity Framework Core                |
+--------------------------------------+
                |
                |
+--------------------------------------+
|           SQL Server                 |
+--------------------------------------+
```

This is the architecture we'll build throughout the course.

---

# Why Was Angular Created?

Before Angular, developers built websites using:

```text
HTML

CSS

JavaScript

jQuery
```

Suppose a page displays employees.

```text
Employees

1 Raman

2 John
```

When a new employee is added,

JavaScript manually updates the HTML.

Problems:

* Too much code
* Difficult to maintain
* Hard to test
* Slow development

Angular solves these problems.

---

# Before Angular

Imagine a login page.

HTML

```html
<input>

<button>
```

JavaScript

```javascript
document.getElementById(...)

document.createElement(...)

appendChild(...)

removeChild(...)
```

You manually manipulate the DOM.

---

# With Angular

HTML

```html
<input [(ngModel)]="employee.name">

<button (click)="save()">
    Save
</button>
```

TypeScript

```typescript
save()
{
    console.log("Saving...");
}
```

Angular updates the page automatically.

No manual DOM manipulation.

---

# What is SPA?

SPA stands for

**Single Page Application**

---

## Traditional Website

Suppose you open

```text
https://company.com
```

Browser

↓

Requests

```text
Home Page
```

Server sends

Entire HTML page

Now click Employees

Browser again requests

Entire Employees page

Click Departments

Browser requests

Entire Departments page

Every navigation reloads the page.

---

## Flow

```text
Browser

↓

Request

↓

Server

↓

Complete HTML

↓

Browser

↓

Another Request

↓

Complete HTML
```

Every click loads a new page.

---

# Single Page Application

Browser loads

Angular Application

Only once.

```
Browser

↓

Angular App

↓

Loaded Once
```

Now click

Employees

Angular changes only the content.

No full page reload.

---

# SPA Flow

```text
Browser

↓

Angular Loads Once

↓

Dashboard

↓

Employees

↓

Departments

↓

Reports

↓

Settings
```

The page itself never reloads.

Only the displayed content changes.

---

# Traditional Website vs SPA

## Traditional Website

```text
Home

↓

Reload

↓

Employees

↓

Reload

↓

Departments

↓

Reload
```

---

## Angular SPA

```text
Angular Loaded Once

↓

Home

↓

Employees

↓

Departments

↓

Reports
```

No full page refresh.

---

# Advantages of SPA

✔ Faster

✔ Better User Experience

✔ Less Server Load

✔ Modern UI

✔ Desktop-like experience

---

# Angular Architecture

Every Angular application is built from **Components**.

```
App Component

↓

Header Component

↓

Menu Component

↓

Employee Component

↓

Footer Component
```

Think of Components like Lego blocks.

---

# What is a Component?

A Component is a reusable part of the UI.

Example

```text
Header

Menu

Employee List

Footer
```

Each is an independent component.

---

# Example

Facebook

```text
Header

Left Menu

Posts

Friends

Right Menu
```

Each section is a separate component.

---

# Employee Application

```
Header

↓

Navigation

↓

Dashboard

↓

Employee List

↓

Employee Form

↓

Footer
```

Each part is a component.

---

# Angular Component Internally

Every component has

```text
HTML

↓

CSS

↓

TypeScript
```

Example

```
EmployeeComponent

employee.component.html

employee.component.css

employee.component.ts
```

Three files together form one component.

---

# Angular Application Structure

```
Angular Application

↓

Components

↓

Services

↓

Models

↓

Routes
```

We'll learn each one in detail.

---

# How Angular Talks to ASP.NET Core

Suppose the user clicks

```
View Employees
```

Flow

```
Angular

↓

HTTP GET

↓

/api/employees

↓

ASP.NET Core

↓

SQL Server

↓

Employee Data

↓

Angular

↓

Display Table
```

This is exactly how real companies build applications.

---

# Example

Angular

```typescript
this.http.get<Employee[]>(
"/api/employees"
);
```

ASP.NET Core

```csharp
[HttpGet]
public IActionResult GetEmployees()
{
    return Ok(employees);
}
```

Angular receives JSON and displays it.

---

# Angular CLI

CLI means

**Command Line Interface**

Instead of manually creating files,

Angular CLI generates them.

Example

Create project

```bash
ng new EmployeeManagement
```

Create component

```bash
ng generate component employee
```

or

```bash
ng g c employee
```

Create service

```bash
ng g s employee
```

Very similar to how Visual Studio scaffolds controllers or views.

---

# Angular Development Life Cycle

```
Create Project

↓

Create Components

↓

Create Services

↓

Call ASP.NET Core API

↓

Display Data

↓

Forms

↓

Validation

↓

Deployment
```

---

# Why Companies Prefer Angular

Large companies choose Angular because it provides:

* Strong TypeScript support
* Clear project structure
* Dependency Injection
* Routing
* Forms
* HTTP Client
* Testing support
* Long-term support from Google

Angular is especially popular for enterprise applications where maintainability and consistency are important.

---

# Real Project Structure We'll Build

```
EmployeeManagement

│

├── EmployeeManagement.Api

│      ASP.NET Core

│

├── EmployeeManagement.UI

│      Angular

│

└── SQL Server
```

This is the same structure used in many enterprise projects.

---

# Summary

| Topic        | Description                                    |
| ------------ | ---------------------------------------------- |
| Angular      | Front-end framework                            |
| ASP.NET Core | Backend API                                    |
| SQL Server   | Database                                       |
| SPA          | Single Page Application                        |
| Component    | Reusable UI block                              |
| Angular CLI  | Tool to generate projects and code             |
| HTTP         | Communication between Angular and ASP.NET Core |

---

# Practice Questions

### Exercise 1

Explain the difference between:

* Angular
* ASP.NET Core

---

### Exercise 2

Draw the architecture of:

```
Angular

↓

ASP.NET Core

↓

SQL Server
```

---

### Exercise 3

Why is Angular called a Single Page Application framework?

---

### Exercise 4

List five advantages of Angular.

---

# Interview Questions

1. What is Angular?
2. What is a Single Page Application (SPA)?
3. What is the difference between Angular and ASP.NET Core?
4. What is a component in Angular?
5. Why is Angular popular for enterprise applications?
6. What is Angular CLI?
7. How does Angular communicate with ASP.NET Core?

---

# Before the Next Lesson

We'll move from theory to hands-on.

## Lesson 2: Installing Angular and Creating Your First Project

We'll cover:

* Installing Node.js (and how Angular depends on it)
* Installing Angular CLI
* Understanding npm and `npx`
* Creating your first Angular application with `ng new`
* Running the application with `ng serve`
* Understanding every generated file and folder
* How the Angular development server works
* How Angular differs from an ASP.NET Core project structure

This lesson is important because you'll understand **why Angular generates so many files**, instead of just memorizing them.
