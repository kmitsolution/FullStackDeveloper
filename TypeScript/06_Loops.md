Excellent! This lesson is extremely important because every Angular application uses control flow for business logic, loops, validation, API responses, and UI rendering.

---

# Lesson 6: Control Flow Statements in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

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

---

# What is Control Flow?

Control flow determines **which statements execute and in what order**.

Without control flow, a program executes sequentially:

```text
Statement 1
      ↓
Statement 2
      ↓
Statement 3
      ↓
Statement 4
```

With control flow, the program can:

* Make decisions
* Repeat tasks
* Skip statements
* Stop loops

---

# Types of Control Flow

```text
Decision Making
---------------
if
if...else
else if
switch

Loops
-----
for
while
do...while
for...of
for...in

Loop Control
------------
break
continue
```

---

# 1. if Statement

Used when you want to execute code only if a condition is true.

Syntax

```typescript
if(condition)
{
    // statements
}
```

Example

```typescript
let age = 20;

if(age >= 18)
{
    console.log("Eligible to vote");
}
```

Output

```text
Eligible to vote
```

---

# if with false condition

```typescript
let age = 15;

if(age >= 18)
{
    console.log("Eligible");
}
```

Output

```text
No Output
```

The condition is false, so the block is skipped.

---

# Real Angular Example

```typescript
if(this.isLoggedIn)
{
    this.loadDashboard();
}
```

---

# 2. if...else

When one of two blocks should execute.

```typescript
let age = 16;

if(age >=18)
{
    console.log("Adult");
}
else
{
    console.log("Minor");
}
```

Output

```text
Minor
```

---

# Flow

```text
Condition

     │

 True ─────────► Adult

 False ────────► Minor
```

---

# 3. else if

Used when there are multiple conditions.

```typescript
let marks = 85;

if(marks >=90)
{
    console.log("Grade A");
}
else if(marks >=75)
{
    console.log("Grade B");
}
else if(marks >=60)
{
    console.log("Grade C");
}
else
{
    console.log("Fail");
}
```

Output

```text
Grade B
```

---

# Flow

```text
Marks

   │

>=90 ?

 │

No

 │

>=75 ?

 │

Yes

 │

Grade B
```

Only the **first matching condition** executes.

---

# 4. switch

Used when checking one variable against many fixed values.

Instead of:

```typescript
if(day==1)
{

}
else if(day==2)
{

}
else if(day==3)
{

}
```

Use

```typescript
let day = 3;

switch(day)
{
    case 1:
        console.log("Monday");
        break;

    case 2:
        console.log("Tuesday");
        break;

    case 3:
        console.log("Wednesday");
        break;

    default:
        console.log("Invalid");
}
```

Output

```text
Wednesday
```

---

# Why break?

Without `break`:

```typescript
let day = 1;

switch(day)
{
    case 1:
        console.log("Monday");

    case 2:
        console.log("Tuesday");

    case 3:
        console.log("Wednesday");
}
```

Output

```text
Monday
Tuesday
Wednesday
```

This is called **fall-through**.

Always use `break` unless you intentionally want fall-through.

---

# 5. for Loop

Used when the number of iterations is known.

Syntax

```typescript
for(initialization;
    condition;
    increment)
{

}
```

Example

```typescript
for(let i=1;i<=5;i++)
{
    console.log(i);
}
```

Output

```text
1
2
3
4
5
```

---

# Example

Print even numbers

```typescript
for(let i=2;i<=10;i+=2)
{
    console.log(i);
}
```

Output

```text
2
4
6
8
10
```

---

# Real Angular Example

Creating a list manually (before learning templates)

```typescript
for(let i=0;i<employees.length;i++)
{
    console.log(employees[i]);
}
```

---

# 6. while Loop

Used when you don't know exactly how many times the loop will execute.

```typescript
let i = 1;

while(i<=5)
{
    console.log(i);

    i++;
}
```

Output

```text
1
2
3
4
5
```

---

# Flow

```text
Condition

True

↓

Execute

↓

Increment

↓

Condition Again
```

---

# 7. do...while

Difference?

The loop executes **at least once**.

```typescript
let i = 6;

do
{
    console.log(i);

    i++;
}
while(i<=5);
```

Output

```text
6
```

Although the condition is false, the body runs once.

---

# while vs do...while

### while

```text
Condition

↓

Body
```

Condition checked first.

---

### do...while

```text
Body

↓

Condition
```

Body runs first.

---

# 8. for...of

One of the most commonly used loops in TypeScript.

Suppose

```typescript
let numbers = [10,20,30,40];
```

Instead of

```typescript
for(let i=0;i<numbers.length;i++)
{
    console.log(numbers[i]);
}
```

Write

```typescript
for(let number of numbers)
{
    console.log(number);
}
```

Output

```text
10
20
30
40
```

---

# Real Angular Example

```typescript
let employees =
[
    "Raman",
    "John",
    "David"
];

for(let employee of employees)
{
    console.log(employee);
}
```

Output

```text
Raman
John
David
```

---

# 9. for...in

Returns the **keys (indexes)**.

```typescript
let numbers = [10,20,30];
```

```typescript
for(let index in numbers)
{
    console.log(index);
}
```

Output

```text
0
1
2
```

Access values

```typescript
for(let index in numbers)
{
    console.log(numbers[index]);
}
```

Output

```text
10
20
30
```

---

# Difference

### for...of

Returns

```text
Values
```

Example

```text
10

20

30
```

---

### for...in

Returns

```text
Indexes
```

Example

```text
0

1

2
```

---

# 10. break

Stops the loop immediately.

```typescript
for(let i=1;i<=10;i++)
{
    if(i==5)
    {
        break;
    }

    console.log(i);
}
```

Output

```text
1
2
3
4
```

---

# 11. continue

Skips the current iteration.

```typescript
for(let i=1;i<=5;i++)
{
    if(i==3)
    {
        continue;
    }

    console.log(i);
}
```

Output

```text
1
2
4
5
```

Notice that `3` is skipped.

---

# Real Angular Example

Imagine filtering inactive users:

```typescript
let users = [
    { name: "Raman", active: true },
    { name: "John", active: false },
    { name: "David", active: true }
];

for(let user of users)
{
    if(!user.active)
    {
        continue;
    }

    console.log(user.name);
}
```

Output

```text
Raman
David
```

---

# Summary

| Statement    | Purpose                              |
| ------------ | ------------------------------------ |
| `if`         | Execute code if condition is true    |
| `if...else`  | Choose between two paths             |
| `else if`    | Multiple conditions                  |
| `switch`     | Compare one value against many cases |
| `for`        | Loop a known number of times         |
| `while`      | Loop while a condition is true       |
| `do...while` | Execute at least once                |
| `for...of`   | Iterate over values                  |
| `for...in`   | Iterate over indexes or object keys  |
| `break`      | Exit a loop                          |
| `continue`   | Skip the current iteration           |

---

# Best Practices

✅ Use `for...of` for arrays when you need the values.

```typescript
for (let employee of employees)
{
    console.log(employee);
}
```

✅ Use `for...in` mainly for object properties or when you specifically need array indexes.

```typescript
for (let key in employee)
{
    console.log(key);
}
```

✅ Prefer `switch` over a long chain of `if...else` when comparing one variable against many fixed values.

---

# Practice Exercises

### Exercise 1

Predict the output:

```typescript
for(let i=1;i<=3;i++)
{
    console.log(i);
}
```

---

### Exercise 2

Predict the output:

```typescript
let x = 5;

while(x>2)
{
    console.log(x);

    x--;
}
```

---

### Exercise 3

What is the output?

```typescript
let colors = ["Red","Green","Blue"];

for(let color of colors)
{
    console.log(color);
}
```

---

### Exercise 4

What is the output?

```typescript
let colors = ["Red","Green","Blue"];

for(let index in colors)
{
    console.log(index);
}
```

---

# Interview Questions

1. What is the difference between `for...of` and `for...in`?
2. When would you use `while` instead of `for`?
3. What is the difference between `break` and `continue`?
4. Why is `break` important in a `switch` statement?
5. What is the difference between `while` and `do...while`?

---

# What's Next?

Now we've covered:

* ✅ Variables
* ✅ Data Types
* ✅ Operators
* ✅ Control Flow

The next lesson is **Functions**, which is one of the most important topics before Angular.

We'll learn:

* Function declaration
* Function expressions
* Arrow functions (`=>`)
* Parameters
* Optional parameters
* Default parameters
* Rest parameters
* Return values
* Function overloading
* Anonymous functions

Understanding functions well is essential because Angular components, services, event handlers, and lifecycle methods are all built around them.
