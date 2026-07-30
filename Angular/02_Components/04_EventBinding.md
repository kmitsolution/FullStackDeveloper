Until now, our Angular application could **display data**.

Now we're going to make it **respond to user actions**.

This is where Angular applications become interactive.

For example:

* Click **Save** → Save Employee
* Click **Delete** → Delete Employee
* Type in a textbox → Search Employees
* Select a department → Filter Employees

All of this is done using **Event Binding**.

---

# Lesson 7: Event Binding

## Learning Objectives

By the end of this lesson, you will understand:

* What is Event Binding?
* Why Event Binding is needed
* Event Binding Syntax
* Button Click Events
* Mouse Events
* Keyboard Events
* Input Events
* Passing Parameters
* The `$event` Object
* Event Binding + ASP.NET Core Example

---

# What is an Event?

An event is **an action performed by the user**.

Examples:

* Mouse Click
* Double Click
* Key Press
* Typing
* Mouse Hover
* Mouse Leave
* Focus
* Blur

---

# Real World Example

Suppose the user clicks

```text
Save
```

The application should

```text
Save Employee
```

That click is called an **event**.

---

# Event Binding

Event Binding connects

```text
User Action

↓

Component Method
```

---

# Syntax

```html
(event)="method()"
```

Notice

Parentheses

```html
(click)
```

Square brackets were for **Property Binding**.

Parentheses are for **Events**.

---

# Remember

| Binding          | Syntax       |
| ---------------- | ------------ |
| Interpolation    | `{{ }}`      |
| Property Binding | `[property]` |
| Event Binding    | `(event)`    |

This is one of the most common Angular interview questions.

---

# First Example

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

    saveEmployee() {
        alert("Employee Saved");
    }

}
```

employee.html

```html
<button (click)="saveEmployee()">

Save

</button>
```

---

# Browser

Click

```text
Save
```

Output

```text
Employee Saved
```

---

# Flow

```text
User

↓

Button Click

↓

(click)

↓

saveEmployee()

↓

Alert
```

---

# Calling Another Method

```typescript
deleteEmployee() {

    alert("Employee Deleted");

}
```

HTML

```html
<button (click)="deleteEmployee()">

Delete

</button>
```

---

# Multiple Buttons

```html
<button (click)="saveEmployee()">

Save

</button>

<button (click)="deleteEmployee()">

Delete

</button>
```

Each button calls a different method.

---

# Updating a Variable

employee.ts

```typescript
message = "";

saveEmployee() {

    this.message = "Employee Saved Successfully";

}
```

employee.html

```html
<button (click)="saveEmployee()">

Save

</button>

<hr>

{{ message }}
```

---

Initially

```text
(empty)
```

Click Save

Browser

```text
Employee Saved Successfully
```

Notice

Angular automatically updates the UI.

No manual DOM manipulation.

---

# Counter Example

employee.ts

```typescript
count = 0;

increment() {

    this.count++;

}
```

employee.html

```html
<h2>{{ count }}</h2>

<button (click)="increment()">

Increase

</button>
```

Output

Click

```text
Increase
```

Display

```text
0

↓

1

↓

2

↓

3
```

Angular refreshes the UI automatically.

---

# Passing Parameters

Suppose

```typescript
showMessage(name: string) {

    alert(name);

}
```

HTML

```html
<button (click)="showMessage('Raman')">

Click

</button>
```

Output

```text
Raman
```

---

# Passing Numbers

```typescript
calculateBonus(salary: number) {

    alert(salary * 0.10);

}
```

HTML

```html
<button (click)="calculateBonus(50000)">

Bonus

</button>
```

Output

```text
5000
```

---

# Mouse Events

Mouse Enter

```html
<h2 (mouseenter)="showMessage('Mouse Enter')">

Angular

</h2>
```

---

Mouse Leave

```html
<h2 (mouseleave)="showMessage('Mouse Left')">

Angular

</h2>
```

---

Double Click

```html
<button (dblclick)="saveEmployee()">

Double Click

</button>
```

---

# Keyboard Events

Suppose

```typescript
keyPressed() {

    console.log("Key Pressed");

}
```

HTML

```html
<input (keyup)="keyPressed()">
```

Every key release calls

```typescript
keyPressed()
```

---

Another

```html
<input (keydown)="keyPressed()">
```

---

# Focus Event

```html
<input (focus)="showMessage('Focused')">
```

When the textbox receives focus.

---

# Blur Event

```html
<input (blur)="showMessage('Lost Focus')">
```

When the textbox loses focus.

---

# Input Event

```html
<input (input)="keyPressed()">
```

Every change in the textbox fires the event.

---

# The `$event` Object

Sometimes we need information about the event.

Example

```html
<input (keyup)="showKey($event)">
```

Component

```typescript
showKey(event: KeyboardEvent) {

    console.log(event);

}
```

Angular passes the event object automatically.

---

# Reading Typed Characters

```typescript
showKey(event: KeyboardEvent) {

    console.log(event.key);

}
```

Suppose the user types

```text
A
```

Output

```text
A
```

---

# Reading Input Value

```html
<input (input)="showValue($event)">
```

Component

```typescript
showValue(event: Event) {

    const input = event.target as HTMLInputElement;

    console.log(input.value);

}
```

Suppose the user types

```text
Angular
```

Console

```text
Angular
```

---

# Why Type Casting?

`event.target` is of type `EventTarget`, which doesn't expose a `value` property.

So we tell TypeScript:

```typescript
const input = event.target as HTMLInputElement;
```

Now TypeScript knows it is an `<input>` element and allows:

```typescript
input.value
```

---

# Real ASP.NET Core Example

Employee List

User

Clicks

```text
Load Employees
```

Flow

```text
Button Click

↓

loadEmployees()

↓

HTTP GET

↓

ASP.NET Core API

↓

Employee Data

↓

Display Table
```

Code

```html
<button (click)="loadEmployees()">

Load Employees

</button>
```

Later

```typescript
loadEmployees() {

    this.http.get<Employee[]>(
        "https://localhost:5001/api/employees"
    ).subscribe(data => {

        this.employees = data;

    });

}
```

We'll build this in the HTTP Client lessons.

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

    count = 0;

    increment() {

        this.count++;

    }

    reset() {

        this.count = 0;

    }

}
```

employee.html

```html
<h2>{{ count }}</h2>

<button (click)="increment()">

Increase

</button>

<button (click)="reset()">

Reset

</button>
```

Browser

```text
0

[Increase]

[Reset]
```

Click

Increase

```text
1

↓

2

↓

3
```

Click

Reset

```text
0
```

---

# Summary

| Event        | Example        |
| ------------ | -------------- |
| Click        | `(click)`      |
| Double Click | `(dblclick)`   |
| Key Up       | `(keyup)`      |
| Key Down     | `(keydown)`    |
| Input        | `(input)`      |
| Focus        | `(focus)`      |
| Blur         | `(blur)`       |
| Mouse Enter  | `(mouseenter)` |
| Mouse Leave  | `(mouseleave)` |

---

# Property Binding vs Event Binding

| Property Binding | Event Binding                   |
| ---------------- | ------------------------------- |
| `[value]="name"` | `(click)="save()"`              |
| Component → HTML | HTML → Component                |
| Sends data to UI | Sends user actions to component |

---

# Best Practices

✅ Keep event handlers small.

```typescript
saveEmployee() {

}
```

✅ Move business logic to services later.

✅ Use meaningful method names.

```typescript
saveEmployee()

deleteEmployee()

loadEmployees()
```

Avoid names like

```typescript
click1()

button2()
```

---

# Practice Exercises

### Exercise 1

Create a button that displays

```text
Welcome Raman
```

when clicked.

---

### Exercise 2

Create a counter with:

* Increase
* Decrease
* Reset

buttons.

---

### Exercise 3

Create a textbox and print every key pressed using `$event`.

---

### Exercise 4

Create a textbox and display the current value below it as the user types.

---

# Interview Questions

1. What is Event Binding?
2. What is the syntax of Event Binding?
3. What is the difference between Property Binding and Event Binding?
4. What is the `$event` object?
5. Why do we cast `event.target` to `HTMLInputElement`?
6. Name five commonly used Angular events.
7. How would you call an ASP.NET Core API when a button is clicked?

---

# Before the Next Lesson

You've now learned:

* ✅ String Interpolation
* ✅ Property Binding
* ✅ Event Binding

The next lesson combines all three into one of Angular's most famous features:

# Lesson 8: Two-Way Data Binding

You'll learn:

* `[(ngModel)]`
* How data flows from the component to the UI **and back**
* Building forms
* Real-time updates
* Creating an Employee Entry form

This is one of the most frequently used features in Angular forms and is essential before we start building CRUD screens with an ASP.NET Core backend.
