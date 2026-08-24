# Lesson 19: Angular Forms and Validation

Now we'll improve our Employee form.

Until now, the user could submit:

```text
Name: ""
Salary: -5000
Department: ""
```

That's not acceptable in a real application.

We'll add validation so Angular prevents invalid data before sending it to ASP.NET Core.

---

# 1. What We'll Build

Our form will have:

```text
Employee Form

ID
[ 101 ]

Name
[ Raman              ]

Salary
[ 50000              ]

Department
[ IT                 ]

[ Save ]
```

If the user enters invalid data:

```text
Name
[                    ]
Name is required

Salary
[ -5000              ]
Salary must be greater than 0
```

---

# 2. Two Types of Angular Forms

Angular provides two major approaches:

### Template-driven forms

Uses:

```html
[(ngModel)]
```

### Reactive forms

Uses:

```typescript
FormGroup
FormControl
Validators
```

We've already used:

```html
[(ngModel)]
```

So we'll first learn **template-driven validation**.

Later, we'll learn **Reactive Forms**, which are very important for enterprise Angular applications.

---

# 3. Import FormsModule

You already have:

```typescript
import { FormsModule } from '@angular/forms';
```

Your component should have:

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

`FormsModule` provides `ngModel` and Angular form functionality.

---

# 4. Add Validation to Name

Currently:

```html
<input
    [(ngModel)]="employee.name">
```

Change it to:

```html
<input
    [(ngModel)]="employee.name"
    name="name"
    required>
```

Important:

```html
required
```

means:

> The user must enter a value.

And:

```html
name="name"
```

gives Angular a name for the form control.

---

# 5. Create a Form

Instead of individual inputs, let's use an Angular form.

```html
<form #employeeForm="ngForm">

    <label>Name</label>

    <br>

    <input
        [(ngModel)]="employee.name"
        name="name"
        required>

</form>
```

Here:

```html
#employeeForm="ngForm"
```

creates a reference to the Angular form.

Now Angular tracks:

```text
Valid
Invalid
Touched
Dirty
Pristine
Submitted
```

---

# 6. Check Form Validity

We can use:

```html
employeeForm.valid
```

For example:

```html
<button
    [disabled]="employeeForm.invalid">

    Save

</button>
```

If the form is invalid:

```text
Save button → Disabled
```

If valid:

```text
Save button → Enabled
```

---

# 7. Add Salary Validation

Change:

```html
<input
    [(ngModel)]="employee.salary"
    name="salary"
    type="number">
```

to:

```html
<input
    [(ngModel)]="employee.salary"
    name="salary"
    type="number"
    required
    min="1">
```

Now Angular validates:

```text
Salary > 0
```

---

# 8. Add Department Validation

```html
<input
    [(ngModel)]="employee.department"
    name="department"
    required>
```

---

# 9. Complete Form

Your form can now be:

```html
<h2>Employee Form</h2>

<form #employeeForm="ngForm">

    <label>Id</label>

    <br>

    <input
        type="number"
        [(ngModel)]="employee.id"
        name="id"
        [disabled]="isEditMode">

    <br><br>


    <label>Name</label>

    <br>

    <input
        [(ngModel)]="employee.name"
        name="name"
        required>

    <br><br>


    <label>Salary</label>

    <br>

    <input
        type="number"
        [(ngModel)]="employee.salary"
        name="salary"
        required
        min="1">

    <br><br>


    <label>Department</label>

    <br>

    <input
        [(ngModel)]="employee.department"
        name="department"
        required>

    <br><br>


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

# 10. Display Validation Message

Let's improve the Name field.

```html
<input
    [(ngModel)]="employee.name"
    name="name"
    required
    #name="ngModel">
```

Now Angular gives us information about the field.

We can write:

```html
@if (name.invalid && name.touched) {

    <p>Name is required</p>

}
```

Complete example:

```html
<label>Name</label>

<br>

<input
    [(ngModel)]="employee.name"
    name="name"
    required
    #name="ngModel">

@if (name.invalid && name.touched) {

    <p>Name is required</p>

}
```

---

# 11. What Does `name.invalid` Mean?

Angular tracks the control.

```text
name.valid
name.invalid
name.touched
name.untouched
name.dirty
name.pristine
```

For example:

```typescript
name.invalid
```

means:

> The Name field currently contains invalid data.

And:

```typescript
name.touched
```

means:

> The user has interacted with the field and then moved away from it.

Therefore:

```html
@if (name.invalid && name.touched)
```

means:

> Show the error only after the user has interacted with the field.

This gives a much better user experience.

---

# 12. Salary Validation Message

```html
<input
    type="number"
    [(ngModel)]="employee.salary"
    name="salary"
    required
    min="1"
    #salary="ngModel">
```

Then:

```html
@if (salary.invalid && salary.touched) {

    @if (salary.errors?.['required']) {

        <p>Salary is required</p>

    }

    @if (salary.errors?.['min']) {

        <p>Salary must be greater than 0</p>

    }

}
```

---

# 13. Department Validation

```html
<input
    [(ngModel)]="employee.department"
    name="department"
    required
    #department="ngModel">
```

Then:

```html
@if (department.invalid && department.touched) {

    <p>Department is required</p>

}
```

---

# 14. Complete Improved Form

Here is the complete form:

```html
<h2>Employee Form</h2>

<form #employeeForm="ngForm">

    <!-- ID -->

    <label>Id</label>

    <br>

    <input
        type="number"
        [(ngModel)]="employee.id"
        name="id"
        [disabled]="isEditMode">

    <br><br>


    <!-- NAME -->

    <label>Name</label>

    <br>

    <input
        [(ngModel)]="employee.name"
        name="name"
        required
        #name="ngModel">

    @if (name.invalid && name.touched) {

        <p>Name is required</p>

    }

    <br>


    <!-- SALARY -->

    <label>Salary</label>

    <br>

    <input
        type="number"
        [(ngModel)]="employee.salary"
        name="salary"
        required
        min="1"
        #salary="ngModel">

    @if (salary.invalid && salary.touched) {

        @if (salary.errors?.['required']) {

            <p>Salary is required</p>

        }

        @if (salary.errors?.['min']) {

            <p>Salary must be greater than 0</p>

        }

    }

    <br>


    <!-- DEPARTMENT -->

    <label>Department</label>

    <br>

    <input
        [(ngModel)]="employee.department"
        name="department"
        required
        #department="ngModel">

    @if (department.invalid && department.touched) {

        <p>Department is required</p>

    }

    <br><br>


    <!-- BUTTON -->

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

# 15. What Happens Now?

Suppose the user opens the form.

Initially:

```text
Name
[                    ]

Salary
[                    ]

Department
[                    ]

[ Save ]  ← disabled
```

Why?

Because:

```typescript
employeeForm.invalid
```

is `true`.

---

The user enters:

```text
Name: Raman
Salary: 50000
Department: IT
```

Now:

```text
employeeForm.invalid
```

becomes:

```text
false
```

Therefore:

```text
[ Save ]  ← enabled
```

---

# 16. Validation Happens Before HTTP

This is an important architecture concept.

```text
User enters data
       ↓
Angular validation
       ↓
Valid?
   ↙       ↘
 No         Yes
 ↓           ↓
Show error   HTTP POST
             ↓
       ASP.NET Core
```

Angular provides **client-side validation**.

But remember:

> Client-side validation is not enough.

ASP.NET Core should also validate the data.

Why?

Because someone can call the API directly using:

* Postman
* curl
* another application

and bypass Angular completely.

So eventually we'll have:

```text
Angular Validation
        +
ASP.NET Core Validation
```

---

# 17. Example

Angular prevents:

```text
Salary = -5000
```

But even if somebody sends:

```http
POST /employees
```

directly from Postman:

```json
{
    "name": "Raman",
    "salary": -5000
}
```

ASP.NET Core should reject it.

We'll implement server-side validation later.

---

# 18. Important Angular Concepts

You have now learned:

### `required`

```html
required
```

Field cannot be empty.

### `min`

```html
min="1"
```

Number must be at least 1.

### `ngModel`

```html
[(ngModel)]="employee.name"
```

Provides two-way binding.

### `ngForm`

```html
#employeeForm="ngForm"
```

Gives Angular information about the entire form.

### `invalid`

```html
employeeForm.invalid
```

Checks whether the form is invalid.

### `touched`

```html
name.touched
```

Checks whether the user has interacted with the field.

### `errors`

```html
name.errors
```

Contains validation errors.

---

# 19. Your Application Architecture

We're now getting closer to a real enterprise application:

```text
                     Angular
                        │
       ┌────────────────┼────────────────┐
       │                │                │
       ▼                ▼                ▼
    Routing          Forms          Components
       │                │                │
       └────────────────┼────────────────┘
                        │
                        ▼
                 EmployeeService
                        │
                        ▼
                    HttpClient
                        │
                        ▼
                ASP.NET Core API
                        │
                        ▼
                    Validation
                        │
                        ▼
                    Data Store
```

---

# 20. Practice Exercises

### Exercise 1

Make Name require at least 3 characters.

Hint:

```html
minlength="3"
```

Display:

```text
Name must contain at least 3 characters
```

---

### Exercise 2

Add an Email field.

Use:

```html
type="email"
```

and:

```html
required
```

---

### Exercise 3

Add a Department dropdown:

```html
<select
    [(ngModel)]="employee.department"
    name="department"
    required>

    <option value="">Select Department</option>

    <option value="IT">IT</option>

    <option value="HR">HR</option>

    <option value="Sales">Sales</option>

    <option value="Finance">Finance</option>

</select>
```

---

# Current Progress

You have now covered a significant part of Angular:

```text
TypeScript
   ↓
Components
   ↓
Templates
   ↓
Data Binding
   ↓
Services
   ↓
Dependency Injection
   ↓
HttpClient
   ↓
Observables
   ↓
ASP.NET Core API
   ↓
CRUD
   ↓
Routing
   ↓
Route Parameters
   ↓
Forms
   ↓
Validation
```

## Next Lesson — Lesson 20

We'll move to **Reactive Forms**, which are particularly important for enterprise Angular applications.

You'll learn:

```text
FormGroup
FormControl
Validators
formControlName
ReactiveFormsModule
```

and we'll rebuild our Employee form using Reactive Forms.
