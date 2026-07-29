Excellent! This is the **final TypeScript lesson** before we start Angular.

If there is one TypeScript topic that explains **how Angular works internally**, it's **Decorators**.

When you open any Angular project, you'll immediately see code like:

```typescript
@Component({...})

@Injectable()

@Pipe({...})

@Directive({...})

@Input()

@Output()
```

All of these are **decorators**.

Once you understand decorators, Angular code becomes much easier to read.

---

# Lesson 13: Decorators in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

* What are Decorators?
* Why Decorators are needed
* Class Decorators
* Method Decorators
* Property Decorators
* Parameter Decorators
* Decorator Factories
* Execution Order
* How Angular uses Decorators

---

# What is a Decorator?

A decorator is a **special function** that adds extra behavior or metadata to a class, method, property, or parameter.

Think of it as attaching a label to something.

Example:

```text
Employee Class

↓

@Component

↓

Angular now knows

"This class is a Component"
```

Without decorators, Angular wouldn't know what role a class plays.

---

# Real World Analogy

Imagine a file in an office.

Without a label

```text
File
```

Nobody knows what it contains.

Attach a label

```text
Employee Records
```

Now everyone understands its purpose.

A decorator works similarly—it **labels** a class or member with additional information.

---

# Without Decorators

```typescript
class Employee
{

}
```

This is just a normal TypeScript class.

Angular doesn't know whether it's

* Component
* Service
* Directive
* Pipe

---

# With a Decorator

```typescript
@Component({

})
class EmployeeComponent
{

}
```

Now Angular understands

```text
EmployeeComponent

↓

@Component

↓

Angular Component
```

---

# What is the @ Symbol?

Whenever you see

```typescript
@Component

@Injectable

@Input
```

the `@` indicates a decorator.

General syntax

```typescript
@Decorator
class MyClass
{

}
```

---

# Enabling Decorators

Decorators are enabled through the TypeScript compiler configuration.

```json
{
  "compilerOptions": {
    "experimentalDecorators": true
  }
}
```

Modern Angular projects enable the necessary configuration automatically, so you typically don't need to change this yourself.

---

# 1. Class Decorator

A class decorator executes when the class is defined.

Example

```typescript
function Logger(constructor: Function)
{
    console.log("Class Loaded");
}

@Logger
class Employee
{

}
```

Output

```text
Class Loaded
```

Notice

You never called

```typescript
Logger();
```

The decorator is called automatically.

---

# What Happens Internally?

```text
@Logger

↓

Employee Class Created

↓

Logger(Employee)

↓

Program Continues
```

---

# Decorator Receiving the Constructor

```typescript
function Logger(constructor: Function)
{
    console.log(constructor.name);
}

@Logger
class Employee
{

}
```

Output

```text
Employee
```

The decorator receives the class constructor.

---

# Decorator Factory

Sometimes decorators need parameters.

Example

```typescript
@Logger("Development")
```

Implementation

```typescript
function Logger(message: string)
{
    return function(constructor: Function)
    {
        console.log(message);
        console.log(constructor.name);
    }
}
```

Usage

```typescript
@Logger("Development")
class Employee
{

}
```

Output

```text
Development
Employee
```

---

# 2. Method Decorator

Suppose

```typescript
class Employee
{
    save()
    {
        console.log("Saving");
    }
}
```

Decorate

```typescript
function LogMethod(
    target:any,
    propertyKey:string,
    descriptor:PropertyDescriptor)
{
    console.log(propertyKey);
}
```

Usage

```typescript
class Employee
{
    @LogMethod
    save()
    {

    }
}
```

Output

```text
save
```

---

# Method Decorator Parameters

```typescript
target
```

The prototype of the class.

```typescript
propertyKey
```

Method name.

```typescript
descriptor
```

Information about the method.

---

# 3. Property Decorator

Example

```typescript
function Required(
    target:any,
    propertyKey:string)
{
    console.log(propertyKey);
}
```

Usage

```typescript
class Employee
{
    @Required
    name:string="";
}
```

Output

```text
name
```

---

# 4. Parameter Decorator

```typescript
function LogParameter(
    target:any,
    methodName:string,
    parameterIndex:number)
{
    console.log(parameterIndex);
}
```

Usage

```typescript
class Employee
{
    save(
        @LogParameter
        id:number)
    {

    }
}
```

Output

```text
0
```

The decorator tells you which parameter it was applied to.

---

# Multiple Decorators

```typescript
function First(constructor:Function)
{
    console.log("First");
}

function Second(constructor:Function)
{
    console.log("Second");
}
```

Usage

```typescript
@First
@Second
class Employee
{

}
```

Output

```text
Second
First
```

Decorators are applied from **bottom to top**.

---

# Decorator Execution Order

```text
@First

@Second

Class
```

Execution

```text
Second

↓

First
```

---

# Real Angular Example

Component

```typescript
import { Component } from '@angular/core';

@Component({
    selector: 'app-employee',
    template: '<h1>Employee</h1>'
})
export class EmployeeComponent
{

}
```

Break it down.

```text
@Component

↓

This class is an Angular Component

↓

selector

↓

HTML Tag

↓

template

↓

UI
```

---

# @Injectable

Service

```typescript
@Injectable({
    providedIn:'root'
})
export class EmployeeService
{

}
```

Meaning

```text
Angular

↓

Create this service

↓

Dependency Injection
```

---

# @Input

Parent Component

```html
<app-employee
    [name]="employeeName">
</app-employee>
```

Child Component

```typescript
@Input()
name:string="";
```

Angular automatically copies the value from the parent into the child.

---

# @Output

Child

```typescript
@Output()
save =
new EventEmitter<void>();
```

Parent

```html
<app-employee
(save)="saveEmployee()">
</app-employee>
```

The child raises an event, and the parent responds to it.

---

# @Pipe

```typescript
@Pipe({
    name:'currency'
})
```

Angular now recognizes the class as a pipe.

---

# @Directive

```typescript
@Directive({
    selector:'[appHighlight]'
})
```

Angular recognizes it as a custom directive.

---

# How Angular Works

Suppose Angular starts your application.

It scans every file.

When it sees

```typescript
@Component
```

it registers the class as a component.

When it sees

```typescript
@Injectable
```

it registers the class as a service.

When it sees

```typescript
@Pipe
```

it registers the class as a pipe.

Without decorators, Angular wouldn't know what these classes represent.

---

# Summary

| Decorator | Applied To | Angular Example                                    |
| --------- | ---------- | -------------------------------------------------- |
| Class     | Class      | `@Component`, `@Injectable`, `@Pipe`, `@Directive` |
| Method    | Method     | Rare in Angular apps (more common in libraries)    |
| Property  | Property   | `@Input`, `@Output`, `@ViewChild`                  |
| Parameter | Parameter  | Used internally by Angular's dependency injection  |

---

# Complete Angular Example

```typescript
import { Component, Input } from '@angular/core';

@Component({
    selector: 'app-student',
    template: `
        <h2>{{ name }}</h2>
    `
})
export class StudentComponent
{
    @Input()
    name:string="";
}
```

Notice how multiple decorators work together:

* `@Component` tells Angular this is a component.
* `@Input` tells Angular that `name` can receive a value from a parent component.

---

# TypeScript Decorators vs Angular Decorators

This is an important distinction.

## TypeScript Decorators

TypeScript provides the **language feature** that allows decorators.

Example:

```typescript
function Logger(constructor: Function)
{
    console.log("Loaded");
}

@Logger
class Employee
{

}
```

TypeScript simply executes the decorator function.

---

## Angular Decorators

Angular **builds on top of** TypeScript decorators.

Example:

```typescript
@Component({
    selector: 'app-home',
    template: '<h1>Home</h1>'
})
export class HomeComponent
{

}
```

Here, `@Component` is **provided by Angular**, not by TypeScript itself. Angular uses the metadata you provide to build and run your application.

---

# Practice Exercises

### Exercise 1

Create a class decorator that logs:

```text
Employee class loaded
```

when the class is defined.

---

### Exercise 2

Create a decorator factory:

```typescript
@Version("1.0")
```

that prints:

```text
Version: 1.0
```

along with the class name.

---

### Exercise 3

Create a property decorator that logs the name of the property it decorates.

---

### Exercise 4

Explain the purpose of these Angular decorators:

* `@Component`
* `@Injectable`
* `@Input`
* `@Output`

---

# Interview Questions

1. What is a decorator?
2. Why does Angular use decorators?
3. What is the difference between a TypeScript decorator and an Angular decorator?
4. What is a decorator factory?
5. In what order are multiple decorators executed?
6. What is the purpose of `@Component`?
7. What is the purpose of `@Injectable`?

---

# 🎉 Congratulations!

You have now completed the **TypeScript foundation required for Angular**.

## What You've Learned

* ✅ Variables
* ✅ Data Types
* ✅ Operators
* ✅ Control Flow
* ✅ Functions
* ✅ Classes
* ✅ OOP
* ✅ Arrays
* ✅ Modules (`import` / `export`)
* ✅ Generics
* ✅ Decorators

This is more than enough TypeScript to become productive with Angular.

---

# Next: Angular Begins 🚀

Starting with the next lesson, we'll move into Angular itself.

Our roadmap will be:

1. **What is Angular?**
2. Angular Architecture
3. Installing Node.js and Angular CLI
4. Creating your first Angular application
5. Understanding the Angular project structure
6. Components
7. Templates
8. Data Binding
9. Directives
10. Services & Dependency Injection
11. Routing
12. Forms
13. HTTP Client & REST APIs
14. RxJS
15. Signals
16. Building a complete Angular + ASP.NET Core CRUD application

From this point onward, we'll build everything step by step and connect it to an ASP.NET Core backend so you can see how the frontend and backend work together in a real application.
