# 31. Most Asked JavaScript and TypeScript Interview Questions

> A practical, high-frequency interview revision guide with concise answers, runnable examples, common pitfalls, and coding challenges.

## 📚 Overview

This chapter brings together the questions most commonly asked in JavaScript and TypeScript interviews. Use it as a final revision checklist after studying the detailed chapters.

### What You'll Review

- JavaScript types, scope, closures, objects, prototypes, and `this`
- Asynchronous JavaScript, promises, the event loop, and browser concepts
- TypeScript fundamentals, narrowing, generics, and advanced types
- Production-oriented design, safety, performance, and debugging decisions
- Common coding challenges with explained solutions

## 📋 Table of Contents

- [JavaScript Fundamentals](#-javascript-fundamentals)
- [Functions, Objects, and OOP](#-functions-objects-and-oop)
- [Asynchronous JavaScript](#-asynchronous-javascript)
- [Browser and Performance Questions](#-browser-and-performance-questions)
- [TypeScript Fundamentals](#-typescript-fundamentals)
- [Advanced TypeScript](#-advanced-typescript)
- [Coding Challenges](#-coding-challenges)
- [Rapid-Fire Output Questions](#-rapid-fire-output-questions)
- [Interview Checklist](#-interview-checklist)

---

## 🟨 JavaScript Fundamentals

### 1. What is the difference between `var`, `let`, and `const`? ⭐

**Answer:**

| Keyword | Scope | Redeclare | Reassign | Hoisted behavior |
|---|---|---:|---:|---|
| `var` | Function | Yes | Yes | Initialized with `undefined` |
| `let` | Block | No | Yes | Temporal Dead Zone |
| `const` | Block | No | No | Temporal Dead Zone |

```javascript
function example() {
  if (true) {
    var functionScoped = 'visible outside the block';
    let blockScoped = 'visible only here';
    const fixedBinding = { count: 1 };

    fixedBinding.count = 2; // Allowed: the object is mutable
    // fixedBinding = {};   // TypeError: binding cannot be reassigned
  }

  console.log(functionScoped);
  // console.log(blockScoped); // ReferenceError
}
```

**Best practice:** Prefer `const`; use `let` only when reassignment is required. Avoid `var` in modern code.

### 2. What are JavaScript's primitive types? ⭐

**Answer:** `string`, `number`, `bigint`, `boolean`, `undefined`, `symbol`, and `null`. Everything else is an object, including arrays and functions (functions are callable objects).

```javascript
console.log(typeof 'text');       // 'string'
console.log(typeof 10);           // 'number'
console.log(typeof 10n);          // 'bigint'
console.log(typeof true);         // 'boolean'
console.log(typeof undefined);    // 'undefined'
console.log(typeof Symbol('id')); // 'symbol'
console.log(typeof null);         // 'object' (historical JavaScript bug)
console.log(Array.isArray([]));    // true
```

Primitives are immutable values. Objects are mutable and variables hold references to them.

### 3. What is the difference between `==` and `===`? ⭐

**Answer:** `==` performs type coercion before comparison; `===` compares type and value without coercion.

```javascript
console.log(0 == false);       // true
console.log(0 === false);      // false
console.log('5' == 5);         // true
console.log('5' === 5);        // false
console.log(null == undefined);  // true
console.log(null === undefined); // false
```

**Best practice:** Use `===` and `!==` unless loose equality is deliberate and documented.

### 4. What is the difference between `null` and `undefined`? ⭐

**Answer:** `undefined` usually means a value has not been assigned or a property is missing. `null` is an intentional empty value.

```javascript
let notAssigned;
const user = { middleName: null };

console.log(notAssigned);          // undefined
console.log(user.lastName);        // undefined
console.log(user.middleName);      // null
console.log(JSON.stringify(user)); // {"middleName":null}
```

`JSON.stringify()` omits object properties whose values are `undefined`, but preserves `null`.

### 5. What are truthy and falsy values? ⭐

**Answer:** The falsy values are `false`, `0`, `-0`, `0n`, `''`, `null`, `undefined`, and `NaN`. Every other value is truthy, including `[]`, `{}`, and `'0'`.

```javascript
if ([]) console.log('An empty array is truthy');
if ({}) console.log('An empty object is truthy');

const count = 0;
console.log(count || 10); // 10: OR treats 0 as falsy
console.log(count ?? 10); // 0: nullish coalescing only replaces null/undefined
```

### 6. What is type coercion? ⭐⭐

**Answer:** Type coercion is JavaScript converting a value from one type to another, implicitly or explicitly.

```javascript
console.log('5' + 2);       // '52' (number becomes string)
console.log('5' - 2);       // 3 (string becomes number)
console.log(Number('5'));   // 5 (explicit)
console.log(String(5));     // '5' (explicit)
console.log(Boolean(''));   // false (explicit)
console.log(Number('abc')); // NaN
```

Prefer explicit conversion at system boundaries to make intent clear.

### 7. What are scope, lexical scope, and the scope chain? ⭐⭐

**Answer:** Scope controls where identifiers are accessible. JavaScript uses lexical scope: accessibility is determined by where code is written, not where a function is called. If a variable is not found locally, JavaScript searches outer scopes.

```javascript
const globalValue = 'global';

function outer() {
  const outerValue = 'outer';

  return function inner() {
    const innerValue = 'inner';
    return `${globalValue}, ${outerValue}, ${innerValue}`;
  };
}

console.log(outer()()); // 'global, outer, inner'
```

### 8. What are hoisting and the Temporal Dead Zone? ⭐⭐

**Answer:** Declarations are processed before execution. Function declarations can be called before their source line. `var` is hoisted and initialized with `undefined`. `let`, `const`, and `class` are hoisted but cannot be accessed before initialization; this period is the Temporal Dead Zone (TDZ).

```javascript
sayHello(); // Works
function sayHello() {
  console.log('Hello');
}

console.log(total); // undefined
var total = 5;

// console.log(name); // ReferenceError: TDZ
let name = 'Ada';
```

### 9. What is the difference between shallow and deep copying? ⭐⭐

**Answer:** A shallow copy duplicates only the top-level container; nested objects remain shared. A deep copy recursively duplicates supported nested values.

```javascript
const original = { name: 'Ada', address: { city: 'London' } };
const shallow = { ...original };
shallow.address.city = 'Paris';
console.log(original.address.city); // 'Paris'

const deep = structuredClone(original);
deep.address.city = 'Tokyo';
console.log(original.address.city); // 'Paris'
```

`structuredClone()` supports cycles and many built-in types, but not functions or DOM nodes. JSON serialization is not a reliable general-purpose deep clone.

### 10. What is destructuring, and what is the difference between rest and spread? ⭐

**Answer:** Destructuring extracts values. Rest gathers remaining values; spread expands an iterable or object.

```javascript
const user = { id: 1, name: 'Lin', role: 'admin' };
const { name, ...metadata } = user; // rest

const numbers = [1, 2, 3];
const copy = [...numbers, 4];       // spread

function sum(...values) {           // rest parameters
  return values.reduce((total, value) => total + value, 0);
}

console.log(name, metadata, copy, sum(...numbers));
```

### 11. How do `map`, `filter`, `reduce`, and `forEach` differ? ⭐

**Answer:**

- `map()` transforms every item and returns a new array.
- `filter()` keeps matching items and returns a new array.
- `reduce()` combines items into one accumulated result.
- `forEach()` performs side effects and returns `undefined`.

```javascript
const numbers = [1, 2, 3, 4];

const doubled = numbers.map(number => number * 2);          // [2, 4, 6, 8]
const evens = numbers.filter(number => number % 2 === 0);   // [2, 4]
const total = numbers.reduce((sum, number) => sum + number, 0); // 10
```

### 12. Which common array methods mutate the original array? ⭐⭐

**Answer:** `push`, `pop`, `shift`, `unshift`, `splice`, `sort`, `reverse`, `fill`, and `copyWithin` mutate. `map`, `filter`, `slice`, `concat`, `toSorted`, `toReversed`, `toSpliced`, and `with` return new arrays.

```javascript
const values = [3, 1, 2];
const sortedCopy = values.toSorted((a, b) => a - b);

console.log(values);     // [3, 1, 2]
console.log(sortedCopy); // [1, 2, 3]
```

### 13. Why does `typeof null` return `'object'`? ⭐

**Answer:** It is a historical implementation bug retained for web compatibility. Check null explicitly:

```javascript
function isObject(value) {
  return typeof value === 'object' && value !== null;
}
```

### 14. What is `NaN`, and how should it be checked? ⭐

**Answer:** `NaN` means “Not-a-Number,” but its type is `number`. It is the only JavaScript value not equal to itself. Use `Number.isNaN()` for a strict check.

```javascript
const result = Number('not numeric');

console.log(typeof result);          // 'number'
console.log(result === result);      // false
console.log(Number.isNaN(result));   // true
console.log(isNaN('not numeric'));   // true (coerces first; usually avoid)
```

### 15. What is optional chaining and nullish coalescing? ⭐

**Answer:** Optional chaining (`?.`) safely stops when the left side is `null` or `undefined`. Nullish coalescing (`??`) supplies a default only for `null` or `undefined`.

```javascript
const user = { profile: { displayName: '' } };

const city = user.profile?.address?.city ?? 'Unknown';
const displayName = user.profile?.displayName ?? 'Anonymous';

console.log(city);        // 'Unknown'
console.log(displayName); // '' (empty string is preserved)
```

---

## 🧩 Functions, Objects, and OOP

### 16. What is a closure? ⭐⭐

**Answer:** A closure is a function together with access to its lexical environment, even after the outer function has finished. Closures support private state, factories, memoization, and callbacks.

```javascript
function createCounter(initialValue = 0) {
  let count = initialValue;

  return {
    increment: () => ++count,
    value: () => count
  };
}

const counter = createCounter(10);
console.log(counter.increment()); // 11
console.log(counter.value());     // 11
```

### 17. How do function declarations, expressions, and arrow functions differ? ⭐⭐

**Answer:** Function declarations are fully hoisted. Function expressions are values assigned during execution. Arrow functions have lexical `this`, no own `arguments`, and cannot be used as constructors.

```javascript
function declared(a, b) {
  return a + b;
}

const expressed = function (a, b) {
  return a + b;
};

const arrow = (a, b) => a + b;
```

Use a regular method when dynamic `this` is required; use arrows for callbacks that should inherit surrounding `this`.

### 18. How is `this` determined? ⭐⭐⭐

**Answer:** For regular functions, `this` depends on the call site:

1. `new Fn()` → new instance
2. `fn.call(value)` / `apply` / bound function → explicit value
3. `object.fn()` → object before the dot
4. Plain strict-mode call → `undefined`

Arrow functions capture `this` lexically.

```javascript
const user = {
  name: 'Grace',
  regular() {
    return this.name;
  },
  delayed() {
    return () => this.name;
  }
};

console.log(user.regular());  // 'Grace'
console.log(user.delayed()()); // 'Grace'
```

### 19. What is the difference between `call`, `apply`, and `bind`? ⭐⭐

**Answer:** `call` invokes immediately with separate arguments, `apply` invokes immediately with an argument array, and `bind` returns a new function.

```javascript
function introduce(greeting, punctuation) {
  return `${greeting}, I am ${this.name}${punctuation}`;
}

const person = { name: 'Ada' };

console.log(introduce.call(person, 'Hello', '!'));
console.log(introduce.apply(person, ['Hi', '.']));
const boundIntroduce = introduce.bind(person, 'Welcome');
console.log(boundIntroduce('!'));
```

### 20. What is the prototype chain? ⭐⭐⭐

**Answer:** Objects can delegate property lookup to another object called their prototype. Lookup continues along the prototype chain until a property is found or the chain reaches `null`. JavaScript `class` syntax is built on this mechanism.

```javascript
const animal = {
  speak() {
    return `${this.name} makes a sound`;
  }
};

const dog = Object.create(animal);
dog.name = 'Rex';

console.log(dog.speak());
console.log(Object.getPrototypeOf(dog) === animal); // true
```

### 21. Class syntax versus constructor functions: what changes? ⭐⭐

**Answer:** Both use prototypes for instance methods. Classes add clearer syntax, strict-mode behavior, private fields, `extends`, and a requirement to use `new`.

```javascript
class Account {
  #balance = 0;

  constructor(owner) {
    this.owner = owner;
  }

  deposit(amount) {
    if (amount <= 0) throw new RangeError('Amount must be positive');
    this.#balance += amount;
  }

  get balance() {
    return this.#balance;
  }
}
```

### 22. What is the difference between composition and inheritance? ⭐⭐⭐

**Answer:** Inheritance models an “is-a” relationship and couples subclasses to a base class. Composition builds behavior from smaller “has-a” capabilities and is usually easier to test and change.

```javascript
const canEat = state => ({ eat: () => `${state.name} eats` });
const canWalk = state => ({ walk: () => `${state.name} walks` });

function createPerson(name) {
  const state = { name };
  return { ...state, ...canEat(state), ...canWalk(state) };
}

const person = createPerson('Sam');
console.log(person.walk());
```

### 23. What are pure functions and immutability? ⭐⭐

**Answer:** A pure function has no observable side effects and returns the same output for the same input. Immutability means creating new values instead of changing existing ones.

```javascript
function addTodo(todos, id, title) {
  return [...todos, { id, title, done: false }];
}

const todos = [];
const updated = addTodo(todos, 'todo-1', 'Prepare for interview');
console.log(todos.length);   // 0
console.log(updated.length); // 1
```

The function does not mutate its input and produces the same output for the same arguments.

### 24. What is currying? ⭐⭐⭐

**Answer:** Currying transforms a function taking multiple arguments into a sequence of functions that each take one argument. It enables partial application and reusable configuration.

```javascript
const multiply = a => b => a * b;
const double = multiply(2);

console.log(double(5)); // 10
```

### 25. What is memoization? ⭐⭐⭐

**Answer:** Memoization caches results for repeated inputs. It is useful for expensive pure computations but trades memory for speed.

```javascript
function memoize(fn) {
  const cache = new Map();

  return value => {
    if (cache.has(value)) return cache.get(value);
    const result = fn(value);
    cache.set(value, result);
    return result;
  };
}

const square = memoize(value => value * value);
console.log(square(9)); // Calculated
console.log(square(9)); // Cached
```

For object or multiple arguments, define a safe cache-key strategy rather than blindly using `JSON.stringify()`.

### 26. What are ES modules and CommonJS? ⭐⭐

**Answer:** ES modules use static `import`/`export`, support browser and Node.js tooling, and enable tree shaking. CommonJS uses runtime `require()` and `module.exports`, mainly in older Node.js code.

```javascript
// math.js (ES module)
export const add = (a, b) => a + b;

// app.js
import { add } from './math.js';
console.log(add(2, 3));
```

In Node.js, ESM can be selected with `.mjs` or `"type": "module"` in `package.json`.

---

## ⏳ Asynchronous JavaScript

### 27. How does the event loop work? ⭐⭐⭐

**Answer:** JavaScript executes one call stack at a time. The host environment performs timers and I/O. After the stack is empty, the event loop drains the microtask queue before taking the next task (macrotask).

```javascript
console.log('A');

setTimeout(() => console.log('B'), 0); // Task
Promise.resolve().then(() => console.log('C')); // Microtask
queueMicrotask(() => console.log('D'));         // Microtask

console.log('E');
// A, E, C, D, B
```

Common microtasks include promise reactions and `queueMicrotask`. Timers and DOM events are tasks.

### 28. What states can a Promise have? ⭐⭐

**Answer:** A promise starts **pending**, then becomes **fulfilled** with a value or **rejected** with a reason. Once settled, its state cannot change.

```javascript
function wait(milliseconds) {
  return new Promise(resolve => {
    setTimeout(resolve, milliseconds);
  });
}

wait(100).then(() => console.log('Done'));
```

A promise represents a future result; it does not make CPU-heavy work run in parallel.

### 29. How does `async`/`await` relate to promises? ⭐⭐

**Answer:** An `async` function always returns a promise. `await` pauses only that async function until a value settles; it does not block the JavaScript thread.

```javascript
async function loadUser(id, signal) {
  const response = await fetch(`/api/users/${id}`, { signal });

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

Use `try`/`catch` where an error can be handled meaningfully; otherwise let rejection propagate.

### 30. When should operations run sequentially or concurrently? ⭐⭐⭐

**Answer:** Await sequentially when one result depends on another. Start independent operations together and await them with `Promise.all()`.

```javascript
// Concurrent: independent requests
const [user, permissions] = await Promise.all([
  fetch('/api/user').then(response => response.json()),
  fetch('/api/permissions').then(response => response.json())
]);

// Sequential: second request depends on the first
const order = await createOrder();
const receipt = await payForOrder(order.id);
```

Avoid `await` inside a loop unless sequential behavior or rate limiting is intended.

### 31. How do Promise combinators differ? ⭐⭐⭐

**Answer:**

| Method | Settles when | Rejects when |
|---|---|---|
| `Promise.all` | All fulfill | Any rejects (fail fast) |
| `Promise.allSettled` | All settle | Never due to an input rejection |
| `Promise.race` | First input settles | First input rejects |
| `Promise.any` | First input fulfills | All reject (`AggregateError`) |

```javascript
const results = await Promise.allSettled([
  Promise.resolve('success'),
  Promise.reject(new Error('failure'))
]);

for (const result of results) {
  if (result.status === 'fulfilled') console.log(result.value);
  else console.error(result.reason);
}
```

### 32. Why does `forEach(async () => ...)` cause problems? ⭐⭐⭐

**Answer:** `forEach()` ignores callback return values, so it does not await promises and errors may become unhandled.

```javascript
// Concurrent
await Promise.all(items.map(item => processItem(item)));

// Sequential
for (const item of items) {
  await processItem(item);
}
```

Choose explicitly between concurrency and sequencing.

### 33. How can an asynchronous operation be cancelled? ⭐⭐⭐

**Answer:** Promises themselves are not generally cancellable, but many APIs accept an `AbortSignal`.

```javascript
const controller = new AbortController();

const request = fetch('/api/report', { signal: controller.signal });
controller.abort();

try {
  await request;
} catch (error) {
  if (error.name !== 'AbortError') throw error;
}
```

Pass the signal through application layers so cancellation reaches the underlying operation.

### 34. How should errors be handled in async code? ⭐⭐

**Answer:** Throw meaningful `Error` objects, catch only where recovery or context can be added, and preserve the original cause.

```javascript
async function getSettings() {
  try {
    const response = await fetch('/api/settings');
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return await response.json();
  } catch (error) {
    throw new Error('Unable to load settings', { cause: error });
  }
}
```

Use `finally` for cleanup. Do not silently swallow failures.

---

## 🌐 Browser and Performance Questions

### 35. What is event bubbling, capturing, and delegation? ⭐⭐

**Answer:** During capturing, an event travels from the document toward the target. It then reaches the target and bubbles back through ancestors. Event delegation handles events on a stable ancestor instead of attaching a listener to every child.

```javascript
const list = document.querySelector('#todo-list');

list.addEventListener('click', event => {
  const button = event.target.closest('[data-remove-id]');
  if (!button || !list.contains(button)) return;

  removeTodo(button.dataset.removeId);
});
```

Delegation works well for dynamic lists and reduces listener count.

### 36. What is the difference between debounce and throttle? ⭐⭐⭐

**Answer:** Debounce waits for calls to stop before running once. Throttle limits execution to at most once per interval.

- Debounce: search input, autosave, resize completion
- Throttle: scroll progress, pointer tracking, continuous telemetry

```javascript
function debounce(fn, delay) {
  let timeoutId;

  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}

const search = debounce(query => console.log(query), 300);
```

### 37. How do cookies, `localStorage`, and `sessionStorage` differ? ⭐⭐

**Answer:**

| Storage | Lifetime | Sent with HTTP requests | Typical size |
|---|---|---:|---:|
| Cookie | Configurable | Yes, for matching scope | About 4 KB |
| `localStorage` | Until cleared | No | Usually several MB |
| `sessionStorage` | Browser-tab session | No | Usually several MB |

All Web Storage values are strings and synchronous. Never store sensitive tokens in JavaScript-readable storage when an `HttpOnly`, `Secure`, `SameSite` cookie is appropriate.

### 38. What causes memory leaks in JavaScript applications? ⭐⭐⭐

**Answer:** Common causes include forgotten event listeners or timers, unbounded caches, detached DOM nodes retained by references, and closures retaining large objects.

```javascript
const controller = new AbortController();

window.addEventListener('resize', handleResize, {
  signal: controller.signal
});

// Component cleanup
controller.abort(); // Removes the listener
```

Use browser memory profiling and allocation timelines to find retaining references.

### 39. What is CORS? ⭐⭐

**Answer:** Cross-Origin Resource Sharing is an HTTP mechanism through which a server allows selected cross-origin browser requests. The browser enforces it; it is not an authentication system.

For non-simple requests, the browser may send an `OPTIONS` preflight. The server must return appropriate headers such as `Access-Control-Allow-Origin`. The client cannot fix a missing server CORS policy by adding that response header itself.

### 40. What is the difference between synchronous code, Web Workers, and async I/O? ⭐⭐⭐

**Answer:** Synchronous CPU work blocks the main thread. Async I/O lets the host wait for network or timers without blocking but still runs callbacks on the main thread. Web Workers execute JavaScript in another thread and communicate through messages.

Use workers for CPU-heavy parsing, image processing, or calculations; use promises for I/O orchestration.

---

## 🔷 TypeScript Fundamentals

### 41. What is TypeScript, and does it run at runtime? ⭐

**Answer:** TypeScript is JavaScript plus a static type system and tooling. Type annotations are erased during compilation; browsers and standard JavaScript runtimes execute the emitted JavaScript, not TypeScript types.

```typescript
function greet(name: string): string {
  return `Hello, ${name}`;
}

// Emitted JavaScript contains no ': string' annotations.
```

Types cannot validate untrusted runtime data. Use runtime validation at API, file, or user-input boundaries.

### 42. What is type inference? ⭐

**Answer:** TypeScript often derives a type from a value or context, reducing unnecessary annotations.

```typescript
const language = 'TypeScript'; // Type: 'TypeScript'
let score = 10;                // Type: number
const scores = [10, 20, 30];   // Type: number[]

const doubled = scores.map(score => score * 2); // score inferred as number
```

Annotate public boundaries and non-obvious intent; allow inference for straightforward local values.

### 43. What is the difference between `any`, `unknown`, and `never`? ⭐⭐

**Answer:**

- `any` disables type safety and can flow almost anywhere.
- `unknown` can hold any value but must be narrowed before use.
- `never` represents a value that cannot occur, such as a function that always throws or an exhausted union.

```typescript
function parseJson(text: string): unknown {
  return JSON.parse(text);
}

const value = parseJson('{"name":"Ada"}');
if (typeof value === 'object' && value !== null && 'name' in value) {
  console.log(value.name);
}

function fail(message: string): never {
  throw new Error(message);
}
```

### 44. Interface versus type alias: when should each be used? ⭐⭐

**Answer:** Both can describe object shapes. Interfaces support declaration merging and are natural for extensible object contracts. Type aliases can represent primitives, unions, tuples, intersections, and mapped types.

```typescript
interface User {
  id: string;
  name: string;
}

interface Admin extends User {
  permissions: string[];
}

type Status = 'idle' | 'loading' | 'success' | 'error';
type Coordinates = readonly [number, number];
```

Use the form that communicates the model best; consistency matters more than a universal rule.

### 45. What are union and intersection types? ⭐⭐

**Answer:** A union (`A | B`) allows either type. An intersection (`A & B`) requires all members from both types.

```typescript
type Id = string | number;
type Timestamped = { createdAt: Date };
type User = { id: Id; name: string };
type StoredUser = User & Timestamped;

const user: StoredUser = {
  id: 1,
  name: 'Ada',
  createdAt: new Date()
};
```

### 46. What is narrowing, and which type guards are available? ⭐⭐

**Answer:** Narrowing refines a broad type based on control flow. Common guards are `typeof`, `instanceof`, property checks with `in`, equality checks, discriminants, and user-defined predicates.

```typescript
function format(value: string | Date): string {
  if (value instanceof Date) {
    return value.toISOString();
  }

  return value.trim(); // value is string here
}

function isError(value: unknown): value is Error {
  return value instanceof Error;
}
```

### 47. What is a discriminated union? ⭐⭐⭐

**Answer:** It is a union whose members share a literal property that identifies each variant. It models state more safely than many optional properties.

```typescript
type Result<T> =
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };

function render<T>(result: Result<T>): string {
  switch (result.status) {
    case 'success':
      return JSON.stringify(result.data);
    case 'error':
      return result.error.message;
  }
}
```

### 48. What are generics? ⭐⭐

**Answer:** Generics preserve relationships between types while keeping code reusable. They are preferable to `any` when output types depend on input types.

```typescript
function first<T>(values: readonly T[]): T | undefined {
  return values[0];
}

const number = first([10, 20]);       // number | undefined
const name = first(['Ada', 'Grace']); // string | undefined
```

### 49. What are generic constraints? ⭐⭐⭐

**Answer:** Constraints restrict accepted type arguments while retaining their specific information.

```typescript
function getProperty<T extends object, K extends keyof T>(
  object: T,
  key: K
): T[K] {
  return object[key];
}

const user = { id: 1, name: 'Ada' };
const name = getProperty(user, 'name'); // string
// getProperty(user, 'email');          // Compile error
```

### 50. What is the difference between optional properties and properties containing `undefined`? ⭐⭐⭐

**Answer:** `value?: string` permits the property to be absent. `value: string | undefined` requires the property to exist, though its value may be `undefined`.

```typescript
type Optional = { value?: string };
type RequiredMaybeUndefined = { value: string | undefined };

const a: Optional = {};
const b: RequiredMaybeUndefined = { value: undefined };
```

With `exactOptionalPropertyTypes`, explicitly assigning `undefined` to an optional property is rejected unless `undefined` is included in its type.

### 51. What do `readonly` and `as const` do? ⭐⭐

**Answer:** `readonly` prevents mutation through a typed reference at compile time. `as const` keeps literal types and makes object properties and array elements readonly.

```typescript
const routes = {
  home: '/',
  users: '/users'
} as const;

type Route = (typeof routes)[keyof typeof routes]; // '/' | '/users'

function total(values: readonly number[]): number {
  return values.reduce((sum, value) => sum + value, 0);
}
```

Neither feature freezes objects at runtime; use `Object.freeze()` when runtime shallow freezing is needed.

### 52. What is structural typing? ⭐⭐

**Answer:** TypeScript compatibility is mainly based on shape, not explicit declarations or class names.

```typescript
interface Named {
  name: string;
}

class Customer {
  constructor(public name: string, public accountId: string) {}
}

function printName(value: Named): void {
  console.log(value.name);
}

printName(new Customer('Ada', 'A-100')); // Compatible by shape
```

Private or protected class members introduce nominal-like compatibility constraints.

### 53. What are function overloads? ⭐⭐⭐

**Answer:** Overload signatures describe related call forms, followed by one implementation signature broad enough to implement all of them.

```typescript
function format(value: number): string;
function format(value: Date): string;
function format(value: number | Date): string {
  return value instanceof Date
    ? value.toISOString()
    : value.toFixed(2);
}

format(12.5);       // Valid
format(new Date()); // Valid
```

Prefer unions when input and output are not correlated; use overloads when call forms have distinct return types.

### 54. Enums or union literals: which should be used? ⭐⭐

**Answer:** String literal unions are lightweight and emit no JavaScript. Enums create runtime objects (except `const enum`, depending on compiler settings) and can be useful for interoperating with enum-like runtime APIs.

```typescript
const directions = ['north', 'south', 'east', 'west'] as const;
type Direction = (typeof directions)[number];

function move(direction: Direction): void {
  console.log(direction);
}
```

A constant object plus `as const` often provides both runtime values and precise types.

### 55. What is the difference between `private` and JavaScript `#private`? ⭐⭐⭐

**Answer:** TypeScript's `private` is primarily compile-time access control and is usually emitted as a normal property. A JavaScript `#private` field is enforced by the runtime.

```typescript
class Session {
  private label = 'internal to TypeScript';
  #token = 'runtime private';

  getToken(): string {
    return this.#token;
  }
}
```

---

## 🧠 Advanced TypeScript

### 56. What do `keyof`, `typeof`, and indexed access types do? ⭐⭐⭐

**Answer:** `keyof` creates a union of property keys. Type-position `typeof` obtains the static type of a value. Indexed access selects a property type.

```typescript
const config = {
  apiUrl: 'https://example.com',
  retries: 3
};

type Config = typeof config;
type ConfigKey = keyof Config;       // 'apiUrl' | 'retries'
type ApiUrl = Config['apiUrl'];      // string
type ConfigValue = Config[ConfigKey]; // string | number
```

### 57. What are mapped types? ⭐⭐⭐

**Answer:** Mapped types transform every property in a key union, often using `keyof`.

```typescript
type Flags<T> = {
  [K in keyof T]: boolean;
};

type Features = {
  darkMode: () => void;
  notifications: () => void;
};

type FeatureFlags = Flags<Features>;
// { darkMode: boolean; notifications: boolean }
```

Modifiers can be added or removed with `readonly`, `?`, `-readonly`, and `-?`.

### 58. What are conditional types and `infer`? ⭐⭐⭐

**Answer:** Conditional types choose a type based on assignability. `infer` captures part of a matched type.

```typescript
type AwaitedValue<T> = T extends PromiseLike<infer U> ? U : T;
type ApiValue = AwaitedValue<Promise<{ id: string }>>;
// { id: string }

type ElementType<T> = T extends readonly (infer U)[] ? U : never;
type Name = ElementType<readonly string[]>; // string
```

Conditional types distribute over unions when the checked type is a naked type parameter.

### 59. What are the most useful built-in utility types? ⭐⭐

**Answer:**

- `Partial<T>`: all properties optional
- `Required<T>`: all properties required
- `Readonly<T>`: all properties readonly
- `Pick<T, K>` / `Omit<T, K>`: select or remove keys
- `Record<K, V>`: map keys to values
- `Exclude<U, M>` / `Extract<U, M>`: filter union members
- `NonNullable<T>`: remove `null` and `undefined`
- `ReturnType<F>` / `Parameters<F>`: inspect function types
- `Awaited<T>`: unwrap promise-like values

```typescript
interface User {
  id: string;
  name: string;
  passwordHash: string;
}

type PublicUser = Omit<User, 'passwordHash'>;
type UserPatch = Partial<Pick<User, 'name'>>;
```

### 60. What is the difference between annotation, assertion, and `satisfies`? ⭐⭐⭐

**Answer:** An annotation checks and sets the variable's type. An assertion (`as`) tells the compiler to treat a value as a type and can hide mistakes. `satisfies` checks compatibility while preserving the value's inferred specific type.

```typescript
type Palette = Record<'primary' | 'danger', string | readonly number[]>;

const palette = {
  primary: '#3366ff',
  danger: [255, 0, 0]
} satisfies Palette;

palette.primary.toUpperCase(); // Preserved as string
```

Prefer narrowing and validation over assertions. Never use double assertions merely to silence an error.

### 61. How is exhaustive checking implemented? ⭐⭐⭐

**Answer:** Assign the remaining value to `never` in a `default` branch. Adding a new union member then creates a compile error until it is handled.

```typescript
type Shape =
  | { kind: 'circle'; radius: number }
  | { kind: 'square'; size: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2;
    case 'square':
      return shape.size ** 2;
    default: {
      const unreachable: never = shape;
      throw new Error(`Unhandled shape: ${JSON.stringify(unreachable)}`);
    }
  }
}
```

### 62. What are declaration files? ⭐⭐

**Answer:** `.d.ts` files describe the types of existing JavaScript code without providing runtime implementation. They are used for libraries, global APIs, and untyped modules.

```typescript
// analytics-sdk.d.ts
declare module 'analytics-sdk' {
  export interface EventProperties {
    [key: string]: string | number | boolean;
  }

  export function track(
    eventName: string,
    properties?: EventProperties
  ): void;
}
```

Publishing libraries commonly includes generated declarations via `declaration: true`.

### 63. Why must API responses be validated at runtime? ⭐⭐⭐

**Answer:** A generic type argument or assertion does not inspect network data. External data is `unknown` until validated.

```typescript
interface User {
  id: string;
  name: string;
}

function isUser(value: unknown): value is User {
  if (typeof value !== 'object' || value === null) return false;
  return 'id' in value && typeof value.id === 'string'
    && 'name' in value && typeof value.name === 'string';
}

async function fetchUser(): Promise<User> {
  const response = await fetch('/api/user');
  const data: unknown = await response.json();

  if (!isUser(data)) throw new TypeError('Invalid user response');
  return data;
}
```

For complex schemas, use a runtime schema-validation library and infer TypeScript types from the schema.

### 64. Which `tsconfig` options matter most for safety? ⭐⭐⭐

**Answer:** Start with `strict: true`. Frequently useful additional checks include:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "noFallthroughCasesInSwitch": true,
    "useUnknownInCatchVariables": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

Compiler settings depend on the runtime and build tool, so `target`, `module`, and `moduleResolution` should match the actual environment.

### 65. How should TypeScript errors be approached instead of silenced? ⭐⭐

**Answer:** Treat the error as evidence that the model or control flow is incomplete:

1. Read the full error and identify expected versus actual types.
2. Narrow `unknown` values.
3. Correct inaccurate domain types.
4. Handle `undefined`, `null`, and all union variants.
5. Use an assertion only when a runtime invariant is known but cannot be expressed.
6. Document unavoidable suppression with `@ts-expect-error` and a reason.

Avoid `any`, `as unknown as T`, and `@ts-ignore` as routine fixes.

---

## 💻 Coding Challenges

### Challenge 1: Remove duplicate values ⭐

**Question:** Return unique primitive values while preserving first-seen order.

```javascript
function unique(values) {
  return [...new Set(values)];
}

console.log(unique([3, 1, 3, 2, 1])); // [3, 1, 2]
```

**Complexity:** $O(n)$ average time and $O(n)$ space. For objects, define uniqueness using a key such as `id`.

### Challenge 2: Count item frequency ⭐

```javascript
function countBy(values) {
  return values.reduce((counts, value) => {
    counts[value] = (counts[value] ?? 0) + 1;
    return counts;
  }, Object.create(null));
}

console.log(countBy(['a', 'b', 'a'])); // { a: 2, b: 1 }
```

`Object.create(null)` avoids inherited keys. A `Map` is preferable when keys are not strings or symbols.

### Challenge 3: Flatten a nested array ⭐⭐

```javascript
function flatten(values) {
  const result = [];

  for (const value of values) {
    if (Array.isArray(value)) result.push(...flatten(value));
    else result.push(value);
  }

  return result;
}

console.log(flatten([1, [2, [3, 4]], 5])); // [1, 2, 3, 4, 5]
```

For ordinary application code, `values.flat(Infinity)` is simpler. An iterative solution avoids call-stack limits for extremely deep input.

### Challenge 4: Group objects by a property ⭐⭐

```javascript
function groupBy(items, getKey) {
  return items.reduce((groups, item) => {
    const key = getKey(item);
    (groups[key] ??= []).push(item);
    return groups;
  }, Object.create(null));
}

const users = [
  { name: 'Ada', team: 'platform' },
  { name: 'Lin', team: 'web' },
  { name: 'Grace', team: 'platform' }
];

console.log(groupBy(users, user => user.team));
```

### Challenge 5: Implement debounce with preserved `this` ⭐⭐

```javascript
function debounce(fn, delay) {
  let timeoutId;

  function debounced(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  }

  debounced.cancel = () => clearTimeout(timeoutId);
  return debounced;
}
```

A production implementation may also support leading calls, trailing calls, and result caching.

### Challenge 6: Retry an asynchronous operation ⭐⭐⭐

```javascript
async function retry(operation, maxAttempts, baseDelay = 100) {
  let lastError;

  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await operation();
    } catch (error) {
      lastError = error;
      if (attempt === maxAttempts) break;

      const delay = baseDelay * 2 ** (attempt - 1);
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }

  throw lastError;
}
```

Retry only transient, idempotent operations. In production, add jitter, cancellation, and error classification.

### Challenge 7: Limit asynchronous concurrency ⭐⭐⭐

```javascript
async function mapWithConcurrency(items, limit, mapper) {
  if (!Number.isInteger(limit) || limit < 1) {
    throw new RangeError('limit must be a positive integer');
  }

  const results = new Array(items.length);
  let nextIndex = 0;

  async function worker() {
    while (true) {
      const index = nextIndex++;
      if (index >= items.length) return;
      results[index] = await mapper(items[index], index);
    }
  }

  const workerCount = Math.min(limit, items.length);
  await Promise.all(Array.from({ length: workerCount }, worker));
  return results;
}
```

This preserves output order while allowing at most `limit` mapper calls at once.

### Challenge 8: Write a type-safe property getter ⭐⭐

```typescript
function getProperty<T extends object, K extends keyof T>(
  object: T,
  key: K
): T[K] {
  return object[key];
}

const product = { id: 1, title: 'Keyboard', price: 99 };
const price = getProperty(product, 'price'); // number
```

The generic relationship prevents invalid keys and retains the exact property type.

### Challenge 9: Model request state safely ⭐⭐⭐

```typescript
type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };

function message<T>(state: AsyncState<T>): string {
  switch (state.status) {
    case 'idle':
      return 'Ready';
    case 'loading':
      return 'Loading…';
    case 'success':
      return `Loaded: ${JSON.stringify(state.data)}`;
    case 'error':
      return state.error.message;
  }
}
```

This prevents impossible states such as `loading: true` while both `data` and `error` are present.

### Challenge 10: Create a typed event emitter ⭐⭐⭐

```typescript
type EventMap = Record<string, unknown>;

type Listener<T> = (payload: T) => void;

class TypedEmitter<Events extends EventMap> {
  private listeners: {
    [K in keyof Events]?: Set<Listener<Events[K]>>;
  } = {};

  on<K extends keyof Events>(event: K, listener: Listener<Events[K]>): () => void {
    const listeners = (this.listeners[event] ??= new Set());
    listeners.add(listener);
    return () => listeners.delete(listener);
  }

  emit<K extends keyof Events>(event: K, payload: Events[K]): void {
    this.listeners[event]?.forEach(listener => listener(payload));
  }
}

type AppEvents = {
  login: { userId: string };
  logout: undefined;
};

const emitter = new TypedEmitter<AppEvents>();
emitter.on('login', ({ userId }) => console.log(userId));
emitter.emit('login', { userId: 'user-1' });
```

---

## ⚡ Rapid-Fire Output Questions

### 1. Closure in a loop

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
// 3, 3, 3
```

`var` creates one function-scoped binding. Replace it with `let` to create a new binding per iteration and print `0, 1, 2`.

### 2. Object key coercion

```javascript
const object = {};
object[{}] = 'first';
object[{}] = 'second';
console.log(object); // { '[object Object]': 'second' }
```

Plain-object keys are strings or symbols. Use `Map` when object identity must be preserved as a key.

### 3. Floating-point precision

```javascript
console.log(0.1 + 0.2 === 0.3); // false
console.log(0.1 + 0.2);         // 0.30000000000000004
```

Most decimal fractions are not represented exactly in binary floating point. For approximate comparison:

```javascript
const nearlyEqual = (a, b, tolerance = Number.EPSILON) =>
  Math.abs(a - b) <= tolerance * Math.max(1, Math.abs(a), Math.abs(b));
```

For money, use integer minor units or a decimal arithmetic library.

### 4. Reference equality

```javascript
console.log({} === {}); // false
const value = {};
console.log(value === value); // true
```

Objects compare by reference identity, not structure.

### 5. Promise executor timing

```javascript
console.log('start');

new Promise(resolve => {
  console.log('executor');
  resolve();
}).then(() => console.log('then'));

console.log('end');
// start, executor, end, then
```

The promise executor runs synchronously; reaction callbacks run as microtasks.

### 6. TypeScript excess property checking

```typescript
interface User {
  name: string;
}

// const direct: User = { name: 'Ada', role: 'admin' }; // Error

const value = { name: 'Ada', role: 'admin' };
const indirect: User = value; // Allowed by structural compatibility
```

Fresh object literals receive additional excess-property checks. This is not exact-object typing.

---

## ✅ Interview Checklist

### Before the Interview

- Explain every solution aloud, including assumptions and edge cases.
- Practice without relying on framework-specific helpers.
- Review time and space complexity.
- Know the runtime and compiler versions used by the role.
- Prepare examples of debugging, performance, testing, and design decisions.

### During a Coding Question

1. Restate the problem and clarify input constraints.
2. Work through a small example.
3. Start with a correct simple solution.
4. Name edge cases: empty input, duplicates, invalid data, overflow, cancellation.
5. State complexity using Big O notation.
6. Test normal, boundary, and failure cases.
7. Refactor only after correctness is clear.

### Strong Answer Pattern

A complete technical answer usually contains:

1. A one-sentence definition
2. The important contrast or trade-off
3. A small, correct example
4. A real-world use case
5. A pitfall or best practice

## 📖 Related Chapters

- [Variables and Data Types](./01-variables-and-types.md)
- [Functions](./04-functions.md)
- [Arrays](./05-arrays.md)
- [Objects](./06-objects.md)
- [Async Programming](./08-async-programming.md)
- [Closures](./11-closures.md)
- [Prototypes](./12-prototypes.md)
- [`this` Keyword](./13-this-keyword.md)
- [TypeScript Basics](./19-typescript-basics.md)
- [Interfaces](./21-interfaces.md)
- [Generics](./24-generics.md)
- [Advanced Types](./25-advanced-types.md)
- [Type Manipulation](./29-type-manipulation.md)
- [TypeScript Best Practices](./30-typescript-best-practices.md)

---

[← Previous: TypeScript Best Practices](./30-typescript-best-practices.md) | [🏠 Home](./README.md)
