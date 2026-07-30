Absolutely. Since you've now started **building components** and asked how to display one component inside another, I'll rewrite **Lesson 4** so it's more practical and aligned with **Angular 20 Standalone Components**.

---

# Lesson 4: Angular Components (Angular 20 Standalone)

## Learning Objectives

By the end of this lesson, you will understand:

* What is a Component?
* Why Components are needed
* Component Anatomy
* Root Component
* Child Components
* Component Selector
* Creating Components using Angular CLI
* Displaying one component inside another
* Component Tree
* Real-world Angular + ASP.NET Core architecture

---

# What is a Component?

A **Component** is a reusable piece of the User Interface (UI).

Everything you see in an Angular application is a component.

Examples:

* Login Page
* Dashboard
* Header
* Navigation Menu
* Employee List
* Footer
* Search Box

Think of a component as a small application with its own:

* HTML (View)
* TypeScript (Logic)
* CSS (Style)

---

# Real World Example

Think about a car.

```text
Car

├── Engine

├── Steering

├── Dashboard

├── Tyres

└── Doors
```

Each part has a separate responsibility.

Angular applications are built exactly the same way.

---

# Employee Management Application

Instead of creating one huge page

```text
Employee Management

5000 Lines of HTML
```

Angular divides the application into components.

```text
Employee Management

│

├── Header

├── Navigation

├── Dashboard

├── Employee List

├── Employee Form

└── Footer
```

Each component performs one task.

---

# Why Components?

Imagine building an application using one HTML page.

Problems

* Huge files
* Difficult maintenance
* Difficult debugging
* Difficult teamwork
* Difficult reuse

Components solve all of these problems.

---

# Component-Based Architecture

```text
App

├── Header

├── Menu

├── Dashboard

│      ├── Employee List

│      └── Employee Form

└── Footer
```

Every box is an Angular Component.

---

# Every Component Contains

Three files.

```text
employee

├── employee.ts

├── employee.html

└── employee.css
```

---

# employee.ts

Contains

* Variables
* Methods
* Business Logic

Example

```typescript
export class Employee {

    title = "Employees";

}
```

Think of it like a C# class.

---

# employee.html

Contains the UI.

```html
<h2>Employees</h2>
```

---

# employee.css

Contains component-specific CSS.

```css
h2
{
    color: blue;
}
```

---

# Component Flow

```text
employee.ts

↓

Data

↓

employee.html

↓

Browser
```

---

# Creating a Component

Angular CLI

```bash
ng generate component employee
```

Short version

```bash
ng g c employee
```

Angular creates

```text
employee

├── employee.ts

├── employee.html

├── employee.css

└── employee.spec.ts
```

Automatically.

---

# Understanding employee.ts

Angular generates something similar to:

```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-employee',
    imports: [],
    templateUrl: './employee.html',
    styleUrl: './employee.css'
})
export class Employee {

}
```

Let's understand every property.

---

# @Component

You already learned decorators.

```typescript
@Component(...)
```

This tells Angular

> "This class is a Component."

Without it,

Angular treats the class as a normal TypeScript class.

---

# selector

```typescript
selector: 'app-employee'
```

This creates a **custom HTML tag**.

```html
<app-employee></app-employee>
```

Whenever Angular sees this tag,

it loads the Employee Component.

---

# templateUrl

```typescript
templateUrl: './employee.html'
```

Angular reads the UI from

```text
employee.html
```

---

# styleUrl

```typescript
styleUrl: './employee.css'
```

Angular reads the CSS from

```text
employee.css
```

---

# Root Component

Every Angular application has one root component.

```
App
```

Angular starts here.

Everything else is placed inside this component.

---

# How to Display One Component Inside Another

This is the question every beginner asks.

Let's create an Employee Component.

---

## Step 1

Generate the component.

```bash
ng g c employee
```

---

## Step 2

employee.ts

```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-employee',
    imports: [],
    templateUrl: './employee.html',
    styleUrl: './employee.css'
})
export class Employee {

}
```

Notice

```typescript
selector: 'app-employee'
```

---

## Step 3

employee.html

```html
<h2>Employee Component</h2>

<p>Welcome Raman</p>
```

---

## Step 4

Import the Employee Component into the App Component.

app.ts

```typescript
import { Component } from '@angular/core';
import { Employee } from './employee/employee';

@Component({
    selector: 'app-root',
    imports: [Employee],
    templateUrl: './app.html',
    styleUrl: './app.css'
})
export class App {

}
```

Notice

```typescript
imports: [Employee]
```

In **Standalone Components**, this is mandatory.

---

## Step 5

Open

```text
app.html
```

Write

```html
<h1>Employee Management System</h1>

<app-employee></app-employee>
```

Notice

```html
<app-employee>
```

This comes from

```typescript
selector: 'app-employee'
```

---

# What Happens Internally?

```text
app.html

↓

<app-employee>

↓

Angular finds

selector:'app-employee'

↓

Employee Component

↓

employee.html

↓

Browser Displays UI
```

---

# Browser Output

```text
Employee Management System

Employee Component

Welcome Raman
```

---

# Component Tree

Now your application becomes

```text
App Component

│

└── Employee Component
```

Later

```text
App

├── Header

├── Menu

├── Dashboard

│      ├── Employee List

│      └── Employee Form

└── Footer
```

---

# Angular + ASP.NET Core

Suppose the user clicks

```
Employees
```

Flow

```text
Employee Component

↓

HTTP GET

↓

/api/employees

↓

ASP.NET Core Web API

↓

SQL Server

↓

JSON Response

↓

Employee Component

↓

Display Employee List
```

The component displays data.

The API provides data.

---

# Reusability

Suppose Employee Card

```text
-------------------

Raman

50000

-------------------
```

Instead of writing it 100 times,

Create

```
EmployeeCardComponent
```

Use

```html
<app-employee-card></app-employee-card>
```

wherever needed.

---

# Best Practices

✅ One responsibility per component.

✅ Keep components small.

✅ Reuse components.

✅ Put business logic in TypeScript.

✅ Keep HTML focused on presentation.

---

# Summary

| Part            | Purpose                      |
| --------------- | ---------------------------- |
| Component       | UI Building Block            |
| `@Component`    | Registers Component          |
| `selector`      | Custom HTML Tag              |
| `templateUrl`   | HTML File                    |
| `styleUrl`      | CSS File                     |
| `imports`       | Import Standalone Components |
| Root Component  | First Component              |
| Child Component | Nested Component             |

---

# Practice Exercises

### Exercise 1

Create

```bash
ng g c employee
```

Display it inside the App Component.

---

### Exercise 2

Create

```bash
ng g c department
```

Display both

```html
<app-employee></app-employee>

<app-department></app-department>
```

inside

```text
app.html
```

---

### Exercise 3

Modify

```text
employee.html
```

to display

```html
<h2>Employee Module</h2>

<p>This is my first Angular Component.</p>
```

---

### Exercise 4

Draw the component tree

```text
App

├── Employee

└── Department
```

---

# Interview Questions

1. What is a Component?
2. Why are Components used in Angular?
3. What is the purpose of `@Component`?
4. What is a selector?
5. How do you display one component inside another?
6. Why is `imports: [Employee]` required in Standalone Components?
7. What is the Root Component?
8. What is the difference between `employee.ts` and `employee.html`?

---

# Before the Next Lesson

Now that you know **how to create and display components**, we'll start writing code inside them.

## Lesson 5: Component Properties & Data Binding (Introduction)

We'll learn:

* Creating variables inside a component
* Displaying variables in HTML
* String Interpolation (`{{ }}`)
* Calling methods from HTML
* Updating values
* Understanding how Angular connects the TypeScript class with the HTML template

This is where your components become dynamic instead of just displaying static HTML.
