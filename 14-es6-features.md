# 14. ES6+ Features (Modern JavaScript)

> ES6 (ES2015) and newer versions added features that make JavaScript more expressive, safer, and easier to write and maintain.

## 📋 Overview

Modern JavaScript (ES6+) introduced new syntax and APIs that you’ll see in essentially every real project: `let/const`, arrow functions, template literals, destructuring, spread/rest, default parameters, enhanced objects, classes, modules, iterables, `Map/Set`, `Promise`, and more.

**What You’ll Learn:**
- ✅ `let` / `const` and block scoping
- ✅ Template literals
- ✅ Destructuring (arrays/objects)
- ✅ Spread and rest (`...`)
- ✅ Default parameters
- ✅ Enhanced object literals
- ✅ Arrow functions (and differences)
- ✅ Optional chaining (`?.`) and nullish coalescing (`??`)
- ✅ Symbols and BigInt
- ✅ Collections (`Map`, `Set`, `WeakMap`, `WeakSet`)
- ✅ Common modern patterns and gotchas

---

## 🎯 Key Concepts

### 1. `let` and `const` (Block Scope)

```javascript
// var: function-scoped + hoisted (initialized as undefined)
console.log(a); // undefined
var a = 1;

// let/const: block-scoped + temporal dead zone
// console.log(b); // ReferenceError
let b = 2;

// const: cannot reassign binding
const config = { debug: true };
// config = {}; // TypeError
config.debug = false; // OK (mutating object)

// Block scoping
if (true) {
  let x = 10;
  const y = 20;
}
// console.log(x); // ReferenceError
// console.log(y); // ReferenceError

// Best practice:
// - default to const
// - use let when you must reassign
```

---

### 2. Template Literals

```javascript
const name = "Alice";
const age = 28;

// Old way
const msg1 = "Hi, " + name + ". You are " + age + " years old.";

// ES6 template literal
const msg2 = `Hi, ${name}. You are ${age} years old.`;

// Multi-line strings
const sql = `
SELECT id, name
FROM users
WHERE active = true;
`;

// Tagged templates (advanced)
function escapeHtml(strings, ...values) {
  const escape = (s) => String(s)
    .replaceAll("&", "&amp;")
    .replaceAll("<", "&lt;")
    .replaceAll(">", "&gt;")
    .replaceAll('"', "&quot;")
    .replaceAll("'", "&#39;");

  return strings.reduce((acc, str, i) => {
    const value = i < values.length ? escape(values[i]) : "";
    return acc + str + value;
  }, "");
}

const userInput = "<script>alert(1)</script>";
console.log(escapeHtml`Hello ${userInput}`);
// Hello &lt;script&gt;alert(1)&lt;/script&gt;
```

---

### 3. Destructuring (Arrays and Objects)

#### Array destructuring

```javascript
const arr = [10, 20, 30, 40];

const [a, b] = arr;
console.log(a, b); // 10 20

const [first, , third] = arr;
console.log(first, third); // 10 30

const [head, ...tail] = arr;
console.log(head); // 10
console.log(tail); // [20, 30, 40]

// Defaults
const [x = 1, y = 2] = [5];
console.log(x, y); // 5 2

// Swapping
let p = 1;
let q = 2;
[p, q] = [q, p];
console.log(p, q); // 2 1
```

#### Object destructuring

```javascript
const user = {
  id: 123,
  profile: {
    name: "Sam",
    city: "Adelaide",
  },
  roles: ["admin", "editor"],
};

const { id } = user;
console.log(id); // 123

// Rename
const { id: userId } = user;
console.log(userId); // 123

// Nested
const {
  profile: { name, city },
} = user;
console.log(name, city); // Sam Adelaide

// Defaults
const { missing = "default" } = user;
console.log(missing); // default

// Rest
const { profile, ...rest } = user;
console.log(rest); // { id: 123, roles: [...] }

// Destructuring parameters
function printUser({ id, profile: { name } }) {
  console.log(id, name);
}

printUser(user); // 123 Sam
```

**Gotcha:** destructuring `undefined` throws.

```javascript
const maybe = undefined;
// const { x } = maybe; // TypeError

const safe = maybe ?? {};
const { x } = safe;
console.log(x); // undefined
```

---

### 4. Spread and Rest (`...`)

#### Spread (expand)

```javascript
const nums = [1, 2, 3];
console.log(Math.max(...nums)); // 3

const a = [1, 2];
const b = [3, 4];
const combined = [...a, ...b];
console.log(combined); // [1, 2, 3, 4]

const base = { a: 1, b: 2 };
const copy = { ...base };
const merged = { ...base, b: 999, c: 3 };
console.log(copy);   // { a: 1, b: 2 }
console.log(merged); // { a: 1, b: 999, c: 3 }

// Spread order matters (later keys win)
```

#### Rest (collect)

```javascript
function sum(...values) {
  return values.reduce((acc, n) => acc + n, 0);
}

console.log(sum(1, 2, 3)); // 6

const [first, ...others] = [10, 20, 30];
console.log(first);  // 10
console.log(others); // [20, 30]

const { id, ...rest } = { id: 1, name: "A", active: true };
console.log(id);   // 1
console.log(rest); // { name: "A", active: true }
```

**Important:** object spread is a shallow copy.

```javascript
const original = { nested: { x: 1 } };
const shallow = { ...original };
shallow.nested.x = 99;
console.log(original.nested.x); // 99
```

---

### 5. Default Parameters

```javascript
function greet(name = "Guest", greeting = "Hello") {
  return `${greeting}, ${name}!`;
}

console.log(greet()); // Hello, Guest!
console.log(greet("Alice")); // Hello, Alice!
console.log(greet("Alice", "Hi")); // Hi, Alice!

// Defaults can be expressions
function createId(prefix = "id", rand = Math.random()) {
  return `${prefix}-${String(rand).slice(2, 8)}`;
}

// Defaults apply only when arg is undefined
function f(x = 10) {}

f(undefined); // uses default
f(null);      // does NOT use default (null is a real value)
```

---

### 6. Enhanced Object Literals

```javascript
const name = "Widget";
const price = 9.99;

// Property shorthand
const product = { name, price };

// Method shorthand
const cart = {
  items: [],
  add(item) {
    this.items.push(item);
  },
};

// Computed property keys
const key = "dynamic";
const obj = {
  [key]: 123,
  ["a" + "b"]: 456,
};

console.log(product); // { name: "Widget", price: 9.99 }
console.log(obj);     // { dynamic: 123, ab: 456 }
```

---

### 7. Arrow Functions (and Key Differences)

```javascript
// Regular function
function add(a, b) {
  return a + b;
}

// Arrow function
const add2 = (a, b) => a + b;

// Implicit return
const square = (n) => n * n;

// Returning objects requires parentheses
const makeUser = (name) => ({ name, createdAt: Date.now() });

// Differences:
// 1) Arrow functions do NOT have their own 'this'
// 2) No 'arguments' object
// 3) Cannot be used as constructors (no 'new')

const counter = {
  value: 0,
  incLater() {
    setTimeout(() => {
      this.value++;
      console.log(this.value);
    }, 100);
  },
};

counter.incLater();

function showArgs() {
  console.log(arguments);
}

const showArgsArrow = () => {
  // console.log(arguments); // ReferenceError
};

// Use rest instead
const showArgsRest = (...args) => console.log(args);
showArgsRest(1, 2, 3); // [1, 2, 3]
```

---

### 8. Optional Chaining (`?.`) and Nullish Coalescing (`??`)

```javascript
const user = {
  profile: {
    name: "Ava",
  },
};

console.log(user.profile?.name);      // "Ava"
console.log(user.settings?.theme);    // undefined (no error)

// Optional chaining with calls
const api = {
  maybeFn: null,
};

api.maybeFn?.(); // does nothing

// Nullish coalescing: only falls back on null/undefined
const input = 0;
console.log(input || 10); // 10 (because 0 is falsy)
console.log(input ?? 10); // 0  (because 0 is not null/undefined)

const name = "";
console.log(name || "Guest"); // "Guest"
console.log(name ?? "Guest"); // "" (empty string is valid)
```

---

### 9. Symbols and BigInt

#### Symbols

```javascript
// Symbol: unique, non-string property keys
const ID = Symbol("id");

const obj = {
  [ID]: 123,
  name: "Item",
};

console.log(obj[ID]); // 123

// Symbols don't show up in Object.keys
console.log(Object.keys(obj)); // ["name"]

// But you can access them
console.log(Object.getOwnPropertySymbols(obj)); // [Symbol(id)]

// Well-known symbols
const iterable = {
  items: [1, 2, 3],
  [Symbol.iterator]() {
    let i = 0;
    const items = this.items;
    return {
      next() {
        if (i < items.length) return { value: items[i++], done: false };
        return { value: undefined, done: true };
      },
    };
  },
};

console.log([...iterable]); // [1, 2, 3]
```

#### BigInt

```javascript
// BigInt for integers larger than Number.MAX_SAFE_INTEGER
console.log(Number.MAX_SAFE_INTEGER); // 9007199254740991

const big = 9007199254740991n;
console.log(big + 2n); // 9007199254740993n

// Can't mix BigInt and Number
// console.log(big + 1); // TypeError

// Convert explicitly
console.log(Number(big)); // may lose precision for very large values
console.log(BigInt(42));  // 42n
```

---

### 10. Modern Collections: `Map`, `Set`, `WeakMap`, `WeakSet`

#### Map

```javascript
const map = new Map();

map.set("a", 1);
map.set({ k: 1 }, "object-key");

console.log(map.get("a")); // 1
console.log(map.has("a")); // true

for (const [key, value] of map) {
  // iterates in insertion order
  console.log(key, value);
}

// Convert to array
console.log([...map.entries()]);
```

#### Set

```javascript
const set = new Set([1, 1, 2, 3]);
console.log(set); // Set { 1, 2, 3 }

set.add(3);
set.add(4);

console.log(set.has(2)); // true

// Useful for de-duplication
const unique = [...new Set(["a", "a", "b"])];
console.log(unique); // ["a", "b"]
```

#### WeakMap / WeakSet

```javascript
// WeakMap keys must be objects; entries are weakly held (GC-friendly)
const wm = new WeakMap();

let keyObj = { id: 1 };
wm.set(keyObj, { secret: "token" });

console.log(wm.get(keyObj)); // { secret: "token" }

// If keyObj becomes unreachable elsewhere, it can be garbage collected
keyObj = null;

// WeakMap/WeakSet are not iterable and have limited APIs by design
```

---

## 💡 Real-World Examples

### Example 1: API Response Handling with Destructuring + Defaults

```javascript
function normalizeUser(apiResponse) {
  const {
    id,
    profile: {
      name = "Unknown",
      avatarUrl = null,
    } = {},
    settings: {
      theme = "light",
    } = {},
  } = apiResponse ?? {};

  return {
    id,
    name,
    avatarUrl,
    theme,
  };
}

console.log(normalizeUser({ id: 1, profile: { name: "Ada" } }));
// { id: 1, name: "Ada", avatarUrl: null, theme: "light" }
```

### Example 2: Immutable Updates with Spread

```javascript
const state = {
  user: { id: 1, name: "Sam" },
  todos: [{ id: 1, text: "Learn ES6" }],
};

const nextState = {
  ...state,
  user: {
    ...state.user,
    name: "Samuel",
  },
  todos: [...state.todos, { id: 2, text: "Practice destructuring" }],
};

console.log(state.user.name);     // "Sam"
console.log(nextState.user.name); // "Samuel"
```

### Example 3: Safer Optional Access with `?.` and `??`

```javascript
function getPrimaryEmail(user) {
  return user?.contacts?.emails?.[0] ?? null;
}

console.log(getPrimaryEmail({ contacts: { emails: ["a@b.com"] } })); // a@b.com
console.log(getPrimaryEmail({})); // null
```

---

## 🎤 Interview Questions

### Q1: What’s the difference between `var`, `let`, and `const`?

**Answer:**

- `var` is function-scoped and hoisted (initialized as `undefined`).
- `let` is block-scoped and hoisted into the TDZ (cannot be used before declaration).
- `const` is block-scoped and must be initialized; the binding can’t be reassigned.

```javascript
if (true) {
  var a = 1;
  let b = 2;
  const c = 3;
}

console.log(a); // 1
// console.log(b); // ReferenceError
// console.log(c); // ReferenceError
```

---

### Q2: What’s the difference between `||` and `??`?

**Answer:**

`||` falls back on any falsy value (`0`, `""`, `false`, `NaN`, `null`, `undefined`), while `??` falls back only when the left side is `null` or `undefined`.

```javascript
console.log(0 || 10);  // 10
console.log(0 ?? 10);  // 0
console.log("" || "x"); // "x"
console.log("" ?? "x"); // ""
```

---

### Q3: When should you avoid arrow functions?

**Answer:**

Avoid arrow functions for object/class methods when you need dynamic `this` binding (or when you need `arguments` or `new`).

```javascript
const obj = {
  name: "X",
  bad: () => console.log(this.name),
  good() { console.log(this.name); },
};

obj.good(); // X
obj.bad();  // undefined (or global)
```

---

### Q4: Is object spread a deep clone?

**Answer:**

No. It’s a shallow copy.

```javascript
const a = { nested: { x: 1 } };
const b = { ...a };
b.nested.x = 2;
console.log(a.nested.x); // 2
```

---

### Q5: Why use `Map` instead of an object?

**Answer:**

`Map` supports keys of any type, preserves insertion order, has a clear size (`map.size`), and avoids prototype-key collisions.

```javascript
const m = new Map();
const key = { id: 1 };
m.set(key, "value");
console.log(m.get(key)); // value
```

---

## 📚 Summary

**Key Takeaways:**
- ES6+ features make code cleaner and safer: destructuring, spread/rest, template literals, default params.
- Prefer `const` by default, `let` when reassignment is required.
- Use `??` for “missing value” defaults, and `?.` to avoid crashy property access.
- Arrow functions are great for callbacks but not for methods needing their own `this`.
- `Map/Set` provide better semantics than plain objects/arrays for certain tasks.

---

**Next Topic:** [15. Iterators & Generators](./15-iterators-generators.md)
