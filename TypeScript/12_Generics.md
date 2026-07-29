Excellent! This is one of the **most important TypeScript topics for Angular**.

If I had to rank the TypeScript topics used in Angular, it would be:

1. ⭐⭐⭐⭐⭐ **Generics**
2. ⭐⭐⭐⭐⭐ **Decorators**
3. ⭐⭐⭐⭐⭐ **Interfaces**
4. ⭐⭐⭐⭐ Modules
5. ⭐⭐⭐ Arrays

Why?

Because Angular's `HttpClient`, `Observable`, `EventEmitter`, and many APIs all rely heavily on **Generics**.

---

# Lesson 12: Generics in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

* What are Generics?
* Why Generics are needed
* Generic Functions
* Generic Classes
* Generic Interfaces
* Generic Constraints
* Multiple Generic Types
* Real Angular Examples

---

# What Problem Do Generics Solve?

Suppose you write a function to print a number.

```typescript
function display(value: number): void {
    console.log(value);
}

display(100);
```

Output

```text
100
```

Now you want to print a string.

```typescript
display("Angular");
```

Compiler Error

Because the function only accepts a `number`.

---

# Solution 1 (Bad Practice)

Use `any`.

```typescript
function display(value: any): void {
    console.log(value);
}
```

Now this works:

```typescript
display(100);

display("Angular");

display(true);
```

Output

```text
100
Angular
true
```

Looks good.

But there is a problem.

---

# Why is `any` Bad?

```typescript
function display(value: any) {
    console.log(value.toUpperCase());
}

display(100);
```

Runtime Error

Because `100` doesn't have a `toUpperCase()` method.

`any` removes type safety.

---

# Solution 2 (Generics)

Instead of `any`, use a **generic type**.

```typescript
function display<T>(value: T): void {
    console.log(value);
}
```

Notice

```typescript
<T>
```

This means

> "I don't know the type yet. The caller will decide."

---

# Using a Generic Function

Number

```typescript
display<number>(100);
```

String

```typescript
display<string>("Angular");
```

Boolean

```typescript
display<boolean>(true);
```

Output

```text
100
Angular
true
```

---

# Type Inference

Usually, you don't need to specify the type.

Instead of

```typescript
display<number>(100);
```

Simply write

```typescript
display(100);
```

TypeScript automatically infers

```text
T = number
```

Similarly

```typescript
display("Angular");
```

becomes

```text
T = string
```

---

# Visual Representation

```text
display(100)

↓

T = number

↓

display(number)
```

Another

```text
display("Angular")

↓

T = string

↓

display(string)
```

The compiler creates the appropriate type automatically.

---

# Generic Function Returning a Value

```typescript
function getValue<T>(value: T): T {
    return value;
}
```

Example

```typescript
let name = getValue("Raman");

console.log(name);
```

Output

```text
Raman
```

Another

```typescript
let age = getValue(25);

console.log(age);
```

Output

```text
25
```

---

# Generic Class

Suppose

```typescript
class Box {
    value: any;

    constructor(value: any) {
        this.value = value;
    }
}
```

Again

`any`

Let's improve it.

```typescript
class Box<T> {

    constructor(
        public value: T
    ) {

    }
}
```

---

# Create Objects

```typescript
let numberBox = new Box<number>(100);

console.log(numberBox.value);
```

Output

```text
100
```

Another

```typescript
let stringBox =
new Box<string>("Angular");

console.log(stringBox.value);
```

Output

```text
Angular
```

---

# Generic Interface

```typescript
interface Result<T> {
    data: T;
}
```

Now

```typescript
let result: Result<string> =
{
    data: "Success"
};

console.log(result.data);
```

Output

```text
Success
```

---

Another

```typescript
let result: Result<number> =
{
    data: 100
};
```

Works perfectly.

---

# Multiple Generic Types

Sometimes we need more than one type.

```typescript
class Pair<T, U> {

    constructor(
        public first: T,
        public second: U
    ) {

    }
}
```

Object

```typescript
let pair =
new Pair<number,string>(
    1,
    "Raman"
);

console.log(pair.first);

console.log(pair.second);
```

Output

```text
1
Raman
```

---

# Generic Constraints

Suppose

```typescript
function printLength<T>(value: T)
{
    console.log(value.length);
}
```

Compiler Error

Because every type doesn't have `length`.

Example

```typescript
printLength(100);
```

A number has no `length`.

---

# Solution

Create an interface.

```typescript
interface Length {
    length:number;
}
```

Now

```typescript
function printLength<T extends Length>(
    value:T
)
{
    console.log(value.length);
}
```

Now

```typescript
printLength("Angular");
```

Output

```text
7
```

Another

```typescript
printLength([10,20,30]);
```

Output

```text
3
```

But

```typescript
printLength(100);
```

Compiler Error

Perfect!

---

# Generic Array

You already know

```typescript
let numbers:number[] =
[
10,
20,
30
];
```

Another way

```typescript
let numbers:Array<number> =
[
10,
20,
30
];
```

Notice

```typescript
Array<number>
```

This is actually a **generic class**.

---

# Generic Promise

Suppose

```typescript
Promise<string>
```

means

```text
This Promise returns a string
```

Another

```typescript
Promise<number>
```

returns a number.

---

# The Most Important Angular Example

Suppose you call an API.

Without Generics

```typescript
this.http.get("/api/employees");
```

What type does it return?

TypeScript doesn't know.

---

With Generics

```typescript
this.http.get<Employee[]>(
"/api/employees"
);
```

Now TypeScript knows

```text
The API returns

↓

Array

↓

Employee Objects
```

Now IntelliSense works perfectly.

```typescript
employees[0].name
```

TypeScript knows

* id
* name
* salary

Everything.

---

# Another Angular Example

Suppose

```typescript
class Employee {

    id:number = 0;

    name:string = "";
}
```

API

```typescript
this.http.get<Employee[]>(
"/api/employees"
)
.subscribe(data=>
{
    console.log(data[0].name);
});
```

Notice

Because of

```typescript
Employee[]
```

TypeScript already knows every property.

---

# Generic Repository Example

```typescript
class Repository<T> {

    private items:T[] = [];

    add(item:T)
    {
        this.items.push(item);
    }

    getAll():T[]
    {
        return this.items;
    }
}
```

Employee Repository

```typescript
let repo =
new Repository<Employee>();

repo.add(
new Employee()
);
```

Product Repository

```typescript
class Product {

}
```

```typescript
let productRepo =
new Repository<Product>();
```

One class

Many types.

---

# Summary

| Generic           | Example                 |
| ----------------- | ----------------------- |
| Generic Function  | `function display<T>()` |
| Generic Class     | `class Box<T>`          |
| Generic Interface | `interface Result<T>`   |
| Multiple Types    | `<T,U>`                 |
| Constraint        | `T extends Length`      |

---

# Why Generics Matter in Angular

You will constantly see code like:

```typescript
Observable<Employee[]>
```

```typescript
HttpClient.get<Employee>()
```

```typescript
EventEmitter<string>
```

```typescript
Signal<number>
```

Every one of these uses **Generics**.

Without understanding Generics, Angular code looks confusing.

With Generics, it's easy to read.

---

# C# vs TypeScript Generics

If you've worked with C#, you'll notice they're almost identical.

### C#

```csharp
List<string> names = new List<string>();
```

### TypeScript

```typescript
let names:Array<string> = [];
```

---

### C#

```csharp
class Repository<T>
{

}
```

### TypeScript

```typescript
class Repository<T>
{

}
```

Very similar!

---

# Practice Exercises

### Exercise 1

Create a generic function

```typescript
identity<T>()
```

that returns whatever value is passed to it.

Test it with:

* number
* string
* boolean

---

### Exercise 2

Create a generic class

```typescript
Storage<T>
```

with:

* `add(item:T)`
* `getAll()`

Store strings and numbers separately.

---

### Exercise 3

Create

```typescript
interface ApiResponse<T>
```

with

```typescript
data:T
```

Create one response for:

* Employee
* Product

---

### Exercise 4

Create

```typescript
class Pair<T,U>
```

Store:

```text
101
Raman
```

Print both values.

---

# Interview Questions

1. What are Generics?
2. Why are Generics better than `any`?
3. What is a generic function?
4. What is a generic class?
5. What does `T extends SomeInterface` mean?
6. How does Angular's `HttpClient.get<Employee[]>()` use Generics?
7. What is the difference between `any` and `T`?

---

# What's Next?

Only **one TypeScript topic** remains before Angular:

# Lesson 13: Decorators

This is the most exciting lesson because Angular is built on decorators.

You'll learn:

* What are decorators?
* Class decorators
* Method decorators
* Property decorators
* Parameter decorators
* How decorators work internally
* Why Angular uses `@Component`, `@Injectable`, `@Directive`, `@Pipe`, and `@Input`

After that, we'll install Angular CLI and build your **first Angular application**. Once you understand decorators, Angular's syntax will feel much more natural.
