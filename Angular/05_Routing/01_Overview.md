# Lesson 17: Angular Routing

Now that our Angular + ASP.NET Core CRUD flow is working, let's learn **Angular Routing**.

Routing is important because a real Angular application has multiple pages/views:

```text
/                    → Home
/employees           → Employee List
/employees/add       → Add Employee
/about               → About
```

The important point is that Angular is a **Single Page Application (SPA)**. The browser doesn't need to reload the entire application every time you navigate.

---

# 1. What is Angular Routing?

Routing maps a URL to an Angular component.

For example:

```text
http://localhost:4200/employees
```

can display:

```text
EmployeeComponent
```

And:

```text
http://localhost:4200/about
```

can display:

```text
AboutComponent
```

The basic flow is:

```text
URL
 ↓
Angular Router
 ↓
Find matching route
 ↓
Create Component
 ↓
Display Component
```

---

# 2. Your `app.routes.ts`

You already have this file:

```text
src/app/app.routes.ts
```

Let's create our first route.

```typescript
import { Routes } from '@angular/router';

import { Employee } from './employee/employee';

export const routes: Routes = [

  {
    path: 'employees',
    component: Employee
  }

];
```

Now Angular understands:

```text
/employees
```

means:

```text
Employee component
```

---

# 3. What is `path`?

This:

```typescript
path: 'employees'
```

means the URL:

```text
http://localhost:4200/employees
```

Notice that we don't write:

```typescript
path: '/employees'
```

There is no `/` at the beginning.

---

# 4. What is `component`?

This:

```typescript
component: Employee
```

tells Angular:

> When the user visits `/employees`, display the Employee component.

---

# 5. Router Outlet

Now we need to tell Angular **where** the routed component should appear.

Open:

```text
src/app/app.html
```

Put:

```html
<h1>Employee Management System</h1>

<router-outlet></router-outlet>
```

`router-outlet` is essentially a placeholder.

Angular will put the routed component here.

Think of it like:

```text
AppComponent
│
├── Header
│
└── router-outlet
       │
       └── EmployeeComponent
```

When `/employees` is selected:

```text
router-outlet
      ↓
EmployeeComponent
```

---

# 6. Import RouterOutlet

Because you're using standalone Angular components, your root component needs `RouterOutlet`.

Open:

```text
src/app/app.ts
```

You might currently have something similar to:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  imports: [],
  templateUrl: './app.html',
  styleUrl: './app.css'
})
export class App {
}
```

Change it to:

```typescript
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet],
  templateUrl: './app.html',
  styleUrl: './app.css'
})
export class App {
}
```

Now Angular recognizes:

```html
<router-outlet></router-outlet>
```

---

# 7. Test It

Start Angular:

```bash
ng serve
```

Open:

```text
http://localhost:4200/employees
```

Your Employee component should appear.

For example:

```text
Employee Management System

Employee Form

Id
Name
Salary
Department

[Save]

Employee List

101 Raman 50000 IT
102 John 60000 HR
103 David 70000 Sales
```

---

# 8. What About `/`?

Currently we only have:

```typescript
{
  path: 'employees',
  component: Employee
}
```

If you visit:

```text
http://localhost:4200/
```

there is no matching route.

Let's create a Home component.

---

# 9. Create Home Component

Run:

```bash
ng generate component home
```

Depending on your Angular CLI naming configuration, you'll get something like:

```text
src/app/home/
    home.ts
    home.html
    home.css
    home.spec.ts
```

---

# 10. Home Component

`home.ts`

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-home',
  imports: [],
  templateUrl: './home.html',
  styleUrl: './home.css'
})
export class Home {

}
```

`home.html`

```html
<h2>Welcome to Employee Management System</h2>

<p>This is the Home page.</p>
```

---

# 11. Add Home Route

Update:

```text
app.routes.ts
```

```typescript
import { Routes } from '@angular/router';

import { Employee } from './employee/employee';
import { Home } from './home/home';

export const routes: Routes = [

  {
    path: '',
    component: Home
  },

  {
    path: 'employees',
    component: Employee
  }

];
```

Now:

```text
http://localhost:4200/
```

shows:

```text
Welcome to Employee Management System

This is the Home page.
```

And:

```text
http://localhost:4200/employees
```

shows the Employee component.

---

# 12. Navigation with routerLink

We don't want users to manually type URLs.

Let's create a menu.

Open:

```text
app.html
```

```html
<h1>Employee Management System</h1>

<nav>

  <a routerLink="/">Home</a>

  |

  <a routerLink="/employees">Employees</a>

</nav>

<hr>

<router-outlet></router-outlet>
```

---

# 13. Import RouterLink

Because `routerLink` is an Angular directive, import it.

`app.ts`:

```typescript
import { Component } from '@angular/core';
import {
  RouterLink,
  RouterOutlet
} from '@angular/router';

@Component({
  selector: 'app-root',
  imports: [
    RouterLink,
    RouterOutlet
  ],
  templateUrl: './app.html',
  styleUrl: './app.css'
})
export class App {

}
```

Now the menu works.

```text
Home | Employees
```

Click:

```text
Employees
```

Angular changes:

```text
/
```

to:

```text
/employees
```

and displays the Employee component.

---

# 14. Important Difference: `routerLink` vs `<a href>`

You might wonder why we don't simply use:

```html
<a href="/employees">Employees</a>
```

In Angular applications, we normally use:

```html
<a routerLink="/employees">Employees</a>
```

because Angular Router handles the navigation without performing a complete browser page reload.

### Traditional HTML

```text
Click link
 ↓
Browser reloads page
 ↓
Server request
 ↓
Page loaded
```

### Angular

```text
Click routerLink
 ↓
Angular Router
 ↓
Component changes
 ↓
No full page reload
```

That's one of the important characteristics of an SPA.

---

# 15. Default Route

We currently have:

```typescript
{
  path: '',
  component: Home
}
```

This means:

```text
http://localhost:4200/
```

loads Home.

---

# 16. Wildcard Route

What happens if someone enters:

```text
http://localhost:4200/abc
```

There is no route called `abc`.

We can create a wildcard route.

```typescript
{
  path: '**',
  component: Home
}
```

But there's an important rule:

> Put the wildcard route **last**.

For example:

```typescript
export const routes: Routes = [

  {
    path: '',
    component: Home
  },

  {
    path: 'employees',
    component: Employee
  },

  {
    path: '**',
    component: Home
  }

];
```

Angular checks the routes from top to bottom.

---

# 17. Route Parameters

This becomes very important for our Employee application.

Suppose we want:

```text
/employees/101
```

where `101` is the employee ID.

We can define:

```typescript
{
  path: 'employees/:id',
  component: Employee
}
```

Here:

```text
:id
```

is a route parameter.

For example:

```text
/employees/101
```

means:

```text
id = 101
```

And:

```text
/employees/205
```

means:

```text
id = 205
```

We'll use this in a future lesson when we build an Employee Details page.

---

# 18. Angular + ASP.NET Core Architecture

Your application now looks like:

```text
                    Browser
                       │
                       ▼
               Angular Application
                       │
                Angular Router
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼
        Home                    Employees
                                    │
                                    ▼
                              EmployeeService
                                    │
                                    ▼
                                HttpClient
                                    │
                                    ▼
                            ASP.NET Core API
```

Notice something important:

**Routing and API communication are separate concepts.**

Routing answers:

> Which Angular component should I display?

HttpClient answers:

> Which ASP.NET Core API should I call?

---

# 19. Current Application Structure

Your project is becoming:

```text
src/app
│
├── app.ts
├── app.html
├── app.css
├── app.config.ts
├── app.routes.ts
│
├── employee.ts              ← EmployeeService
│
├── models
│   └── employee.ts          ← Employee interface
│
├── employee
│   ├── employee.ts          ← Employee Component
│   ├── employee.html
│   ├── employee.css
│   └── employee.spec.ts
│
└── home
    ├── home.ts
    ├── home.html
    ├── home.css
    └── home.spec.ts
```

---

# 20. Important Concepts Learned

You now know:

### Route

```typescript
{
  path: 'employees',
  component: Employee
}
```

### Router Outlet

```html
<router-outlet></router-outlet>
```

### Navigation

```html
<a routerLink="/employees">Employees</a>
```

### Route Parameter

```typescript
path: 'employees/:id'
```

### Wildcard

```typescript
path: '**'
```

---

# Practice

Try these yourself.

### Exercise 1

Create an `About` component:

```bash
ng generate component about
```

Create the route:

```text
/about
```

and display:

```text
About Employee Management System
```

---

### Exercise 2

Add this menu:

```text
Home | Employees | About
```

---

### Exercise 3

Create these routes:

```text
/
/employees
/about
```

and verify that each displays the correct component.

---

### Exercise 4

Try:

```text
/employees/101
```

and create the route:

```typescript
{
  path: 'employees/:id',
  component: Employee
}
```

Don't worry about reading the `id` yet. We'll do that in the next routing lesson.

---

# One Important Point

You might notice that our Employee component currently does this:

```typescript
ngOnInit() {
    this.loadEmployees();
}
```

When Angular navigates to `/employees`, the component is created and `ngOnInit()` runs.

So our architecture is now:

```text
URL
 ↓
Router
 ↓
Employee Component
 ↓
ngOnInit()
 ↓
EmployeeService
 ↓
HttpClient
 ↓
ASP.NET Core API
```

That's a very common pattern in real Angular applications.

---

## Next Lesson — Lesson 18

We'll make the routing more useful by creating an **Employee Details page**:

```text
/employees
      ↓
Click employee
      ↓
/employees/101
      ↓
Employee Details
```

You'll learn **route parameters** and how Angular gets the `id` from the URL and uses it to call:

```http
GET /employees/101
```

This will connect **Angular Routing + Angular Service + ASP.NET Core API** into one complete flow.
