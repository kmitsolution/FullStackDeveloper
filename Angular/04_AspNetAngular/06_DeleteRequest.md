# Lesson 16: Delete Employee — HTTP DELETE

Great. We now have:

* ✅ GET — Read employees
* ✅ POST — Add employee
* ✅ PUT — Update employee

Now we implement the final CRUD operation:

> **DELETE — Remove an employee**

---

# 1. The DELETE Flow

When the user clicks **Delete**:

```text
Delete Button
     ↓
Angular Component
     ↓
Employee Service
     ↓
HttpClient.delete()
     ↓
ASP.NET Core
     ↓
Find Employee
     ↓
Remove Employee
     ↓
Return Success
     ↓
Angular reloads list
```

---

# 2. ASP.NET Core — Add DELETE API

Using our existing in-memory employee list, add:

```csharp
app.MapDelete("/employees/{id:int}", (int id) =>
{
    var employee = employees.FirstOrDefault(e => e.Id == id);

    if (employee == null)
    {
        return Results.NotFound();
    }

    employees.Remove(employee);

    return Results.Ok();
});
```

So your CRUD APIs are now:

```text
GET     /employees
POST    /employees
PUT     /employees/{id}
DELETE  /employees/{id}
```

---

# 3. Test the API with Postman

For example, delete employee `102`.

Request:

```http
DELETE http://localhost:5010/employees/102
```

If employee 102 exists, ASP.NET Core removes it.

The response is:

```text
200 OK
```

Then:

```http
GET http://localhost:5010/employees
```

should no longer contain employee `102`.

---

# 4. Add DELETE to EmployeeService

Open:

```text
src/app/employee.ts
```

Add:

```typescript
deleteEmployee(id: number) {

    return this.http.delete(
        `${this.apiUrl}/${id}`
    );

}
```

Your service now contains all four operations:

```typescript
import { Service, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Employee } from './models/employee';

@Service()
export class EmployeeService {

    private http = inject(HttpClient);

    private apiUrl = "http://localhost:5010/employees";

    getEmployees() {

        return this.http.get<Employee[]>(this.apiUrl);

    }

    addEmployee(employee: Employee) {

        return this.http.post<Employee>(
            this.apiUrl,
            employee
        );

    }

    updateEmployee(employee: Employee) {

        return this.http.put<Employee>(
            `${this.apiUrl}/${employee.id}`,
            employee
        );

    }

    deleteEmployee(id: number) {

        return this.http.delete(
            `${this.apiUrl}/${id}`
        );

    }

    getCompanyName() {

        return "KMIT Solutions";

    }
}
```

---

# 5. Add Delete Method to Component

In your `employee.ts` component, add:

```typescript
deleteEmployee(id: number) {

    if (!confirm("Are you sure you want to delete this employee?")) {

        return;

    }

    this.employeeService
        .deleteEmployee(id)
        .subscribe({

            next: () => {

                alert("Employee deleted");

                this.loadEmployees();

            },

            error: (error) => {

                console.error(
                    "Error deleting employee:",
                    error
                );

            }

        });

}
```

---

# 6. Add Delete Button

In your employee table:

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

        </td>

    </tr>

}
```

Now each employee has:

```text
[ Edit ] [ Delete ]
```

---

# 7. What Happens When Delete Is Clicked?

Suppose you click:

```text
Delete
```

for employee:

```text
102 | John | 60000 | HR
```

Angular calls:

```typescript
deleteEmployee(102)
```

Then:

```typescript
this.employeeService.deleteEmployee(102)
```

The service creates:

```http
DELETE http://localhost:5010/employees/102
```

---

# 8. ASP.NET Core Receives the Request

This endpoint executes:

```csharp
app.MapDelete("/employees/{id:int}", (int id) =>
{
    var employee = employees.FirstOrDefault(e => e.Id == id);

    if (employee == null)
    {
        return Results.NotFound();
    }

    employees.Remove(employee);

    return Results.Ok();
});
```

It finds:

```text
Id = 102
```

and removes that object from:

```csharp
employees
```

---

# 9. Why Do We Call `loadEmployees()` Again?

After successful deletion:

```typescript
next: () => {

    this.loadEmployees();

}
```

Why?

Because the database/API has changed.

Before deletion:

```text
101 Raman
102 John
103 David
```

After deletion:

```text
101 Raman
103 David
```

Calling:

```typescript
loadEmployees()
```

gets the latest data from the API.

The UI then updates automatically.

---

# 10. Complete CRUD

Congratulations! 🎉

You now understand the basic CRUD communication between Angular and ASP.NET Core.

```text
             Employee Management
                    │
       ┌────────────┼────────────┐
       │            │            │
       ▼            ▼            ▼
     Angular      Service       API
       │            │            │
       └────────────┼────────────┘
                    │
                  HTTP
```

| Operation | HTTP   | Angular         | ASP.NET Core  |
| --------- | ------ | --------------- | ------------- |
| Create    | POST   | `http.post()`   | `MapPost()`   |
| Read      | GET    | `http.get()`    | `MapGet()`    |
| Update    | PUT    | `http.put()`    | `MapPut()`    |
| Delete    | DELETE | `http.delete()` | `MapDelete()` |

---

# 11. Your Application Flow Now

```text
                     Angular
                        │
              ┌─────────┴─────────┐
              │                   │
         Employee UI        EmployeeService
                                  │
                              HttpClient
                                  │
                     ┌────────────┼────────────┐
                     │            │            │
                    GET          POST          PUT
                     │            │            │
                     └────────────┼────────────┘
                                  │
                                DELETE
                                  │
                                  ▼
                          ASP.NET Core API
                                  │
                                  ▼
                            Employee Data
```

---

# 12. Important Concept: Observable

All of these:

```typescript
this.http.get()
this.http.post()
this.http.put()
this.http.delete()
```

return an **Observable**.

For example:

```typescript
this.employeeService
    .deleteEmployee(id)
    .subscribe({
        next: () => {
            console.log("Deleted");
        }
    });
```

The Observable represents the asynchronous HTTP operation.

---

# 13. Why Doesn't the UI Automatically Know the Employee Was Deleted?

Because these are two different things:

```text
Server data
```

and

```text
Angular data
```

When the server deletes employee 102:

```text
ASP.NET Core
       ↓
Employee 102 removed
```

Angular's existing:

```typescript
employees
```

array doesn't automatically change.

So we call:

```typescript
this.loadEmployees();
```

and get the latest server data.

---

# 14. Better Approach

Instead of making another GET request, you could remove the employee locally:

```typescript
next: () => {

    this.employees =
        this.employees.filter(e => e.id !== id);

}
```

This avoids another HTTP request.

For example:

```text
Before:

101 Raman
102 John
103 David

       DELETE 102

After:

101 Raman
103 David
```

However, while you're learning, I recommend:

```typescript
this.loadEmployees();
```

because it keeps Angular synchronized with the server and makes the concept easier to understand.

---

# 15. Important: Our Data Is Not Permanent

Our ASP.NET Core application currently uses:

```csharp
var employees = new List<Employee>
{
    ...
};
```

This is **in-memory data**.

If you stop the API:

```text
Ctrl + C
```

and start it again, your changes disappear.

For example:

```text
Add Raman
Update John
Delete David
```

All changes are lost when the application restarts.

That's because we haven't connected SQL Server yet.

---

# Your CRUD Is Complete

```text
CREATE  ✅
READ    ✅
UPDATE  ✅
DELETE  ✅
```

You have now learned the complete basic flow:

```text
Angular
   ↓
Service
   ↓
HttpClient
   ↓
HTTP
   ↓
ASP.NET Core
   ↓
Data
```

## Next Lesson — Lesson 17

Before moving to SQL Server, we'll improve the Angular application itself.

We'll learn **Angular Routing**:

```text
/employee
/home
/about
```

and understand:

* `app.routes.ts`
* `router-outlet`
* `routerLink`
* Navigation between components
* Route parameters
* Why routing is important in Angular applications

Then we'll connect our Employee page to a proper `/employees` route.
