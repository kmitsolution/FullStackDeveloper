# Lesson 20: Angular Reactive Forms

In the previous lesson, we used **Template-Driven Forms** with:

```html
[(ngModel)]
```

Now we'll learn **Reactive Forms**.

For enterprise Angular applications, Reactive Forms are extremely useful because the form structure and validation rules are defined mainly in **TypeScript**, rather than being scattered across the HTML.

---

# 1. Template-Driven vs Reactive Forms

### Template-driven

We previously used:

```html
<input
    [(ngModel)]="employee.name"
    name="name"
    required>
```

The form logic is mostly in HTML.

### Reactive

We'll now define the form in TypeScript:

```typescript
this.employeeForm = new FormGroup({
    name: new FormControl(''),
    salary: new FormControl(0),
    department: new FormControl('')
});
```

The HTML becomes simpler:

```html
<input formControlName="name">
```

---

# 2. Why Learn Reactive Forms?

Reactive Forms are especially useful when forms become complicated.

For example:

```text
Employee Form
│
├── Name
├── Email
├── Salary
├── Department
├── Address
│   ├── City
│   ├── State
│   └── Pincode
└── Skills
```

You may have:

* many fields
* complex validation
* conditional validation
* dynamic fields
* nested forms
* validation based on other fields

Reactive Forms make these scenarios easier to manage.

---

# 3. Create a Reactive Form

We'll use our Employee form.

Our model is:

```typescript
export interface Employee {

    id: number;

    name: string;

    salary: number;

    department: string;

}
```

---

# 4. Import ReactiveFormsModule

Open:

```text
employee/employee.ts
```

Add:

```typescript
import { ReactiveFormsModule } from '@angular/forms';
```

Your component metadata should contain:

```typescript
@Component({
    selector: 'app-employee',

    imports: [
        ReactiveFormsModule,
        RouterLink
    ],

    templateUrl: './employee.html',
    styleUrl: './employee.css'
})
```

Notice that we are using:

```typescript
ReactiveFormsModule
```

instead of:

```typescript
FormsModule
```

for this form.

---

# 5. Import FormGroup, FormControl and Validators

Add:

```typescript
import {
    FormGroup,
    FormControl,
    Validators
} from '@angular/forms';
```

So the imports become:

```typescript
import { Component } from '@angular/core';

import {
    FormGroup,
    FormControl,
    Validators,
    ReactiveFormsModule
} from '@angular/forms';

import { RouterLink } from '@angular/router';

import { EmployeeService } from '../employee';
import { Employee as EmployeeModel } from '../models/employee';
```

---

# 6. Create FormGroup

Inside the component:

```typescript
employeeForm = new FormGroup({

    id: new FormControl(0),

    name: new FormControl(''),

    salary: new FormControl(0),

    department: new FormControl('')

});
```

We have created four form controls:

```text
Employee Form
│
├── id
├── name
├── salary
└── department
```

---

# 7. Add Validation

Now let's add validation.

```typescript
employeeForm = new FormGroup({

    id: new FormControl(0),

    name: new FormControl(
        '',
        [
            Validators.required,
            Validators.minLength(3)
        ]
    ),

    salary: new FormControl(
        0,
        [
            Validators.required,
            Validators.min(1)
        ]
    ),

    department: new FormControl(
        '',
        Validators.required
    )

});
```

Now Angular knows:

### Name

```text
Required
Minimum 3 characters
```

### Salary

```text
Required
Minimum 1
```

### Department

```text
Required
```

---

# 8. `formControlName`

Now let's connect our HTML to the TypeScript form.

Instead of:

```html
[(ngModel)]="employee.name"
```

we use:

```html
formControlName="name"
```

---

# 9. Reactive Form HTML

Replace the previous form with:

```html
<h2>Employee Form</h2>

<form [formGroup]="employeeForm">

    <label>Id</label>

    <br>

    <input
        type="number"
        formControlName="id"
        [disabled]="isEditMode">

    <br><br>


    <label>Name</label>

    <br>

    <input
        formControlName="name">

    <br>

    @if (
        employeeForm.get('name')?.invalid &&
        employeeForm.get('name')?.touched
    ) {

        <p>Name is required and must contain at least 3 characters.</p>

    }

    <br>


    <label>Salary</label>

    <br>

    <input
        type="number"
        formControlName="salary">

    <br>

    @if (
        employeeForm.get('salary')?.invalid &&
        employeeForm.get('salary')?.touched
    ) {

        <p>Salary must be greater than 0.</p>

    }

    <br>


    <label>Department</label>

    <br>

    <select formControlName="department">

        <option value="">
            Select Department
        </option>

        <option value="IT">
            IT
        </option>

        <option value="HR">
            HR
        </option>

        <option value="Sales">
            Sales
        </option>

        <option value="Finance">
            Finance
        </option>

    </select>

    <br>

    @if (
        employeeForm.get('department')?.invalid &&
        employeeForm.get('department')?.touched
    ) {

        <p>Department is required.</p>

    }

    <br>


    <button
        type="button"
        [disabled]="employeeForm.invalid"
        (click)="saveEmployee()">

        {{ isEditMode ? 'Update' : 'Save' }}

    </button>


    @if (isEditMode) {

        <button
            type="button"
            (click)="clearForm()">

            Cancel

        </button>

    }

</form>
```

---

# 10. Understanding `[formGroup]`

This is very important:

```html
<form [formGroup]="employeeForm">
```

It connects the HTML form to:

```typescript
employeeForm
```

in the component.

Think of it as:

```text
HTML
 │
 │ [formGroup]
 ▼
employeeForm
 │
 ├── id
 ├── name
 ├── salary
 └── department
```

---

# 11. Understanding `formControlName`

This:

```html
<input formControlName="name">
```

connects the input to:

```typescript
name: new FormControl(...)
```

Similarly:

```html
<input formControlName="salary">
```

connects to:

```typescript
salary: new FormControl(...)
```

---

# 12. Getting Form Data

Now comes one of the biggest differences.

Previously we had:

```typescript
employee.name
```

With Reactive Forms, we can get the complete form value:

```typescript
this.employeeForm.value
```

For example:

```typescript
saveEmployee() {

    console.log(this.employeeForm.value);

}
```

It might print:

```text
{
    id: 101,
    name: "Raman",
    salary: 50000,
    department: "IT"
}
```

---

# 13. Send Form Data to ASP.NET Core

We need to convert the form value into our Employee model.

We can write:

```typescript
saveEmployee() {

    if (this.employeeForm.invalid) {

        this.employeeForm.markAllAsTouched();

        return;

    }

    const employee =
        this.employeeForm.getRawValue();

    console.log(employee);

}
```

Notice:

```typescript
getRawValue()
```

instead of:

```typescript
value
```

This is useful because we disabled the ID during editing:

```html
[disabled]="isEditMode"
```

Disabled controls aren't included in `.value`.

`getRawValue()` includes them.

---

# 14. Complete `saveEmployee()`

Let's connect it to our existing API.

```typescript
saveEmployee() {

    if (this.employeeForm.invalid) {

        this.employeeForm.markAllAsTouched();

        return;

    }

    const employee =
        this.employeeForm.getRawValue();

    if (this.isEditMode) {

        this.employeeService
            .updateEmployee(employee)
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
    else {

        this.employeeService
            .addEmployee(employee)
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

}
```

---

# 15. `markAllAsTouched()`

This is extremely useful.

Suppose the user clicks Save without entering anything.

The form is invalid.

We execute:

```typescript
this.employeeForm.markAllAsTouched();
```

Angular marks every control as touched.

Therefore validation messages immediately appear.

For example:

```text
Name is required and must contain at least 3 characters.

Salary must be greater than 0.

Department is required.
```

---

# 16. Edit Employee

Our previous `editEmployee()` needs to change.

Previously:

```typescript
this.employee = {
    ...employee
};
```

Now we'll populate the Reactive Form.

```typescript
editEmployee(employee: EmployeeModel) {

    this.employeeForm.patchValue({

        id: employee.id,

        name: employee.name,

        salary: employee.salary,

        department: employee.department

    });

    this.isEditMode = true;

}
```

---

# 17. What is `patchValue()`?

Suppose the employee is:

```json
{
    "id": 101,
    "name": "Raman",
    "salary": 50000,
    "department": "IT"
}
```

We execute:

```typescript
this.employeeForm.patchValue(employee);
```

Angular puts those values into the form.

So the form becomes:

```text
Id
[101]

Name
[Raman]

Salary
[50000]

Department
[IT]
```

---

# 18. `patchValue()` vs `setValue()`

You will encounter both.

### patchValue

```typescript
this.employeeForm.patchValue({
    name: "Raman"
});
```

You can update only some fields.

### setValue

```typescript
this.employeeForm.setValue({
    id: 101,
    name: "Raman",
    salary: 50000,
    department: "IT"
});
```

All controls must be supplied.

For most partial updates, `patchValue()` is convenient.

---

# 19. Clear the Form

Now we don't need to create a new `employee` object.

We can simply reset the form:

```typescript
clearForm() {

    this.employeeForm.reset({

        id: 0,

        name: '',

        salary: 0,

        department: ''

    });

    this.isEditMode = false;

}
```

---

# 20. Complete Component

Your Employee component can now look like this:

```typescript
import { Component } from '@angular/core';

import {
    FormGroup,
    FormControl,
    Validators,
    ReactiveFormsModule
} from '@angular/forms';

import { RouterLink } from '@angular/router';

import { EmployeeService } from '../employee';
import { Employee as EmployeeModel } from '../models/employee';

@Component({
    selector: 'app-employee',

    imports: [
        ReactiveFormsModule,
        RouterLink
    ],

    templateUrl: './employee.html',
    styleUrl: './employee.css'
})
export class Employee {

    employees: EmployeeModel[] = [];

    isEditMode = false;

    employeeForm = new FormGroup({

        id: new FormControl(0),

        name: new FormControl(
            '',
            [
                Validators.required,
                Validators.minLength(3)
            ]
        ),

        salary: new FormControl(
            0,
            [
                Validators.required,
                Validators.min(1)
            ]
        ),

        department: new FormControl(
            '',
            Validators.required
        )

    });


    constructor(
        private employeeService: EmployeeService
    ) {
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


    saveEmployee() {

        if (this.employeeForm.invalid) {

            this.employeeForm.markAllAsTouched();

            return;

        }

        const employee =
            this.employeeForm.getRawValue();


        if (this.isEditMode) {

            this.employeeService
                .updateEmployee(employee)
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
        else {

            this.employeeService
                .addEmployee(employee)
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

    }


    editEmployee(employee: EmployeeModel) {

        this.employeeForm.patchValue({

            id: employee.id,

            name: employee.name,

            salary: employee.salary,

            department: employee.department

        });

        this.isEditMode = true;

    }


    deleteEmployee(id: number) {

        if (!confirm(
            "Are you sure you want to delete this employee?"
        )) {

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

                    console.error(error);

                }

            });

    }


    clearForm() {

        this.employeeForm.reset({

            id: 0,

            name: '',

            salary: 0,

            department: ''

        });

        this.isEditMode = false;

    }

}
```

---

# 21. One Important TypeScript Detail

You may notice that:

```typescript
this.employeeForm.getRawValue()
```

returns the form data.

Because our controls are typed based on their initial values, Angular/TypeScript can provide useful type information.

This is another benefit of Reactive Forms.

---

# 22. Template-Driven vs Reactive

| Feature                 | Template-Driven | Reactive    |
| ----------------------- | --------------- | ----------- |
| Main logic              | HTML            | TypeScript  |
| `ngModel`               | ✅               | Usually no  |
| `FormGroup`             | ❌               | ✅           |
| `FormControl`           | ❌               | ✅           |
| Complex validation      | More difficult  | Easier      |
| Dynamic forms           | More difficult  | Easier      |
| Enterprise applications | Sometimes       | Very common |
| Testing                 | More difficult  | Easier      |

---

# 23. Which One Should You Use?

For simple forms:

```text
Login
Search
Simple Contact Form
```

Template-driven forms can be perfectly fine.

For complex enterprise applications:

```text
Employee Management
Banking
Insurance
Order Management
Customer Management
```

Reactive Forms are usually a better choice.

---

# 24. Important Architecture

Our application is now becoming:

```text
                    Angular
                       │
       ┌───────────────┼───────────────┐
       │               │               │
       ▼               ▼               ▼
    Routing       Reactive Forms    Components
                       │
                       ▼
                  Validation
                       │
                       ▼
                Employee Service
                       │
                       ▼
                   HttpClient
                       │
                       ▼
                ASP.NET Core API
                       │
                       ▼
                 Data Storage
```

---

# 25. Practice Exercises

### Exercise 1 — Email

Add:

```typescript
email: new FormControl(
    '',
    [
        Validators.required,
        Validators.email
    ]
)
```

Then add:

```html
<input formControlName="email">
```

---

### Exercise 2 — Salary Range

Change salary validation to:

```typescript
Validators.min(10000)
```

and:

```typescript
Validators.max(1000000)
```

Now salary must be between:

```text
10,000 and 10,00,000
```

---

### Exercise 3 — Employee Name

Require:

```text
Minimum 3 characters
Maximum 50 characters
```

Use:

```typescript
Validators.minLength(3)

Validators.maxLength(50)
```

---

# What You Learned

You now understand:

```text
ReactiveFormsModule
        ↓
FormGroup
        ↓
FormControl
        ↓
Validators
        ↓
formControlName
        ↓
patchValue()
        ↓
getRawValue()
        ↓
markAllAsTouched()
```

And most importantly:

```text
Angular Form
     ↓
Validation
     ↓
Employee Object
     ↓
EmployeeService
     ↓
HTTP POST / PUT
     ↓
ASP.NET Core
```

---

# Next Lesson — Lesson 21

Before connecting SQL Server, we'll learn an important Angular concept:

## **HTTP Error Handling**

We'll handle real API failures such as:

```text
404 Not Found
400 Bad Request
401 Unauthorized
403 Forbidden
500 Internal Server Error
```

We'll also learn how to display a friendly message instead of just:

```text
Error fetching employees data.
```

This becomes especially important when your Angular application starts communicating with a real ASP.NET Core backend.
