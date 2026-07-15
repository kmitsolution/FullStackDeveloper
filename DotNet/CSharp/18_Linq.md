# Chapter – LINQ in C#

# What is LINQ?

**LINQ (Language Integrated Query)** is a feature in C# that allows us to query data using a SQL-like syntax directly inside C#.

LINQ was introduced in **C# 3.0 (.NET Framework 3.5)**.

LINQ allows querying data from:

* Collections (`List`, `Array`)
* Databases (Entity Framework)
* XML
* JSON
* DataSets
* APIs

---

# Why Do We Need LINQ?

Before LINQ:

```csharp id="hn1"
List<int> numbers =
    new List<int>()
    {
        10,20,30,40,50
    };

List<int> result =
    new List<int>();

foreach(int number in numbers)
{
    if(number > 20)
    {
        result.Add(number);
    }
}
```

LINQ simplifies this:

```csharp id="hn2"
var result =
    numbers.Where(x => x > 20);
```

Much shorter and easier to read.

---

# Real World Analogy

Suppose you have an Employee table.

| Id | Name  | Salary |
| -- | ----- | ------ |
| 1  | Raman | 50000  |
| 2  | John  | 70000  |
| 3  | David | 60000  |

SQL Query:

```sql id="hn3"
SELECT *
FROM Employee
WHERE Salary > 55000
```

LINQ Equivalent:

```csharp id="hn4"
employees.Where(
    e => e.Salary > 55000);
```

---

# Advantages of LINQ

* Less code
* Strongly typed
* Compile-time checking
* IntelliSense support
* Readable
* Easy filtering and sorting
* Reduces loops

---

# LINQ Architecture

```text id="hn5"
Data Source

↓

LINQ Query

↓

Query Provider

↓

Result
```

---

# LINQ Query Execution Steps

```text id="hn6"
1. Data Source

2. Query Creation

3. Query Execution
```

---

# Example

```csharp id="hn7"
List<int> numbers =
    new List<int>()
    {
        10,20,30,40,50
    };

var result =
    numbers.Where(
        x => x > 20);

foreach(var item in result)
{
    Console.WriteLine(item);
}
```

Output

```text id="hn8"
30
40
50
```

---

# LINQ Query Syntax

LINQ supports two syntaxes.

## 1. Query Syntax

Similar to SQL.

## 2. Method Syntax

Uses extension methods.

---

# Query Syntax

```csharp id="hn9"
var result =
    from n in numbers
    where n > 20
    select n;
```

---

# Method Syntax

```csharp id="hn10"
var result =
    numbers.Where(
        x => x > 20);
```

Method syntax is used more frequently in modern applications.

---

# Data Source

LINQ requires a data source implementing:

```csharp id="hn11"
IEnumerable<T>
```

Examples:

```csharp id="hn12"
Array

List

Dictionary

HashSet
```

---

# Example

```csharp id="hn13"
int[] numbers =
{
    10,20,30,40
};
```

---

# Query Syntax Example

```csharp id="hn14"
int[] numbers =
{
    10,20,30,40,50
};

var result =
    from number in numbers
    where number > 20
    select number;

foreach(var item in result)
{
    Console.WriteLine(item);
}
```

Output

```text id="hn15"
30
40
50
```

---

# Method Syntax Example

```csharp id="hn16"
var result =
    numbers.Where(
        x => x > 20);
```

---

# Filtering Operators

---

# Where()

Filters records.

```csharp id="hn17"
var result =
    numbers.Where(
        x => x > 30);
```

Output

```text id="hn18"
40
50
```

---

# Select()

Projects data.

```csharp id="hn19"
var result =
    numbers.Select(
        x => x * 10);
```

Output

```text id="hn20"
100
200
300
400
500
```

---

# Select Example

```csharp id="hn21"
var names =
    employees.Select(
        e => e.Name);
```

---

# OrderBy()

Ascending order.

```csharp id="hn22"
var result =
    numbers.OrderBy(
        x => x);
```

---

# OrderByDescending()

```csharp id="hn23"
var result =
    numbers.OrderByDescending(
        x => x);
```

---

# ThenBy()

Used after OrderBy.

```csharp id="hn24"
employees
.OrderBy(e => e.Department)
.ThenBy(e => e.Name);
```

---

# First()

Returns first element.

```csharp id="hn25"
var value =
    numbers.First();
```

---

# FirstOrDefault()

```csharp id="hn26"
var value =
    numbers.FirstOrDefault();
```

Returns default if no record exists.

---

# Single()

Exactly one record must exist.

```csharp id="hn27"
var employee =
    employees.Single(
        x => x.Id == 1);
```

Throws exception if:

* No record found
* More than one record found

---

# SingleOrDefault()

Returns default if none exists.

---

# Last()

```csharp id="hn28"
numbers.Last();
```

---

# Any()

Checks whether records exist.

```csharp id="hn29"
bool result =
    numbers.Any(
        x => x > 50);
```

---

# All()

Checks whether all records satisfy condition.

```csharp id="hn30"
bool result =
    numbers.All(
        x => x > 5);
```

---

# Count()

```csharp id="hn31"
int total =
    numbers.Count();
```

---

# Sum()

```csharp id="hn32"
int total =
    numbers.Sum();
```

---

# Min()

```csharp id="hn33"
numbers.Min();
```

---

# Max()

```csharp id="hn34"
numbers.Max();
```

---

# Average()

```csharp id="hn35"
numbers.Average();
```

---

# Skip()

Skips records.

```csharp id="hn36"
numbers.Skip(2);
```

---

# Take()

Returns specified number.

```csharp id="hn37"
numbers.Take(3);
```

---

# Pagination Example

```csharp id="hn38"
employees
.Skip(20)
.Take(10);
```

This is heavily used in APIs.

---

# Distinct()

Removes duplicates.

```csharp id="hn39"
numbers.Distinct();
```

---

# Contains()

```csharp id="hn40"
numbers.Contains(20);
```

---

# Reverse()

```csharp id="hn41"
numbers.Reverse();
```

---

# GroupBy()

Very important operator.

```csharp id="hn42"
var result =
    employees.GroupBy(
        e => e.Department);
```

---

Example

```csharp id="hn43"
foreach(var group in result)
{
    Console.WriteLine(group.Key);

    foreach(var employee in group)
    {
        Console.WriteLine(employee.Name);
    }
}
```

---

# Join()

Similar to SQL Join.

```csharp id="hn44"
var result =
from e in employees
join d in departments
on e.DepartmentId equals d.Id
select new
{
    e.Name,
    d.DepartmentName
};
```

---

# Projection

Creating custom objects.

```csharp id="hn45"
var result =
employees.Select(
e => new
{
    e.Name,
    e.Salary
});
```

---

# Deferred Execution

LINQ queries execute only when needed.

```csharp id="hn46"
var result =
    numbers.Where(
        x => x > 20);
```

No execution yet.

Execution occurs here:

```csharp id="hn47"
foreach(var item in result)
{
}
```

---

# Immediate Execution

```csharp id="hn48"
var result =
    numbers
    .Where(x => x > 20)
    .ToList();
```

Query executes immediately.

---

# Deferred vs Immediate

| Deferred            | Immediate                  |
| ------------------- | -------------------------- |
| Executes later      | Executes immediately       |
| IEnumerable         | List, Array                |
| Better memory usage | Stores results immediately |

---

# IEnumerable vs IQueryable

This is an important interview topic.

---

# IEnumerable

* Executes in memory.
* Used with collections.

```csharp id="hn49"
IEnumerable<Employee>
```

---

# IQueryable

* Query executes on database.
* Entity Framework translates LINQ into SQL.

```csharp id="hn50"
IQueryable<Employee>
```

---

Example

```csharp id="hn51"
dbContext.Employees
.Where(e => e.Id > 10);
```

Entity Framework converts this into SQL.

---

# LINQ Execution Flow in EF Core

```text id="hn52"
LINQ Query

↓

Expression Tree

↓

Entity Framework

↓

SQL Query

↓

Database

↓

Objects
```

---

# LINQ in ASP.NET Core

LINQ is heavily used in:

* Entity Framework Core
* APIs
* Repository Pattern
* Services
* Filtering
* Pagination
* Searching

Example:

```csharp id="hn53"
var employees =
await _context.Employees
.Where(e => e.IsActive)
.OrderBy(e => e.Name)
.ToListAsync();
```

---

# Example – Search API

```csharp id="hn54"
var employees =
_context.Employees
.Where(e =>
    e.Name.Contains(searchText))
.ToList();
```

---

# Example – Pagination API

```csharp id="hn55"
var employees =
_context.Employees
.Skip((page-1)*pageSize)
.Take(pageSize)
.ToList();
```

---

# Example – Sorting API

```csharp id="hn56"
var employees =
_context.Employees
.OrderBy(e => e.Name)
.ToList();
```

---

# Important LINQ Operators

| Category      | Methods                       |
| ------------- | ----------------------------- |
| Filtering     | Where                         |
| Projection    | Select                        |
| Sorting       | OrderBy, ThenBy               |
| Grouping      | GroupBy                       |
| Joining       | Join                          |
| Aggregation   | Count, Sum, Min, Max, Average |
| Element       | First, Single, Last           |
| Quantifier    | Any, All                      |
| Paging        | Skip, Take                    |
| Conversion    | ToList, ToArray               |
| Set Operators | Distinct, Union, Intersect    |

---

# Best Practices

* Prefer Method Syntax in modern applications.
* Use `FirstOrDefault()` instead of `First()` if data may not exist.
* Avoid `ToList()` too early in Entity Framework.
* Use pagination (`Skip`, `Take`) in APIs.
* Prefer `Any()` instead of `Count() > 0`.
* Use projection (`Select`) to fetch only required columns.

---

# Summary

| Concept             | Description               |
| ------------------- | ------------------------- |
| LINQ                | Language Integrated Query |
| Query Syntax        | SQL-like syntax           |
| Method Syntax       | Extension method syntax   |
| Deferred Execution  | Executes when enumerated  |
| Immediate Execution | Executes immediately      |
| IEnumerable         | In-memory execution       |
| IQueryable          | Database execution        |

---

# Interview Questions

### 1. What is LINQ?

LINQ is a feature that provides query capabilities directly in C#.

---

### 2. Difference between Query Syntax and Method Syntax?

| Query Syntax      | Method Syntax          |
| ----------------- | ---------------------- |
| SQL-like          | Extension methods      |
| Limited operators | Supports all operators |
| Less used         | More commonly used     |

---

### 3. Difference between IEnumerable and IQueryable?

| IEnumerable            | IQueryable                |
| ---------------------- | ------------------------- |
| In-memory execution    | Database execution        |
| Faster for collections | Better for EF Core        |
| No SQL translation     | SQL translation supported |

---

### 4. Difference between First and Single?

| First                    | Single                           |
| ------------------------ | -------------------------------- |
| Returns first record     | Exactly one record must exist    |
| Multiple records allowed | Multiple records cause exception |

---

### 5. Difference between Deferred and Immediate Execution?

Deferred execution delays query execution until enumeration, while immediate execution executes the query instantly using methods like:

```csharp id="hn57"
ToList()

ToArray()

Count()

First()
```

---

### 6. Why is LINQ heavily used in ASP.NET Core?

Because Entity Framework Core uses LINQ to:

* Query databases
* Build APIs
* Implement searching
* Implement pagination
* Implement filtering
* Generate SQL automatically

LINQ is one of the most important concepts for ASP.NET Core and Entity Framework Core development.
