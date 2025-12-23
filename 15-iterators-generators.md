# 15. Iterators & Generators in JavaScript

> Iterators and generators power `for...of`, spread, destructuring, and many streaming / lazy patterns.

## 📋 Overview

JavaScript has a protocol for **iterables** (things you can loop over with `for...of`) and **iterators** (objects that produce a sequence via `.next()`). **Generators** are a special kind of function that can pause/resume, and they’re the easiest way to build iterators.

**What You’ll Learn:**
- ✅ Iterable protocol (`Symbol.iterator`)
- ✅ Iterator protocol (`next()` returning `{ value, done }`)
- ✅ Built-in iterables (Array, String, Map, Set, etc.)
- ✅ Creating custom iterables
- ✅ Generator functions (`function*`) and `yield`
- ✅ Delegation with `yield*`
- ✅ Generator patterns (lazy sequences, pipelines)
- ✅ Async iterables and async generators (`Symbol.asyncIterator`, `for await...of`)
- ✅ Common pitfalls and best practices

---

## 🎯 Key Concepts

### 1. Iterables vs Iterators

- **Iterable**: an object that has a method at `obj[Symbol.iterator]()` which returns an iterator.
- **Iterator**: an object with a `.next()` method that returns `{ value, done }`.

```javascript
// An iterator produces values one-by-one
const iterator = {
  next() {
    return { value: 1, done: true };
  },
};

console.log(iterator.next()); // { value: 1, done: true }

// An iterable can create an iterator
const iterable = {
  [Symbol.iterator]() {
    let i = 0;
    return {
      next() {
        i++;
        if (i <= 3) return { value: i, done: false };
        return { value: undefined, done: true };
      },
    };
  },
};

for (const x of iterable) {
  console.log(x); // 1, 2, 3
}

// Spread uses iteration
console.log([...iterable]); // [1, 2, 3]
```

---

### 2. Built-in Iterables

Many JS values are iterable out of the box.

```javascript
// Arrays
for (const n of [1, 2, 3]) console.log(n);

// Strings (iterates code points)
console.log([...'hi']); // ['h', 'i']

// Maps (iterates [key, value])
const map = new Map([
  ['a', 1],
  ['b', 2],
]);
for (const [k, v] of map) console.log(k, v);

// Sets (iterates values)
const set = new Set([1, 1, 2]);
console.log([...set]); // [1, 2]

// Typed arrays
console.log([...new Uint8Array([10, 20])]); // [10, 20]

// Arguments (iterable in modern JS), NodeLists in browsers are iterable too

// Not iterable: plain objects
// for (const x of { a: 1 }) {} // TypeError
```

To iterate objects, convert:

```javascript
const obj = { a: 1, b: 2 };
console.log(Object.keys(obj));   // ['a', 'b']
console.log(Object.values(obj)); // [1, 2]
console.log(Object.entries(obj)); // [['a',1], ['b',2]]

for (const [k, v] of Object.entries(obj)) {
  console.log(k, v);
}
```

---

### 3. How `for...of` Works (Protocol)

`for...of` calls the iterable’s `Symbol.iterator` to get an iterator, then repeatedly calls `next()` until `done: true`.

```javascript
const arr = [10, 20];
const it = arr[Symbol.iterator]();

console.log(it.next()); // { value: 10, done: false }
console.log(it.next()); // { value: 20, done: false }
console.log(it.next()); // { value: undefined, done: true }

// `for...of` is basically this loop:
const iter = arr[Symbol.iterator]();
while (true) {
  const { value, done } = iter.next();
  if (done) break;
  console.log(value);
}
```

---

### 4. Creating a Custom Iterable (Manual Iterator)

Example: a `range` that iterates from `start` to `end`.

```javascript
function makeRange(start, end) {
  return {
    [Symbol.iterator]() {
      let current = start;
      return {
        next() {
          if (current <= end) {
            return { value: current++, done: false };
          }
          return { value: undefined, done: true };
        },
      };
    },
  };
}

for (const n of makeRange(3, 6)) {
  console.log(n); // 3, 4, 5, 6
}

console.log([...makeRange(1, 4)]); // [1, 2, 3, 4]
```

**Option: iterable iterator** (iterator is also iterable) — useful for composability.

```javascript
function counter(limit) {
  let i = 0;
  const iterator = {
    [Symbol.iterator]() {
      return iterator;
    },
    next() {
      i += 1;
      if (i <= limit) return { value: i, done: false };
      return { value: undefined, done: true };
    },
  };

  return iterator;
}

console.log([...counter(3)]); // [1, 2, 3]
```

---

### 5. Generator Functions (`function*`) and `yield`

Generators build iterators with far less code.

```javascript
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

const it = gen();
console.log(it.next()); // { value: 1, done: false }
console.log(it.next()); // { value: 2, done: false }
console.log(it.next()); // { value: 3, done: false }
console.log(it.next()); // { value: undefined, done: true }

console.log([...gen()]); // [1, 2, 3]

for (const x of gen()) {
  console.log(x);
}
```

Generator-based `range`:

```javascript
function* range(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}

console.log([...range(5, 8)]); // [5, 6, 7, 8]
```

---

### 6. Sending Values Into Generators (`next(value)`)

You can feed values back into a generator; the argument becomes the result of the paused `yield` expression.

```javascript
function* ask() {
  const name = yield "What is your name?";
  const color = yield `Hi ${name}. Favorite color?`;
  return `${name} likes ${color}`;
}

const it = ask();
console.log(it.next().value);       // "What is your name?"
console.log(it.next("Ada").value); // "Hi Ada. Favorite color?"
console.log(it.next("Blue").value); // undefined (done)

console.log(it.next("ignored")); // { value: undefined, done: true }
```

**Important:** the first `next()` argument is ignored because there is no paused `yield` yet.

---

### 7. Error Handling in Generators (`throw`, `return`)

```javascript
function* worker() {
  try {
    yield "step-1";
    yield "step-2";
  } catch (err) {
    yield `caught: ${err.message}`;
  } finally {
    // runs on completion OR when closed
    // (e.g., break from for...of)
  }

  return "done";
}

const it = worker();
console.log(it.next()); // step-1
console.log(it.throw(new Error("boom"))); // caught: boom
console.log(it.next()); // done

// Closing generator early
function* g() {
  try {
    yield 1;
    yield 2;
  } finally {
    console.log("cleanup");
  }
}

const it2 = g();
console.log(it2.next());
console.log(it2.return("stop")); // triggers finally
// logs: cleanup
```

---

### 8. Delegation With `yield*`

`yield*` delegates to another iterable (often another generator).

```javascript
function* letters() {
  yield "a";
  yield "b";
}

function* numbers() {
  yield 1;
  yield 2;
}

function* combined() {
  yield* letters();
  yield* numbers();
  yield "end";
}

console.log([...combined()]); // ['a','b',1,2,'end']

// yield* over arrays/strings too
function* mixed() {
  yield* [10, 20];
  yield* "hi";
}

console.log([...mixed()]); // [10, 20, 'h', 'i']
```

---

### 9. Generator Patterns (Lazy & Composable)

#### Lazy map/filter pipeline

```javascript
function* mapGen(iterable, fn) {
  for (const x of iterable) {
    yield fn(x);
  }
}

function* filterGen(iterable, predicate) {
  for (const x of iterable) {
    if (predicate(x)) yield x;
  }
}

function* take(iterable, count) {
  let i = 0;
  for (const x of iterable) {
    yield x;
    i++;
    if (i >= count) return;
  }
}

function* naturals() {
  let n = 0;
  while (true) {
    yield n++;
  }
}

const evens = filterGen(naturals(), (n) => n % 2 === 0);
const doubled = mapGen(evens, (n) => n * 2);
console.log([...take(doubled, 5)]); // [0, 4, 8, 12, 16]
```

#### Flattening nested arrays

```javascript
function* flatten(value) {
  if (Array.isArray(value)) {
    for (const item of value) {
      yield* flatten(item);
    }
  } else {
    yield value;
  }
}

console.log([...flatten([1, [2, [3, 4]], 5])]); // [1,2,3,4,5]
```

---

### 10. Async Iterables and Async Generators

Async iteration is for sequences where each step may be asynchronous (streaming data, paginated APIs, reading chunks, etc.).

- **Async iterable**: has `obj[Symbol.asyncIterator]()`.
- **Async iterator**: `.next()` returns a **Promise** of `{ value, done }`.

#### `for await...of`

```javascript
// for await...of works with async iterables
async function demo() {
  for await (const chunk of asyncChunks()) {
    console.log(chunk);
  }
}
```

#### Async generator (`async function*`)

```javascript
const delay = (ms) => new Promise((r) => setTimeout(r, ms));

async function* asyncRange(start, end, ms = 50) {
  for (let i = start; i <= end; i++) {
    await delay(ms);
    yield i;
  }
}

(async () => {
  for await (const n of asyncRange(1, 3, 10)) {
    console.log(n); // 1, 2, 3
  }
})();
```

#### Async pagination pattern

```javascript
async function fetchPage(page) {
  // pretend API
  await new Promise((r) => setTimeout(r, 10));
  if (page > 3) return { items: [], nextPage: null };
  return {
    items: [`item-${page}-a`, `item-${page}-b`],
    nextPage: page + 1,
  };
}

async function* paginate(startPage = 1) {
  let page = startPage;
  while (page != null) {
    const { items, nextPage } = await fetchPage(page);
    for (const item of items) yield item;
    page = nextPage;
  }
}

(async () => {
  const results = [];
  for await (const item of paginate(1)) {
    results.push(item);
  }
  console.log(results);
})();
```

---

## 💡 Real-World Examples

### Example 1: Streaming CSV Parsing (Iterator-Friendly)

```javascript
// A simplified line splitter iterable
function* lines(text) {
  let start = 0;
  while (start < text.length) {
    const end = text.indexOf("\n", start);
    if (end === -1) {
      yield text.slice(start);
      return;
    }
    yield text.slice(start, end);
    start = end + 1;
  }
}

function* csvRows(text) {
  for (const line of lines(text)) {
    if (!line.trim()) continue;
    yield line.split(",");
  }
}

const csv = "id,name\n1,Ada\n2,Linus\n";
for (const row of csvRows(csv)) {
  console.log(row);
}
// ['id','name']
// ['1','Ada']
// ['2','Linus']
```

### Example 2: On-Demand ID Generator

```javascript
function* idGenerator(prefix = "id") {
  let n = 1;
  while (true) {
    yield `${prefix}-${n++}`;
  }
}

const ids = idGenerator("user");
console.log(ids.next().value); // user-1
console.log(ids.next().value); // user-2
```

### Example 3: Backpressure-Friendly Async Processing

```javascript
const delay = (ms) => new Promise((r) => setTimeout(r, ms));

async function* source() {
  for (let i = 1; i <= 5; i++) {
    await delay(10);
    yield i;
  }
}

async function* transform(asyncIterable, fn) {
  for await (const x of asyncIterable) {
    yield fn(x);
  }
}

(async () => {
  for await (const out of transform(source(), (n) => n * n)) {
    // consumer speed controls total throughput
    await delay(30);
    console.log(out);
  }
})();
```

---

## 🎤 Interview Questions

### Q1: What’s the difference between an iterable and an iterator?

**Answer:**

An **iterable** can produce an iterator via `[Symbol.iterator]()` (or `[Symbol.asyncIterator]()` for async). An **iterator** is the object that returns values via `.next()`.

```javascript
const iterable = [1, 2, 3];
const iterator = iterable[Symbol.iterator]();
console.log(iterator.next()); // { value: 1, done: false }
```

---

### Q2: Why does `for...of` work on arrays but not on plain objects?

**Answer:**

Arrays are iterable (have `Symbol.iterator`). Plain objects are not iterable by default.

```javascript
console.log(typeof [][Symbol.iterator]); // function
console.log(typeof ({}[Symbol.iterator])); // undefined

// Use entries
for (const [k, v] of Object.entries({ a: 1 })) {
  console.log(k, v);
}
```

---

### Q3: What does `yield*` do?

**Answer:**

It delegates iteration to another iterable, yielding each of its values.

```javascript
function* a() { yield 1; yield 2; }
function* b() { yield* a(); yield 3; }
console.log([...b()]); // [1,2,3]
```

---

### Q4: How do async generators differ from regular generators?

**Answer:**

Async generators (`async function*`) produce an **async iterator** where `.next()` returns a Promise. You consume them with `for await...of`.

```javascript
async function* g() { yield 1; }
const it = g();
it.next().then(console.log); // { value: 1, done: false }
```

---

### Q5: When are generators a good fit?

**Answer:**

- Lazy sequences (infinite or large data)
- Streaming / chunked processing
- Pipelines (map/filter/take)
- Complex state machines (pause/resume)

They can make code more memory efficient and composable.

---

## 📚 Summary

**Key Takeaways:**
- Iteration is based on protocols: iterable (`Symbol.iterator`) → iterator (`next()`).
- `for...of`, spread, destructuring all use iteration.
- Generators (`function*`) are the simplest way to create iterators.
- `yield*` delegates to another iterable.
- Async iterables (`Symbol.asyncIterator`) + `for await...of` help model streaming async data.

---

**Next Topic:** [16. Regex](./16-regex.md)
