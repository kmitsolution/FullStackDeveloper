Excellent! This lesson is one of the **most important concepts in Angular**.

Many beginners think **Interpolation** and **Property Binding** are the same.

They are **not**.

Understanding the difference will make Angular much easier.

---

# Lesson 6: Property Binding

## Learning Objectives

By the end of this lesson, you will understand:

* What is Property Binding?
* Why do we need Property Binding?
* Difference between Interpolation and Property Binding
* Binding HTML Properties
* Binding Images
* Binding Buttons
* Binding Input Fields
* Binding CSS Classes
* Binding Styles
* Binding Boolean Properties
* Real ASP.NET Core Example

---

# Recap

Last lesson we learned

```html
{{ company }}
```

This is called

**String Interpolation**

It displays text.

Example

employee.ts

```typescript
company = "KMIT Solutions";
```

employee.html

```html
<h2>{{ company }}</h2>
```

Output

```
KMIT Solutions
```

Easy.

---

# What is Property Binding?

Property Binding sets the **property of an HTML element**.

Syntax

```html
[property]="expression"
```

Notice

Square brackets

```html
[property]
```

---

# Why Do We Need Property Binding?

Suppose we have

```typescript
imagePath =
"https://angular.dev/assets/images/press-kit/angular_icon_gradient.gif";
```

Can we do this?

```html
<img src="{{ imagePath }}">
```

Yes!

It works.

Then why do we need Property Binding?

Because interpolation converts everything to **text**.

Property Binding updates the **actual DOM property**.

Angular recommends Property Binding whenever you're setting an HTML property.

---

# First Example

employee.ts

```typescript
company = "KMIT Solutions";
```

employee.html

```html
<input [value]="company">
```

Browser

```
-----------------------

KMIT Solutions

-----------------------
```

Notice

The value of the textbox comes from

```typescript
company
```

---

# Flow

```text
employee.ts

↓

company

↓

[value]

↓

Textbox
```

---

# Compare with Interpolation

Interpolation

```html
<h2>{{ company }}</h2>
```

Displays text.

Property Binding

```html
<input [value]="company">
```

Sets the value property.

Different purposes.

---

# Image Example

employee.ts

```typescript
logo =
"https://angular.dev/assets/images/press-kit/angular_icon_gradient.gif";
```

employee.html

```html
<img [src]="logo">
```

Browser

Shows the image.

Notice

```html
[src]
```

This is Property Binding.

---

# Width Example

```typescript
imageWidth = 200;
```

HTML

```html
<img
[src]="logo"
[width]="imageWidth">
```

Output

Image width becomes

```
200 pixels
```

---

# Button Example

Suppose

```typescript
isDisabled = true;
```

HTML

```html
<button [disabled]="isDisabled">

Save

</button>
```

Browser

```
[ Save ]

Disabled
```

---

Change

```typescript
isDisabled = false;
```

Button becomes enabled.

---

# Flow

```text
employee.ts

↓

isDisabled

↓

disabled Property

↓

Button
```

---

# Input Placeholder

```typescript
placeholderText =
"Enter Employee Name";
```

HTML

```html
<input
[placeholder]="placeholderText">
```

Output

```
---------------------

Enter Employee Name

---------------------
```

---

# Hyperlink Example

```typescript
website =
"https://www.kmitcourses.com";
```

HTML

```html
<a [href]="website">

Visit Website

</a>
```

Angular sets

```html
href
```

property.

---

# Title Property

```typescript
tooltip =
"Employee Information";
```

HTML

```html
<h2 [title]="tooltip">

Employee

</h2>
```

Hover

Displays

```
Employee Information
```

---

# Boolean Properties

Checkbox

```typescript
isChecked = true;
```

HTML

```html
<input
type="checkbox"
[checked]="isChecked">
```

Output

☑ Checked

---

Readonly

```typescript
isReadOnly = true;
```

HTML

```html
<input
[readonly]="isReadOnly">
```

---

# Property Binding with Expressions

```typescript
salary = 50000;
```

HTML

```html
<input
[value]="salary + 5000">
```

Output

```
55000
```

Expressions are allowed.

---

# Binding CSS Classes

```typescript
isError = true;
```

HTML

```html
<p
[class.error]="isError">

Invalid Employee

</p>
```

Suppose CSS

```css
.error
{
color:red;
}
```

Output

Text becomes red.

We'll learn this more deeply with `ngClass`.

---

# Binding Styles

```typescript
fontSize = 30;
```

HTML

```html
<h2
[style.font-size.px]="fontSize">

Angular

</h2>
```

Output

Font size

30px

---

# Compare Interpolation vs Property Binding

Interpolation

```html
<h2>{{ company }}</h2>
```

Displays

Text

---

Property Binding

```html
<input [value]="company">
```

Sets

HTML Property

---

Another Example

Interpolation

```html
{{ imageWidth }}
```

Displays

```
200
```

Property Binding

```html
<img [width]="imageWidth">
```

Actually changes the image width.

---

# Interpolation Internally

Suppose

```html
{{ company }}
```

Angular converts it into text.

---

Property Binding

```html
[value]="company"
```

Angular updates

DOM Property.

---

# Can We Use Interpolation Here?

Suppose

```html
<img src="{{logo}}">
```

Works.

Angular internally converts it.

However,

Angular recommends

```html
<img [src]="logo">
```

because it directly binds to the DOM property.

---

# Real ASP.NET Core Example

Suppose API returns

```json
{
"name":"Raman",

"photo":"raman.jpg",

"isActive":true
}
```

Angular

employee.ts

```typescript
employee = {

name:"Raman",

photo:"raman.jpg",

isActive:true

};
```

HTML

```html
<h2>

{{ employee.name }}

</h2>

<img [src]="employee.photo">

<button
[disabled]="!employee.isActive">

Edit

</button>
```

Browser

Displays

```
Raman

Photo

Enabled Button
```

---

# Complete Example

employee.ts

```typescript
import { Component } from '@angular/core';

@Component({
    selector:'app-employee',
    imports:[],
    templateUrl:'./employee.html',
    styleUrl:'./employee.css'
})
export class Employee {

    company = "KMIT Solutions";

    logo = "https://angular.dev/assets/images/press-kit/angular_icon_gradient.gif";

    imageWidth = 150;

    isDisabled = false;

    placeholder = "Enter Employee Name";

}
```

employee.html

```html
<h2>{{ company }}</h2>

<img
[src]="logo"
[width]="imageWidth">

<br><br>

<input
[placeholder]="placeholder">

<br><br>

<button
[disabled]="isDisabled">

Save

</button>
```

---

# Summary

| Property    | Example                |
| ----------- | ---------------------- |
| Value       | `[value]="company"`    |
| Source      | `[src]="logo"`         |
| Width       | `[width]="200"`        |
| Disabled    | `[disabled]="true"`    |
| Placeholder | `[placeholder]="text"` |
| Checked     | `[checked]="true"`     |
| Readonly    | `[readonly]="true"`    |
| Href        | `[href]="website"`     |

---

# Interpolation vs Property Binding

| Interpolation      | Property Binding                         |
| ------------------ | ---------------------------------------- |
| `{{ company }}`    | `[value]="company"`                      |
| Displays text      | Sets DOM property                        |
| Used in text nodes | Used for HTML element properties         |
| Output is text     | Output can be string, number, or boolean |

---

# Best Practices

✅ Use **Interpolation** for displaying text.

```html
<h2>{{ company }}</h2>
```

✅ Use **Property Binding** for HTML properties.

```html
<input [value]="company">

<img [src]="logo">

<button [disabled]="true">
```

This is the convention you'll see in Angular applications.

---

# Practice Exercises

### Exercise 1

Create a textbox whose value comes from:

```typescript
employeeName = "Raman";
```

---

### Exercise 2

Display an image using

```typescript
photo = "https://angular.dev/assets/images/press-kit/angular_icon_gradient.gif";
```

and set its width to 200 pixels.

---

### Exercise 3

Create a button that is disabled using a boolean property.

---

### Exercise 4

Create a hyperlink whose URL comes from a component property.

---

# Interview Questions

1. What is Property Binding?
2. What is the syntax of Property Binding?
3. What is the difference between Interpolation and Property Binding?
4. Can you use interpolation for an image's `src`? Does Angular recommend it?
5. When should you use Property Binding?
6. What types of values can Property Binding work with?

---

# Before the Next Lesson

Now you've learned how to **display values** and **set HTML properties**.

The next step is to make your application respond to user actions.

## Lesson 7: Event Binding

You'll learn how to:

* Respond to button clicks
* Handle keyboard events
* Read user input
* React to mouse events
* Call component methods from events

This is where your Angular application starts becoming interactive. After that, we'll combine what you've learned so far into **Two-Way Data Binding**, which is one of Angular's most recognizable features.
