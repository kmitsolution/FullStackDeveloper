Excellent! Now that you understand variables and data types, the next topic is **Operators**.

Why operators before functions?

Because every Angular component, service, and API call uses operators for calculations, conditions, comparisons, and assignments.

---

# Lesson 5: Operators in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

* Arithmetic Operators
* Assignment Operators
* Comparison Operators
* Logical Operators
* Increment & Decrement
* Ternary Operator
* Nullish Coalescing (`??`)
* Optional Chaining (`?.`)
* Typeof Operator

---

# What is an Operator?

An **operator** is a symbol that performs an operation on one or more operands.

Example

```typescript
10 + 20
```

Here

```text
10     Operand

+      Operator

20     Operand
```

Result

```text
30
```

---

# Types of Operators

```text
Arithmetic

Assignment

Comparison

Logical

Increment

Ternary

Nullish Coalescing

Optional Chaining

Typeof
```

---

# 1. Arithmetic Operators

Exactly like C#.

| Operator | Meaning        |
| -------- | -------------- |
| +        | Addition       |
| -        | Subtraction    |
| *        | Multiplication |
| /        | Division       |
| %        | Modulus        |
| **       | Power          |

---

## Addition

```typescript
let a = 10;
let b = 20;

console.log(a + b);
```

Output

```text
30
```

---

## Subtraction

```typescript
let a = 20;
let b = 5;

console.log(a - b);
```

Output

```text
15
```

---

## Multiplication

```typescript
let a = 10;
let b = 5;

console.log(a * b);
```

Output

```text
50
```

---

## Division

```typescript
let a = 20;
let b = 4;

console.log(a / b);
```

Output

```text
5
```

---

## Modulus (%)

Returns the remainder.

```typescript
console.log(10 % 3);
```

Output

```text
1
```

Why?

```text
3 × 3 = 9

10 - 9 = 1
```

Very useful for checking even/odd numbers.

```typescript
let number = 14;

if(number % 2 == 0)
{
    console.log("Even");
}
```

---

## Power (**)

```typescript
console.log(2 ** 3);
```

Output

```text
8
```

Meaning

```text
2 × 2 × 2
```

---

# 2. Assignment Operators

Simple assignment

```typescript
let age = 20;
```

Compound assignments

```typescript
let x = 10;

x += 5;
```

Equivalent to

```typescript
x = x + 5;
```

Output

```text
15
```

---

Other assignment operators

```typescript
x -= 2;

x *= 3;

x /= 5;

x %= 2;
```

Exactly like C#.

---

# 3. Comparison Operators

Used in conditions.

| Operator | Meaning            |
| -------- | ------------------ |
| ==       | Value comparison   |
| ===      | Strict comparison  |
| !=       | Not Equal          |
| !==      | Strict Not Equal   |
| >        | Greater Than       |
| <        | Less Than          |
| >=       | Greater Than Equal |
| <=       | Less Than Equal    |

---

## ==

```typescript
console.log(10 == "10");
```

Output

```text
true
```

Why?

JavaScript converts `"10"` into `10`.

This is called **type coercion**.

---

## ===

```typescript
console.log(10 === "10");
```

Output

```text
false
```

Now TypeScript compares

* Value
* Data Type

```text
10 → number

"10" → string
```

Different types.

---

### Which one should we use?

Always use

```typescript
===
```

Avoid

```typescript
==
```

This is considered a best practice in JavaScript and TypeScript.

---

# !=

```typescript
console.log(10 != 20);
```

Output

```text
true
```

---

# !==

```typescript
console.log(10 !== "10");
```

Output

```text
true
```

Again, it checks both type and value.

---

# Greater Than

```typescript
console.log(30 > 20);
```

Output

```text
true
```

---

# Less Than

```typescript
console.log(10 < 5);
```

Output

```text
false
```

---

# 4. Logical Operators

Used to combine conditions.

| Operator | Meaning |
| -------- | ------- |
| &&       | AND     |
| ||       | OR      |
| !        | NOT     |

---

## AND

```typescript
let age = 25;
let isAdmin = true;

console.log(age > 18 && isAdmin);
```

Output

```text
true
```

Both conditions must be true.

---

## OR

```typescript
let isStudent = false;
let isTeacher = true;

console.log(isStudent || isTeacher);
```

Output

```text
true
```

Only one condition needs to be true.

---

## NOT

```typescript
let isAdmin = true;

console.log(!isAdmin);
```

Output

```text
false
```

---

# 5. Increment and Decrement

Exactly like C#.

```typescript
let count = 10;

count++;

console.log(count);
```

Output

```text
11
```

---

Decrement

```typescript
count--;
```

Output

```text
10
```

---

Pre Increment

```typescript
let x = 10;

console.log(++x);
```

Output

```text
11
```

---

Post Increment

```typescript
let x = 10;

console.log(x++);
```

Output

```text
10
```

Then

```typescript
console.log(x);
```

Output

```text
11
```

---

# 6. Ternary Operator

Instead of

```typescript
let age = 20;

if(age >=18)
{
    console.log("Adult");
}
else
{
    console.log("Minor");
}
```

Write

```typescript
let age = 20;

let result = age >=18 ? "Adult" : "Minor";

console.log(result);
```

Output

```text
Adult
```

Angular templates use the ternary operator frequently.

Example:

```html
{{ age >= 18 ? 'Adult' : 'Minor' }}
```

---

# 7. Nullish Coalescing (??)

One of the most useful operators in Angular.

Suppose

```typescript
let name = null;
```

Instead of

```typescript
let displayName;

if(name==null)
{
    displayName="Guest";
}
else
{
    displayName=name;
}
```

Simply write

```typescript
let displayName = name ?? "Guest";

console.log(displayName);
```

Output

```text
Guest
```

---

Another example

```typescript
let city = "Bangalore";

console.log(city ?? "Delhi");
```

Output

```text
Bangalore
```

Because `city` already has a value.

---

# Difference Between `||` and `??`

Consider:

```typescript
let value = "";

console.log(value || "Default");
```

Output:

```text
Default
```

Because `""` (empty string) is considered *falsy*.

Now:

```typescript
let value = "";

console.log(value ?? "Default");
```

Output:

```text
```

(an empty string)

`??` only falls back when the value is `null` or `undefined`. It does **not** replace valid values like `0`, `false`, or `""`.

---

# 8. Optional Chaining (?.)

Suppose

```typescript
let employee =
{
    name:"Raman"
};
```

Access

```typescript
console.log(employee.name);
```

Output

```text
Raman
```

Now

```typescript
let employee = null;
```

This

```typescript
console.log(employee.name);
```

Throws an error.

Instead

```typescript
console.log(employee?.name);
```

Output

```text
undefined
```

No crash.

Angular uses this extensively.

Example

```html
{{ employee?.name }}
```

---

# 9. typeof Operator

Returns the data type.

```typescript
let age = 25;

console.log(typeof age);
```

Output

```text
number
```

Example

```typescript
let course = "Angular";

console.log(typeof course);
```

Output

```text
string
```

---

Useful with `unknown`

```typescript
let value: unknown = "Angular";

if(typeof value === "string")
{
    console.log(value.toUpperCase());
}
```

---

# Real Angular Example

Imagine an employee component:

```typescript
export class EmployeeComponent {

    employeeName = "Raman";
    salary = 50000;
    manager = null;

    save()
    {
        console.log(this.salary > 30000);

        console.log(this.manager ?? "No Manager");

        console.log(this.employeeName?.toUpperCase());
    }
}
```

Notice how comparison, nullish coalescing, and optional chaining work together.

---

# Summary

| Operator | Purpose                        | Example           |
| -------- | ------------------------------ | ----------------- |
| `+`      | Addition                       | `10 + 20`         |
| `-`      | Subtraction                    | `20 - 10`         |
| `*`      | Multiplication                 | `5 * 3`           |
| `/`      | Division                       | `20 / 4`          |
| `%`      | Modulus                        | `10 % 3`          |
| `==`     | Loose equality                 | Avoid             |
| `===`    | Strict equality                | Recommended       |
| `&&`     | AND                            | `a && b`          |
| `\|\|`   | OR                             | `a \|\| b`        |
| `!`      | NOT                            | `!flag`           |
| `??`     | Default for `null`/`undefined` | `name ?? "Guest"` |
| `?.`     | Safe property access           | `employee?.name`  |
| `typeof` | Get data type                  | `typeof age`      |

---

# Practice Exercises

### Exercise 1

Predict the output:

```typescript
console.log(15 % 4);
```

---

### Exercise 2

Predict the output:

```typescript
console.log(10 == "10");
console.log(10 === "10");
```

---

### Exercise 3

What is the output?

```typescript
let name = null;

console.log(name ?? "Guest");
```

---

### Exercise 4

What happens?

```typescript
let employee = null;

console.log(employee?.name);
```

---

# Interview Questions

1. What is the difference between `==` and `===`?
2. What is the difference between `||` and `??`?
3. What is optional chaining (`?.`) and why is it useful?
4. Why should `===` be preferred over `==`?
5. What does the `typeof` operator return?

---

## Next Lesson: Control Flow Statements

We'll learn:

* `if`
* `if...else`
* `else if`
* `switch`
* `for`
* `while`
* `do...while`
* `for...of`
* `for...in`
* `break`
* `continue`

These control structures are used constantly in Angular components, services, and business logic before we move on to functions and classes.
