Excellent! 🎉

Now we begin writing **real Angular code**.

Until now, our components displayed **static HTML**.

From this lesson onward, your components will become **dynamic**.

This is one of the most important Angular concepts because you'll use it in **every single component**.

---

# Lesson 5: Component Properties & String Interpolation

## Learning Objectives

By the end of this lesson, you will understand:

* Component Properties
* Component Methods
* What is String Interpolation?
* Displaying Variables
* Displaying Numbers
* Displaying Boolean Values
* Displaying Objects
* Calling Methods from HTML
* Calling Methods with Parameters
* Angular Expression Rules
* Real ASP.NET Core Example

---

# What is a Component Property?

A property is simply a variable inside a component.

Example

```typescript
export class Employee {

    companyName = "KMIT Solutions";

    employeeName = "Raman";

    salary = 50000;

}
```

These properties store data.

Think of it exactly like a C# class.

---

## C#

```csharp
public class Employee
{
    public string Name { get; set; }
}
```

---

## TypeScript

```typescript
export class Employee {

    name = "Raman";

}
```

Almost identical.

---

# Component

Suppose

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

    companyName = "KMIT Solutions";

}
```

---

# employee.html

```html
<h2>{{ companyName }}</h2>
```

---

# Browser Output

```text
KMIT Solutions
```

---

# What is {{ }} ?

This is called

# String Interpolation

Syntax

```html
{{ expression }}
```

Angular evaluates the expression and displays the result.

---

# Flow

```text
employee.ts

↓

companyName

↓

employee.html

↓

{{ companyName }}

↓

Browser
```

---

# Display Multiple Variables

```typescript
export class Employee {

    company = "KMIT";

    trainer = "Raman";

}
```

HTML

```html
<h2>{{ company }}</h2>

<p>{{ trainer }}</p>
```

Output

```text
KMIT

Raman
```

---

# Display Numbers

```typescript
salary = 75000;
```

HTML

```html
Salary

{{ salary }}
```

Output

```text
Salary

75000
```

---

# Display Boolean

```typescript
isActive = true;
```

HTML

```html
{{ isActive }}
```

Output

```text
true
```

---

# Display Multiple Values

```typescript
name = "Raman";

salary = 50000;

department = "IT";
```

HTML

```html
Name

{{ name }}

<hr>

Salary

{{ salary }}

<hr>

Department

{{ department }}
```

Output

```text
Name

Raman

----------------

Salary

50000

----------------

Department

IT
```

---

# Expressions

Interpolation accepts expressions.

Example

```typescript
price = 500;

tax = 100;
```

HTML

```html
{{ price + tax }}
```

Output

```text
600
```

---

Another

```html
{{ 100 + 200 }}
```

Output

```text
300
```

---

# String Concatenation

```typescript
firstName = "Raman";

lastName = "Sharma";
```

HTML

```html
{{ firstName + " " + lastName }}
```

Output

```text
Raman Sharma
```

---

# Calling Methods

Suppose

employee.ts

```typescript
export class Employee {

    getCompany()
    {
        return "KMIT Solutions";
    }

}
```

HTML

```html
{{ getCompany() }}
```

Output

```text
KMIT Solutions
```

Angular can call component methods.

---

# Another Example

```typescript
getSalary()
{
    return 50000;
}
```

HTML

```html
{{ getSalary() }}
```

Output

```text
50000
```

---

# Method with Parameters

```typescript
getBonus(salary:number)
{
    return salary * 0.10;
}
```

HTML

```html
{{ getBonus(50000) }}
```

Output

```text
5000
```

---

# Returning an Object

Suppose

```typescript
employee = {

    id:1,

    name:"Raman"

};
```

HTML

```html
{{ employee.name }}
```

Output

```text
Raman
```

---

# Arrays

```typescript
cities = [

"Delhi",

"Mumbai",

"Bangalore"

];
```

HTML

```html
{{ cities[0] }}
```

Output

```text
Delhi
```

---

# Date Example

```typescript
today = new Date();
```

HTML

```html
{{ today }}
```

Output

```text
Thu Jul 30 2026 ...
```

Later we'll use Angular Pipes to format dates.

---

# Angular Expressions

Allowed

```html
{{ 10 + 20 }}
```

```html
{{ employee.name }}
```

```html
{{ getSalary() }}
```

```html
{{ salary * 2 }}
```

---

# Not Allowed

Assignments

```html
{{ salary = 100 }}
```

❌

Loops

```html
{{ for(...) }}
```

❌

Creating objects

```html
{{ new Employee() }}
```

❌

Complex business logic

❌

Keep templates simple.

---

# Why?

Business logic belongs in

```text
employee.ts
```

Presentation belongs in

```text
employee.html
```

This separation makes the code easier to read and maintain.

---

# Real ASP.NET Core Comparison

Suppose ASP.NET Core returns

```json
{
"id":1,
"name":"Raman",
"salary":50000
}
```

Angular

employee.ts

```typescript
employee = {

id:1,

name:"Raman",

salary:50000

};
```

HTML

```html
<h2>{{ employee.name }}</h2>

<p>{{ employee.salary }}</p>
```

Browser

```text
Raman

50000
```

Later

Instead of hardcoding

```typescript
employee = {...}
```

We'll retrieve this object using

```typescript
this.http.get<Employee>()
```

from an ASP.NET Core API.

---

# Component Flow

```text
ASP.NET Core

↓

JSON

↓

Employee Component

↓

Properties

↓

Interpolation

↓

Browser
```

---

# Best Practices

✅ Keep HTML simple.

✅ Put calculations inside methods.

✅ Store data in component properties.

✅ Use interpolation only for displaying values.

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

    company = "KMIT Solutions";

    trainer = "Raman";

    salary = 50000;

    getBonus()
    {
        return this.salary * 0.10;
    }

}
```

employee.html

```html
<h2>{{ company }}</h2>

<p>Trainer : {{ trainer }}</p>

<p>Salary : {{ salary }}</p>

<p>Bonus : {{ getBonus() }}</p>
```

Browser

```text
KMIT Solutions

Trainer : Raman

Salary : 50000

Bonus : 5000
```

---

# Summary

| Feature       | Example            |
| ------------- | ------------------ |
| Property      | `company = "KMIT"` |
| Number        | `salary = 50000`   |
| Boolean       | `isActive = true`  |
| Object        | `employee.name`    |
| Array         | `cities[0]`        |
| Method        | `getBonus()`       |
| Interpolation | `{{ company }}`    |

---

# Practice Exercises

### Exercise 1

Create properties

```typescript
company

trainer

course

fees
```

Display all using interpolation.

---

### Exercise 2

Create

```typescript
employee = {

id:101,

name:"Raman",

salary:70000

};
```

Display all properties.

---

### Exercise 3

Create

```typescript
getFullName()
```

Return

```text
Raman Sharma
```

Display it.

---

### Exercise 4

Create

```typescript
getTax()

getBonus()
```

Display both in HTML.

---

# Interview Questions

1. What is String Interpolation?
2. What does `{{ }}` mean in Angular?
3. Can interpolation call methods?
4. Can interpolation display objects?
5. Why should business logic be kept in the component instead of the template?
6. What kinds of expressions are allowed inside interpolation?
7. What is the difference between a component property and a method?

---

# Before the Next Lesson

So far, you've learned how to **display data** using interpolation.

In the next lesson, we'll learn **Property Binding**, which is different from interpolation.

You'll understand the difference between:

```html
{{ value }}
```

and

```html
[property]="value"
```

This is a very common Angular interview topic and something you'll use constantly for images, input fields, buttons, links, and many other HTML element properties.
