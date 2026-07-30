Excellent! This is one of the **most important Angular lessons**.

Most beginners simply memorize Angular files, but after this lesson you'll understand **how Angular starts internally**, similar to understanding the ASP.NET Core request pipeline.

Since you already know ASP.NET Core, I'll compare every Angular concept with ASP.NET Core.

---

# Lesson 3: Angular Project Structure & Application Startup

## Learning Objectives

By the end of this lesson, you will understand:

* How Angular starts
* What happens when the browser requests an Angular application
* Every generated file
* How Angular bootstraps the application
* Standalone Components
* How Angular displays the first page
* Comparison with ASP.NET Core

---

# Our Project Structure

Suppose we created

```bash
ng new EmployeeManagement.UI
```

Angular generates

```text
EmployeeManagement.UI

│

├── node_modules

├── public

├── src

│     ├── app

│     │      app.ts

│     │      app.html

│     │      app.css

│     │      app.config.ts

│     │      app.routes.ts

│     │      app.spec.ts

│     │

│     ├── index.html

│     ├── main.ts

│     └── styles.css

│

├── angular.json

├── package.json

├── tsconfig.json

└── README.md
```

---

# Which Files Do We Work With Daily?

In a real Angular project, you'll mostly work with:

```text
src

↓

app

↓

Components

Services

Models

Routes

↓

main.ts

↓

styles.css
```

You will rarely modify

* `angular.json`
* `package.json`
* `tsconfig.json`

after the initial setup.

---

# How Angular Starts

This is the complete startup flow.

```text
Browser

↓

index.html

↓

main.ts

↓

bootstrapApplication()

↓

app.config.ts

↓

App Component

↓

app.html

↓

Browser Displays UI
```

Let's understand each step.

---

# Step 1

Browser Requests

```text
http://localhost:4200
```

Angular Development Server responds with

```text
index.html
```

---

# What is index.html?

Unlike ASP.NET MVC,

Angular has only **one HTML page**.

Example

```html
<!doctype html>

<html>

<head>

</head>

<body>

    <app-root></app-root>

</body>

</html>
```

Notice

```html
<app-root></app-root>
```

At first, this looks like a normal HTML tag, but it is **not**.

It is a placeholder where Angular will render the root component.

---

# ASP.NET Core Comparison

MVC

```text
Request

↓

HomeController

↓

Index.cshtml
```

Angular

```text
Request

↓

index.html

↓

Angular

↓

Component
```

---

# Step 2

Angular loads

```text
main.ts
```

---

# main.ts

Think of

```text
main.ts
```

as Angular's

```text
Program.cs
```

Example

```typescript
import { bootstrapApplication } from '@angular/platform-browser';

import { App } from './app/app';

import { appConfig } from './app/app.config';

bootstrapApplication(App, appConfig)
.catch(err => console.error(err));
```

---

# What is bootstrapApplication()?

Suppose

```typescript
bootstrapApplication(App, appConfig);
```

means

```text
Start Angular

↓

Create App Component

↓

Apply Configuration

↓

Display UI
```

This is the entry point of your Angular application.

---

# ASP.NET Core Comparison

ASP.NET Core

```csharp
var builder =
WebApplication.CreateBuilder(args);
```

Angular

```typescript
bootstrapApplication(App, appConfig);
```

Both initialize the application.

---

# Step 3

Angular loads

```text
app.config.ts
```

---

# app.config.ts

Example

```typescript
import { ApplicationConfig } from '@angular/core';

import { provideRouter } from '@angular/router';

import { routes } from './app.routes';

export const appConfig: ApplicationConfig =
{
    providers:
    [
        provideRouter(routes)
    ]
};
```

---

# What is app.config.ts?

This file registers services that the entire application can use.

Later we'll register:

* Router
* HttpClient
* Interceptors
* Animations

Think of it as:

```csharp
builder.Services
```

in ASP.NET Core.

---

# ASP.NET Core Comparison

ASP.NET Core

```csharp
builder.Services.AddControllers();

builder.Services.AddAuthentication();

builder.Services.AddSwagger();
```

Angular

```typescript
providers:
[
    provideRouter(routes)
]
```

Later

```typescript
providers:
[
    provideRouter(routes),

    provideHttpClient()
]
```

The idea is very similar: register application-wide services.

---

# Step 4

Angular loads

```text
app.routes.ts
```

Example

```typescript
import { Routes } from '@angular/router';

export const routes: Routes =
[
];
```

Currently

No routes.

Later

```typescript
export const routes: Routes =
[
    {
        path:'',
        component:HomeComponent
    },

    {
        path:'employees',
        component:EmployeeComponent
    }
];
```

---

# What Does Routing Mean?

Suppose the user enters

```text
http://localhost:4200/employees
```

Angular checks

```text
app.routes.ts
```

Finds

```typescript
{
    path:'employees',
    component:EmployeeComponent
}
```

Displays

```text
EmployeeComponent
```

Exactly like ASP.NET Core endpoint routing.

---

# Step 5

Angular creates

```text
App Component
```

---

# What is App Component?

The App Component is the **root component**.

Everything starts here.

Example

```typescript
export class App
{

}
```

Think of it as the root of the component tree.

---

# Component Tree

```text
App Component

↓

Header

↓

Menu

↓

Dashboard

↓

Footer
```

Every Angular application begins with the root component.

---

# Step 6

Angular loads

```text
app.html
```

Example

```html
<h1>

Welcome to Angular

</h1>
```

Browser

Displays

```text
Welcome to Angular
```

---

# Step 7

Browser Displays

```text
Angular Application
```

Startup Complete.

---

# Complete Startup Flow

```text
Browser

↓

index.html

↓

main.ts

↓

bootstrapApplication()

↓

app.config.ts

↓

App Component

↓

app.html

↓

Browser UI
```

This sequence is worth remembering because it explains how every Angular application starts.

---

# Understanding app.ts

This is a class.

Example

```typescript
export class App
{

    title =
    "Employee Management";

}
```

Notice

No HTML here.

Only

* Properties
* Methods
* Business Logic

Just like a C# class.

---

# Understanding app.html

This is the UI.

Example

```html
<h1>

{{title}}

</h1>
```

Notice

```html
{{title}}
```

Angular automatically reads

```typescript
title
```

from the component class.

We'll learn this in **Data Binding**.

---

# app.ts + app.html

Think of them together.

```text
app.ts

↓

Data

↓

app.html

↓

UI
```

This separation keeps business logic and presentation organized.

---

# app.css

Contains CSS for

only

```text
App Component
```

Example

```css
h1
{
    color:blue;
}
```

Component CSS is scoped to that component by default.

---

# styles.css

Global CSS.

Example

```css
body
{
    margin:0;
}
```

Affects the entire application.

---

# app.spec.ts

Contains Unit Tests.

We won't use it immediately.

---

# index.html

Notice

Angular has

only

one

```text
index.html
```

Unlike MVC,

there are no multiple HTML pages.

Angular changes components,

not pages.

That's why Angular is called a

Single Page Application.

---

# Angular vs ASP.NET Core

| ASP.NET Core         | Angular                     |
| -------------------- | --------------------------- |
| Program.cs           | main.ts                     |
| builder.Services     | app.config.ts               |
| MapControllers()     | app.routes.ts               |
| Controller           | Component                   |
| View                 | HTML Template               |
| Model                | TypeScript Interface/Class  |
| Dependency Injection | Dependency Injection        |
| Middleware Pipeline  | Component & Router Pipeline |

---

# Project Structure We'll Build Later

```text
EmployeeManagement.UI

↓

Components

↓

Services

↓

Models

↓

Guards

↓

Interceptors

↓

Routes

↓

Shared

↓

Core
```

This is a common enterprise organization.

---

# Summary

| File          | Responsibility                 |
| ------------- | ------------------------------ |
| index.html    | First page sent to browser     |
| main.ts       | Starts Angular                 |
| app.config.ts | Registers application services |
| app.routes.ts | URL routing                    |
| app.ts        | Root component logic           |
| app.html      | Root component UI              |
| app.css       | Root component styles          |
| styles.css    | Global styles                  |

---

# Practice Exercises

### Exercise 1

Draw the startup flow:

```text
Browser
↓
index.html
↓
main.ts
↓
bootstrapApplication()
↓
app.config.ts
↓
App Component
↓
app.html
```

---

### Exercise 2

Explain the purpose of:

* `main.ts`
* `app.config.ts`
* `app.routes.ts`

---

### Exercise 3

What is the difference between:

* `app.css`
* `styles.css`

---

### Exercise 4

Explain why Angular applications have only one `index.html`.

---

# Interview Questions

1. How does an Angular application start?
2. What is the purpose of `bootstrapApplication()`?
3. What is the role of `main.ts`?
4. What is the purpose of `app.config.ts`?
5. What is the purpose of `app.routes.ts`?
6. What is the difference between `app.css` and `styles.css`?
7. Why is Angular called a Single Page Application?
8. How is Angular's startup process similar to ASP.NET Core's `Program.cs`?

---

# Before Lesson 4

We're finally going to start building Angular features.

The next lesson is **Components**, the most important concept in Angular.

We'll cover:

* What is a Component?
* Why Components exist
* Creating Components using Angular CLI
* Component selector
* Component template
* Component styles
* Component class
* Component lifecycle (introduction)
* Parent and child components
* How components fit into an Angular application

Once you understand components, you'll realize that **everything you build in Angular—pages, menus, forms, tables, dialogs—is just a collection of components working together.**
