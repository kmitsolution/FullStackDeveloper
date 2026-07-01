# Chapter – Object Life Cycle in C#

## What is an Object Life Cycle?

The **Object Life Cycle** describes the complete journey of an object from the moment it is declared until its memory is reclaimed by the **Garbage Collector (GC)**.

Understanding the object life cycle helps developers understand:

* How objects are created
* Where objects are stored
* When constructors execute
* When objects are destroyed
* How Garbage Collection works

---

# Stages of an Object Life Cycle

An object goes through the following stages:

```text
Class Definition

        │

        ▼

Variable Declaration

        │

        ▼

Object Creation (new)

        │

        ▼

Memory Allocation (Heap)

        │

        ▼

Default Field Initialization

        │

        ▼

Constructor Execution

        │

        ▼

Object Ready for Use

        │

        ▼

Object Usage

        │

        ▼

Object Becomes Unreachable

        │

        ▼

Garbage Collection

        │

        ▼

Finalization (If Available)

        │

        ▼

Memory Reclaimed
```

---

# Stage 1 – Class Definition

Before creating an object, a class must exist.

Example

```csharp
class Employee
{
    public int Id;

    public string Name;

    public Employee()
    {
        Console.WriteLine("Constructor Executed");
    }
}
```

At this stage:

* No object exists.
* No heap memory is allocated.
* Only the blueprint (class definition) exists.

---

# Stage 2 – Variable Declaration

Next, declare a reference variable.

```csharp
Employee emp;
```

Memory

```text
STACK

emp

↓

null
```

Notice:

* No object exists.
* The variable can only store a reference.
* It currently points to `null`.

---

# Stage 3 – Object Creation (`new`)

The object is created using the `new` keyword.

```csharp
Employee emp = new Employee();
```

The `new` keyword tells the CLR to create a new object.

---

# What Does `new` Do?

Internally, the CLR performs several operations.

```text
new Employee()

        │

        ▼

Allocate Heap Memory

        ▼

Initialize Fields

        ▼

Call Constructor

        ▼

Return Reference
```

---

# Stage 4 – Memory Allocation

The CLR allocates memory on the **Managed Heap**.

Memory

```text
STACK                     HEAP

emp --------------->   Employee Object

                        Id = 0

                        Name = null
```

The reference variable is stored on the **Stack**.

The actual object is stored on the **Heap**.

---

# Stage 5 – Default Field Initialization

Before the constructor executes, the CLR initializes all fields with default values.

Example

```csharp
class Employee
{
    public int Id;

    public string Name;

    public bool Active;
}
```

After allocation

```text
Id = 0

Name = null

Active = false
```

---

# Stage 6 – Constructor Execution

Once memory is allocated and fields are initialized, the constructor executes.

```csharp
class Employee
{
    public int Id;

    public Employee()
    {
        Id = 100;
    }
}
```

Memory

Before Constructor

```text
Id = 0
```

After Constructor

```text
Id = 100
```

Output

```text
Constructor Executed
```

---

# Stage 7 – Object Ready for Use

Now the object is completely initialized.

```csharp
Employee emp = new Employee();

emp.Name = "Raman";

Console.WriteLine(emp.Name);
```

Output

```text
Raman
```

The object is now ready for use.

---

# Stage 8 – Object Usage

The object performs its intended work.

Example

```csharp
Employee emp = new Employee();

emp.Id = 1001;

emp.Name = "Raman";
```

Memory

```text
STACK

emp

 │

 ▼

HEAP

Employee

Id =1001

Name = Raman
```

The object remains alive because the reference variable (`emp`) still points to it.

---

# Stage 9 – Object Becomes Unreachable

An object becomes **unreachable** when no active references point to it.

Example

```csharp
Employee emp = new Employee();

emp = null;
```

Memory

Before

```text
STACK

emp

 │

 ▼

HEAP

Employee Object
```

After

```text
STACK

emp

↓

null


HEAP

Employee Object

(No References)
```

The object still exists in memory, but it is no longer reachable.

---

Another Example

```csharp
Employee emp1 = new Employee();

Employee emp2 = emp1;

emp1 = null;

emp2 = null;
```

Now

```text
Employee Object

↓

No References
```

The object becomes eligible for Garbage Collection.

---

# Stage 10 – Garbage Collection

The **Garbage Collector (GC)** automatically identifies unreachable objects and removes them from memory.

You never explicitly destroy an object in C#.

Instead,

```text
Object Unreachable

↓

GC Detects

↓

Memory Released
```

Example

```csharp
Employee emp = new Employee();

emp = null;
```

Later,

```text
Garbage Collector

↓

Deletes Object
```

---

# Stage 11 – Finalization (If Applicable)

If a class contains a **Finalizer (Destructor)**,

```csharp
class Employee
{
    ~Employee()
    {
        Console.WriteLine("Finalizer Executed");
    }
}
```

Before the object is removed,

the CLR calls

```text
Finalize()
```

After finalization,

memory becomes eligible for reclamation.

**Note:** The exact time when a finalizer executes is **not deterministic**. It depends on when the Garbage Collector runs.

---

# Stage 12 – Memory Reclaimed

Finally,

the Garbage Collector reclaims the heap memory.

Memory

Before GC

```text
HEAP

Employee Object
```

After GC

```text
HEAP

Memory Available
```

The memory can now be reused for future objects.

---

# Complete Life Cycle Diagram

```text
              Class Definition

                     │

                     ▼

            Reference Declaration

                     │

                     ▼

              new Employee()

                     │

                     ▼

          Heap Memory Allocated

                     │

                     ▼

      Default Values Assigned

                     │

                     ▼

        Constructor Executes

                     │

                     ▼

           Object Ready

                     │

                     ▼

            Object Used

                     │

                     ▼

      Object Becomes Unreachable

                     │

                     ▼

        Garbage Collector Runs

                     │

                     ▼

       Finalizer Executes (if any)

                     │

                     ▼

          Heap Memory Released
```

---

# Real-World Example

```csharp
class Employee
{
    public string Name;

    public Employee()
    {
        Console.WriteLine("Employee Created");
    }

    ~Employee()
    {
        Console.WriteLine("Employee Destroyed");
    }
}
```

Usage

```csharp
Employee emp = new Employee();

emp.Name = "Raman";

emp = null;
```

Execution

```text
Employee Created

↓

Object Used

↓

Reference Removed

↓

GC Runs

↓

Employee Destroyed

↓

Memory Released
```

Remember, the "Employee Destroyed" message appears **only when the finalizer is executed**, which may happen much later than `emp = null`.

---

# Object Life Cycle in ASP.NET Core

Objects are created continuously in ASP.NET Core.

Examples

```csharp
EmployeeController controller;
```

Request arrives

↓

DI Container

↓

Creates Controller Object

↓

Constructor Executes

↓

Action Executes

↓

Request Completes

↓

Object Becomes Unreachable

↓

Garbage Collector Removes It

```

The same process applies to:

- Controllers
- Services
- Repositories
- DbContext
- Middleware (depending on lifetime)
- Models

The **Dependency Injection (DI)** container manages the creation and lifetime of many of these objects.

---

# Important Points

- Objects are stored on the **Managed Heap**.
- Reference variables are stored on the **Stack** (when they are local variables).
- Constructors execute immediately after memory allocation and field initialization.
- Objects remain alive as long as they are reachable.
- Setting a reference to `null` does **not** immediately destroy the object.
- The Garbage Collector automatically removes unreachable objects.
- Finalizers execute only for classes that define one, and their execution time is not guaranteed.

---

# Best Practices

- Do not manually force object destruction; let the Garbage Collector manage memory.
- Release unmanaged resources by implementing `IDisposable` and using `Dispose()` rather than relying on finalizers.
- Keep object lifetimes as short as practical.
- Avoid keeping unnecessary references to objects, as this prevents them from being collected.
- Avoid calling `GC.Collect()` unless you have a very specific and well-understood reason.

---

# Summary

| Stage | Description |
|--------|-------------|
| Declaration | A reference variable is declared. |
| Object Creation | The `new` keyword creates the object. |
| Memory Allocation | Memory is allocated on the managed heap. |
| Default Initialization | Fields receive their default values. |
| Constructor Execution | The constructor initializes the object. |
| Object Usage | The object performs its intended tasks. |
| Unreachable | No references point to the object. |
| Garbage Collection | The GC identifies unreachable objects. |
| Finalization | The finalizer executes (if defined). |
| Memory Reclaimed | Heap memory is released and becomes available for reuse. |

---

# Interview Questions

### 1. What is the life cycle of an object in C#?

**Answer:**

1. Class definition
2. Reference declaration
3. Object creation (`new`)
4. Memory allocation
5. Default field initialization
6. Constructor execution
7. Object usage
8. Object becomes unreachable
9. Garbage Collection
10. Finalization (if applicable)
11. Memory reclaimed

---

### 2. Does `emp = null` destroy the object?

**No.** It only removes one reference to the object. The object becomes eligible for Garbage Collection only when **no reachable references** remain.

---

### 3. When is an object eligible for Garbage Collection?

An object is eligible when it becomes **unreachable**, meaning no active references can access it.

---

### 4. Is the finalizer guaranteed to execute immediately after an object becomes unreachable?

**No.** Finalizer execution depends on when the Garbage Collector runs and is **non-deterministic**.

---

### 5. Who manages the life cycle of objects in ASP.NET Core?

The **Dependency Injection (DI) container** manages the creation and lifetime of many application objects (such as controllers, services, and repositories), while the **CLR Garbage Collector** manages memory reclamation when those objects become unreachable.
```
