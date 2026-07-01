# Loops in C#

## What is a Loop?

A **loop** is a control statement that allows you to execute a block of code **repeatedly** until a specified condition becomes false.

Instead of writing the same code multiple times, loops help automate repetitive tasks.

For example, if you want to print numbers from **1 to 10**, you could write:

```csharp
Console.WriteLine(1);
Console.WriteLine(2);
Console.WriteLine(3);
...
Console.WriteLine(10);
```

This is inefficient.

Using a loop:

```csharp
for (int i = 1; i <= 10; i++)
{
    Console.WriteLine(i);
}
```

The output is the same, but the code is much shorter and easier to maintain.

---

# Why Do We Need Loops?

Loops are commonly used to:

* Process arrays and collections
* Read data from files
* Display reports
* Perform repeated calculations
* Process database records
* Execute a task multiple times

---

# Types of Loops in C#

C# provides four looping statements:

1. `for`
2. `while`
3. `do-while`
4. `foreach`

Additionally, loop control statements are:

* `break`
* `continue`

---

# Loop Components

Every loop has three important parts:

```text
Initialization

↓

Condition

↓

Execute Statements

↓

Increment / Decrement

↓

Repeat
```

---

# 1. for Loop

The `for` loop is used when you **know in advance how many times** the loop should execute.

## Syntax

```csharp
for(initialization; condition; increment/decrement)
{
    // Statements
}
```

---

## Flow Diagram

```text
Initialization
      │
      ▼
 Condition
      │
 ┌────┴────┐
 │         │
True     False
 │         │
 ▼         ▼
Execute   Exit
 │
 ▼
Increment
 │
 └──────────► Condition
```

---

## Example 1: Print Numbers

```csharp
for (int i = 1; i <= 5; i++)
{
    Console.WriteLine(i);
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

## Example 2: Print Even Numbers

```csharp
for (int i = 2; i <= 10; i += 2)
{
    Console.WriteLine(i);
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

## Example 3: Countdown

```csharp
for (int i = 5; i >= 1; i--)
{
    Console.WriteLine(i);
}
```

Output

```text
5
4
3
2
1
```

---

## Example 4: Sum of Numbers

```csharp
int sum = 0;

for (int i = 1; i <= 5; i++)
{
    sum += i;
}

Console.WriteLine(sum);
```

Output

```text
15
```

---

# 2. while Loop

The `while` loop executes **as long as the condition is true**.

It is generally used when **the number of iterations is not known beforehand**.

## Syntax

```csharp
while(condition)
{
    // Statements
}
```

---

## Flow Diagram

```text
Condition
   │
┌──┴──┐
│     │
True False
│     │
▼     ▼
Execute Exit
│
└────────► Condition
```

---

## Example 1

```csharp
int i = 1;

while (i <= 5)
{
    Console.WriteLine(i);
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

## Example 2: Password Attempts

```csharp
int attempts = 1;

while (attempts <= 3)
{
    Console.WriteLine($"Attempt {attempts}");
    attempts++;
}
```

Output

```text
Attempt 1
Attempt 2
Attempt 3
```

---

# 3. do-while Loop

The `do-while` loop is similar to the `while` loop, but the **loop body executes at least once**, even if the condition is false.

## Syntax

```csharp
do
{
    // Statements
}
while(condition);
```

---

## Flow Diagram

```text
Execute
   │
   ▼
Condition
   │
┌──┴──┐
│     │
True False
│     │
└──►Repeat
      Exit
```

---

## Example 1

```csharp
int i = 1;

do
{
    Console.WriteLine(i);
    i++;
}
while (i <= 5);
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

## Example 2: Condition is Initially False

```csharp
int i = 10;

do
{
    Console.WriteLine(i);
}
while (i < 5);
```

Output

```text
10
```

Notice that the loop executes once before checking the condition.

---

# while vs do-while

| while                  | do-while                     |
| ---------------------- | ---------------------------- |
| Checks condition first | Executes first, checks later |
| May execute zero times | Executes at least once       |

---

# 4. foreach Loop

The `foreach` loop is used to iterate through **arrays and collections**.

You do not need to manage an index manually.

## Syntax

```csharp
foreach(datatype variable in collection)
{
    // Statements
}
```

---

## Example 1: Array

```csharp
int[] numbers = { 10, 20, 30, 40 };

foreach (int number in numbers)
{
    Console.WriteLine(number);
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

## Example 2: String Array

```csharp
string[] cities =
{
    "Bangalore",
    "Delhi",
    "Mumbai"
};

foreach (string city in cities)
{
    Console.WriteLine(city);
}
```

Output

```text
Bangalore
Delhi
Mumbai
```

---

## Example 3: List

```csharp
List<string> students = new List<string>
{
    "Raman",
    "Amit",
    "Neha"
};

foreach (string student in students)
{
    Console.WriteLine(student);
}
```

Output

```text
Raman
Amit
Neha
```

---

# Nested Loops

A loop inside another loop is called a **nested loop**.

## Example: Multiplication Table

```csharp
for (int i = 1; i <= 3; i++)
{
    for (int j = 1; j <= 3; j++)
    {
        Console.Write($"{i * j}\t");
    }

    Console.WriteLine();
}
```

Output

```text
1   2   3
2   4   6
3   6   9
```

---

# Infinite Loop

A loop that never ends is called an **infinite loop**.

Example

```csharp
while (true)
{
    Console.WriteLine("Running...");
}
```

This loop continues until the application is terminated or a `break` statement is encountered.

---

# break Statement

The `break` statement immediately terminates the loop.

## Example

```csharp
for (int i = 1; i <= 10; i++)
{
    if (i == 6)
    {
        break;
    }

    Console.WriteLine(i);
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

The loop stops when `i` becomes `6`.

---

# continue Statement

The `continue` statement skips the current iteration and proceeds with the next one.

## Example

```csharp
for (int i = 1; i <= 5; i++)
{
    if (i == 3)
    {
        continue;
    }

    Console.WriteLine(i);
}
```

Output

```text
1
2
4
5
```

The value `3` is skipped.

---

# break vs continue

| break                                              | continue                                           |
| -------------------------------------------------- | -------------------------------------------------- |
| Exits the loop completely                          | Skips only the current iteration                   |
| Control moves to the next statement after the loop | Control returns to the loop for the next iteration |

---

# Choosing the Right Loop

| Loop       | When to Use                             |
| ---------- | --------------------------------------- |
| `for`      | Number of iterations is known           |
| `while`    | Number of iterations is unknown         |
| `do-while` | Loop must execute at least once         |
| `foreach`  | Iterating through arrays or collections |

---

# Real-World ASP.NET Core Examples

### `for` Loop

* Generating pagination links.
* Processing a fixed number of records.

### `while` Loop

* Reading data from a stream until the end.
* Processing messages from a queue.

### `do-while` Loop

* Displaying a menu in a console application until the user chooses to exit.
* Prompting for user input until valid data is entered.

### `foreach` Loop

* Displaying a list of products from a database.
* Iterating through HTTP request headers.
* Processing collections returned by Entity Framework Core.

Example:

```csharp
foreach (Product product in products)
{
    Console.WriteLine(product.Name);
}
```

---

# Best Practices

* Use **`for`** when the number of iterations is known.
* Use **`while`** when the termination condition depends on external input or runtime state.
* Use **`do-while`** when the loop body must execute at least once.
* Use **`foreach`** for arrays and collections because it is simpler and less error-prone.
* Avoid modifying a collection while iterating over it with `foreach`, as it can cause exceptions.
* Ensure loop conditions eventually become false to prevent infinite loops.
* Use `break` only when you need to exit a loop early, and `continue` when you want to skip a particular iteration.

---

# Summary

| Loop       | Condition Checked     | Executes at Least Once | Typical Use                            |
| ---------- | --------------------- | ---------------------- | -------------------------------------- |
| `for`      | Before each iteration | No                     | Fixed number of iterations             |
| `while`    | Before each iteration | No                     | Unknown number of iterations           |
| `do-while` | After each iteration  | Yes                    | Menu-driven programs, input validation |
| `foreach`  | Automatically handled | Depends on collection  | Iterating arrays and collections       |

**Interview Question:** *Which loop is most commonly used in ASP.NET Core applications?*

**Answer:** While all loops have their place, `foreach` is used most frequently because ASP.NET Core applications often work with collections such as database records, API responses, request headers, and model lists. `for` is commonly used when an index is required, while `while` and `do-while` are less common in typical web application code.
