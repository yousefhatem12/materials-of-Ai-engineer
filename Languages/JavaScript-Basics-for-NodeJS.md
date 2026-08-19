# أساسيات JavaScript اللازمة لتعلم Node.js

دليل مرجعي يجمع كل المفاهيم الأساسية في JavaScript التي تحتاجها قبل أو خلال تعلم Node.js.

---

## 1. المتغيرات (Variables)

```javascript
var x = 10;     // قديم، نادراً يُستخدم الآن (function-scoped)
let y = 20;     // يمكن تغيير قيمته (block-scoped)
const z = 30;   // ثابت، لا يمكن تغيير قيمته (block-scoped)

// const لا تمنع تعديل محتوى object أو array
const arr = [1, 2, 3];
arr.push(4); // مسموح
// arr = [5,6]; // ❌ خطأ
```

**القاعدة:** استخدم `const` افتراضياً، و `let` فقط لو فعلاً ستغيّر القيمة. تجنب `var`.

---

## 2. أنواع البيانات (Data Types)

```javascript
// Primitive types
let str = "Ahmed";        // String
let num = 25;              // Number
let isTrue = true;         // Boolean
let nothing = null;        // Null
let notDefined;             // Undefined
let big = 123n;             // BigInt
let sym = Symbol("id");     // Symbol

// Reference types
let obj = { name: "Ali" };  // Object
let list = [1, 2, 3];       // Array
let func = function() {};   // Function

// معرفة النوع
typeof num;   // "number"
typeof obj;   // "object"
```

---

## 3. العمليات الحسابية والمقارنة

```javascript
// حسابية
10 + 5; 10 - 5; 10 * 5; 10 / 5; 10 % 3; 2 ** 3; // قوة

// مقارنة - استخدم === دايماً بدل ==
5 === "5";  // false (يقارن القيمة والنوع)
5 == "5";   // true  (يقارن القيمة فقط - تجنبه)
5 !== "5";  // true

// منطقية
true && false;  // AND
true || false;  // OR
!true;          // NOT

// Optional chaining & Nullish coalescing (مهم جداً في Node.js)
const user = { profile: { name: "Sara" } };
user.profile?.age;        // undefined (بدون خطأ لو غير موجود)
let port = process.env.PORT ?? 3000; // لو undefined/null استخدم 3000
```

---

## 4. الشروط (Conditionals)

```javascript
if (age >= 18) {
  console.log("بالغ");
} else if (age >= 13) {
  console.log("شاب");
} else {
  console.log("طفل");
}

// Switch
switch (role) {
  case "admin":
    console.log("مدير");
    break;
  default:
    console.log("مستخدم عادي");
}

// Ternary
const status = age >= 18 ? "بالغ" : "قاصر";
```

---

## 5. الحلقات (Loops)

```javascript
for (let i = 0; i < 5; i++) { console.log(i); }

let i = 0;
while (i < 5) { console.log(i); i++; }

const arr = [1, 2, 3];
for (const item of arr) { console.log(item); }      // للقيم

const obj = { a: 1, b: 2 };
for (const key in obj) { console.log(key, obj[key]); } // للمفاتيح

// الأكثر استخداماً في Node.js: methods الخاصة بالـ Array
arr.forEach(item => console.log(item));
```

---

## 6. الدوال (Functions)

```javascript
// Function declaration
function add(a, b) {
  return a + b;
}

// Function expression
const subtract = function (a, b) {
  return a - b;
};

// Arrow function (الأكثر استخداماً في Node.js)
const multiply = (a, b) => a * b;
const square = x => x * x;          // معامل واحد بدون أقواس
const greet = () => console.log("Hi"); // بدون معاملات

// Default parameters
function greetUser(name = "زائر") {
  console.log(`أهلاً ${name}`);
}

// Rest parameters (عدد غير محدد من المعاملات)
function sum(...numbers) {
  return numbers.reduce((acc, n) => acc + n, 0);
}
sum(1, 2, 3, 4); // 10
```

**فرق مهم:** الـ arrow function لا تملك `this` خاصة بها (تأخذ `this` من السياق المحيط) — هذا مهم جداً عند كتابة كلاسات أو callbacks في Node.js.

---

## 7. الكائنات (Objects)

```javascript
const person = {
  name: "Omar",
  age: 30,
  greet() {
    console.log(`اسمي ${this.name}`);
  }
};

person.name;        // الوصول بالنقطة
person["name"];      // الوصول بالقوس (مفيد لو الاسم متغير)

// Destructuring (تستخدم كثيراً في Node.js مع require)
const { name, age } = person;

// Spread operator - نسخ أو دمج objects
const updated = { ...person, age: 31 };

// Object methods مفيدة
Object.keys(person);     // ["name", "age", "greet"]
Object.values(person);
Object.entries(person);  // [["name","Omar"], ["age",30], ...]
```

---

## 8. المصفوفات (Arrays) والـ Methods الأساسية

```javascript
const numbers = [1, 2, 3, 4, 5];

numbers.map(n => n * 2);        // [2,4,6,8,10] - تحويل كل عنصر
numbers.filter(n => n % 2 === 0); // [2,4] - تصفية
numbers.reduce((acc, n) => acc + n, 0); // 15 - تجميع لقيمة واحدة
numbers.find(n => n > 3);        // 4 - أول عنصر مطابق
numbers.includes(3);             // true
numbers.some(n => n > 4);        // true - هل يوجد عنصر مطابق؟
numbers.every(n => n > 0);       // true - هل كل العناصر مطابقة؟

// Destructuring & Spread
const [first, second, ...rest] = numbers;
const combined = [...numbers, 6, 7];
```

---

## 9. Template Literals (سلاسل النصوص)

```javascript
const name = "Layla";
const age = 22;

console.log(`الاسم: ${name}, العمر: ${age}`);

// متعدد الأسطر
const message = `
السطر الأول
السطر الثاني
`;
```

---

## 10. التعامل مع الأخطاء (Error Handling)

```javascript
try {
  const data = JSON.parse("{invalid json}");
} catch (error) {
  console.error("حدث خطأ:", error.message);
} finally {
  console.log("ينفذ دائماً");
}

// إنشاء خطأ مخصص
function checkAge(age) {
  if (age < 0) {
    throw new Error("العمر لا يمكن أن يكون سالباً");
  }
}
```

---

## 11. غير المتزامن (Asynchronous JS) — أهم جزء لـ Node.js

### Callbacks (الطريقة القديمة)
```javascript
function fetchData(callback) {
  setTimeout(() => {
    callback(null, "تم جلب البيانات");
  }, 1000);
}

fetchData((err, data) => {
  if (err) return console.error(err);
  console.log(data);
});
```

### Promises
```javascript
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const success = true;
      if (success) resolve("تم جلب البيانات");
      else reject("حدث خطأ");
    }, 1000);
  });
}

fetchData()
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

### Async/Await (الأكثر استخداماً في Node.js الحديث)
```javascript
async function getData() {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}

getData();

// مثال حقيقي مع Node.js: قراءة ملف
const fs = require("fs/promises");

async function readFile() {
  try {
    const content = await fs.readFile("file.txt", "utf-8");
    console.log(content);
  } catch (err) {
    console.error("فشل القراءة:", err.message);
  }
}
```

---

## 12. الـ Modules (مهم جداً لـ Node.js)

### CommonJS (النظام التقليدي في Node.js)
```javascript
// file: math.js
function add(a, b) { return a + b; }
module.exports = { add };

// file: app.js
const { add } = require("./math");
console.log(add(2, 3));
```

### ES Modules (الأحدث - يحتاج "type": "module" في package.json)
```javascript
// file: math.js
export function add(a, b) { return a + b; }
export default add;

// file: app.js
import { add } from "./math.js";
```

---

## 13. الكلاسات (Classes)

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} يصدر صوتاً`);
  }
}

class Dog extends Animal {
  speak() {
    console.log(`${this.name} ينبح`);
  }
}

const dog = new Dog("ريكس");
dog.speak(); // ريكس ينبح
```

---

## 14. JSON

```javascript
const obj = { name: "Hana", age: 28 };

const jsonString = JSON.stringify(obj); // تحويل لنص JSON
const parsedObj = JSON.parse(jsonString); // تحويل من نص لـ object

// مستخدم بكثرة في Node.js مع APIs وملفات config
```

---

## 15. Scope و Closures

```javascript
function outer() {
  let count = 0;
  function inner() {
    count++;
    console.log(count);
  }
  return inner;
}

const counter = outer();
counter(); // 1
counter(); // 2
// الـ closure حافظ على قيمة count بين الاستدعاءات
```

---

## 16. Event Loop (مفهوم أساسي لفهم Node.js)

JavaScript تعمل بنظام **single-threaded** لكنها تتعامل مع العمليات غير المتزامنة (مثل قراءة ملفات، طلبات شبكة) عبر **Event Loop**، فلا تتوقف العملية كلها في انتظار نتيجة عملية واحدة.

```javascript
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");

// الناتج: 1, 4, 3, 2
// لأن: synchronous code أولاً، ثم microtasks (Promises)، ثم macrotasks (setTimeout)
```

---

## ✅ خلاصة: ماذا تحتاج أن تتقنه قبل Node.js؟

| الأولوية | الموضوع |
|---|---|
| 🔴 ضروري جداً | Variables, Functions, Arrow Functions, Objects, Arrays |
| 🔴 ضروري جداً | Async/Await, Promises, Callbacks |
| 🔴 ضروري جداً | require/import (Modules) |
| 🟡 مهم | Destructuring, Spread/Rest, Template Literals |
| 🟡 مهم | Error Handling (try/catch) |
| 🟢 جيد تعرفه | Classes, Closures, Event Loop |

---

### الخطوة التالية
بعد إتقان هذه الأساسيات، يمكنك البدء في Node.js مباشرة بـ:
```javascript
const http = require("http");

const server = http.createServer((req, res) => {
  res.end("أهلاً من Node.js!");
});

server.listen(3000, () => console.log("Server running on port 3000"));
```
