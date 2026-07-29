Absolutely. Since we discovered the **Node.js ES Module** behavior during your hands-on practice, let's regenerate the lesson with **modern TypeScript (Node.js v22/v24)** so it matches what you're actually using. This will save you from confusion later.

---

# Lesson 11: Modules (Import & Export) in TypeScript (Updated for Modern Node.js)

## Learning Objectives

By the end of this lesson, you will understand:

* Why modules are needed
* What is a module?
* `export`
* `import`
* Named Exports
* Default Exports
* Import Alias
* Export All
* CommonJS vs ES Modules
* Why `.js` is used instead of `.ts` in imports
* How Angular handles imports

---

# Why Do We Need Modules?

Imagine writing an application in a single file.

```text
app.ts

Employee Class

Student Class

Product Class

Order Class

Customer Class

Invoice Class

5000+ Lines
```

Problems:

* Difficult to read
* Difficult to maintain
* Difficult to debug
* Difficult for multiple developers to work together

Instead, split the code.

```text
Employee.ts

Student.ts

Product.ts

Order.ts
```

Each file has a single responsibility.

---

# What is a Module?

A module is simply a **TypeScript file that exports something**.

Example

```text
Employee.ts

↓

Exports Employee class

↓

Other files can import Employee
```

---

# Creating Your First Module

## Employee.ts

```typescript
export class Employee
{
    constructor(
        public id:number,
        public name:string
    )
    {

    }

    display()
    {
        console.log(this.id);
        console.log(this.name);
    }
}
```

Notice

```typescript
export
```

This makes the class available to other files.

---

# Importing a Module

Create

## app.ts

### Modern Node.js (Recommended)

```typescript
import { Employee } from "./Employee.js";

let emp = new Employee(
    101,
    "Raman"
);

emp.display();
```

Output

```text
101
Raman
```

---

# Why are we importing `Employee.js` instead of `Employee.ts`?

This surprises many beginners.

Your project contains

```text
Employee.ts
```

but you write

```typescript
import { Employee } from "./Employee.js";
```

Why?

Because after compilation, the file becomes

```text
Employee.js
```

Node.js executes JavaScript files, not TypeScript files.

TypeScript understands that:

```text
Development Time

Employee.ts

↓

Compilation

↓

Employee.js

↓

Node.js Executes Employee.js
```

So writing

```typescript
"./Employee.js"
```

is the correct approach for modern Node.js projects.

---

# What Happens if You Write

```typescript
import { Employee } from "./Employee";
```

TypeScript compiles successfully.

However, Node.js throws

```text
Error [ERR_MODULE_NOT_FOUND]
```

because modern ES Modules require the exact filename.

Node searches for

```text
Employee
```

instead of

```text
Employee.js
```

---

# Folder Structure

```text
TypeScriptDemo

│

├── Employee.ts

├── Employee.js

├── app.ts

└── app.js
```

---

# Understanding the Import Path

```typescript
import { Employee } from "./Employee.js";
```

Break it down

```text
.

↓

Current Folder

↓

Employee.js
```

---

# Exporting Multiple Functions

## MathUtility.ts

```typescript
export function add(a:number,b:number)
{
    return a+b;
}

export function sub(a:number,b:number)
{
    return a-b;
}
```

---

Import

```typescript
import
{
    add,
    sub
}
from "./MathUtility.js";

console.log(add(10,20));

console.log(sub(30,10));
```

Output

```text
30
20
```

---

# Named Exports

You can export multiple items.

```typescript
export class Employee
{

}

export class Student
{

}

export class Product
{

}
```

Import only what you need.

```typescript
import
{
    Employee,
    Student
}
from "./Models.js";
```

---

# Import Alias

Suppose

```typescript
export class Employee
{

}
```

Import

```typescript
import
{
    Employee as Emp
}
from "./Employee.js";

let e = new Emp();
```

Useful when two modules export the same name.

---

# Export Everything

Folder

```text
Models

Employee.ts

Student.ts

Product.ts

index.ts
```

---

## index.ts

```typescript
export * from "./Employee.js";

export * from "./Student.js";

export * from "./Product.js";
```

Now

```typescript
import
{
    Employee,
    Student,
    Product
}
from "./index.js";
```

---

# Default Export

Instead of

```typescript
export class Employee
{

}
```

Write

```typescript
export default class Employee
{

}
```

Import

```typescript
import Employee
from "./Employee.js";
```

Notice

No braces.

---

# Named Export vs Default Export

Named Export

```typescript
export class Employee
{

}
```

Import

```typescript
import { Employee }
from "./Employee.js";
```

---

Default Export

```typescript
export default class Employee
{

}
```

Import

```typescript
import Employee
from "./Employee.js";
```

---

# Import Everything

```typescript
import * as Math
from "./MathUtility.js";

console.log(Math.add(10,20));

console.log(Math.sub(50,10));
```

Output

```text
30
40
```

---

# Complete Example

## Employee.ts

```typescript
export class Employee
{
    constructor(
        public id:number,
        public name:string,
        public salary:number
    )
    {

    }
}
```

---

## EmployeeService.ts

```typescript
import { Employee }
from "./Employee.js";

export class EmployeeService
{
    getEmployees()
    {
        return
        [
            new Employee(1,"Raman",50000),
            new Employee(2,"John",60000)
        ];
    }
}
```

---

## app.ts

```typescript
import
{
    EmployeeService
}
from "./EmployeeService.js";

let service =
new EmployeeService();

let employees =
service.getEmployees();

console.log(employees);
```

---

# CommonJS vs ES Modules

There are two major module systems in JavaScript.

## CommonJS (Older)

```typescript
import { Employee } from "./Employee";
```

Generated JavaScript

```javascript
const Employee = require("./Employee");
```

Run

```bash
node app.js
```

No `.js` extension needed in imports.

---

## ES Modules (Modern)

```typescript
import { Employee } from "./Employee.js";
```

Generated JavaScript

```javascript
import { Employee } from "./Employee.js";
```

Node.js follows the ECMAScript standard and requires the `.js` extension.

---

# Which One Should I Learn?

For modern JavaScript and Node.js:

✅ ES Modules

For older projects:

CommonJS

For Angular:

Angular uses **ES Modules**, but Angular CLI resolves imports automatically, so you usually write:

```typescript
import { Employee } from "./employee";
```

without the `.js` extension.

---

# How Angular Uses Modules

Angular Component

```typescript
export class EmployeeComponent
{

}
```

Another file

```typescript
import
{
    EmployeeComponent
}
from "./employee.component";
```

Notice there is **no `.js` extension**.

Why?

Because Angular CLI handles module resolution during the build process.

---

# Angular Project Structure

```text
src

└── app

    ├── employee

    │     employee.component.ts

    │     employee.service.ts

    │     employee.model.ts

    │     employee.routes.ts

    │

    └── app.component.ts
```

Every file exports something.

Other files import what they need.

---

# Summary

| Concept        | Modern Node.js                             | Angular                                 |
| -------------- | ------------------------------------------ | --------------------------------------- |
| Export         | `export class Employee`                    | Same                                    |
| Import         | `import { Employee } from "./Employee.js"` | `import { Employee } from "./employee"` |
| File Extension | `.js` required                             | No extension                            |
| Module System  | ES Modules                                 | ES Modules handled by Angular CLI       |

---

# Best Practices

✅ One class per file.

```text
Employee.ts
```

✅ Use named exports.

```typescript
export class Employee
{

}
```

✅ Keep file names meaningful.

```text
employee.component.ts

employee.service.ts

employee.model.ts
```

Exactly like Angular.

---

# Practice Exercises

### Exercise 1

Create:

```text
Student.ts
```

Export a `Student` class.

Import it into:

```text
app.ts
```

using:

```typescript
import { Student } from "./Student.js";
```

Create an object and print its values.

---

### Exercise 2

Create:

```text
MathUtility.ts
```

Export:

* `multiply()`
* `divide()`

Import them into `app.ts` and call both.

---

### Exercise 3

Create:

```text
Employee.ts
Product.ts
index.ts
```

Use `export *` in `index.ts` and import both classes from `index.ts`.

---

# Interview Questions

1. What is a module?
2. What is the difference between `export` and `import`?
3. What is the difference between a named export and a default export?
4. Why do modern Node.js ES Modules require the `.js` extension in import statements?
5. Why doesn't Angular require `.js` extensions in import paths?
6. What is the difference between CommonJS and ES Modules?

---

## Before We Start Angular

You've now completed the core TypeScript topics needed for Angular. There are just **two advanced TypeScript topics** left:

1. **Generics** ⭐⭐⭐⭐⭐ (used extensively by Angular's `HttpClient`, RxJS, and services)
2. **Decorators** ⭐⭐⭐⭐⭐ (the foundation of Angular: `@Component`, `@Injectable`, `@Pipe`, etc.)

After these, we'll install Angular CLI and build your first Angular application.
