Excellent! This lesson is **extremely important for Angular** because Angular works extensively with arrays.

Examples:

* Displaying employees in a table
* Showing products
* Displaying orders
* Calling APIs
* Filtering data
* Searching
* Sorting

Almost every Angular application manipulates arrays.

---

# Lesson 10: Arrays and Array Methods in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

* Arrays
* Creating Arrays
* Accessing Elements
* Looping Arrays
* Array Methods
* map()
* filter()
* find()
* findIndex()
* some()
* every()
* includes()
* sort()
* reverse()
* push()
* pop()
* shift()
* unshift()
* splice()
* slice()
* reduce()

---

# What is an Array?

An array stores **multiple values of the same type**.

Instead of

```typescript
let emp1 = "Raman";
let emp2 = "John";
let emp3 = "David";
let emp4 = "Sam";
```

Use

```typescript
let employees = [
    "Raman",
    "John",
    "David",
    "Sam"
];
```

One variable

Many values

---

# Memory Representation

```text
employees

0 → Raman

1 → John

2 → David

3 → Sam
```

Arrays always start from **index 0**.

---

# Creating Arrays

## Method 1

```typescript
let numbers:number[] = [10,20,30];
```

---

## Method 2

```typescript
let cities:Array<string> =
[
    "Bangalore",
    "Hyderabad",
    "Delhi"
];
```

Both are correct.

Most developers prefer

```typescript
number[]
```

because it is shorter.

---

# Accessing Elements

```typescript
let numbers = [10,20,30];

console.log(numbers[0]);

console.log(numbers[1]);

console.log(numbers[2]);
```

Output

```text
10
20
30
```

---

# Length

```typescript
let numbers = [10,20,30];

console.log(numbers.length);
```

Output

```text
3
```

---

# Changing Elements

```typescript
let numbers = [10,20,30];

numbers[1] = 50;

console.log(numbers);
```

Output

```text
10
50
30
```

---

# Looping Arrays

Traditional

```typescript
let numbers = [10,20,30];

for(let i=0;i<numbers.length;i++)
{
    console.log(numbers[i]);
}
```

---

Better

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
```

---

# Arrays of Objects

Very important.

```typescript
class Employee
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

Create array

```typescript
let employees =
[
    new Employee(1,"Raman",50000),
    new Employee(2,"John",60000),
    new Employee(3,"David",70000)
];
```

Loop

```typescript
for(let employee of employees)
{
    console.log(employee.name);
}
```

Output

```text
Raman
John
David
```

Angular APIs almost always return arrays of objects like this.

---

# push()

Adds an item at the end.

```typescript
let numbers = [10,20];

numbers.push(30);

console.log(numbers);
```

Output

```text
10
20
30
```

---

# pop()

Removes the last item.

```typescript
let numbers = [10,20,30];

numbers.pop();

console.log(numbers);
```

Output

```text
10
20
```

---

# unshift()

Adds at the beginning.

```typescript
let numbers = [20,30];

numbers.unshift(10);

console.log(numbers);
```

Output

```text
10
20
30
```

---

# shift()

Removes the first item.

```typescript
let numbers = [10,20,30];

numbers.shift();

console.log(numbers);
```

Output

```text
20
30
```

---

# includes()

Checks whether an item exists.

```typescript
let numbers = [10,20,30];

console.log(numbers.includes(20));
```

Output

```text
true
```

---

# indexOf()

```typescript
let numbers = [10,20,30];

console.log(numbers.indexOf(20));
```

Output

```text
1
```

---

# map()

One of the **most important functions** in Angular.

Suppose

```typescript
let numbers = [10,20,30];
```

Multiply each by 10.

```typescript
let result = numbers.map(x=>x*10);

console.log(result);
```

Output

```text
100
200
300
```

Original array

```text
10
20
30
```

New array

```text
100
200
300
```

Notice

`map()` never changes the original array.

---

# Real Angular Example

Suppose

```typescript
let employees =
[
    new Employee(1,"Raman",50000),
    new Employee(2,"John",60000)
];
```

Get only names

```typescript
let names =
employees.map(emp=>emp.name);

console.log(names);
```

Output

```text
Raman
John
```

Very common in Angular.

---

# filter()

Returns matching records.

```typescript
let numbers =
[
10,
20,
30,
40,
50
];
```

Keep values greater than 25.

```typescript
let result =
numbers.filter(x=>x>25);

console.log(result);
```

Output

```text
30
40
50
```

---

# Angular Example

Employees

```typescript
let employees =
[
    new Employee(1,"Raman",50000),
    new Employee(2,"John",25000),
    new Employee(3,"David",70000)
];
```

Filter

```typescript
let result =
employees.filter(emp=>emp.salary>40000);
```

Result

```text
Raman
David
```

---

# find()

Returns the first matching item.

```typescript
let numbers =
[
10,
20,
30,
40
];
```

```typescript
let result =
numbers.find(x=>x>25);

console.log(result);
```

Output

```text
30
```

---

# findIndex()

```typescript
let numbers =
[
10,
20,
30
];
```

```typescript
console.log(
numbers.findIndex(x=>x==20)
);
```

Output

```text
1
```

---

# some()

Checks whether **at least one** item matches.

```typescript
let numbers =
[
10,
20,
30
];
```

```typescript
console.log(
numbers.some(x=>x>25)
);
```

Output

```text
true
```

---

# every()

Checks whether **all** items match.

```typescript
console.log(
numbers.every(x=>x>5)
);
```

Output

```text
true
```

---

Another

```typescript
console.log(
numbers.every(x=>x>15)
);
```

Output

```text
false
```

---

# sort()

```typescript
let numbers =
[
50,
10,
40,
20
];

numbers.sort();

console.log(numbers);
```

Output

```text
10
20
40
50
```

---

Objects

```typescript
employees.sort(
(a,b)=>a.salary-b.salary
);
```

Sorts employees by salary.

---

# reverse()

```typescript
numbers.reverse();
```

Output

```text
50
40
20
10
```

---

# slice()

Creates a new array.

```typescript
let numbers =
[
10,
20,
30,
40,
50
];
```

```typescript
let result =
numbers.slice(1,4);

console.log(result);
```

Output

```text
20
30
40
```

Original array remains unchanged.

---

# splice()

Changes the original array.

```typescript
let numbers =
[
10,
20,
30,
40
];
```

Remove

```typescript
numbers.splice(1,2);

console.log(numbers);
```

Output

```text
10
40
```

---

Insert

```typescript
numbers.splice(1,0,25);
```

Output

```text
10
25
40
```

---

# reduce()

Very important.

Sum all numbers.

```typescript
let numbers =
[
10,
20,
30
];
```

```typescript
let total =
numbers.reduce(
(sum,x)=>sum+x,
0
);

console.log(total);
```

Output

```text
60
```

Flow

```text
0+10=10

10+20=30

30+30=60
```

---

# Complete Angular Example

```typescript
class Employee
{
    constructor(
        public id:number,
        public name:string,
        public salary:number
    )
    {

    }
}

let employees =
[
    new Employee(1,"Raman",50000),
    new Employee(2,"John",25000),
    new Employee(3,"David",70000)
];

let highSalary =
employees
.filter(emp=>emp.salary>30000)
.map(emp=>emp.name);

console.log(highSalary);
```

Output

```text
Raman
David
```

Notice how we **chain** methods together.

---

# Summary

| Method      | Purpose                |
| ----------- | ---------------------- |
| push()      | Add at end             |
| pop()       | Remove from end        |
| shift()     | Remove first           |
| unshift()   | Add first              |
| includes()  | Check value exists     |
| map()       | Transform items        |
| filter()    | Select matching items  |
| find()      | First matching item    |
| findIndex() | Index of first match   |
| some()      | At least one matches   |
| every()     | All match              |
| sort()      | Sort array             |
| reverse()   | Reverse array          |
| slice()     | Copy part of array     |
| splice()    | Add/remove items       |
| reduce()    | Combine into one value |

---

# Array Methods Most Used in Angular

If you learn only five methods really well, make them these:

1. `map()`
2. `filter()`
3. `find()`
4. `some()`
5. `reduce()`

You'll use them constantly when processing API data.

---

# Practice Exercises

### Exercise 1

Create an array of 5 employee names and print them using `for...of`.

---

### Exercise 2

Create an array of numbers and use `map()` to square each number.

Expected:

```text
1
4
9
16
25
```

---

### Exercise 3

Create an array of employees and use `filter()` to display employees with a salary greater than 50,000.

---

### Exercise 4

Use `reduce()` to calculate the total salary of all employees.

---

# Interview Questions

1. What is the difference between `map()` and `filter()`?
2. What is the difference between `find()` and `filter()`?
3. What is the difference between `slice()` and `splice()`?
4. Does `map()` modify the original array?
5. When would you use `reduce()`?
6. What is the difference between `some()` and `every()`?

---

# What's Next?

You've now learned almost all of the TypeScript language you'll use daily.

The next lesson is **Modules (`import` and `export`)**, where you'll learn how TypeScript organizes code across multiple files.

This is a critical concept because every Angular application consists of many files, and you'll constantly use:

* `export`
* `import`
* Named exports
* Default exports
* Re-exporting

Understanding modules is the final step before we begin building Angular applications.
