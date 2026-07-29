Excellent! This is one of the **most important lessons** in TypeScript. If you master functions, learning Angular components, services, event handlers, and HTTP calls becomes much easier.

---

# Lesson 7: Functions in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

* What is a function?
* Function Declaration
* Function Expression
* Anonymous Function
* Arrow Function
* Function Parameters
* Return Values
* Optional Parameters
* Default Parameters
* Rest Parameters
* Function Overloading

---

# What is a Function?

A **function** is a reusable block of code that performs a specific task.

Instead of writing the same code multiple times, you write it once and call it whenever needed.

Imagine you want to calculate the sum of two numbers.

Without a function:

```typescript
let a = 10;
let b = 20;

console.log(a + b);

let x = 50;
let y = 60;

console.log(x + y);

let p = 100;
let q = 200;

console.log(p + q);
```

Notice that the same logic is repeated.

Functions solve this problem.

---

# Function Declaration

Syntax

```typescript
function functionName(parameters)
{
    // code
}
```

Example

```typescript
function greet()
{
    console.log("Welcome to TypeScript");
}

greet();
```

Output

```
Welcome to TypeScript
```

---

# Calling a Function Multiple Times

```typescript
function greet()
{
    console.log("Welcome");
}

greet();
greet();
greet();
```

Output

```
Welcome
Welcome
Welcome
```

This demonstrates **code reusability**.

---

# Function with Parameters

Parameters allow you to pass values to a function.

```typescript
function greet(name:string)
{
    console.log("Welcome " + name);
}

greet("Raman");
```

Output

```
Welcome Raman
```

Another call

```typescript
greet("John");
```

Output

```
Welcome John
```

---

# Multiple Parameters

```typescript
function add(a:number,b:number)
{
    console.log(a+b);
}

add(10,20);
```

Output

```
30
```

Another call

```typescript
add(50,100);
```

Output

```
150
```

---

# Returning Values

Instead of printing inside the function, return the result.

```typescript
function add(a:number,b:number):number
{
    return a+b;
}

let result = add(10,20);

console.log(result);
```

Output

```
30
```

---

# Understanding the Return Type

```typescript
function add(a:number,b:number):number
```

Break it down:

```
function

↓

add

↓

(number,number)

↓

returns number
```

---

# Function Returning a String

```typescript
function getName():string
{
    return "Raman";
}

console.log(getName());
```

Output

```
Raman
```

---

# Function Returning Boolean

```typescript
function isAdult(age:number):boolean
{
    return age>=18;
}

console.log(isAdult(20));
```

Output

```
true
```

---

# Void Function

If a function doesn't return anything:

```typescript
function printMessage():void
{
    console.log("Hello");
}
```

Output

```
Hello
```

`void` means:

> This function doesn't return a value.

---

# Optional Parameters

Suppose:

```typescript
function display(name:string,city:string)
{

}

display("Raman");
```

Error

Because the second parameter is missing.

Make it optional.

```typescript
function display(name:string,city?:string)
{
    console.log(name);
    console.log(city);
}

display("Raman");
```

Output

```
Raman
undefined
```

Now both work:

```typescript
display("Raman");
display("Raman","Bangalore");
```

Output

```
Raman
undefined

Raman
Bangalore
```

---

# Default Parameters

Instead of getting `undefined`:

```typescript
function display(name:string,city:string="Bangalore")
{
    console.log(name);
    console.log(city);
}

display("Raman");
```

Output

```
Raman
Bangalore
```

Override it

```typescript
display("Raman","Hyderabad");
```

Output

```
Raman
Hyderabad
```

---

# Rest Parameters

Suppose you want to add many numbers.

Without rest parameters

```typescript
add(10,20);

add(10,20,30);

add(10,20,30,40);
```

Impossible using a fixed parameter list.

Instead

```typescript
function add(...numbers:number[])
{
    let total = 0;

    for(let number of numbers)
    {
        total += number;
    }

    return total;
}

console.log(add(10,20));

console.log(add(10,20,30));

console.log(add(10,20,30,40));
```

Output

```
30

60

100
```

---

# Function Expression

Functions can also be stored in variables.

```typescript
let greet = function()
{
    console.log("Welcome");
};

greet();
```

Output

```
Welcome
```

---

# Anonymous Function

This function has no name.

```typescript
function()
{

}
```

It is usually assigned to a variable or passed as an argument.

Example

```typescript
let message = function()
{
    console.log("Hello");
};
```

---

# Arrow Functions (Most Important)

Angular uses arrow functions everywhere.

Traditional function

```typescript
function add(a:number,b:number)
{
    return a+b;
}
```

Arrow function

```typescript
const add = (a:number,b:number):number =>
{
    return a+b;
};

console.log(add(10,20));
```

Output

```
30
```

---

# Short Arrow Function

If there is only one statement:

```typescript
const add = (a:number,b:number) => a+b;

console.log(add(10,20));
```

Output

```
30
```

Very common in Angular.

---

# Real Angular Example

Button click

```typescript
save()
{
    console.log("Saved");
}
```

Template

```html
<button (click)="save()">Save</button>
```

The `save()` method is simply a function.

---

# Arrow Function with Array

```typescript
let numbers = [10,20,30];

numbers.forEach(num => console.log(num));
```

Output

```
10
20
30
```

Notice how concise arrow functions are.

---

# Function Overloading

TypeScript allows multiple function signatures.

```typescript
function display(value:number):void;

function display(value:string):void;

function display(value:any):void
{
    console.log(value);
}

display(100);

display("Angular");
```

Output

```
100
Angular
```

The last function contains the implementation.

---

# Real Angular Example

Suppose an API returns employees.

```typescript
loadEmployees()
{
    console.log("Loading...");
}
```

Save employee

```typescript
saveEmployee(employee:any)
{
    console.log(employee);
}
```

Delete employee

```typescript
deleteEmployee(id:number)
{
    console.log(id);
}
```

Every component is filled with functions like these.

---

# Summary

| Concept              | Example                |
| -------------------- | ---------------------- |
| Function Declaration | `function add(){}`     |
| Function Expression  | `let add=function(){}` |
| Anonymous Function   | `function(){}`         |
| Arrow Function       | `(a,b)=>a+b`           |
| Optional Parameter   | `city?:string`         |
| Default Parameter    | `city="Bangalore"`     |
| Rest Parameter       | `...numbers:number[]`  |
| Return Type          | `:number`              |
| Void                 | `:void`                |
| Overloading          | Multiple signatures    |

---

# Function Declaration vs Arrow Function

| Function Declaration    | Arrow Function                                      |
| ----------------------- | --------------------------------------------------- |
| Uses `function` keyword | Uses `=>`                                           |
| Traditional syntax      | Modern syntax                                       |
| Can be hoisted          | Not hoisted like function declarations              |
| Used everywhere         | Very common in Angular callbacks and event handlers |

---

# Why Angular Uses Arrow Functions

You'll often see code like this:

```typescript
this.http.get("/api/employees")
    .subscribe(data =>
    {
        console.log(data);
    });
```

The code inside `subscribe()` is an **arrow function**. They are widely used because they keep the surrounding `this` context, which is very useful inside Angular components and services.

---

# Practice Exercises

### Exercise 1

Write a function that multiplies two numbers and returns the result.

---

### Exercise 2

Write a function with a default parameter:

```typescript
greet("Raman");
```

Output

```
Welcome Raman from Bangalore
```

---

### Exercise 3

Write a function using rest parameters that calculates the average of any number of values.

---

### Exercise 4

Convert this function into an arrow function:

```typescript
function square(x:number)
{
    return x*x;
}
```

---

# Interview Questions

1. What is the difference between a parameter and an argument?
2. What is the difference between a function declaration and a function expression?
3. What is an arrow function, and why is it commonly used in Angular?
4. What are optional and default parameters?
5. What are rest parameters, and when would you use them?
6. What is function overloading in TypeScript?

---

# What's Next?

Now you know almost everything about procedural programming in TypeScript.

The next lesson is **Objects and Classes**, where we'll cover:

* Object literals
* Classes
* Constructors
* Properties
* Methods
* `this` keyword
* Access modifiers (`public`, `private`, `protected`)
* Getters and setters
* Static members

This is the bridge to Angular because **every Angular Component, Service, Pipe, Directive, and Guard is a TypeScript class**. Once you understand classes, Angular will start to make much more sense.
