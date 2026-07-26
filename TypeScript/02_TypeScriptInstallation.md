Great! Before we start Angular, we'll learn TypeScript exactly as if we're learning C# first before ASP.NET Core.

---

# Lesson 2: Installing TypeScript and Your First Program

## Learning Objectives

By the end of this lesson, you will understand:

* What Node.js is
* What npm is
* What the TypeScript compiler (tsc) is
* How `.ts` files become `.js` files
* How to write and run your first TypeScript program

---

# Step 1: Why Do We Need Node.js?

You might ask:

> If TypeScript compiles to JavaScript, why do we need Node.js?

Good question.

The TypeScript compiler (`tsc`) is a **Node.js package**. It runs using the Node.js runtime.

```
Your Computer

      │
      ▼

Node.js Runtime

      │
      ▼

TypeScript Compiler (tsc)

      │
      ▼

JavaScript File
```

Without Node.js, you cannot install or run the TypeScript compiler.

---

# Step 2: Install Node.js

Download and install the **LTS (Long-Term Support)** version of Node.js.

After installation, open a terminal (Command Prompt, PowerShell, or VS Code Terminal) and verify the installation:

```bash
node -v
```

Example output:

```text
v22.18.0
```

Check npm:

```bash
npm -v
```

Example:

```text
10.9.3
```

> **Note:** Your version numbers may differ. That's perfectly fine.

---

# What is npm?

**npm** stands for:

> **Node Package Manager**

It is used to install JavaScript and TypeScript libraries.

Examples:

Install Angular CLI

```bash
npm install -g @angular/cli
```

Install TypeScript

```bash
npm install -g typescript
```

Install React

```bash
npm install react
```

Think of npm like **NuGet** in the .NET world.

| .NET             | JavaScript       |
| ---------------- | ---------------- |
| NuGet            | npm              |
| Package Manager  | Package Manager  |
| Install Packages | Install Packages |

---

# Step 3: Install TypeScript

Install globally:

```bash
npm install -g typescript
```

Verify installation:

```bash
tsc -v
```

Example:

```text
Version 5.9.2
```

Now your computer knows how to compile TypeScript.

---

# Step 4: Create Your First Project

Create a folder:

```
TypeScriptDemo
```

Open it in Visual Studio Code.

Create a file:

```
app.ts
```

Notice the extension:

```
.ts
```

This indicates a TypeScript file.

---

# Step 5: Write Your First TypeScript Program

Open `app.ts`.

```typescript
console.log("Hello TypeScript");
```

This looks exactly like JavaScript.

Remember:

> Every JavaScript program is also valid TypeScript.

---

# Step 6: Compile the Program

Open the terminal.

Run:

```bash
tsc app.ts
```

A new file is created:

```
app.js
```

Your folder now looks like this:

```
TypeScriptDemo

│

├── app.ts

└── app.js
```

Open `app.js`.

```javascript
console.log("Hello TypeScript");
```

Notice that the generated JavaScript looks the same because there were no TypeScript-specific features in the original code.

---

# Step 7: Run the JavaScript File

TypeScript **cannot** be run directly by the browser or Node.js.

Instead, run the generated JavaScript:

```bash
node app.js
```

Output:

```text
Hello TypeScript
```

---

# What Actually Happened?

Let's follow the complete process.

```
You Write

app.ts

console.log("Hello");

          │

          ▼

TypeScript Compiler (tsc)

          │

          ▼

Generates

app.js

console.log("Hello");

          │

          ▼

Node.js

          │

          ▼

Console Output

Hello
```

This is the workflow you'll use throughout Angular development.

---

# Why Doesn't the Browser Read `.ts` Files?

Browsers understand only JavaScript.

If you try to include a `.ts` file directly in HTML:

```html
<script src="app.ts"></script>
```

The browser will not execute it.

Instead, include the compiled JavaScript:

```html
<script src="app.js"></script>
```

Angular automatically handles this compilation for you, so you typically won't invoke `tsc` manually when working on Angular projects.

---

# Let's Add a Type

Modify `app.ts`:

```typescript
let name: string = "Raman";

console.log(name);
```

Compile:

```bash
tsc app.ts
```

Generated JavaScript:

```javascript
let name = "Raman";
console.log(name);
```

Notice:

The type information disappears.

Why?

Because JavaScript has no type annotations.

TypeScript uses the type only during compilation to validate your code.

```
TypeScript

let name:string="Raman";

         │

         ▼

JavaScript

let name="Raman";
```

This is why TypeScript is often described as a **development-time language**—its type information helps you while coding but isn't included in the JavaScript sent to the browser.

---

# Compile-Time Error Example

```typescript
let age: number = 25;

age = "Thirty";
```

Compile:

```bash
tsc app.ts
```

Output:

```text
Type 'string' is not assignable to type 'number'
```

No JavaScript is generated until the error is resolved (depending on your compiler settings).

This is one of the biggest advantages of TypeScript.

---

# Real-World Analogy

Imagine writing a book.

```
Draft (TypeScript)

↓

Editor checks grammar and spelling (TypeScript Compiler)

↓

Final printed book (JavaScript)

↓

Readers (Browser)
```

The editor catches mistakes before the book is published.

Similarly, the TypeScript compiler catches coding mistakes before the application runs.

---

# Summary

* **Node.js** provides the runtime needed to execute tools like the TypeScript compiler.
* **npm** is the package manager, similar to NuGet in .NET.
* **TypeScript (`.ts`)** is compiled into **JavaScript (`.js`)** using the `tsc` compiler.
* Browsers execute only JavaScript, not TypeScript.
* Type annotations exist only during development and are removed from the generated JavaScript.
* The compiler helps detect many errors before the application runs.

---

# Practice Exercises

1. Install Node.js (if not already installed).
2. Install TypeScript globally:

```bash
npm install -g typescript
```

3. Create a folder named `TypeScriptDemo`.
4. Create `app.ts`.
5. Write:

```typescript
let course: string = "Angular";
console.log(course);
```

6. Compile it:

```bash
tsc app.ts
```

7. Run it:

```bash
node app.js
```

8. Change the code to intentionally cause a type error:

```typescript
let price: number = 100;

price = "Hundred";
```

Observe the compiler error and then fix it.

---
