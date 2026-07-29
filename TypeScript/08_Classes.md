Excellent! This is probably the **most important TypeScript lesson for Angular**.

If you understand **classes**, then Angular Components, Services, Pipes, Directives, Guards, and Interceptors will all become much easier because **everything in Angular is a class**.

---

# Lesson 8: Classes and Objects in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

* What is an Object?
* What is a Class?
* Creating Classes
* Creating Objects
* Properties
* Methods
* Constructors
* `this` keyword
* Access Modifiers
* Readonly Properties
* Static Members
* Getters & Setters

---

# What is an Object?

Think about a real Employee.

An employee has

* Employee Id
* Name
* Salary
* Department

An employee can also perform actions

* Work
* Login
* Logout

Everything together becomes an object.

```text
Employee

Properties
----------
Id
Name
Salary
Department

Methods
-------
Work()
Login()
Logout()
```

---

# Real World Examples

A Car

Properties

```text
Color

Model

Speed

Brand
```

Methods

```text
Start()

Stop()

Brake()
```

---

A Student

Properties

```text
Id

Name

Marks
```

Methods

```text
Study()

AttendExam()
```

---

# What is a Class?

A class is a **blueprint**.

Imagine an architect.

Before constructing a house, he creates a blueprint.

```text
Blueprint

↓

House
```

Similarly

```text
Class

↓

Object
```

A class describes

* What properties an object has
* What methods an object can perform

---

# Creating a Class

Syntax

```typescript
class Employee
{

}
```

Currently it is just an empty blueprint.

---

# Adding Properties

```typescript
class Employee
{
    id:number = 0;

    name:string = "";

    salary:number = 0;
}
```

---

# Creating an Object

A class alone doesn't allocate memory.

Create an object.

```typescript
let emp = new Employee();
```

Memory

```text
Employee Class

↓

new

↓

Employee Object
```

---

# Access Properties

```typescript
class Employee
{
    id:number = 0;

    name:string = "";

    salary:number = 0;
}

let emp = new Employee();

emp.id = 101;

emp.name = "Raman";

emp.salary = 50000;

console.log(emp.id);

console.log(emp.name);

console.log(emp.salary);
```

Output

```text
101

Raman

50000
```

---

# Multiple Objects

One class

Many objects

```typescript
let emp1 = new Employee();

let emp2 = new Employee();

let emp3 = new Employee();
```

Memory

```text
Employee Class

↓

Object 1

↓

Object 2

↓

Object 3
```

Exactly like C#.

---

# Methods

Methods define the behavior of an object.

```typescript
class Employee
{
    id:number = 0;

    name:string = "";

    display()
    {
        console.log(this.id);

        console.log(this.name);
    }
}
```

---

# Calling a Method

```typescript
let emp = new Employee();

emp.id = 101;

emp.name = "Raman";

emp.display();
```

Output

```text
101

Raman
```

---

# Understanding `this`

Inside a class

```typescript
this
```

refers to the **current object**.

Example

```typescript
class Employee
{
    id:number = 0;

    show()
    {
        console.log(this.id);
    }
}
```

Suppose

```typescript
let emp = new Employee();

emp.id = 200;

emp.show();
```

Then

```text
this

↓

emp
```

Output

```text
200
```

---

# Constructor

Instead of writing

```typescript
let emp = new Employee();

emp.id = 101;

emp.name = "Raman";

emp.salary = 50000;
```

Initialize values automatically.

```typescript
class Employee
{
    id:number;

    name:string;

    salary:number;

    constructor(id:number,name:string,salary:number)
    {
        this.id=id;

        this.name=name;

        this.salary=salary;
    }
}
```

Create object

```typescript
let emp = new Employee(101,"Raman",50000);

console.log(emp.name);
```

Output

```text
Raman
```

---

# Constructor Executes Automatically

Notice

```typescript
let emp = new Employee(...);
```

You never call

```typescript
constructor();
```

The constructor executes automatically.

---

# Constructor vs Method

| Constructor                                  | Method                  |
| -------------------------------------------- | ----------------------- |
| Same name as class? No, always `constructor` | Any name                |
| Executes automatically                       | Must be called          |
| Used for initialization                      | Used for business logic |

---

# Parameter Properties (TypeScript Feature)

Instead of

```typescript
class Employee
{
    id:number;

    name:string;

    constructor(id:number,name:string)
    {
        this.id=id;

        this.name=name;
    }
}
```

Write

```typescript
class Employee
{
    constructor(
        public id:number,
        public name:string
    )
    {

    }
}
```

TypeScript automatically creates the properties.

Very common in Angular.

---

# Access Modifiers

Exactly like C#.

---

## Public

Default modifier.

```typescript
class Employee
{
    public name:string = "Raman";
}
```

Accessible everywhere.

---

## Private

```typescript
class Employee
{
    private salary:number = 50000;
}
```

Outside the class

```typescript
let emp = new Employee();

console.log(emp.salary);
```

Error

```text
Property 'salary' is private.
```

---

## Protected

Accessible

* Inside the class
* Inside child classes

Not accessible outside.

We'll revisit this when we cover inheritance.

---

# Readonly

Suppose Employee ID should never change.

```typescript
class Employee
{
    readonly id:number;

    constructor(id:number)
    {
        this.id=id;
    }
}
```

Now

```typescript
emp.id = 200;
```

Compiler Error

```text
Cannot assign because it is readonly.
```

---

# Static Members

Static belongs to the class, not an object.

Example

```typescript
class Employee
{
    static company = "KMIT";
}
```

Access

```typescript
console.log(Employee.company);
```

No object required.

---

# Normal vs Static

Normal

```typescript
let emp = new Employee();

console.log(emp.name);
```

Static

```typescript
console.log(Employee.company);
```

---

# Getters

```typescript
class Employee
{
    private _salary = 50000;

    get salary()
    {
        return this._salary;
    }
}

let emp = new Employee();

console.log(emp.salary);
```

Output

```text
50000
```

Notice that you access it like a property (`emp.salary`), even though a getter method is called behind the scenes.

---

# Setters

```typescript
class Employee
{
    private _salary = 0;

    set salary(value:number)
    {
        if(value>=0)
        {
            this._salary = value;
        }
    }

    get salary()
    {
        return this._salary;
    }
}

let emp = new Employee();

emp.salary = 50000;

console.log(emp.salary);
```

Output

```text
50000
```

The setter lets you validate data before storing it.

---

# Complete Example

```typescript
class Employee
{
    constructor(
        public id:number,
        public name:string,
        private salary:number
    )
    {

    }

    display()
    {
        console.log(this.id);
        console.log(this.name);
        console.log(this.salary);
    }
}

let emp = new Employee(
    101,
    "Raman",
    50000
);

emp.display();
```

Output

```text
101
Raman
50000
```

---

# How Angular Uses Classes

Suppose Angular Component

```typescript
export class EmployeeComponent
{
    title = "Employees";

    total = 100;

    loadEmployees()
    {
        console.log("Loading...");
    }

    saveEmployee()
    {
        console.log("Saving...");
    }
}
```

Notice

Everything you learned

* Properties
* Methods
* Class

is directly used in Angular.

---

# Summary

| Concept     | Example                            |
| ----------- | ---------------------------------- |
| Class       | `class Employee {}`                |
| Object      | `new Employee()`                   |
| Property    | `name:string`                      |
| Method      | `display()`                        |
| Constructor | `constructor(...)`                 |
| this        | Current object                     |
| public      | Accessible everywhere              |
| private     | Accessible only inside class       |
| protected   | Accessible in class and subclasses |
| readonly    | Value cannot be reassigned         |
| static      | Belongs to the class               |
| getter      | `get salary()`                     |
| setter      | `set salary()`                     |

---

# C# vs TypeScript Classes

Since you already know C#, you'll notice many similarities.

| C#               | TypeScript       |
| ---------------- | ---------------- |
| `class Employee` | `class Employee` |
| Constructor      | `constructor()`  |
| `this`           | `this`           |
| `public`         | `public`         |
| `private`        | `private`        |
| `protected`      | `protected`      |
| `readonly`       | `readonly`       |
| `static`         | `static`         |
| Properties       | Properties       |
| Methods          | Methods          |

The biggest syntax difference is that TypeScript uses `constructor()` instead of naming the constructor after the class.

---

# Practice Exercises

### Exercise 1

Create a `Student` class with:

* `id`
* `name`
* `marks`

Create an object and print the values.

---

### Exercise 2

Add a method:

```typescript
display()
```

that prints all the student details.

---

### Exercise 3

Modify the class to use a constructor for initialization instead of assigning values after object creation.

---

### Exercise 4

Add a private property:

```typescript
fees
```

and create getter and setter methods to control access.

---

# Interview Questions

1. What is the difference between a class and an object?
2. What is the purpose of a constructor?
3. What does the `this` keyword refer to?
4. What is the difference between `public`, `private`, and `protected`?
5. What is a static member?
6. What are getters and setters, and why are they useful?

---

# What's Next?

The next lesson is **Object-Oriented Programming (OOP) in TypeScript**, where we'll cover:

* Inheritance
* Method Overriding
* `super`
* Abstract Classes
* Interfaces
* Polymorphism

These concepts are used extensively in Angular. For example:

* Services often implement interfaces.
* Angular lifecycle hooks such as `OnInit` are interfaces.
* Components inherit behavior from base classes in many real-world applications.

Once you complete that lesson, you'll have all the TypeScript OOP knowledge needed before diving into Angular itself.
