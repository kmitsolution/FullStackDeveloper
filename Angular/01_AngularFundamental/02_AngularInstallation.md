Absolutely. Since you're learning **Angular 20 (Standalone Components)**, I'll teach using the **latest Angular architecture**, not the older `app.module.ts` approach. I'll also point out the older approach when relevant because you'll likely encounter it in enterprise projects.

This updated Lesson 2 matches the project structure you actually have.

---

# Lesson 2: Installing Angular & Creating Your First Angular Application (Angular 20)

## Learning Objectives

By the end of this lesson, you will understand:

* Why Angular needs Node.js
* What is npm?
* What is npx?
* What is Angular CLI?
* Installing Angular CLI
* Creating your first Angular project
* Running the Angular application
* Understanding every generated file
* Angular startup process
* Angular Standalone Architecture

---

# Before Installing Angular

Remember:

Angular applications are written using

* HTML
* CSS
* TypeScript

But browsers only understand

* HTML
* CSS
* JavaScript

Therefore Angular must

* Compile TypeScript
* Bundle JavaScript
* Optimize CSS
* Start a Development Server

Node.js provides the runtime environment for these build tools.

---

# Angular Development Architecture

```text
Developer

↓

TypeScript

↓

Angular CLI

↓

Build

↓

JavaScript

↓

Browser
```

---

# Step 1: Install Node.js

Download the latest **LTS** version.

Verify installation.

```bash
node -v
```

Example

```text
v24.4.0
```

Verify npm

```bash
npm -v
```

Example

```text
11.4.2
```

---

# What is Node.js?

Node.js allows JavaScript to run **outside the browser**.

Angular itself does **not** run on Node.js.

Instead,

Node.js runs

* Angular CLI
* TypeScript Compiler
* Development Server
* Build Tools

After the application is built, Angular runs inside the browser.

---

# What is npm?

npm means

**Node Package Manager**

It downloads JavaScript libraries.

Example

```bash
npm install bootstrap
```

downloads Bootstrap.

Similarly

```bash
npm install @angular/cli
```

downloads Angular CLI.

---

# What is npx?

Suppose Angular CLI is not installed globally.

Instead of

```bash
npm install -g @angular/cli
```

you can run

```bash
npx @angular/cli new EmployeeManagement.UI
```

npx

* Downloads temporarily
* Executes
* Removes temporary files

---

# npm vs npx

| npm                | npx                 |
| ------------------ | ------------------- |
| Installs package   | Executes package    |
| Stored permanently | Temporary execution |
| `npm install`      | `npx package`       |

---

# What is Angular CLI?

CLI means

**Command Line Interface**

Instead of manually creating files,

Angular CLI generates them automatically.

Similar to Visual Studio scaffolding.

Examples

Create project

```bash
ng new EmployeeManagement.UI
```

Create component

```bash
ng generate component employee
```

or

```bash
ng g c employee
```

Create service

```bash
ng g s employee
```

---

# Install Angular CLI

```bash
npm install -g @angular/cli
```

Verify

```bash
ng version
```

Example

```text
Angular CLI: 20.x.x

Node: 24.x.x

npm: 11.x.x
```

---

# Create Your First Angular Project

```bash
ng new EmployeeManagement.UI
```

Angular asks a few questions.

---

## Question 1

```text
Would you like to create a zoneless application?
```

Choose

```text
No
```

Reason

Most enterprise applications still use Zone.js.

We'll learn Zoneless Angular later.

---

## Question 2

```text
Which stylesheet format would you like to use?
```

Choose

```text
CSS
```

We'll learn SCSS later.

---

Angular now creates the project.

---

# Project Structure

```text
EmployeeManagement.UI

│

├── src

├── public

├── node_modules

├── angular.json

├── package.json

├── tsconfig.json

└── README.md
```

---

# node_modules

Contains

* Angular
* TypeScript
* RxJS
* Zone.js
* Vite
* Every installed package

Never edit this folder.

---

# package.json

Very important.

Example

```json
{
  "dependencies":
  {
      "@angular/core":"...",
      "@angular/router":"...",
      "rxjs":"..."
  },

  "scripts":
  {
      "start":"...",
      "build":"..."
  }
}
```

Think of it like a **.NET project file** (`.csproj`), but for JavaScript dependencies and project commands.

---

# angular.json

Angular project configuration.

Contains

* Build Configuration
* Assets
* Global Styles
* Scripts
* Output Folder

Similar to project configuration in .NET.

---

# tsconfig.json

You already know this file.

It configures the TypeScript compiler.

Contains

* Strict Mode
* Target Version
* Module Configuration

---

# src Folder

This is where almost all development happens.

```text
src

│

├── app

├── public

├── styles.css

├── index.html

└── main.ts
```

---

# app Folder

Modern Angular generates

```text
app

│

├── app.ts

├── app.html

├── app.css

├── app.config.ts

├── app.routes.ts

└── app.spec.ts
```

Notice

There is **no**

```text
app.module.ts
```

This is because Angular now uses **Standalone Components**.

---

# Understanding Every File

---

## main.ts

This is Angular's entry point.

Exactly like

```text
Program.cs
```

in ASP.NET Core.

Example

```typescript
import { bootstrapApplication } from '@angular/platform-browser';

import { appConfig } from './app/app.config';

import { App } from './app/app';

bootstrapApplication(App, appConfig)
.catch(err => console.error(err));
```

Notice

```typescript
bootstrapApplication()
```

starts Angular.

---

## app.ts

This is the

**Root Component**

Example

```typescript
export class App
{

}
```

Think of it as

The first screen of your application.

---

## app.html

The HTML of the Root Component.

Example

```html
<h1>Welcome to Angular</h1>
```

Whatever you place here appears first.

---

## app.css

CSS for the Root Component.

Example

```css
h1
{
    color:blue;
}
```

---

## app.config.ts

Application configuration.

Example

```typescript
export const appConfig =
{
    providers:
    [

    ]
};
```

Later

We'll register

* Services
* Routing
* HTTP Client

here.

Think of it like

```csharp
builder.Services
```

in ASP.NET Core.

---

## app.routes.ts

One of the most important files.

Contains

Every URL

of your application.

Example

```typescript
import { Routes } from '@angular/router';

export const routes: Routes =
[
];
```

Later

You'll have

```typescript
export const routes: Routes =
[
    {
        path:"",
        component:HomeComponent
    },

    {
        path:"employees",
        component:EmployeeComponent
    }
];
```

Exactly like ASP.NET Core routing.

---

## app.spec.ts

Contains Unit Tests.

We won't use it immediately.

---

# Angular Startup Process

This is extremely important.

```text
Browser

↓

main.ts

↓

bootstrapApplication()

↓

app.config.ts

↓

app.routes.ts

↓

App Component

↓

app.html

↓

Browser Displays UI
```

This is how Angular starts.

---

# Running the Application

Move into the project.

```bash
cd EmployeeManagement.UI
```

Run

```bash
ng serve
```

Output

```text
Application bundle generation complete.

http://localhost:4200
```

Open

```text
http://localhost:4200
```

Congratulations!

Your Angular application is running.

---

# What Happens Internally?

```text
ng serve

↓

Compile TypeScript

↓

Bundle Files

↓

Start Development Server

↓

Browser Opens

↓

Angular Loads
```

---

# Live Reload

Modify

```text
app.html
```

Save.

Angular automatically

```text
Compile

↓

Refresh Browser

↓

Display Changes
```

No restart required.

---

# Build for Production

Development

```bash
ng serve
```

Production

```bash
ng build
```

Production build

* Smaller files
* Faster loading
* Optimized JavaScript
* Minified CSS

---

# Angular + ASP.NET Core

Later

We'll build

```text
Browser

↓

Angular

↓

HTTP

↓

ASP.NET Core API

↓

SQL Server
```

Exactly how enterprise applications work.

---

# Modern Angular vs Old Angular

| Angular 20               | Angular 15 and Earlier  |
| ------------------------ | ----------------------- |
| Standalone Components    | NgModules               |
| `app.ts`                 | `app.component.ts`      |
| `app.config.ts`          | `app.module.ts`         |
| `app.routes.ts`          | `app-routing.module.ts` |
| `bootstrapApplication()` | `bootstrapModule()`     |

You'll still see the older structure in existing enterprise projects, so it's useful to recognize both.

---

# Summary

| File            | Purpose                   |
| --------------- | ------------------------- |
| `main.ts`       | Starts Angular            |
| `app.ts`        | Root Component            |
| `app.html`      | Root HTML                 |
| `app.css`       | Root CSS                  |
| `app.config.ts` | Application Configuration |
| `app.routes.ts` | Routing                   |
| `package.json`  | Dependencies & Scripts    |
| `angular.json`  | Build Configuration       |
| `tsconfig.json` | TypeScript Configuration  |

---

# Practice Exercises

### Exercise 1

Install:

* Node.js
* Angular CLI

Verify

```bash
node -v

npm -v

ng version
```

---

### Exercise 2

Create

```text
EmployeeManagement.UI
```

Run it successfully.

---

### Exercise 3

Open these files and explain their purpose:

* `main.ts`
* `app.ts`
* `app.config.ts`
* `app.routes.ts`
* `package.json`

---

### Exercise 4

Modify

```text
app.html
```

Change

```html
<h1>Welcome to Angular</h1>
```

to

```html
<h1>Welcome Raman to Angular</h1>
```

Save the file and observe Live Reload.

---

# Interview Questions

1. Why does Angular require Node.js?
2. What is Angular CLI?
3. What is the difference between npm and npx?
4. What is the purpose of `main.ts`?
5. What is the purpose of `app.config.ts`?
6. What is the purpose of `app.routes.ts`?
7. What is the difference between `ng serve` and `ng build`?
8. What are Standalone Components, and how do they differ from the older NgModule-based approach?

---

## Next Lesson

We'll go much deeper into the **Angular Project Structure**.

We'll trace the startup sequence step by step:

```text
Browser
    ↓
index.html
    ↓
main.ts
    ↓
bootstrapApplication()
    ↓
app.config.ts
    ↓
App Component
    ↓
app.html
```

By the end of that lesson, you'll understand **how Angular boots the application internally**, just like you understand the request pipeline in ASP.NET Core. That mental model will make every later Angular topic much easier.
