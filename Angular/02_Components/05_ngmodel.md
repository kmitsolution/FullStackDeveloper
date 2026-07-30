
So far you've learned:

* ✅ Interpolation → Display data
* ✅ Property Binding → Send data from Component → HTML
* ✅ Event Binding → Send user actions from HTML → Component

Now we'll combine them.

---

# Lesson 8: Two-Way Data Binding

## Learning Objectives

By the end of this lesson, you will understand:

* What is Two-Way Data Binding?
* Why do we need it?
* `ngModel`
* How `ngModel` works internally
* Importing FormsModule
* Creating Forms
* Real-Time UI Updates
* Angular + ASP.NET Core Example

---

# Data Flow So Far

## Interpolation

```text
Component

↓

HTML
```

One-way.

---

## Property Binding

```text
Component

↓

HTML Element
```

One-way.

---

## Event Binding

```text
User

↓

HTML

↓

Component
```

One-way.

---

# Two-Way Data Binding

Now data moves in **both directions**.

```text
Component

⇅

HTML
```

If the component changes,

the UI changes.

If the user types,

the component changes automatically.

---

# Real World Example

Employee Name

```text
------------------

Raman

------------------
```

User changes it to

```text
------------------

John

------------------
```

Immediately

```typescript
employeeName
```

also becomes

```text
John
```

No extra code.

---

# What is ngModel?

Angular provides

```html
[(ngModel)]
```

for Two-Way Data Binding.

Syntax

```html
[(ngModel)]="variable"
```

Notice

```text
[( )]
```

People call it

> **Banana in a Box**

Because

```text
[ ( ) ]
```

looks like a banana inside a box.

---

# Why "Banana in a Box"?

It combines:

Property Binding

```html
[value]
```

*

Event Binding

```html
(input)
```

into

```html
[(ngModel)]
```

---

# Visual Flow

```text
Component

↓

Textbox

↓

User Types

↓

Component Updated
```

Both directions.

---

# First Example

employee.ts

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
    selector:'app-employee',
    imports:[FormsModule],
    templateUrl:'./employee.html',
    styleUrl:'./employee.css'
})
export class Employee {

    employeeName = "Raman";

}
```

Notice

```typescript
imports:[FormsModule]
```

This is **mandatory**.

Without it,

Angular doesn't recognize

```html
ngModel
```

---

# employee.html

```html
<input [(ngModel)]="employeeName">

<hr>

{{ employeeName }}
```

---

# Browser

Initially

```text
-----------------

Raman

-----------------

Raman
```

---

Now type

```text
John
```

Immediately

```text
-----------------

John

-----------------

John
```

No button.

No click.

Everything updates automatically.

---

# Flow

```text
employeeName

↓

Textbox

↓

User Types

↓

employeeName Updated

↓

Interpolation Updated
```

---

# How Does ngModel Work?

Internally,

Angular converts

```html
[(ngModel)]="employeeName"
```

into

Property Binding

```html
[value]="employeeName"
```

plus

Event Binding

```html
(input)="employeeName = ..."
```

So

```text
[(ngModel)]
```

is just a shortcut.

---

# Multiple Fields

employee.ts

```typescript
employeeName = "Raman";

salary = 50000;

department = "IT";
```

employee.html

```html
Name

<input [(ngModel)]="employeeName">

<hr>

Salary

<input [(ngModel)]="salary">

<hr>

Department

<input [(ngModel)]="department">

<hr>

{{ employeeName }}

<br>

{{ salary }}

<br>

{{ department }}
```

---

# Browser

User edits

Name

↓

Salary

↓

Department

Everything updates instantly.

---

# Employee Object

Instead of separate variables,

we usually use an object.

employee.ts

```typescript
employee = {

    id:101,

    name:"Raman",

    salary:50000

};
```

employee.html

```html
Name

<input [(ngModel)]="employee.name">

<hr>

Salary

<input [(ngModel)]="employee.salary">

<hr>

{{ employee.name }}

<br>

{{ employee.salary }}
```

This is exactly how enterprise applications work.

---

# Why Use an Object?

Later

We'll send

```typescript
employee
```

directly to

ASP.NET Core.

Example

```typescript
this.http.post(
"/api/employees",
this.employee
);
```

No need to create multiple variables.

---

# Different Input Types

Text

```html
<input [(ngModel)]="employee.name">
```

---

Number

```html
<input
type="number"
[(ngModel)]="employee.salary">
```

---

Checkbox

```html
<input
type="checkbox"
[(ngModel)]="employee.isActive">
```

---

Textarea

```html
<textarea
[(ngModel)]="employee.address">
</textarea>
```

---

Select

```html
<select [(ngModel)]="employee.department">

<option>IT</option>

<option>HR</option>

<option>Sales</option>

</select>
```

---

# Real-Time Example

employee.ts

```typescript
employeeName = "";
```

employee.html

```html
<input [(ngModel)]="employeeName">

<h2>

Welcome

{{ employeeName }}

</h2>
```

User types

```text
Raman
```

Output

```text
Welcome Raman
```

Updates

every keystroke.

---

# Why FormsModule?

Suppose you remove

```typescript
imports:[FormsModule]
```

Angular shows

```text
Can't bind to 'ngModel'
```

because Angular doesn't know

what

```text
ngModel
```

is.

FormsModule provides it.

---

# Angular + ASP.NET Core

Suppose

Employee Form

```text
Name

Salary

Department

Save
```

User enters

```text
Raman

50000

IT
```

Flow

```text
Textbox

↓

Employee Object

↓

HTTP POST

↓

ASP.NET Core API

↓

SQL Server
```

Exactly what we'll build later.

---

# Complete Example

employee.ts

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
    selector:'app-employee',
    imports:[FormsModule],
    templateUrl:'./employee.html',
    styleUrl:'./employee.css'
})
export class Employee {

    employee = {

        id:101,

        name:"Raman",

        salary:50000,

        department:"IT"

    };

}
```

employee.html

```html
<h2>Employee Form</h2>

<label>Name</label>

<input [(ngModel)]="employee.name">

<br><br>

<label>Salary</label>

<input
type="number"
[(ngModel)]="employee.salary">

<br><br>

<label>Department</label>

<input [(ngModel)]="employee.department">

<hr>

<h3>Preview</h3>

Name : {{ employee.name }}

<br>

Salary : {{ employee.salary }}

<br>

Department : {{ employee.department }}
```

---

# Browser

Initially

```text
Employee Form

Name

Raman

Salary

50000

Department

IT
```

User edits

```text
Name

John
```

Preview

Immediately

```text
John
```

---

# Summary

| Binding          | Direction                 |
| ---------------- | ------------------------- |
| Interpolation    | Component → HTML          |
| Property Binding | Component → HTML Property |
| Event Binding    | HTML Event → Component    |
| Two-Way Binding  | Component ⇄ HTML          |

---

# Property + Event = Two-Way

```text
Property Binding

+

Event Binding

=

[(ngModel)]
```

Remember this.

It's a very common interview question.

---

# Best Practices

✅ Use objects instead of many individual variables.

```typescript
employee = {

}
```

instead of

```typescript
name

salary

department

address

city
```

---

✅ Import

```typescript
FormsModule
```

only in components that need template-driven forms (or at the appropriate application level in larger apps).

---

# Practice Exercises

### Exercise 1

Create a textbox.

Display

```text
Welcome Raman
```

while typing.

---

### Exercise 2

Create

```typescript
employee = {

name:"",

salary:0

}
```

Bind both properties.

---

### Exercise 3

Create

* Name
* Salary
* Department
* Address

Display a live preview below.

---

### Exercise 4

Add

```text
Active
```

checkbox using

```html
[(ngModel)]
```

Display

```text
true

false
```

---

# Interview Questions

1. What is Two-Way Data Binding?
2. What is `ngModel`?
3. Why is `FormsModule` required?
4. What is "Banana in a Box"?
5. How does `[(ngModel)]` work internally?
6. What is the difference between Property Binding and Two-Way Data Binding?
7. Why is Two-Way Data Binding useful for forms?

---

# Before the Next Lesson

Now you can:

* Display data
* React to user actions
* Edit data in forms

But we still can't **show or hide elements**, or **display lists of data**.

That's where Angular **Directives** come in.

## Lesson 9: Directives (Part 1)

We'll learn:

* What is a Directive?
* `@if` (modern Angular control flow)
* `@for` (modern Angular looping)
* `@switch`
* Conditional rendering
* Looping through employee lists
* Replacing the older `*ngIf` and `*ngFor` syntax with the modern Angular approach

Since you're learning **Angular 20**, I'll teach the **new control flow syntax** (`@if`, `@for`, `@switch`) first, and I'll also show the older `*ngIf` and `*ngFor` syntax because you'll encounter it in many existing enterprise applications.
