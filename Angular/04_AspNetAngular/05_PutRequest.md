# Lesson 15: Updating an Employee — HTTP PUT

In the previous lesson, we implemented:

* **GET** → Read employees
* **POST** → Add an employee

Now we'll implement:

> **PUT → Update an existing employee**

Our flow will be:

```text
Employee List
     ↓
Click Edit
     ↓
Employee data loaded into form
     ↓
Modify data
     ↓
Click Update
     ↓
Angular Service
     ↓
HTTP PUT
     ↓
ASP.NET Core
     ↓
Employee updated
     ↓
Refresh list
```

---

## 1. ASP.NET Core API

Let's first create a simple in-memory employee list.

### Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddCors(options =>
{
    options.AddPolicy("AngularPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:4200")
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});

var app = builder.Build();

app.UseCors("AngularPolicy");

var employees = new List<Employee>
{
    new Employee
    {
        Id = 101,
        Name = "Raman",
        Salary = 50000,
        Department = "IT"
    },

    new Employee
    {
        Id = 102,
        Name = "John",
        Salary = 60000,
        Department = "HR"
    },

    new Employee
    {
        Id = 103,
        Name = "David",
        Salary = 70000,
        Department = "Sales"
    }
};

app.MapGet("/employees", () =>
{
    return employees;
});

app.MapPost("/employees", (Employee employee) =>
{
    employees.Add(employee);

    return Results.Ok(employee);
});

app.MapPut("/employees/{id:int}", (int id, Employee updatedEmployee) =>
{
    var employee = employees.FirstOrDefault(e => e.Id == id);

    if (employee == null)
    {
        return Results.NotFound();
    }

    employee.Name = updatedEmployee.Name;
    employee.Salary = updatedEmployee.Salary;
    employee.Department = updatedEmployee.Department;

    return Results.Ok(employee);
});

app.Run();


public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; } = "";

    public decimal Salary { get; set; }

    public string Department { get; set; } = "";
}
```

---

# 2. Test the PUT API

Suppose we want to update employee `101`.

Request:

```http
PUT http://localhost:5010/employees/101
```

Body:

```json
{
    "id": 101,
    "name": "Raman Sharma",
    "salary": 80000,
    "department": "DevOps"
}
```

ASP.NET Core finds employee `101` and updates:

```text
Name       → Raman Sharma
Salary     → 80000
Department → DevOps
```

---

# 3. Employee Interface

Your existing interface should be:

```typescript
export interface Employee {

    id: number;

    name: string;

    salary: number;

    department: string;

}
```

---

# 4. Update Employee Service

Open:

```text
src/app/employee.ts
```

Your service becomes:

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

}
```

The important method is:

```typescript
updateEmployee(employee: Employee)
```

It sends:

```http
PUT /employees/101
```

---

# 5. Component

Now modify your Employee component.

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

import { EmployeeService } from '../employee';
import { Employee as EmployeeModel } from '../models/employee';

@Component({
    selector: 'app-employee',
    imports: [FormsModule],
    templateUrl: './employee.html',
    styleUrl: './employee.css'
})
export class Employee {

    employees: EmployeeModel[] = [];

    employee: EmployeeModel = {
        id: 0,
        name: '',
        salary: 0,
        department: ''
    };

    isEditMode = false;

    constructor(private employeeService: EmployeeService) {
    }

    ngOnInit() {

        this.loadEmployees();

    }

    loadEmployees() {

        this.employeeService
            .getEmployees()
            .subscribe({

                next: (data) => {

                    this.employees = data;

                },

                error: (error) => {

                    console.error(error);

                }

            });

    }

    editEmployee(employee: EmployeeModel) {

        this.employee = {
            ...employee
        };

        this.isEditMode = true;

    }

    saveEmployee() {

        if (this.isEditMode) {

            this.updateEmployee();

        }
        else {

            this.addEmployee();

        }

    }

    addEmployee() {

        this.employeeService
            .addEmployee(this.employee)
            .subscribe({

                next: () => {

                    alert("Employee added");

                    this.loadEmployees();

                    this.clearForm();

                },

                error: (error) => {

                    console.error(error);

                }

            });

    }

    updateEmployee() {

        this.employeeService
            .updateEmployee(this.employee)
            .subscribe({

                next: () => {

                    alert("Employee updated");

                    this.loadEmployees();

                    this.clearForm();

                },

                error: (error) => {

                    console.error(error);

                }

            });

    }

    clearForm() {

        this.employee = {

            id: 0,

            name: '',

            salary: 0,

            department: ''

        };

        this.isEditMode = false;

    }

}
```

---

# 6. Why do we use `...employee`?

This is an important TypeScript concept.

We could write:

```typescript
this.employee = employee;
```

But we're using:

```typescript
this.employee = {
    ...employee
};
```

This creates a **copy** of the employee.

For example:

```typescript
const employee = {
    id: 101,
    name: "Raman",
    salary: 50000
};
```

Then:

```typescript
const copy = {
    ...employee
};
```

Now we have two separate objects.

This is useful when editing because we don't want to modify the original list item while the user is typing.

---

# 7. Employee HTML

Now add an **Edit** button.

```html
<h2>Employee Form</h2>

<label>Id</label>

<br>

<input
    type="number"
    [(ngModel)]="employee.id"
    [disabled]="isEditMode">

<br><br>


<label>Name</label>

<br>

<input
    [(ngModel)]="employee.name">

<br><br>


<label>Salary</label>

<br>

<input
    type="number"
    [(ngModel)]="employee.salary">

<br><br>


<label>Department</label>

<br>

<input
    [(ngModel)]="employee.department">

<br><br>


<button (click)="saveEmployee()">

    {{ isEditMode ? 'Update' : 'Save' }}

</button>


<button
    *ngIf="isEditMode"
    (click)="clearForm()">

    Cancel

</button>


<hr>


<h2>Employee List</h2>

<table border="1">

    <tr>

        <th>Id</th>

        <th>Name</th>

        <th>Salary</th>

        <th>Department</th>

        <th>Action</th>

    </tr>


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

            </td>

        </tr>

    }

</table>
```

### Important Angular 22 note

Because you're using modern Angular control flow, let's also avoid mixing the old `*ngIf` syntax here.

Replace:

```html
<button
    *ngIf="isEditMode"
    (click)="clearForm()">

    Cancel

</button>
```

with:

```html
@if (isEditMode) {

    <button (click)="clearForm()">

        Cancel

    </button>

}
```

So use the modern Angular syntax consistently.

---

# 8. What Happens When You Click Edit?

Suppose the list contains:

```text
101 | Raman | 50000 | IT | Edit
```

You click:

```text
Edit
```

Angular executes:

```typescript
editEmployee(employee)
```

Then:

```typescript
this.employee = {
    ...employee
};
```

The form becomes:

```text
Id          101
Name        Raman
Salary      50000
Department  IT

[ Update ]
```

---

# 9. User Changes the Data

Suppose the user changes:

```text
Name:       Raman Sharma
Salary:     80000
Department: DevOps
```

Because we're using:

```html
[(ngModel)]="employee.name"
```

the `employee` object automatically changes.

It now contains:

```json
{
    "id": 101,
    "name": "Raman Sharma",
    "salary": 80000,
    "department": "DevOps"
}
```

---

# 10. User Clicks Update

The button executes:

```typescript
saveEmployee()
```

Since:

```typescript
isEditMode = true
```

Angular calls:

```typescript
updateEmployee();
```

which calls the service:

```typescript
this.employeeService.updateEmployee(this.employee)
```

The service sends:

```http
PUT http://localhost:5010/employees/101
```

with:

```json
{
    "id": 101,
    "name": "Raman Sharma",
    "salary": 80000,
    "department": "DevOps"
}
```

---

# 11. ASP.NET Core Receives It

This endpoint receives the request:

```csharp
app.MapPut("/employees/{id:int}", (int id, Employee updatedEmployee) =>
{
    var employee = employees.FirstOrDefault(e => e.Id == id);

    if (employee == null)
    {
        return Results.NotFound();
    }

    employee.Name = updatedEmployee.Name;
    employee.Salary = updatedEmployee.Salary;
    employee.Department = updatedEmployee.Department;

    return Results.Ok(employee);
});
```

The important part is:

```csharp
(int id, Employee updatedEmployee)
```

ASP.NET Core gets:

```text
id
 ↓
101
```

and the JSON body becomes:

```text
updatedEmployee
 ↓
Employee object
```

---

# 12. Complete PUT Flow

```text
Click Edit
     ↓
editEmployee()
     ↓
Employee copied to form
     ↓
User changes values
     ↓
[(ngModel)]
     ↓
employee object updated
     ↓
Click Update
     ↓
saveEmployee()
     ↓
updateEmployee()
     ↓
EmployeeService
     ↓
HttpClient.put()
     ↓
PUT /employees/101
     ↓
ASP.NET Core
     ↓
Find Employee 101
     ↓
Update Employee
     ↓
HTTP 200 OK
     ↓
loadEmployees()
     ↓
Angular displays updated list
```

---

# Create vs Update

You now have:

| Operation | Angular            | HTTP | ASP.NET Core |
| --------- | ------------------ | ---- | ------------ |
| Read      | `getEmployees()`   | GET  | `MapGet()`   |
| Create    | `addEmployee()`    | POST | `MapPost()`  |
| Update    | `updateEmployee()` | PUT  | `MapPut()`   |

The only CRUD operation remaining is:

> **DELETE**

---

# Important Concept: PUT

`PUT` is normally used when we want to **update an existing resource**.

For example:

```http
PUT /employees/101
```

means:

> Update employee 101 with the supplied data.

Compare that with:

```http
POST /employees
```

which means:

> Create a new employee.

---

# Practice

Try these yourself:

### Exercise 1

Change employee `101`:

```text
Name: Raman Sharma
Salary: 90000
Department: DevOps
```

Click **Update**.

---

### Exercise 2

Try editing employee `102`.

Verify that only employee `102` changes.

---

### Exercise 3

Try changing the ID while editing.

You should notice that we disabled the ID field:

```html
[disabled]="isEditMode"
```

because the ID identifies which employee we're updating.

---

# Current CRUD Progress

You now have:

```text
             CRUD

        ┌─────────────┐
        │    CREATE   │  ✅ POST
        ├─────────────┤
        │     READ    │  ✅ GET
        ├─────────────┤
        │    UPDATE   │  ✅ PUT
        ├─────────────┤
        │    DELETE   │  ⏳
        └─────────────┘
```

## Next Lesson — Lesson 16

We'll implement **Delete Employee using HTTP DELETE**.

You'll learn:

```text
Delete button
     ↓
Confirmation
     ↓
Angular Service
     ↓
HttpClient.delete()
     ↓
ASP.NET Core MapDelete()
     ↓
Remove employee
     ↓
Refresh list
```

After that, you'll have your **first complete Angular + ASP.NET Core CRUD application**.
