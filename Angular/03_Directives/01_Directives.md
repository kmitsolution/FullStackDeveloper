This is one of the **most important Angular lessons**.

Until now, we've displayed **one employee**.

But real applications work with **many employees**.

Sometimes we need to:

* Show data
* Hide data
* Display a list
* Display different content based on conditions

Angular provides **Directives** for this.

> **Important:** Since you're learning **Angular 20**, we'll learn the **new Control Flow syntax** (`@if`, `@for`, `@switch`). I'll also show the older `*ngIf`, `*ngFor`, and `*ngSwitch` because you'll definitely encounter them in enterprise projects.

---

# Lesson 9: Directives (Part 1) – Modern Angular Control Flow

## Learning Objectives

By the end of this lesson, you will understand:

* What is a Directive?
* Why Directives are needed
* Types of Directives
* What is Control Flow?
* `@if`
* `@else`
* `@for`
* `track`
* `@empty`
* `@switch`
* Difference between Angular 20 and older Angular versions

---

# What is a Directive?

A Directive is an instruction that tells Angular **how to modify the UI**.

Examples:

* Show an element
* Hide an element
* Repeat an element
* Apply styles
* Change behavior

A directive doesn't create new UI by itself—it changes how existing UI is rendered.

---

# Real World Example

Suppose the employee is active.

Display

```text
Edit Button
```

Otherwise

Hide it.

Without directives

Impossible.

With Angular

Very easy.

---

# Types of Directives

Angular has three types.

| Type                 | Purpose                                                                  |
| -------------------- | ------------------------------------------------------------------------ |
| Component            | A directive with a template (every component is technically a directive) |
| Structural Directive | Changes the DOM (add/remove elements)                                    |
| Attribute Directive  | Changes the appearance or behavior of an element                         |

Today we'll focus on **Structural Directives (Control Flow).**

---

# Control Flow

Think of TypeScript.

```typescript
if(...)
{

}

for(...)
{

}

switch(...)
{

}
```

Angular has similar concepts for HTML.

---

# @if

Suppose

employee.ts

```typescript
isLoggedIn = true;
```

employee.html

```html
@if (isLoggedIn) {

    <h2>Welcome Raman</h2>

}
```

Browser

```text
Welcome Raman
```

---

Change

```typescript
isLoggedIn = false;
```

Browser

Nothing appears.

---

# Flow

```text
isLoggedIn

↓

@if

↓

Show HTML

or

Hide HTML
```

---

# @else

employee.ts

```typescript
isLoggedIn = false;
```

employee.html

```html
@if (isLoggedIn) {

    <h2>Welcome Raman</h2>

}
@else {

    <h2>Please Login</h2>

}
```

Browser

```text
Please Login
```

---

Change

```typescript
isLoggedIn = true;
```

Browser

```text
Welcome Raman
```

---

# Multiple Conditions

employee.ts

```typescript
salary = 70000;
```

employee.html

```html
@if (salary > 50000) {

    <h2>Senior Employee</h2>

}
@else {

    <h2>Junior Employee</h2>

}
```

Output

```text
Senior Employee
```

---

# @for

Real applications display lists.

Suppose

employee.ts

```typescript
employees = [

    "Raman",

    "John",

    "David",

    "Sara"

];
```

employee.html

```html
@for (employee of employees; track employee) {

    <p>{{ employee }}</p>

}
```

Output

```text
Raman

John

David

Sara
```

---

# What Does `track` Mean?

```html
@for (employee of employees; track employee)
```

Angular uses the `track` expression to identify each item efficiently when the list changes.

For simple string lists, tracking by the string value is fine.

For objects, it's better to track by a unique ID.

Example

```typescript
employees = [
    { id: 1, name: "Raman" },
    { id: 2, name: "John" }
];
```

HTML

```html
@for (employee of employees; track employee.id) {

    <p>{{ employee.name }}</p>

}
```

This improves rendering performance.

---

# Using Objects

employee.ts

```typescript
employees = [

    { id:1, name:"Raman", salary:50000 },

    { id:2, name:"John", salary:60000 },

    { id:3, name:"David", salary:70000 }

];
```

employee.html

```html
@for (employee of employees; track employee.id) {

    <hr>

    <p>ID : {{ employee.id }}</p>

    <p>Name : {{ employee.name }}</p>

    <p>Salary : {{ employee.salary }}</p>

}
```

Output

```text
ID : 1

Name : Raman

Salary : 50000

----------------

ID : 2

Name : John

Salary : 60000
```

---

# Loop Variables

Angular provides useful loop variables.

```html
@for (employee of employees; track employee.id) {

    {{ $index }}

}
```

Output

```text
0

1

2
```

Other useful variables:

| Variable | Meaning       |
| -------- | ------------- |
| `$index` | Current index |
| `$first` | First item?   |
| `$last`  | Last item?    |
| `$even`  | Even row?     |
| `$odd`   | Odd row?      |

Example

```html
@for (employee of employees; track employee.id) {

    <p>{{ $index + 1 }} - {{ employee.name }}</p>

}
```

Output

```text
1 - Raman

2 - John

3 - David
```

---

# @empty

Suppose

```typescript
employees = [];
```

HTML

```html
@for (employee of employees; track employee.id) {

    <p>{{ employee.name }}</p>

}
@empty {

    <h2>No Employees Found</h2>

}
```

Output

```text
No Employees Found
```

Very useful in CRUD applications.

---

# @switch

employee.ts

```typescript
department = "IT";
```

employee.html

```html
@switch (department) {

    @case ("IT") {

        <h2>Information Technology</h2>

    }

    @case ("HR") {

        <h2>Human Resources</h2>

    }

    @default {

        <h2>Unknown Department</h2>

    }

}
```

Output

```text
Information Technology
```

---

# Real ASP.NET Core Example

Suppose

ASP.NET Core returns

```json
[
    {
        "id":1,
        "name":"Raman",
        "salary":50000
    },
    {
        "id":2,
        "name":"John",
        "salary":60000
    }
]
```

Angular

```typescript
employees = response;
```

HTML

```html
@for (employee of employees; track employee.id) {

    <tr>

        <td>{{ employee.id }}</td>

        <td>{{ employee.name }}</td>

        <td>{{ employee.salary }}</td>

    </tr>

}
```

This is exactly how you'll display API data.

---

# Angular 20 vs Older Angular

### Angular 20 (Recommended)

```html
@if (isLoggedIn) {

    <h2>Welcome</h2>

}
```

```html
@for (employee of employees; track employee.id) {

    <p>{{ employee.name }}</p>

}
```

---

### Older Angular

```html
<h2 *ngIf="isLoggedIn">

Welcome

</h2>
```

```html
<div *ngFor="let employee of employees">

{{ employee.name }}

</div>
```

Both work, but for **new Angular projects**, prefer the modern syntax.

---

# Complete Example

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

    employees = [

        { id:1, name:"Raman", salary:50000 },

        { id:2, name:"John", salary:60000 },

        { id:3, name:"David", salary:70000 }

    ];

}
```

employee.html

```html
<h2>Employee List</h2>

@for (employee of employees; track employee.id) {

    <hr>

    <p>ID : {{ employee.id }}</p>

    <p>Name : {{ employee.name }}</p>

    <p>Salary : {{ employee.salary }}</p>

}
```

---

# Summary

| Directive | Purpose                                    |
| --------- | ------------------------------------------ |
| `@if`     | Show/Hide content                          |
| `@else`   | Alternative content                        |
| `@for`    | Loop through a collection                  |
| `track`   | Improve list rendering                     |
| `@empty`  | Display content when a collection is empty |
| `@switch` | Display different content based on a value |

---

# Best Practices

✅ Use `track employee.id` for lists of objects.

✅ Keep control flow simple in templates.

✅ Use `@empty` to provide a good user experience when no data is available.

---

# Practice Exercises

### Exercise 1

Create:

```typescript
isAdmin = true;
```

Display:

```text
Welcome Administrator
```

Otherwise display:

```text
Access Denied
```

using `@if`.

---

### Exercise 2

Create an array of five employee names and display them using `@for`.

---

### Exercise 3

Create an array of employee objects with:

* Id
* Name
* Salary

Display all three properties.

---

### Exercise 4

If the employee array is empty, display:

```text
No Employees Available
```

using `@empty`.

---

# Interview Questions

1. What is a Directive?
2. What is the difference between a Component and a Directive?
3. What is the purpose of `@if`?
4. What is the purpose of `@for`?
5. Why should you use `track` in `@for`?
6. What is `@empty`?
7. What is the difference between Angular 20's `@if` and the older `*ngIf`?
8. When displaying data from an ASP.NET Core API, which directive is commonly used?

---

# Before the Next Lesson

You've now learned how to:

* Display values
* Handle user actions
* Bind form fields
* Show and hide content
* Display collections

The next logical step is to **organize application logic**.

## Lesson 10: Services & Dependency Injection

You'll learn:

* Why Services are needed
* Creating a Service
* Dependency Injection (DI)
* Singleton Services
* Injecting a Service into a Component
* Calling an ASP.NET Core API from a Service (instead of directly from the component)

This is how enterprise Angular applications are structured, and it closely mirrors the Dependency Injection pattern you're already familiar with from ASP.NET Core.
