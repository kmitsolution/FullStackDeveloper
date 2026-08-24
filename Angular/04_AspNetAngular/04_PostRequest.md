Excellent! 🎉

Until now you've learned how to **display employees** (Read operation).

Now we'll implement the first **CRUD operation**:

> **Create (Insert Employee)**

This lesson is very important because you'll learn how Angular sends data to an ASP.NET Core API.

---

# Lesson 14: Adding a New Employee (HTTP POST)

## Learning Objectives

By the end of this lesson, you'll understand:

* Creating an HTML Form
* Two-Way Data Binding
* Sending JSON to ASP.NET Core
* HTTP POST
* Model Binding in ASP.NET Core
* Refreshing the Employee List
* Understanding the complete request flow

---

# What We Are Going to Build

```text
----------------------------------------

Employee Form

Name

______________

Salary

______________

Department

______________

[ Save ]

----------------------------------------

Employee List

101 Raman 50000 IT

102 John 60000 HR

103 David 70000 Sales

----------------------------------------
```

When the user clicks **Save**

↓

Angular sends the employee

↓

ASP.NET Core receives it

↓

Returns Success

↓

Angular refreshes the list

---

# Architecture

```text
Angular Form

↓

Employee Component

↓

Employee Service

↓

POST /api/employees

↓

ASP.NET Core

↓

Database

↓

Success

↓

Reload Employees
```

---

# Step 1 - ASP.NET Core POST API

For now, we'll keep it simple.

```csharp
public record EmployeeDto(
    int Id,
    string Name,
    decimal Salary,
    string Department
);
```

Minimal API

```csharp
var employees = new List<EmployeeDto>
{
    new(101,"Raman",50000,"IT"),
    new(102,"John",60000,"HR")
};

app.MapGet("/employees", () =>
{
    return employees;
});

app.MapPost("/employees", (EmployeeDto employee) =>
{
    employees.Add(employee);

    return Results.Ok(employee);
});
```

Notice

Angular sends

```json
{
    "id":103,
    "name":"David",
    "salary":70000,
    "department":"Sales"
}
```

ASP.NET Core automatically converts JSON into an `EmployeeDto`.

This is called **Model Binding**.

---

# Step 2 - Update Interface

```typescript
export interface Employee {

    id:number;

    name:string;

    salary:number;

    department:string;

}
```

---

# Step 3 - Employee Service

```typescript
import { HttpClient } from '@angular/common/http';
import { Service, inject } from '@angular/core';
import { Employee } from './models/employee';

@Service()
export class EmployeeService {

    private http = inject(HttpClient);

    private apiUrl="http://localhost:5010/employees";

    getEmployees(){

        return this.http.get<Employee[]>(this.apiUrl);

    }

    addEmployee(employee:Employee){

        return this.http.post<Employee>(
            this.apiUrl,
            employee
        );

    }

}
```

Notice

POST takes

```typescript
employee
```

as the second parameter.

---

# Step 4 - Component

Create two properties.

```typescript
employees:Employee[]=[];

employee:Employee={

    id:0,

    name:"",

    salary:0,

    department:""

};
```

The first property stores

Employee List.

The second property stores

Employee Form.

---

# Load Employees

```typescript
ngOnInit(){

    this.loadEmployees();

}

loadEmployees(){

    this.employeeService
        .getEmployees()
        .subscribe({

            next:data=>{

                this.employees=data;

            }

        });

}
```

---

# Save Employee

```typescript
saveEmployee(){

    this.employeeService
        .addEmployee(this.employee)
        .subscribe({

            next:()=>{

                alert("Employee Saved");

                this.loadEmployees();

                this.clearForm();

            }

        });

}
```

Notice

After saving

we reload

the employee list.

---

# Clear Form

```typescript
clearForm(){

    this.employee={

        id:0,

        name:"",

        salary:0,

        department:""

    };

}
```

---

# Step 5 - HTML

```html
<h2>Employee Form</h2>

<label>Id</label>

<br>

<input
type="number"
[(ngModel)]="employee.id">

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

<button
(click)="saveEmployee()">

Save

</button>
```

---

# Employee List

```html
<hr>

<h2>Employee List</h2>

<table border="1">

<tr>

<th>Id</th>

<th>Name</th>

<th>Salary</th>

<th>Department</th>

</tr>

@for(employee of employees;track employee.id){

<tr>

<td>{{employee.id}}</td>

<td>{{employee.name}}</td>

<td>{{employee.salary}}</td>

<td>{{employee.department}}</td>

</tr>

}

</table>
```

---

# Don't Forget FormsModule

Since we're using

```html
[(ngModel)]
```

the component must import

```typescript
import { FormsModule } from '@angular/forms';
```

and add it to the component metadata:

```typescript
@Component({
    selector:'app-employee',
    imports:[FormsModule],
    templateUrl:'./employee.html',
    styleUrl:'./employee.css'
})
```

Without `FormsModule`, Angular will report an error that `ngModel` isn't recognized.

---

# Complete Flow

```text
User Types

↓

Textbox

↓

employee Object Updated

↓

Click Save

↓

Employee Service

↓

HttpClient

↓

POST /employees

↓

ASP.NET Core

↓

Employee Added

↓

Success

↓

Reload Employee List

↓

Browser Updated
```

---

# What JSON Does Angular Send?

```json
{
    "id":104,
    "name":"Alex",
    "salary":80000,
    "department":"Finance"
}
```

Notice

Angular automatically converts

```typescript
employee
```

into JSON.

No conversion code is needed.

---

# How ASP.NET Core Receives It

```csharp
app.MapPost("/employees", (EmployeeDto employee) =>
{
    // employee.Id
    // employee.Name
    // employee.Salary
    // employee.Department

    return Results.Ok(employee);
});
```

ASP.NET Core automatically binds the JSON request body to the `EmployeeDto` parameter.

---

# Best Practices

✅ Keep HTTP calls inside the Service.

✅ Use Interfaces instead of `any`.

✅ Reset the form after a successful save.

✅ Refresh the employee list after creating a new record.

---

# Summary

| Concept           | Description                               |
| ----------------- | ----------------------------------------- |
| POST              | Sends data to the server                  |
| `[(ngModel)]`     | Updates the object while typing           |
| `http.post()`     | Sends the object to the API               |
| Model Binding     | ASP.NET Core converts JSON into an object |
| `loadEmployees()` | Refreshes the UI after saving             |

---

# Practice Exercises

### Exercise 1

Add an **Email** field to the form and update the interface and API.

---

### Exercise 2

Replace the **Department** textbox with a `<select>` element containing:

* IT
* HR
* Sales
* Finance

Bind it using `[(ngModel)]`.

---

### Exercise 3

After saving, display:

```text
Employee Saved Successfully
```

instead of using `alert()`.

---

# Interview Questions

1. What is the difference between GET and POST?
2. What does `http.post()` return?
3. What is Model Binding in ASP.NET Core?
4. Why do we use `[(ngModel)]` in forms?
5. Why should API calls be inside a Service instead of a Component?
6. Why do we reload the employee list after saving?

---

# Next Lesson (Lesson 15)

Now users can **add** employees.

The next step is to **edit existing employees**.

We'll learn:

* Edit button
* Populate the form with selected employee data
* HTTP PUT
* Update API
* Save Changes
* Refresh the employee list

At that point, you'll have implemented **Create** and **Read**, and you'll be halfway to a complete CRUD application.
