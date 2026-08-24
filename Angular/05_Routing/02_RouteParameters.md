# Lesson 18: Route Parameters + Employee Details

In the previous lesson, we created:

```text
/employees
```

Now we'll create:

```text
/employees/101
```

where `101` represents the employee ID.

The goal is:

```text
Employee List
     ↓
Click "Details"
     ↓
/employees/101
     ↓
Angular reads 101
     ↓
GET /employees/101
     ↓
ASP.NET Core
     ↓
Employee 101
     ↓
Display details
```

---

# 1. ASP.NET Core — Get Employee by ID

We already have:

```csharp
app.MapGet("/employees", () =>
{
    return employees;
});
```

Now add:

```csharp
app.MapGet("/employees/{id:int}", (int id) =>
{
    var employee = employees.FirstOrDefault(e => e.Id == id);

    if (employee == null)
    {
        return Results.NotFound();
    }

    return Results.Ok(employee);
});
```

Now you have two GET endpoints:

```text
GET /employees
```

returns all employees.

```text
GET /employees/101
```

returns one employee.

For example:

```json
{
    "id": 101,
    "name": "Raman",
    "salary": 50000,
    "department": "IT"
}
```

---

# 2. Add the Details Route

Open:

```text
src/app/app.routes.ts
```

Add:

```typescript
import { Routes } from '@angular/router';

import { Home } from './home/home';
import { Employee } from './employee/employee';

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
    path: 'employees/:id',
    component: Employee
  }

];
```

Now Angular understands:

```text
/employees
```

and:

```text
/employees/101
```

---

# 3. What Does `:id` Mean?

This:

```typescript
path: 'employees/:id'
```

means `id` is a **route parameter**.

For example:

```text
/employees/101
```

Angular interprets as:

```text
id = 101
```

Another URL:

```text
/employees/200
```

means:

```text
id = 200
```

The route itself doesn't contain a fixed ID.

---

# 4. Create Employee Details Component

Let's create a separate component.

Run:

```bash
ng generate component employee-details
```

You'll get something like:

```text
src/app/employee-details/
    employee-details.ts
    employee-details.html
    employee-details.css
    employee-details.spec.ts
```

---

# 5. Change the Route

Now import the new component:

```typescript
import { Routes } from '@angular/router';

import { Home } from './home/home';
import { Employee } from './employee/employee';
import { EmployeeDetails } from './employee-details/employee-details';

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
    path: 'employees/:id',
    component: EmployeeDetails
  }

];
```

Now:

```text
/employees
```

→ Employee component

and:

```text
/employees/101
```

→ EmployeeDetails component

---

# 6. Add `getEmployeeById()` to Service

Open:

```text
src/app/employee.ts
```

Add:

```typescript
getEmployeeById(id: number) {

    return this.http.get<Employee>(
        `${this.apiUrl}/${id}`
    );

}
```

Your service now has:

```typescript
getEmployees() {
    return this.http.get<Employee[]>(this.apiUrl);
}

getEmployeeById(id: number) {
    return this.http.get<Employee>(
        `${this.apiUrl}/${id}`
    );
}
```

---

# 7. Read the Route Parameter

This is the important part.

Angular provides `ActivatedRoute` to read parameters from the URL.

Open:

```text
employee-details.ts
```

Use:

```typescript
import { Component, inject } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

import { EmployeeService } from '../employee';
import { Employee as EmployeeModel } from '../models/employee';

@Component({
    selector: 'app-employee-details',
    imports: [],
    templateUrl: './employee-details.html',
    styleUrl: './employee-details.css'
})
export class EmployeeDetails {

    private route = inject(ActivatedRoute);

    private employeeService = inject(EmployeeService);

    employee!: EmployeeModel;

    ngOnInit() {

        const id = Number(
            this.route.snapshot.paramMap.get('id')
        );

        console.log("Employee ID:", id);

        this.employeeService
            .getEmployeeById(id)
            .subscribe({

                next: (data) => {

                    console.log("Employee:", data);

                    this.employee = data;

                },

                error: (error) => {

                    console.error(error);

                }

            });

    }

}
```

---

# 8. Understand This Code

The URL is:

```text
/employees/101
```

This:

```typescript
this.route.snapshot.paramMap.get('id')
```

gets:

```text
"101"
```

Notice that it is a **string**.

That's why we use:

```typescript
Number(...)
```

So:

```typescript
const id = Number(
    this.route.snapshot.paramMap.get('id')
);
```

becomes:

```text
101
```

which is a number.

---

# 9. Employee Details HTML

Open:

```text
employee-details.html
```

Use:

```html
<h2>Employee Details</h2>

@if (employee) {

    <p>
        <strong>Id:</strong>
        {{ employee.id }}
    </p>

    <p>
        <strong>Name:</strong>
        {{ employee.name }}
    </p>

    <p>
        <strong>Salary:</strong>
        {{ employee.salary }}
    </p>

    <p>
        <strong>Department:</strong>
        {{ employee.department }}
    </p>

}
```

---

# 10. Add Details Link to Employee List

Open:

```text
employee.html
```

Inside your employee table, add:

```html
<td>

    <a
        [routerLink]="['/employees', employee.id]">

        Details

    </a>

</td>
```

For example:

```html
@for(employee of employees; track employee.id) {

    <tr>

        <td>{{ employee.id }}</td>

        <td>{{ employee.name }}</td>

        <td>{{ employee.salary }}</td>

        <td>{{ employee.department }}</td>

        <td>

            <button
                (click)="editEmployee(employee)">

                Edit

            </button>

            <button
                (click)="deleteEmployee(employee.id)">

                Delete

            </button>

            <a
                [routerLink]="['/employees', employee.id]">

                Details

            </a>

        </td>

    </tr>

}
```

---

# 11. Import RouterLink

Because `Employee` is a standalone component, add `RouterLink`.

```typescript
import { RouterLink } from '@angular/router';
```

Then:

```typescript
@Component({
    selector: 'app-employee',

    imports: [
        FormsModule,
        RouterLink
    ],

    templateUrl: './employee.html',
    styleUrl: './employee.css'
})
```

---

# 12. Test It

Start both applications.

### ASP.NET Core

```text
http://localhost:5010
```

### Angular

```text
http://localhost:4200
```

Go to:

```text
http://localhost:4200/employees
```

You should see:

```text
Employee List

101 | Raman | 50000 | IT | Edit | Delete | Details
102 | John  | 60000 | HR | Edit | Delete | Details
103 | David | 70000 | Sales | Edit | Delete | Details
```

Click:

```text
Details
```

for Raman.

The URL becomes:

```text
http://localhost:4200/employees/101
```

Angular reads:

```text
101
```

and calls:

```http
GET http://localhost:5010/employees/101
```

ASP.NET Core returns:

```json
{
    "id": 101,
    "name": "Raman",
    "salary": 50000,
    "department": "IT"
}
```

Angular displays:

```text
Employee Details

Id: 101
Name: Raman
Salary: 50000
Department: IT
```

---

# 13. Complete Flow

This is an important flow to understand:

```text
User clicks Details
        ↓
/employees/101
        ↓
Angular Router
        ↓
EmployeeDetails Component
        ↓
ActivatedRoute
        ↓
Read "101"
        ↓
EmployeeService
        ↓
HttpClient
        ↓
GET /employees/101
        ↓
ASP.NET Core
        ↓
Find Employee 101
        ↓
JSON
        ↓
subscribe()
        ↓
employee
        ↓
HTML
```

---

# 14. Why Do We Need Two GET APIs?

We now have:

### Get all

```http
GET /employees
```

Used for:

```text
Employee List
```

### Get one

```http
GET /employees/101
```

Used for:

```text
Employee Details
```

This is a very common REST API design.

---

# 15. Important Difference

Don't confuse these two:

```typescript
this.route.snapshot.paramMap.get('id')
```

and:

```typescript
this.employeeService.getEmployeeById(id)
```

The first one gets the **ID from the Angular URL**.

The second one calls the **ASP.NET Core API**.

So:

```text
URL
 ↓
Angular Route Parameter
 ↓
ID
 ↓
HttpClient
 ↓
ASP.NET Core
```

---

# 16. One More Angular Concept

You might wonder why we used:

```typescript
private route = inject(ActivatedRoute);
```

instead of:

```typescript
constructor(private route: ActivatedRoute)
```

You're already using the newer Angular dependency injection style in your project.

So we're following the same pattern:

```typescript
private route = inject(ActivatedRoute);

private employeeService = inject(EmployeeService);
```

This also keeps the example consistent with your Angular 22 setup.

---

# Practice

### Exercise 1

Try:

```text
/employees/102
```

You should see John.

### Exercise 2

Try:

```text
/employees/103
```

You should see David.

### Exercise 3

Try:

```text
/employees/999
```

The API should return `404 Not Found`.

Check the browser console.

---

# What You've Learned So Far

Your Angular + ASP.NET Core application now supports:

```text
                    Angular
                       │
       ┌───────────────┼───────────────┐
       │               │               │
       ▼               ▼               ▼
    Routing          Forms          HttpClient
       │               │               │
       └───────────────┼───────────────┘
                       │
                       ▼
                ASP.NET Core API
                       │
       ┌───────────────┼───────────────┐
       │               │               │
      GET             POST            PUT
       │               │               │
       └───────────────┼───────────────┘
                       │
                    DELETE
```

And you now understand:

* Components
* Services
* Dependency Injection
* HttpClient
* Observables
* `subscribe()`
* GET
* POST
* PUT
* DELETE
* JSON
* Interfaces
* Forms
* `ngModel`
* Routing
* `routerLink`
* `router-outlet`
* Route parameters
* `ActivatedRoute`

## Next Lesson — Lesson 19

We'll improve our application by learning **Angular Forms and Validation**.

We'll make the Employee form reject things like:

```text
Name       → empty ❌
Salary     → negative ❌
Department → empty ❌
```

and display validation messages such as:

```text
Name is required
Salary must be greater than 0
Department is required
```

This is an important step before we move toward **SQL Server + Entity Framework Core** on the ASP.NET Core side.
