# Chapter – Events in .NET

## What is an Event?

An **Event** is a mechanism through which one object can **notify other objects when something happens**.

Events are built on top of **Delegates**.

Think of an event as a **publisher-subscriber model**.

```text
Publisher raises event

↓

Subscribers receive notification

↓

Subscribers execute their methods
```

---

# Real Life Example

### YouTube Notification

```text
YouTube Channel uploads video

↓

Subscribers get notification
```

Here:

* YouTube Channel → Publisher
* Subscribers → Event Subscribers
* Notification → Event

---

# Why Do We Need Events?

Suppose we have a Button class.

When the button is clicked, multiple things may happen:

* Display message
* Save data
* Send email
* Log information

The Button should not know who needs the notification.

Events provide **loose coupling**.

---

# Publisher and Subscriber

Events involve two components:

## Publisher

Raises the event.

Example:

```text
Button Clicked
Order Placed
File Download Completed
User Registered
```

---

## Subscriber

Responds to the event.

Example:

```text
Send Email
Write Log
Update Database
Display Message
```

---

# Events are Based on Delegates

Before understanding events, remember:

```text
Delegate

↓

Stores Method References

↓

Event

↓

Controls Access to Delegate
```

An event internally uses a delegate.

---

# Event Declaration Syntax

### Step 1 – Create Delegate

```csharp id="pt7e2o"
public delegate void Notify();
```

### Step 2 – Declare Event

```csharp id="6qdbvw"
public event Notify Notification;
```

Syntax

```csharp id="ghn32t"
access_modifier event DelegateName EventName;
```

---

# Simple Event Example

## Publisher

```csharp id="5ttm8u"
class Process
{
    public delegate void ProcessCompleted();

    public event ProcessCompleted Completed;

    public void Start()
    {
        Console.WriteLine("Processing...");

        Completed?.Invoke();
    }
}
```

---

## Subscriber

```csharp id="ydlq3d"
class Program
{
    static void Notification()
    {
        Console.WriteLine("Process Completed");
    }

    static void Main()
    {
        Process process = new Process();

        process.Completed += Notification;

        process.Start();
    }
}
```

Output

```text
Processing...

Process Completed
```

---

# Understanding `+=`

```csharp id="30z6wd"
process.Completed += Notification;
```

Means:

```text
Subscribe Notification Method

↓

to Completed Event
```

---

# Understanding `-=`

To unsubscribe:

```csharp id="u1q48g"
process.Completed -= Notification;
```

---

# Event Flow

```text
Subscriber

↓

Subscribe

↓

Publisher

↓

Event Raised

↓

Subscriber Method Executes
```

---

# Null Conditional Operator

Usually events are invoked using:

```csharp id="t92drh"
Completed?.Invoke();
```

Equivalent to:

```csharp id="tjlwmv"
if (Completed != null)
{
    Completed();
}
```

This prevents a `NullReferenceException` if no subscriber exists.

---

# Multiple Subscribers

Events support multiple subscribers.

Example

```csharp id="9lycxf"
static void SendEmail()
{
    Console.WriteLine("Email Sent");
}

static void WriteLog()
{
    Console.WriteLine("Log Written");
}
```

Subscribe

```csharp id="cctg5t"
process.Completed += SendEmail;

process.Completed += WriteLog;
```

Raise Event

```csharp id="vjlwmx"
process.Start();
```

Output

```text
Processing...

Email Sent

Log Written
```

---

# Why Use `event` Instead of Delegate?

Suppose we expose delegate directly.

```csharp id="1p0t9u"
public Notify Notification;
```

Outside code can do:

```csharp id="yxhj9z"
process.Notification = null;
```

This removes all subscribers.

Events prevent this.

Outside classes can only:

✔ Subscribe (`+=`)

✔ Unsubscribe (`-=`)

✘ Invoke Event

✘ Assign Event

---

# Example

```csharp id="4lymlz"
process.Completed();
```

Compiler Error.

Only the publisher can raise the event.

This is one major advantage of events.

---

# Standard Event Pattern

.NET follows a standard event pattern.

Delegate:

```csharp id="ckgtg1"
public delegate void EventHandler(
    object sender,
    EventArgs e);
```

Event:

```csharp id="1q2nix"
public event EventHandler Completed;
```

Raise:

```csharp id="3cfw7q"
Completed?.Invoke(this, EventArgs.Empty);
```

---

# Understanding Parameters

### sender

Represents the object that raised the event.

### EventArgs

Contains additional information.

---

# Example

```csharp id="zq4p7h"
class Process
{
    public event EventHandler Completed;

    public void Start()
    {
        Completed?.Invoke(
            this,
            EventArgs.Empty);
    }
}
```

Subscriber

```csharp id="xjlwm1"
static void OnCompleted(
    object sender,
    EventArgs e)
{
    Console.WriteLine("Completed");
}
```

---

# Custom Event Arguments

Sometimes additional data is required.

Create custom EventArgs.

```csharp id="hjlwm0"
class OrderEventArgs : EventArgs
{
    public int OrderId { get; set; }

    public decimal Amount { get; set; }
}
```

Publisher

```csharp id="jjw6yl"
public event EventHandler<OrderEventArgs>
    OrderPlaced;
```

Raise

```csharp id="5x5d6t"
OrderPlaced?.Invoke(
    this,
    new OrderEventArgs
    {
        OrderId = 1001,
        Amount = 5000
    });
```

Subscriber

```csharp id="mjlwmv"
static void OnOrderPlaced(
    object sender,
    OrderEventArgs e)
{
    Console.WriteLine(e.OrderId);

    Console.WriteLine(e.Amount);
}
```

Output

```text
1001

5000
```

---

# EventHandler Delegate

.NET provides built-in delegates.

Instead of:

```csharp id="4cjlwm"
delegate void Notify();
```

Prefer:

```csharp id="ejlwm0"
EventHandler
```

or

```csharp id="jlwm11"
EventHandler<T>
```

---

# Event Example Using EventHandler

```csharp id="cjlwm2"
class Button
{
    public event EventHandler Click;

    public void RaiseClick()
    {
        Click?.Invoke(
            this,
            EventArgs.Empty);
    }
}
```

Subscriber

```csharp id="hjlwm3"
button.Click +=
    (sender,e) =>
{
    Console.WriteLine("Button Clicked");
};
```

---

# Anonymous Methods

```csharp id="zjlwm4"
process.Completed += delegate
{
    Console.WriteLine("Completed");
};
```

---

# Lambda Expressions

Most common approach.

```csharp id="jjlwm5"
process.Completed +=
    (sender,e) =>
{
    Console.WriteLine("Completed");
};
```

---

# Events in ASP.NET Core

Events are not as heavily used as in WinForms/WPF, but they still appear in:

### Background Services

```text
Job Completed

↓

Raise Event
```

### Domain Events

```text
Order Placed

↓

Send Email

↓

Update Inventory

↓

Create Invoice
```

### SignalR Notifications

```text
User Connected

↓

Raise Event
```

### Logging Systems

```text
Application Error

↓

Raise Event

↓

Log Entry
```

---

# Real Example – Order System

Publisher

```csharp id="kjlwm6"
class OrderService
{
    public event EventHandler OrderPlaced;

    public void PlaceOrder()
    {
        Console.WriteLine("Order Created");

        OrderPlaced?.Invoke(
            this,
            EventArgs.Empty);
    }
}
```

Subscriber

```csharp id="0jlwm7"
service.OrderPlaced +=
(sender,e)=>
{
    Console.WriteLine(
        "Email Sent");
};
```

Output

```text
Order Created

Email Sent
```

---

# Delegates vs Events

| Delegate                 | Event                         |
| ------------------------ | ----------------------------- |
| Function pointer         | Notification mechanism        |
| Can be invoked anywhere  | Only publisher can invoke     |
| Can be assigned directly | Only += and -= allowed        |
| Less secure              | More secure                   |
| Used for callbacks       | Used for Publisher-Subscriber |

---

# Events vs Delegates

```text
Delegate

↓

Method Reference

↓

Event

↓

Restricted Delegate
```

An event is essentially a delegate with controlled access.

---

# Best Practices

* Prefer `EventHandler` and `EventHandler<T>`.
* Use `?.Invoke()` when raising events.
* Unsubscribe events when objects are no longer needed.
* Avoid memory leaks caused by long-lived publishers holding references to subscribers.
* Use events for notifications, not for executing business logic.

---

# Summary

| Concept      | Description                  |
| ------------ | ---------------------------- |
| Event        | Notification mechanism       |
| Publisher    | Raises event                 |
| Subscriber   | Receives notification        |
| EventHandler | Built-in delegate for events |
| EventArgs    | Carries event data           |
| `+=`         | Subscribe                    |
| `-=`         | Unsubscribe                  |
| `Invoke()`   | Raise event                  |

---

# Interview Questions

### 1. What is an Event?

An event is a **publisher-subscriber mechanism** built on delegates used for notifications.

---

### 2. Are events based on delegates?

Yes.

Events internally use delegates.

---

### 3. Difference between Delegate and Event?

| Delegate                  | Event                      |
| ------------------------- | -------------------------- |
| Function pointer          | Notification mechanism     |
| Anyone can invoke         | Only publisher can invoke  |
| Direct assignment allowed | Only subscribe/unsubscribe |

---

### 4. Why do we use `EventHandler`?

It follows the .NET standard event pattern and avoids creating custom delegates.

---

### 5. What is `sender`?

The object that raised the event.

---

### 6. What is `EventArgs`?

A class used to pass additional information to event subscribers.

---

### 7. What is the most common event declaration in .NET?

```csharp id="mjlwm8"
public event EventHandler MyEvent;
```

or

```csharp id="5jlwm9"
public event EventHandler<MyEventArgs>
    MyEvent;
```

These are the standard patterns used throughout the .NET ecosystem, including libraries like Microsoft frameworks such as WinForms, WPF, and many server-side components.
