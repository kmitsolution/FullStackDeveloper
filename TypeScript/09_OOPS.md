Excellent! This is the **last major TypeScript OOP lesson** before we start Angular. Once you understand these concepts, you'll recognize them everywhere in Angular.

# Lesson 9: Object-Oriented Programming (OOP) in TypeScript

## Learning Objectives

By the end of this lesson, you will understand:

* Inheritance
* Method Overriding
* `super`
* Abstract Classes
* Interfaces
* Polymorphism
* `instanceof`

---

# What is OOP?

Object-Oriented Programming (OOP) is a programming paradigm that organizes code using **classes** and **objects**.

The four pillars of OOP are:

```text
1. Encapsulation
2. Inheritance
3. Polymorphism
4. Abstraction
```

You've already learned encapsulation using `private`, `public`, and `protected`.

Now let's learn the remaining concepts.

---

# 1. Inheritance

## What is Inheritance?

Inheritance allows one class to reuse the properties and methods of another class.

Real-world example:

```text
Vehicle
   │
   ├── Car
   ├── Bike
   └── Bus
```

All vehicles have:

* Brand
* Speed
* Start()
* Stop()

Instead of writing the same code in every class, we inherit it.

---

## Parent Class

```typescript
class Person
{
    name:string = "";

    age:number = 0;

    display()
    {
        console.log(this.name);
        console.log(this.age);
    }
}
```

---

## Child Class

```typescript
class Employee extends Person
{
    salary:number = 0;
}
```

Notice

```typescript
extends
```

means

> Employee inherits Person.

---

## Create Object

```typescript
let emp = new Employee();

emp.name = "Raman";
emp.age = 25;
emp.salary = 50000;

emp.display();

console.log(emp.salary);
```

Output

```text
Raman
25
50000
```

Notice

Employee inherited

* name
* age
* display()

---

# Memory

```text
Person

↓

name

age

display()

↓

Employee

↓

salary
```

---

# 2. Constructor in Inheritance

Parent

```typescript
class Person
{
    constructor(
        public name:string,
        public age:number
    )
    {

    }
}
```

Child

```typescript
class Employee extends Person
{
    constructor(
        name:string,
        age:number,
        public salary:number
    )
    {
        super(name,age);
    }
}
```

---

# What is super?

`super()` calls the constructor of the parent class.

Without it

```typescript
class Employee extends Person
{
    constructor()
    {

    }
}
```

Compiler Error

Because the parent constructor must be executed first.

---

# Flow

```text
new Employee()

↓

Employee Constructor

↓

super()

↓

Person Constructor

↓

Employee Constructor Continues
```

---

# 3. Method Overriding

Parent

```typescript
class Animal
{
    sound()
    {
        console.log("Animal Sound");
    }
}
```

Child

```typescript
class Dog extends Animal
{
    sound()
    {
        console.log("Bark");
    }
}
```

Object

```typescript
let dog = new Dog();

dog.sound();
```

Output

```text
Bark
```

The child version replaces the parent version.

---

# Calling Parent Method

```typescript
class Dog extends Animal
{
    sound()
    {
        super.sound();

        console.log("Bark");
    }
}
```

Output

```text
Animal Sound
Bark
```

---

# 4. Abstract Class

Sometimes you don't want anyone to create objects from a class.

Example

```text
Shape
```

No one creates a generic Shape.

People create

* Circle
* Rectangle
* Square

---

## Abstract Class

```typescript
abstract class Shape
{
    abstract area():number;
}
```

Cannot do

```typescript
let s = new Shape();
```

Compiler Error

---

## Child Class

```typescript
class Circle extends Shape
{
    constructor(public radius:number)
    {
        super();
    }

    area():number
    {
        return 3.14*this.radius*this.radius;
    }
}
```

Object

```typescript
let c = new Circle(10);

console.log(c.area());
```

Output

```text
314
```

---

# Abstract Method

```typescript
abstract area():number;
```

No implementation.

Every child must implement it.

---

# 5. Interface

An interface defines a **contract**.

It specifies **what** a class must provide, not **how** it provides it.

---

## Interface Example

```typescript
interface Employee
{
    id:number;

    name:string;
}
```

---

## Object

```typescript
let emp:Employee =
{
    id:1,
    name:"Raman"
};

console.log(emp.name);
```

Output

```text
Raman
```

---

## Interface with Class

```typescript
interface Employee
{
    display():void;
}
```

Implement

```typescript
class Manager implements Employee
{
    display()
    {
        console.log("Manager");
    }
}
```

---

# implements vs extends

| extends               | implements                  |
| --------------------- | --------------------------- |
| Inherit another class | Implement an interface      |
| Reuse code            | Follow a contract           |
| One parent class      | Multiple interfaces allowed |

---

# Multiple Interfaces

```typescript
interface A
{
    show():void;
}

interface B
{
    print():void;
}

class Demo implements A,B
{
    show()
    {
        console.log("Show");
    }

    print()
    {
        console.log("Print");
    }
}
```

---

# 6. Polymorphism

"Poly" means many.

"Morphism" means forms.

One interface

Many implementations.

Example

```typescript
class Animal
{
    sound()
    {
        console.log("Animal");
    }
}
```

Children

```typescript
class Dog extends Animal
{
    sound()
    {
        console.log("Bark");
    }
}

class Cat extends Animal
{
    sound()
    {
        console.log("Meow");
    }
}
```

Now

```typescript
let animal:Animal;

animal = new Dog();

animal.sound();

animal = new Cat();

animal.sound();
```

Output

```text
Bark
Meow
```

Notice

Same variable

Different behavior.

That's polymorphism.

---

# 7. instanceof

Checks object type.

```typescript
let dog = new Dog();

console.log(dog instanceof Dog);
```

Output

```text
true
```

Another

```typescript
console.log(dog instanceof Animal);
```

Output

```text
true
```

Because Dog inherits Animal.

---

# Complete Example

```typescript
abstract class Person
{
    constructor(
        public name:string
    )
    {

    }

    abstract work():void;
}

class Employee extends Person
{
    work()
    {
        console.log(this.name + " is working");
    }
}

class Manager extends Person
{
    work()
    {
        console.log(this.name + " is managing");
    }
}

let people:Person[] =
[
    new Employee("Raman"),
    new Manager("John")
];

for(let person of people)
{
    person.work();
}
```

Output

```text
Raman is working
John is managing
```

This is a great example of **inheritance**, **abstraction**, and **polymorphism** working together.

---

# How Angular Uses OOP

## 1. Interfaces

Angular lifecycle hooks are interfaces.

```typescript
import { OnInit } from '@angular/core';

export class EmployeeComponent implements OnInit
{
    ngOnInit()
    {
        console.log("Loaded");
    }
}
```

`OnInit` is an interface that requires you to implement the `ngOnInit()` method.

---

## 2. Inheritance

Many teams create a base component.

```typescript
class BaseComponent
{
    showMessage()
    {
        console.log("Common Message");
    }
}

class EmployeeComponent extends BaseComponent
{

}
```

Now every component can reuse `showMessage()`.

---

## 3. Polymorphism

Services often implement interfaces.

```typescript
interface Logger
{
    log(message:string):void;
}
```

Implementations

```typescript
class ConsoleLogger implements Logger
{
    log(message:string)
    {
        console.log(message);
    }
}

class FileLogger implements Logger
{
    log(message:string)
    {
        // Write to file
    }
}
```

The rest of the application depends on the `Logger` interface, not a specific implementation.

---

# Summary

| Concept             | Keyword                            |
| ------------------- | ---------------------------------- |
| Inheritance         | `extends`                          |
| Parent Constructor  | `super()`                          |
| Method Overriding   | Override same method               |
| Abstract Class      | `abstract`                         |
| Interface           | `interface`                        |
| Implement Interface | `implements`                       |
| Polymorphism        | Same interface, different behavior |
| Type Checking       | `instanceof`                       |

---

# Class vs Abstract Class vs Interface

| Feature               | Class     | Abstract Class | Interface             |
| --------------------- | --------- | -------------- | --------------------- |
| Create Object         | ✅         | ❌              | ❌                     |
| Constructor           | ✅         | ✅              | ❌                     |
| Method Implementation | ✅         | ✅              | ❌ (only declarations) |
| Properties            | ✅         | ✅              | ✅                     |
| Inheritance           | `extends` | `extends`      | `implements`          |

---

# Practice Exercises

### Exercise 1

Create:

* `Person`
* `Employee extends Person`

Add properties and a `display()` method.

---

### Exercise 2

Create an abstract class:

```typescript
abstract class Vehicle
```

Add an abstract method:

```typescript
start()
```

Implement it in:

* `Car`
* `Bike`

---

### Exercise 3

Create an interface:

```typescript
Printable
```

Add:

```typescript
print()
```

Implement it in a `Student` class.

---

### Exercise 4

Create:

```typescript
Animal
```

and child classes:

* `Dog`
* `Cat`
* `Cow`

Store them in an array of `Animal` and call `sound()` on each to observe polymorphism.

---

# Interview Questions

1. What is inheritance?
2. What is the purpose of `super()`?
3. What is method overriding?
4. What is the difference between an abstract class and an interface?
5. What is the difference between `extends` and `implements`?
6. What is polymorphism? Give a real-world example.
7. When would you choose an interface instead of an abstract class?

---

# What's Next?

At this point, you've learned the core TypeScript language:

* ✅ Variables
* ✅ Data Types
* ✅ Operators
* ✅ Control Flow
* ✅ Functions
* ✅ Classes
* ✅ OOP

Before starting Angular, there are a few TypeScript features that Angular uses heavily:

1. **Arrays and Array Methods** (`map`, `filter`, `find`, `reduce`, etc.)
2. **Modules (`import` / `export`)**
3. **Generics**
4. **Enums**
5. **Decorators** (especially important because Angular is built on decorators)

I recommend learning them in that order, because you'll encounter all of them in your very first Angular component.
