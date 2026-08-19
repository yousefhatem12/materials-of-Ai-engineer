# TypeScript Basics for Node.js 🚀

A practical Arabic reference for learning the TypeScript concepts needed to build Node.js backend applications.

> هذا الدليل يحوّل أساسيات JavaScript اللازمة لـ Node.js إلى نسخة عملية باستخدام TypeScript، مع إضافة أهم مفاهيم TypeScript مثل Types, Interfaces, Generics, Utility Types و Type Narrowing.

---

## 📚 Contents

1. [Variables](#1-variables)
2. [Data Types](#2-data-types)
3. [Type Annotations & Inference](#3-type-annotations--inference)
4. [Union & Literal Types](#4-union--literal-types)
5. [Objects & Interfaces](#5-objects--interfaces)
6. [Arrays & Tuples](#6-arrays--tuples)
7. [Functions](#7-functions)
8. [Destructuring & Spread](#8-destructuring--spread)
9. [Conditionals & Type Narrowing](#9-conditionals--type-narrowing)
10. [Loops & Array Methods](#10-loops--array-methods)
11. [Classes](#11-classes)
12. [Enums](#12-enums)
13. [Generics](#13-generics)
14. [Utility Types](#14-utility-types)
15. [Error Handling](#15-error-handling)
16. [Async/Await & Promises](#16-asyncawait--promises)
17. [Modules](#17-modules)
18. [Type Aliases](#18-type-aliases)
19. [Optional Properties & Optional Chaining](#19-optional-properties--optional-chaining)
20. [Node.js Environment](#20-nodejs-environment)
21. [tsconfig.json](#21-tsconfigjson)
22. [What to Master Before Node.js + TypeScript](#22-what-to-master-before-nodejs--typescript)
23. [Next Step](#23-next-step)

---

## 1. Variables

```typescript
const name: string = "Ahmed";
let age: number = 25;

age = 26;

// age = "26"; // ❌ Type error
```

### Rule

- Use `const` by default.
- Use `let` when the value changes.
- Avoid `var` in modern projects.

---

## 2. Data Types

### Primitive Types

```typescript
let name: string = "Ahmed";
let age: number = 25;
let active: boolean = true;

let nothing: null = null;
let notDefined: undefined = undefined;

let big: bigint = 123n;
let id: symbol = Symbol("id");
```

### Special Types

```typescript
let value: any = "hello";
let unknownValue: unknown = "hello";

function log(): void {
  console.log("Hello");
}

function fail(): never {
  throw new Error("Failed");
}
```

> Prefer `unknown` over `any` when the type is not known.

---

## 3. Type Annotations & Inference

```typescript
const username: string = "Omar";
const age: number = 30;
const isAdmin: boolean = true;
```

TypeScript can infer many types automatically:

```typescript
const name = "Sara"; // string
let age = 22;        // number
```

Functions should usually have typed parameters and return values:

```typescript
function add(a: number, b: number): number {
  return a + b;
}
```

---

## 4. Union & Literal Types

### Union

```typescript
let id: string | number;

id = 10;
id = "ABC";
```

### Literal Types

```typescript
let role: "admin" | "user" | "manager";

role = "admin";

// role = "guest"; // ❌ Error
```

Useful for roles, statuses, configuration values, and API states.

---

## 5. Objects & Interfaces

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  active: boolean;
}

const user: User = {
  id: 1,
  name: "Omar",
  email: "omar@example.com",
  active: true
};
```

### Optional Properties

```typescript
interface User {
  id: number;
  name: string;
  phone?: string;
}
```

Interfaces are especially useful for defining domain models and API data structures.

---

## 6. Arrays & Tuples

### Arrays

```typescript
const numbers: number[] = [1, 2, 3, 4];

const names: string[] = [
  "Ahmed",
  "Omar",
  "Sara"
];
```

Alternative:

```typescript
const numbers: Array<number> = [1, 2, 3];
```

### Readonly Arrays

```typescript
const numbers: readonly number[] = [1, 2, 3];

// numbers.push(4); // ❌ Error
```

### Tuples

```typescript
const user: [number, string] = [1, "Ahmed"];
```

---

## 7. Functions

### Basic Function

```typescript
function add(a: number, b: number): number {
  return a + b;
}
```

### Arrow Function

```typescript
const multiply = (a: number, b: number): number => {
  return a * b;
};
```

### Optional Parameter

```typescript
function greet(name?: string): string {
  return `Hello ${name ?? "Guest"}`;
}
```

### Default Parameter

```typescript
function greetUser(name: string = "Guest"): void {
  console.log(`Hello ${name}`);
}
```

### Rest Parameters

```typescript
function sum(...numbers: number[]): number {
  return numbers.reduce((acc, n) => acc + n, 0);
}
```

### Function Type

```typescript
type MathOperation = (a: number, b: number) => number;

const subtract: MathOperation = (a, b) => a - b;
```

---

## 8. Destructuring & Spread

```typescript
interface User {
  name: string;
  age: number;
}

const user: User = {
  name: "Omar",
  age: 30
};

const { name, age } = user;
```

### Array Destructuring

```typescript
const numbers: number[] = [1, 2, 3];

const [first, second, ...rest] = numbers;
```

### Object Spread

```typescript
const updatedUser: User = {
  ...user,
  age: 31
};
```

---

## 9. Conditionals & Type Narrowing

TypeScript can narrow union types after runtime checks.

```typescript
function printId(id: string | number): void {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id.toFixed(0));
  }
}
```

### `instanceof`

```typescript
if (error instanceof Error) {
  console.log(error.message);
}
```

Type narrowing is one of the most important TypeScript concepts for writing safe code.

---

## 10. Loops & Array Methods

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];

for (const number of numbers) {
  console.log(number);
}
```

### map

```typescript
const doubled = numbers.map(n => n * 2);
```

### filter

```typescript
const evenNumbers = numbers.filter(n => n % 2 === 0);
```

### reduce

```typescript
const total = numbers.reduce((acc, n) => acc + n, 0);
```

### find

```typescript
const result = numbers.find(n => n > 3);
// number | undefined
```

---

## 11. Classes

```typescript
class Animal {
  constructor(public name: string) {}

  speak(): void {
    console.log(`${this.name} makes a sound`);
  }
}

class Dog extends Animal {
  speak(): void {
    console.log(`${this.name} barks`);
  }
}

const dog = new Dog("Rex");

dog.speak();
```

### Access Modifiers

```typescript
class User {
  public name: string;
  private password: string;
  protected role: string;

  constructor(
    name: string,
    password: string,
    role: string
  ) {
    this.name = name;
    this.password = password;
    this.role = role;
  }
}
```

Common modifiers:

- `public`
- `private`
- `protected`
- `readonly`

---

## 12. Enums

```typescript
enum Role {
  ADMIN = "admin",
  USER = "user",
  MANAGER = "manager"
}

const role: Role = Role.ADMIN;
```

You can also use union literal types when an enum is unnecessary:

```typescript
type Role = "admin" | "user" | "manager";
```

---

## 13. Generics

Generics make reusable code type-safe.

```typescript
function identity<T>(value: T): T {
  return value;
}

const numberValue = identity(10);
const stringValue = identity("Hello");
```

### Generic Array Function

```typescript
function getFirst<T>(items: T[]): T | undefined {
  return items[0];
}
```

### Generic API Response

```typescript
interface ApiResponse<T> {
  success: boolean;
  data: T;
  message?: string;
}

interface User {
  id: number;
  name: string;
}

const response: ApiResponse<User> = {
  success: true,
  data: {
    id: 1,
    name: "Ahmed"
  }
};
```

Generics are very useful for services, repositories, API responses, and reusable utilities.

---

## 14. Utility Types

### Partial

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

const updateUser: Partial<User> = {
  name: "Omar"
};
```

### Pick

```typescript
type UserPreview = Pick<User, "id" | "name">;
```

### Omit

```typescript
type CreateUser = Omit<User, "id">;
```

### Readonly

```typescript
type ReadonlyUser = Readonly<User>;
```

These are especially useful for DTOs and update/create operations.

---

## 15. Error Handling

```typescript
try {
  const data = JSON.parse("{invalid json}");
} catch (error: unknown) {
  if (error instanceof Error) {
    console.error(error.message);
  }
}
```

### Custom Error

```typescript
class ValidationError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "ValidationError";
  }
}

throw new ValidationError("Invalid user data");
```

---

## 16. Async/Await & Promises

Asynchronous JavaScript is one of the most important parts of Node.js.

### Promise

```typescript
function fetchData(): Promise<string> {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve("Data loaded");
    }, 1000);
  });
}
```

### Async/Await

```typescript
async function getData(): Promise<void> {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (error: unknown) {
    console.error(error);
  }
}
```

### Typed API Result

```typescript
interface User {
  id: number;
  name: string;
}

async function getUser(): Promise<User> {
  return {
    id: 1,
    name: "Ahmed"
  };
}
```

---

## 17. Modules

TypeScript commonly uses ES Modules.

### Export

```typescript
// math.ts

export function add(a: number, b: number): number {
  return a + b;
}

export const PI: number = 3.14;
```

### Import

```typescript
// app.ts

import { add, PI } from "./math.js";

console.log(add(2, 3));
console.log(PI);
```

### Default Export

```typescript
// math.ts

export default function add(a: number, b: number): number {
  return a + b;
}
```

```typescript
// app.ts

import add from "./math.js";
```

> Module behavior depends on your Node.js and TypeScript configuration.

---

## 18. Type Aliases

```typescript
type ID = string | number;

let userId: ID = 10;

userId = "ABC";
```

### Object Type

```typescript
type User = {
  id: number;
  name: string;
  email: string;
};
```

### Intersection

```typescript
type Admin = User & {
  permissions: string[];
};
```

---

## 19. Optional Properties & Optional Chaining

```typescript
interface User {
  name: string;
  phone?: string;
}

const user: User = {
  name: "Ahmed"
};

console.log(user.phone?.toString());
```

### Nullish Coalescing

```typescript
const port = Number(process.env.PORT ?? 3000);
```

`??` uses the fallback when the value is `null` or `undefined`.

---

## 20. Node.js Environment

TypeScript can use Node.js APIs such as:

- `process`
- `fs`
- `http`
- `path`
- `url`
- `crypto`

Example:

```typescript
import { readFile } from "node:fs/promises";

async function readData(): Promise<void> {
  try {
    const content = await readFile("file.txt", "utf-8");
    console.log(content);
  } catch (error: unknown) {
    console.error(error);
  }
}
```

Install Node.js types:

```bash
npm install -D @types/node
```

Environment variables:

```typescript
const port = Number(process.env.PORT ?? 3000);
```

---

## 21. tsconfig.json

`tsconfig.json` controls how TypeScript compiles your project.

Example:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules", "dist"]
}
```

### Important Options

| Option | Purpose |
|---|---|
| `target` | JavaScript version generated |
| `module` | Module system |
| `rootDir` | Source directory |
| `outDir` | Compiled output |
| `strict` | Strict type checking |
| `esModuleInterop` | CommonJS compatibility |
| `skipLibCheck` | Skip declaration-file checking |

> Keep `"strict": true` for new projects whenever possible.

---

## 22. What to Master Before Node.js + TypeScript

| Priority | Topic |
|---|---|
| 🔴 Essential | Variables, Data Types, Functions |
| 🔴 Essential | Objects, Arrays, Destructuring |
| 🔴 Essential | Callbacks, Promises, Async/Await |
| 🔴 Essential | Modules |
| 🔴 Essential | Type Annotations & Type Inference |
| 🔴 Essential | Interfaces & Type Aliases |
| 🔴 Essential | Union Types & Type Narrowing |
| 🟡 Important | Generics |
| 🟡 Important | Utility Types |
| 🟡 Important | Classes |
| 🟡 Important | Error Handling |
| 🟡 Important | `tsconfig.json` |
| 🟢 Good to Know | Enums |
| 🟢 Good to Know | Advanced TypeScript |

---

## 🎯 Recommended Learning Order

```text
JavaScript Fundamentals
        ↓
Variables & Data Types
        ↓
Functions & Arrow Functions
        ↓
Objects & Arrays
        ↓
Destructuring & Spread/Rest
        ↓
Callbacks
        ↓
Promises
        ↓
Async/Await
        ↓
TypeScript Basics
        ↓
Type Annotations
        ↓
Interfaces & Type Aliases
        ↓
Union Types & Narrowing
        ↓
Generics
        ↓
Utility Types
        ↓
Classes
        ↓
Modules
        ↓
tsconfig.json
        ↓
Node.js + TypeScript
        ↓
Express / Fastify / NestJS
        ↓
Database
        ↓
Authentication
        ↓
REST APIs
```

---

## 23. Next Step

Create a basic Node.js + TypeScript project.

### 1. Create Project

```bash
mkdir node-typescript-app
cd node-typescript-app
npm init -y
```

### 2. Install TypeScript

```bash
npm install -D typescript tsx @types/node
```

### 3. Create tsconfig

```bash
npx tsc --init
```

### 4. Project Structure

```text
node-typescript-app/
│
├── src/
│   └── index.ts
│
├── package.json
├── tsconfig.json
└── README.md
```

### 5. Basic HTTP Server

```typescript
import http from "node:http";

const port: number = Number(
  process.env.PORT ?? 3000
);

const server = http.createServer((req, res) => {
  res.writeHead(200, {
    "Content-Type": "text/plain"
  });

  res.end("Hello from Node.js + TypeScript!");
});

server.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

### 6. Run

```bash
npx tsx src/index.ts
```

---

## 🧠 Final Checklist

- [ ] `const` and `let`
- [ ] Primitive and reference types
- [ ] Functions and arrow functions
- [ ] Objects and arrays
- [ ] `map`, `filter`, `reduce`, `find`
- [ ] Destructuring
- [ ] Spread and rest
- [ ] Interfaces
- [ ] Type aliases
- [ ] Union types
- [ ] Type narrowing
- [ ] Optional properties
- [ ] Generics
- [ ] Utility types
- [ ] Classes
- [ ] Promises
- [ ] Async/Await
- [ ] Error handling
- [ ] ES Modules
- [ ] `tsconfig.json`
- [ ] Basic Node.js APIs

---

## 📌 Goal

The goal is not to memorize every TypeScript feature.

The goal is to write **type-safe, readable, maintainable Node.js backend code** and understand the TypeScript types used throughout your project.
