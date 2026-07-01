# Chapter – Access Modifiers in C#

## What are Access Modifiers?

**Access Modifiers** define the **visibility (accessibility)** of classes, methods, fields, properties, constructors, and other members.

They determine:

* Who can access a member?
* From where can it be accessed?
* Which classes or assemblies are allowed to use it?

Think of access modifiers as **security levels** for your code.

---

# Why Do We Need Access Modifiers?

Imagine a Bank Account class.

```csharp
class BankAccount
{
    public string AccountHolder;

    public double Balance;
}
```

Anyone can write:

```csharp
account.Balance = -50000;
```

This is incorrect because a bank account should not allow anyone to modify the balance directly.

Instead,

```csharp
class BankAccount
{
    private double balance;

    public void Deposit(double amount)
    {
        balance += amount;
    }
}
```

Now the balance can only be modified through controlled methods.

This is called **Encapsulation**, one of the four pillars of Object-Oriented Programming.

---

# Types of Access Modifiers

C# provides six access modifiers.

| Access Modifier    | Accessible Within Same Class | Same Project (Assembly) | Derived Class | Other Project (Assembly) |
| ------------------ | ---------------------------- | ----------------------- | ------------- | ------------------------ |
| public             | ✔                            | ✔                       | ✔             | ✔                        |
| private            | ✔                            | ✘                       | ✘             | ✘                        |
| protected          | ✔                            | ✘*                      | ✔             | ✔*                       |
| internal           | ✔                            | ✔                       | ✔             | ✘                        |
| protected internal | ✔                            | ✔                       | ✔             | ✔*                       |
| private protected  | ✔                            | ✔*                      | ✔*            | ✘                        |

* Subject to inheritance rules.

---

# 1. public

The `public` access modifier allows access **from anywhere**.

It has the highest visibility.

---

## Example

```csharp
class Employee
{
    public string Name = "Raman";
}
```

Using it

```csharp
Employee emp = new Employee();

Console.WriteLine(emp.Name);
```

Output

```text
Raman
```

`Name` can be accessed:

* Same class
* Another class
* Another project
* Derived classes

---

# Real World Example

```csharp
public class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }
}
```

Using it

```csharp
Calculator calculator = new Calculator();

Console.WriteLine(calculator.Add(10,20));
```

Output

```text
30
```

---

# 2. private

The `private` access modifier allows access **only within the same class**.

This is the **default access modifier for class members** if none is specified.

---

## Example

```csharp
class Employee
{
    private double salary = 50000;

    public void Display()
    {
        Console.WriteLine(salary);
    }
}
```

Valid

```csharp
Employee emp = new Employee();

emp.Display();
```

Output

```text
50000
```

Invalid

```csharp
Employee emp = new Employee();

Console.WriteLine(emp.salary);
```

Compiler Error

```
'salary' is inaccessible due to its protection level.
```

---

# Why Use private?

It protects internal data.

Example

```csharp
class BankAccount
{
    private double balance;

    public void Deposit(double amount)
    {
        balance += amount;
    }

    public double GetBalance()
    {
        return balance;
    }
}
```

Users cannot modify `balance` directly.

---

# 3. protected

A `protected` member is accessible:

* Inside the same class
* Inside derived (child) classes

Not accessible by unrelated classes.

---

Example

```csharp
class Employee
{
    protected string Name = "Raman";
}

class Manager : Employee
{
    public void Display()
    {
        Console.WriteLine(Name);
    }
}
```

Output

```text
Raman
```

---

Invalid

```csharp
Employee emp = new Employee();

Console.WriteLine(emp.Name);
```

Compiler Error

---

Memory

```text
Employee

|

protected Name

|

Manager (inherits Employee)

|

Can Access
```

---

# 4. internal

`internal` allows access only within the same **Assembly (Project)**.

---

Suppose we have

Project

```
CompanyApp

|

Employee
```

Example

```csharp
internal class Employee
{
}
```

Any class inside the same project can use it.

```csharp
Employee emp = new Employee();
```

Valid

---

Another Project

```
PayrollApp
```

Trying to use

```csharp
Employee emp = new Employee();
```

Compiler Error

---

# What is an Assembly?

An Assembly is the compiled output of a .NET project.

Examples

```
Employee.dll

Payroll.dll

Company.exe
```

Every project generates one assembly.

---

# 5. protected internal

This is a combination of

```
protected

OR

internal
```

Notice the word **OR**.

It means access is allowed if **either** condition is true.

Accessible

✔ Same Assembly

✔ Derived Class

Even if the derived class is in another assembly.

---

Example

```csharp
class Employee
{
    protected internal string Name = "Raman";
}
```

Derived Class

```csharp
class Manager : Employee
{
    public void Display()
    {
        Console.WriteLine(Name);
    }
}
```

Valid

Also valid

```csharp
Employee emp = new Employee();

Console.WriteLine(emp.Name);
```

if both classes are in the same project.

---

# 6. private protected

This is

```
private

AND

protected
```

Notice the word **AND**.

It means

Accessible only

✔ Same Assembly

AND

✔ Derived Classes

---

Example

```csharp
class Employee
{
    private protected string Name = "Raman";
}
```

Derived class

```csharp
class Manager : Employee
{
    public void Display()
    {
        Console.WriteLine(Name);
    }
}
```

Valid

Only if `Manager` is inside the same project.

If Manager is in another project

Compiler Error.

---

# Accessibility Example (Same Class)

```csharp
class Employee
{
    public int A;

    private int B;

    protected int C;

    internal int D;

    protected internal int E;

    private protected int F;

    public void Display()
    {
        Console.WriteLine(A);
        Console.WriteLine(B);
        Console.WriteLine(C);
        Console.WriteLine(D);
        Console.WriteLine(E);
        Console.WriteLine(F);
    }
}
```

Everything is accessible inside the same class.

---

# Accessibility Example (Same Project)

```csharp
class Employee
{
    public int A;

    private int B;

    protected int C;

    internal int D;

    protected internal int E;

    private protected int F;
}

class Test
{
    void Demo()
    {
        Employee emp = new Employee();

        Console.WriteLine(emp.A);

        Console.WriteLine(emp.D);

        Console.WriteLine(emp.E);
    }
}
```

Accessible

✔ public

✔ internal

✔ protected internal

Not Accessible

✘ private

✘ protected

✘ private protected

---

# Accessibility Example (Derived Class)

```csharp
class Employee
{
    protected int Salary = 50000;
}

class Manager : Employee
{
    public void Display()
    {
        Console.WriteLine(Salary);
    }
}
```

Output

```text
50000
```

---

# Accessibility Across Assemblies

Suppose

Assembly 1

```
Company.dll
```

Contains

```csharp
public class Employee
{
    public int A;

    internal int B;

    protected int C;

    protected internal int D;

    private protected int E;
}
```

Assembly 2

```
Payroll.dll
```

### Normal Class

Accessible

✔ public

Not Accessible

✘ internal

✘ protected

✘ private protected

---

### Derived Class

```csharp
class Manager : Employee
{
}
```

Accessible

✔ public

✔ protected

✔ protected internal

Not Accessible

✘ internal

✘ private protected

---

# Visual Summary

| Modifier           | Same Class | Same Project | Derived Class (Same Project) | Derived Class (Different Project) | Other Project      |
| ------------------ | ---------- | ------------ | ---------------------------- | --------------------------------- | ------------------ |
| public             | ✔          | ✔            | ✔                            | ✔                                 | ✔                  |
| private            | ✔          | ✘            | ✘                            | ✘                                 | ✘                  |
| protected          | ✔          | ✘            | ✔                            | ✔                                 | ✘                  |
| internal           | ✔          | ✔            | ✔                            | ✘                                 | ✘                  |
| protected internal | ✔          | ✔            | ✔                            | ✔                                 | ✘ (unless derived) |
| private protected  | ✔          | ✘            | ✔ (same project only)        | ✘                                 | ✘                  |

---

# Access Modifiers in ASP.NET Core

Controller

```csharp
public class EmployeeController : Controller
{
}
```

Controllers must be `public`.

---

Service

```csharp
public class EmployeeService
{
    private readonly EmployeeRepository repository;
}
```

Repository is usually private because it should not be accessible outside the service.

---

Entity

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }
}
```

Properties are public so ASP.NET Core Model Binding and Entity Framework Core can access them.

---

# Best Practices

* Use **private** by default unless broader access is required.
* Expose data through **public properties** instead of public fields.
* Use **protected** for members intended only for inheritance.
* Use **internal** for helper classes that should remain within the project.
* Use **protected internal** only when both inheritance and same-assembly access are needed.
* Use **private protected** sparingly for advanced framework scenarios.

---

# Summary

| Modifier           | Purpose                                                                   |
| ------------------ | ------------------------------------------------------------------------- |
| public             | Accessible from anywhere                                                  |
| private            | Accessible only within the same class                                     |
| protected          | Accessible within the class and derived classes                           |
| internal           | Accessible only within the same assembly (project)                        |
| protected internal | Accessible from the same assembly or from derived classes in any assembly |
| private protected  | Accessible only from derived classes within the same assembly             |

---

# Interview Questions

### 1. Which is the most restrictive access modifier?

**Answer:** `private`

---

### 2. Which is the least restrictive access modifier?

**Answer:** `public`

---

### 3. What is the default access modifier for class members?

If no access modifier is specified for a class member (field, property, or method), it is **private**.

Example:

```csharp
class Employee
{
    int Id;   // private by default
}
```

---

### 4. What is the default access modifier for a top-level class?

A top-level class (declared directly in a namespace) is **internal** by default.

Example:

```csharp
class Employee
{
}
```

This is equivalent to:

```csharp
internal class Employee
{
}
```

---

### 5. What is the difference between `protected internal` and `private protected`?

| `protected internal`                                                          | `private protected`                                               |
| ----------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| Accessible from the same assembly **or** from derived classes in any assembly | Accessible only from derived classes **within the same assembly** |
| Uses **OR** semantics                                                         | Uses **AND** semantics                                            |

---

### 6. Why should fields usually be `private`?

Keeping fields private enforces **encapsulation**. It prevents external code from changing internal state directly and allows validation, logging, or business rules to be applied through properties or methods. This is a core principle followed throughout professional C# and ASP.NET Core development.
