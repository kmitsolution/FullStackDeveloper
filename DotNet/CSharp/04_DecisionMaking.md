# Decision Making in C#

Decision-making statements allow a program to **make choices** based on a condition. They help control the flow of execution by deciding which block of code should run.

In C#, decision-making is based on **Boolean expressions** that evaluate to either `true` or `false`.

For example:

```csharp
int age = 20;

Console.WriteLine(age >= 18);
```

Output:

```text
True
```

Since the condition evaluates to `true`, the program can perform actions such as allowing a user to log in, displaying a message, or processing a request.

---

# Types of Decision-Making Statements

C# provides the following decision-making statements:

1. `if`
2. `if-else`
3. `else if` Ladder
4. Nested `if`
5. `switch`
6. Switch Expression
7. Ternary Operator (`?:`)

---

# 1. if Statement

The `if` statement executes a block of code **only if the condition is true**.

### Syntax

```csharp
if(condition)
{
    // Statements
}
```

### Flow

```text
        Condition
            │
      ┌─────┴─────┐
      │           │
    True       False
      │           │
 Execute      Skip Block
```

### Example

```csharp
int age = 20;

if (age >= 18)
{
    Console.WriteLine("Eligible to vote");
}
```

Output

```text
Eligible to vote
```

If the condition is false, nothing is printed.

---

## Example 2

```csharp
int marks = 80;

if (marks >= 35)
{
    Console.WriteLine("Pass");
}
```

Output

```text
Pass
```

---

# 2. if-else Statement

The `if-else` statement executes one block if the condition is true and another block if it is false.

### Syntax

```csharp
if(condition)
{
    // True Block
}
else
{
    // False Block
}
```

### Flow

```text
            Condition
               │
        ┌──────┴──────┐
        │             │
      True         False
        │             │
  Execute if     Execute else
```

### Example

```csharp
int age = 16;

if (age >= 18)
{
    Console.WriteLine("Eligible to vote");
}
else
{
    Console.WriteLine("Not eligible to vote");
}
```

Output

```text
Not eligible to vote
```

---

## Example 2

```csharp
int number = 10;

if (number % 2 == 0)
{
    Console.WriteLine("Even Number");
}
else
{
    Console.WriteLine("Odd Number");
}
```

Output

```text
Even Number
```

---

# 3. else-if Ladder

When there are **multiple conditions**, use the `else if` ladder.

### Syntax

```csharp
if(condition1)
{
}
else if(condition2)
{
}
else if(condition3)
{
}
else
{
}
```

### Flow

```text
Condition 1
     │
True ─► Execute Block 1

False
 │
Condition 2
 │
True ─► Execute Block 2

False
 │
Condition 3
 │
True ─► Execute Block 3

False
 │
Else Block
```

### Example

```csharp
int marks = 75;

if (marks >= 90)
{
    Console.WriteLine("Grade A");
}
else if (marks >= 75)
{
    Console.WriteLine("Grade B");
}
else if (marks >= 50)
{
    Console.WriteLine("Grade C");
}
else
{
    Console.WriteLine("Fail");
}
```

Output

```text
Grade B
```

Only the **first matching condition** is executed.

---

# 4. Nested if Statement

An `if` statement inside another `if` statement is called a nested `if`.

### Syntax

```csharp
if(condition1)
{
    if(condition2)
    {
    }
}
```

### Example

```csharp
string username = "admin";
string password = "12345";

if (username == "admin")
{
    if (password == "12345")
    {
        Console.WriteLine("Login Successful");
    }
    else
    {
        Console.WriteLine("Invalid Password");
    }
}
else
{
    Console.WriteLine("Invalid Username");
}
```

Output

```text
Login Successful
```

---

## Real-World Example

```csharp
bool hasTicket = true;
int age = 25;

if (hasTicket)
{
    if (age >= 18)
    {
        Console.WriteLine("Entry Allowed");
    }
    else
    {
        Console.WriteLine("Under Age");
    }
}
else
{
    Console.WriteLine("Ticket Required");
}
```

---

# 5. switch Statement

The `switch` statement is used when comparing **one variable against multiple values**.

It is cleaner than writing many `else if` statements.

### Syntax

```csharp
switch(expression)
{
    case value1:
        break;

    case value2:
        break;

    default:
        break;
}
```

### Example

```csharp
int day = 3;

switch (day)
{
    case 1:
        Console.WriteLine("Monday");
        break;

    case 2:
        Console.WriteLine("Tuesday");
        break;

    case 3:
        Console.WriteLine("Wednesday");
        break;

    default:
        Console.WriteLine("Invalid Day");
        break;
}
```

Output

```text
Wednesday
```

---

## switch with String

```csharp
string role = "Admin";

switch (role)
{
    case "Admin":
        Console.WriteLine("Full Access");
        break;

    case "User":
        Console.WriteLine("Limited Access");
        break;

    default:
        Console.WriteLine("Guest Access");
        break;
}
```

Output

```text
Full Access
```

---

# 6. Switch Expression (C# 8+)

A switch expression is a shorter, more readable version of the traditional `switch` statement.

### Syntax

```csharp
variable = expression switch
{
    value1 => result1,
    value2 => result2,
    _ => defaultResult
};
```

### Example

```csharp
int day = 2;

string dayName = day switch
{
    1 => "Monday",
    2 => "Tuesday",
    3 => "Wednesday",
    4 => "Thursday",
    5 => "Friday",
    6 => "Saturday",
    7 => "Sunday",
    _ => "Invalid Day"
};

Console.WriteLine(dayName);
```

Output

```text
Tuesday
```

---

## Example 2

```csharp
char grade = 'A';

string message = grade switch
{
    'A' => "Excellent",
    'B' => "Very Good",
    'C' => "Good",
    'D' => "Average",
    _ => "Fail"
};

Console.WriteLine(message);
```

Output

```text
Excellent
```

---

# 7. Ternary Operator (`?:`)

The ternary operator is a shorthand for a simple `if-else` statement.

### Syntax

```csharp
condition ? valueIfTrue : valueIfFalse;
```

### Example

```csharp
int age = 20;

string result = age >= 18 ? "Adult" : "Minor";

Console.WriteLine(result);
```

Output

```text
Adult
```

Equivalent `if-else`:

```csharp
string result;

if (age >= 18)
{
    result = "Adult";
}
else
{
    result = "Minor";
}
```

---

## Example 2

```csharp
int number = 15;

string output = number % 2 == 0 ? "Even" : "Odd";

Console.WriteLine(output);
```

Output

```text
Odd
```

---

# Comparison of Decision-Making Statements

| Statement         | Used When                                 | Best For                                 |
| ----------------- | ----------------------------------------- | ---------------------------------------- |
| `if`              | One condition                             | Execute code only if a condition is true |
| `if-else`         | Two possible outcomes                     | True/False decisions                     |
| `else if` Ladder  | Multiple conditions                       | Grade calculation, validations           |
| Nested `if`       | Dependent conditions                      | Login, permissions, business rules       |
| `switch`          | One variable compared against many values | Menus, roles, day names, status codes    |
| Switch Expression | Compact form of `switch`                  | Modern C# code with simple mappings      |
| Ternary Operator  | Simple true/false assignment              | Short, readable assignments              |

---

# Best Practices

* Use **`if`** when you have only one condition.
* Use **`if-else`** when there are exactly two possible outcomes.
* Use **`else if`** for multiple range-based conditions (such as marks or salary brackets).
* Use **`switch`** when comparing one variable against many discrete values.
* Use **switch expressions** in modern C# for cleaner, more concise code.
* Use the **ternary operator** only for simple assignments; avoid nesting ternary operators as they reduce readability.
* Keep conditions simple and readable. If a condition becomes complex, consider breaking it into smaller methods or Boolean variables.

---

# Real-World Examples in ASP.NET Core

Decision-making statements are used throughout ASP.NET Core applications:

* **`if`**: Check whether a user is authenticated before accessing a resource.
* **`if-else`**: Return different responses based on whether a record exists.
* **`else if`**: Validate multiple business rules during form submission.
* **Nested `if`**: Check user authentication first, then verify authorization (roles or claims).
* **`switch`**: Process different order statuses, HTTP methods, or user roles.
* **Switch expressions**: Map status codes or enums to display text.
* **Ternary operator**: Display default values when optional data is missing.

Understanding these constructs is essential because they are used extensively in controllers, services, validation logic, authentication, authorization, and business rules in ASP.NET Core MVC and Web API applications.
