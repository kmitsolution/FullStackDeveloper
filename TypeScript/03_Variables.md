Excellent! This is one of the **most important TypeScript (and JavaScript) lessons** because every Angular component, service, and function uses variables.

---

# Lesson 3: Variables in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

* What is a variable?
* `var`
* `let`
* `const`
* Scope
* Hoisting
* Type Inference
* Best Practices

---

# What is a Variable?

A variable is simply a **named memory location** used to store data.

For example:

```text
Age = 25

Name = Raman

Salary = 50000
```

Memory representation

```text
-----------------------
| Name  |  Raman      |
-----------------------

-----------------------
| Age   | 25          |
-----------------------

-----------------------
| Salary| 50000       |
-----------------------
```

In TypeScript

```typescript
let age = 25;
```

Here,

* Variable Name → age
* Value → 25

---

# Variable Declaration Syntax

General syntax

```typescript
let variableName: datatype = value;
```

Example

```typescript
let name: string = "Raman";

let age: number = 20;

let isAdmin: boolean = true;
```

---

# Three Ways to Declare Variables

JavaScript and TypeScript support three keywords.

```typescript
var

let

const
```

Most beginners think they are almost the same.

They are **not**.

Choosing the wrong one can introduce bugs.

---

# 1. var

Example

```typescript
var name = "Raman";

console.log(name);
```

Output

```text
Raman
```

Looks fine.

Now let's understand its behavior.

---

# var is Function Scoped

Example

```typescript
function demo()
{
    var age = 25;

    console.log(age);
}

demo();
```

Output

```text
25
```

The variable belongs to the entire function.

---

# Example

```typescript
function demo()
{
    if(true)
    {
        var age = 25;
    }

    console.log(age);
}

demo();
```

Output

```text
25
```

Many developers expect an error because `age` was declared inside the `if` block.

But `var` ignores block boundaries.

Internally

```text
function demo()
{
    var age;

    if(true)
    {
        age = 25;
    }

    console.log(age);
}
```

---

# Problem with var

Suppose you write

```typescript
for(var i=1;i<=3;i++)
{

}

console.log(i);
```

Output

```text
4
```

Even though the loop has ended, `i` is still accessible.

This often leads to bugs.

---

# 2. let

ES6 introduced `let` to solve many issues with `var`.

Example

```typescript
let name = "Raman";

console.log(name);
```

Works like `var`, but the scope is different.

---

# let is Block Scoped

Example

```typescript
if(true)
{
    let age = 25;

    console.log(age);
}
```

Output

```text
25
```

Outside the block

```typescript
if(true)
{
    let age = 25;
}

console.log(age);
```

Output

```text
Cannot find name 'age'
```

The variable exists only inside the block where it is declared.

---

# Example with for loop

```typescript
for(let i=1;i<=3;i++)
{

}

console.log(i);
```

Output

```text
Cannot find name 'i'
```

This is much safer.

---

# Visual Difference

Using `var`

```text
Function

---------------------------------

var age

if
{
}

Entire function can access age

---------------------------------
```

Using `let`

```text
Function

---------------------------------

if
{
    let age
}

Only this block can access age

---------------------------------
```

---

# 3. const

`const` means the variable **cannot be reassigned** after it is initialized.

Example

```typescript
const country = "India";

console.log(country);
```

Works normally.

Now try

```typescript
const country = "India";

country = "USA";
```

Output

```text
Cannot assign to 'country'
because it is a constant.
```

---

# Why Use const?

Suppose you have

```typescript
const PI = 3.14159;
```

Nobody should accidentally change it.

Another example

```typescript
const API_URL = "https://localhost:5001/api";
```

The API endpoint should remain constant throughout the application.

---

# Does const Make Objects Immutable?

A common misconception is that `const` makes everything inside the object read-only.

Example

```typescript
const employee =
{
    id:1,
    name:"Raman"
};
```

This is allowed

```typescript
employee.name = "John";
```

But this is **not** allowed

```typescript
employee =
{
    id:2,
    name:"David"
};
```

Why?

Because `const` protects the **reference**, not the contents of the object.

---

# Comparison

| Feature                     | var    | let | const |
| --------------------------- | ------ | --- | ----- |
| Can Reassign                | Yes    | Yes | No    |
| Function Scoped             | Yes    | No  | No    |
| Block Scoped                | No     | Yes | Yes   |
| Redeclaration in Same Scope | Yes    | No  | No    |
| Modern JavaScript           | Rarely | Yes | Yes   |

---

# Redeclaration

### var

Allowed

```typescript
var age = 20;

var age = 30;

console.log(age);
```

Output

```text
30
```

---

### let

Not allowed

```typescript
let age = 20;

let age = 30;
```

Output

```text
Cannot redeclare block-scoped variable.
```

---

### const

Also not allowed

```typescript
const age = 20;

const age = 30;
```

Error

---

# Type Inference

One of the best features of TypeScript.

Instead of writing

```typescript
let age:number = 25;
```

You can simply write

```typescript
let age = 25;
```

TypeScript automatically understands

```text
age → number
```

Similarly

```typescript
let name = "Raman";
```

Automatically

```text
name → string
```

Another example

```typescript
let isAdmin = true;
```

Automatically

```text
boolean
```

---

# Verify Type Inference

```typescript
let age = 25;

age = "Twenty";
```

Compiler error

```text
Type 'string' is not assignable
to type 'number'
```

Even though you didn't explicitly specify `: number`, TypeScript inferred it from the initial value.

---

# Explicit vs Inferred Types

Explicit

```typescript
let salary:number = 50000;
```

Inferred

```typescript
let salary = 50000;
```

Both are equivalent.

Many developers prefer inference when the type is obvious because it keeps the code concise.

---

# Best Practices

✅ Prefer `const` whenever the variable doesn't need to change.

```typescript
const company = "KMIT";
```

✅ Use `let` when the value needs to change.

```typescript
let count = 0;

count++;
```

❌ Avoid `var` in modern TypeScript.

---

# Real Angular Example

Inside a component

```typescript
export class ProductComponent
{
    title = "Products";

    constName = "KMIT";   // ❌ Invalid
}
```

Why?

`const` can only be used for local variables inside functions or blocks. Class members are declared without `let`, `var`, or `const`.

Correct

```typescript
export class ProductComponent
{
    title = "Products";

    count = 0;
}
```

Inside a method

```typescript
save()
{
    const api = "/products";

    let count = 10;

    count++;
}
```

---

# Summary

| Keyword | When to Use                                 |
| ------- | ------------------------------------------- |
| `var`   | Avoid in modern TypeScript                  |
| `let`   | Use when the value will change              |
| `const` | Use when the value should not be reassigned |

### Key Rules

* Variables store values in memory.
* `var` is **function-scoped** and can lead to unexpected behavior.
* `let` is **block-scoped** and is the preferred choice for mutable variables.
* `const` is **block-scoped** and prevents reassignment of the variable.
* TypeScript can infer types from initial values, reducing the need for explicit type annotations.

---

# Practice Exercises

### Exercise 1

Predict the output:

```typescript
let x = 10;

x = 20;

console.log(x);
```

---

### Exercise 2

Will this compile?

```typescript
const city = "Bangalore";

city = "Delhi";
```

Why or why not?

---

### Exercise 3

Predict the output:

```typescript
if(true)
{
    let a = 100;
}

console.log(a);
```

---

### Exercise 4

Predict the output:

```typescript
if(true)
{
    var b = 200;
}

console.log(b);
```

---

## Next Lesson

We'll cover **TypeScript Data Types** in depth, including:

* `number`
* `string`
* `boolean`
* `any`
* `unknown`
* `void`
* `null`
* `undefined`
* `never`
* Arrays
* Tuples
* Enums
* Type aliases

These types form the foundation for Angular components, services, models, and API responses.
