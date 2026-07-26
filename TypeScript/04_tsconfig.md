This is actually the **perfect time** to learn about `tsconfig.json`. Every Angular project has one, and understanding it now will make Angular much easier.

---

# What is `tsconfig.json`?

`tsconfig.json` is the **configuration file for the TypeScript compiler (`tsc`)**.

Think of it as a **project settings file**.

Just like in .NET you have:

```text
appsettings.json
```

to configure your application,

TypeScript has:

```text
tsconfig.json
```

to configure the TypeScript compiler.

---

# Without tsconfig.json

Every time you compile, you have to specify files manually.

```bash
tsc app.ts
```

or

```bash
tsc student.ts
```

The compiler doesn't know anything about your project.

---

# With tsconfig.json

Simply type

```bash
tsc
```

The compiler automatically reads

```text
tsconfig.json
```

and knows:

* Which files to compile
* Which JavaScript version to generate
* Which module system to use
* Whether strict checking is enabled
* Where to put generated files
* Hundreds of other settings

---

# Think of it like this

```text
             tsconfig.json

                    │

                    ▼

           TypeScript Compiler

                    │

        Reads all settings

                    │

                    ▼

            Generates JavaScript
```

---

# How is it used?

Suppose your project is

```text
TypeScriptDemo
│
├── app.ts
├── student.ts
├── employee.ts
└── tsconfig.json
```

Compile

```bash
tsc
```

The compiler first reads

```text
tsconfig.json
```

Then compiles all `.ts` files according to the settings.

---

# Let's understand your file

Your generated file contains many options. We'll cover only the important ones first.

---

# compilerOptions

Everything inside

```json
{
  "compilerOptions":
  {

  }
}
```

controls how TypeScript compiles your project.

---

# target

```json
"target": "esnext"
```

This tells TypeScript:

> Which JavaScript version should be generated?

For example

TypeScript

```typescript
let name = "Raman";
```

If target is

```json
"target":"ES5"
```

Generated JavaScript may look like

```javascript
var name = "Raman";
```

because ES5 didn't support `let`.

---

If target is

```json
"target":"ES2020"
```

Generated JavaScript

```javascript
let name = "Raman";
```

Modern syntax is preserved.

---

Common targets

```text
ES5

ES2015

ES2017

ES2020

ES2022

ESNext
```

Angular nowadays generally targets modern JavaScript, so `es2022` or `esnext` is common.

---

# module

Your file says

```json
"module":"nodenext"
```

This tells TypeScript:

> Which module system should be generated?

Examples

Node.js

```json
"module":"NodeNext"
```

Browser

```json
"module":"ESNext"
```

Old JavaScript

```json
"module":"CommonJS"
```

Angular uses ES modules.

---

# strict

```json
"strict":true
```

One of the most important settings.

Suppose

```typescript
let age:number = 25;

age = "Twenty";
```

Compiler

❌ Error

Strict mode catches many mistakes.

If

```json
"strict":false
```

TypeScript becomes much more permissive.

**Recommendation:** Always keep it `true`.

---

# sourceMap

```json
"sourceMap":true
```

Suppose

```typescript
app.ts
```

becomes

```javascript
app.js
```

TypeScript also creates

```text
app.js.map
```

This file allows the browser debugger to map the running JavaScript back to your original TypeScript source.

---

# declaration

```json
"declaration":true
```

Generates

```text
app.d.ts
```

This contains only type information.

Example

TypeScript

```typescript
export function add(a:number,b:number):number
```

Generated

```typescript
export declare function add(a:number,b:number):number;
```

Mostly useful when creating reusable libraries.

Angular application projects usually keep this disabled.

---

# declarationMap

```json
"declarationMap":true
```

Creates

```text
app.d.ts.map
```

Used by tools and library developers.

Not important for learning Angular.

---

# skipLibCheck

```json
"skipLibCheck":true
```

Suppose you install

```text
Angular

RxJS

Express

Lodash
```

Each contains `.d.ts` files.

Without

```json
skipLibCheck:false
```

TypeScript checks every declaration file.

Compilation becomes slower.

Most projects keep

```json
true
```

---

# moduleDetection

```json
"moduleDetection":"force"
```

This is very interesting.

It tells TypeScript

> Treat every `.ts` file as a module.

Remember your earlier problem?

```
Cannot redeclare block-scoped variable
```

This option helps avoid that by preventing files from being treated as global scripts.

---

# noUncheckedIndexedAccess

```json
"noUncheckedIndexedAccess":true
```

Suppose

```typescript
let numbers = [10,20];
```

Access

```typescript
numbers[10]
```

Normally

```text
undefined
```

With this option enabled, TypeScript reminds you that the value might not exist, making array access safer.

---

# exactOptionalPropertyTypes

```json
"exactOptionalPropertyTypes":true
```

Suppose

```typescript
interface Employee
{
    name?:string;
}
```

The `?` means the property is optional.

This setting makes TypeScript treat optional properties more precisely, which helps catch subtle bugs.

We'll revisit this when we study interfaces.

---

# jsx

```json
"jsx":"react-jsx"
```

This option is **only for React**.

You can ignore it while learning TypeScript and Angular.

Angular doesn't use JSX.

---

# isolatedModules

```json
"isolatedModules":true
```

Ensures each file can be compiled independently.

Angular projects often enable similar settings because build tools compile files individually.

---

# verbatimModuleSyntax

```json
"verbatimModuleSyntax":true
```

Controls how `import` and `export` statements are preserved in the generated JavaScript.

This is an advanced setting and not something you need to worry about initially.

---

# What do Angular projects use?

If you create a new Angular application:

```bash
ng new AngularDemo
```

Angular automatically creates a `tsconfig.json`.

It contains settings optimized for Angular development, such as:

* ES modules
* Strict type checking
* Modern JavaScript target
* Angular compiler configuration

You rarely edit it manually unless you need to change compiler behavior.

---

# A Simpler `tsconfig.json` for Learning

For our TypeScript lessons, we don't need all those advanced options.

A minimal configuration is enough:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "strict": true,
    "sourceMap": true,
    "outDir": "./dist"
  }
}
```

Here:

* `target` → Generate modern JavaScript.
* `module` → Use ES modules.
* `strict` → Enable strong type checking.
* `sourceMap` → Allow debugging TypeScript.
* `outDir` → Put generated `.js` files into a `dist` folder.

---

# Where Does `tsconfig.json` Belong?

It should be in the root of your project:

```text
TypeScriptDemo
│
├── app.ts
├── tsconfig.json
└── dist
      app.js
```

Now, simply run:

```bash
tsc
```

The compiler automatically reads `tsconfig.json`, compiles the project, and places the output in `dist`.

---

# Summary

| Option            | Purpose                                 |
| ----------------- | --------------------------------------- |
| `target`          | JavaScript version to generate          |
| `module`          | Module system to use                    |
| `strict`          | Enable strict type checking             |
| `sourceMap`       | Generate source maps for debugging      |
| `outDir`          | Output folder for compiled JavaScript   |
| `skipLibCheck`    | Skip checking library declaration files |
| `moduleDetection` | Decide how files are treated as modules |
| `declaration`     | Generate `.d.ts` type declaration files |
| `declarationMap`  | Generate maps for declaration files     |

## Before the next lesson

I recommend changing your `tsconfig.json` to the simplified version above. It's easier to understand while learning. When we move to Angular, we'll compare it with the `tsconfig.json` that Angular CLI generates and explain why Angular adds additional options.
