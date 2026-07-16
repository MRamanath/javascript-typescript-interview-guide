# 33. Custom Promise Functions from Scratch

> A concise, interview-focused guide to Promise combinators, async utilities, and a learning-only Promise implementation in modern JavaScript.

## Overview

These implementations are **educational**, not replacements for native `Promise` APIs. They model Promises/A+-style resolution and chaining where useful, while the combinator polyfills use the native `Promise` as their constructor and scheduling primitive.

### Assumptions and guarantees

- Inputs are general **iterables** where the corresponding native API accepts an iterable.
- `Array.from(iterable)` runs inside each combinator executor, so iteration failures reject the returned promise.
- Results preserve input order even when operations finish out of order.
- `Promise.resolve` assimilates promises and thenables.
- Non-iterables reject naturally with the `TypeError` thrown by `Array.from`.
- Simplified implementations are identified as such; none is claimed to be fully ECMAScript-spec-complete.

## Table of Contents

- [Before Promises: callbacks and callback hell](#before-promises-callbacks-and-callback-hell)
- [XMLHttpRequest and Fetch](#xmlhttprequest-and-fetch)
- [Promise essentials](#promise-essentials)
- [Promise combinators](#promise-combinators)
- [Resolution and timing helpers](#resolution-and-timing-helpers)
- [Retries, cancellation, and adapters](#retries-cancellation-and-adapters)
- [Concurrency, sequencing, and caching](#concurrency-sequencing-and-caching)
- [How finally passes through](#how-finally-passes-through)
- [Educational CustomPromise](#educational-custompromise)
- [Output-order questions](#output-order-questions)
- [Decision table](#decision-table)
- [Common mistakes](#common-mistakes)
- [Interview questions and answers](#interview-questions-and-answers)

---

## Before Promises: Callbacks and Callback Hell

Before promises became standard, asynchronous APIs commonly accepted a callback to run later. Node.js popularized the **error-first callback** convention: `(error, result) => {}`.

### Basic error-first callback

```javascript
function readUser(id, callback) {
  setTimeout(() => {
    if (!Number.isInteger(id) || id < 1) {
      callback(new TypeError('id must be a positive integer'));
      return;
    }

    callback(null, { id, name: 'Ada' });
  }, 100);
}

readUser(1, (error, user) => {
  if (error) {
    console.error(error);
    return;
  }

  console.log(user);
});
```

A callback is not inherently bad. It is still appropriate for repeated events such as button clicks or streams. Problems arise when one-shot asynchronous operations must be composed manually.

### Callback hell: the old nested style

```javascript
getUser(userId, (userError, user) => {
  if (userError) return handleError(userError);

  getOrders(user.id, (ordersError, orders) => {
    if (ordersError) return handleError(ordersError);

    getPayment(orders[0].id, (paymentError, payment) => {
      if (paymentError) return handleError(paymentError);
      console.log({ user, orders, payment });
    });
  });
});
```

This “pyramid of doom” causes deep nesting, repeated error handling, inversion of control, and difficult composition.

### The same flow with Promises

```javascript
getUserPromise(userId)
  .then((user) =>
    getOrdersPromise(user.id).then((orders) => ({ user, orders }))
  )
  .then(({ user, orders }) =>
    getPaymentPromise(orders[0].id)
      .then((payment) => ({ user, orders, payment }))
  )
  .then(console.log)
  .catch(handleError);
```

### The same flow with `async`/`await`

```javascript
async function loadSummary(userId) {
  try {
    const user = await getUserPromise(userId);
    const orders = await getOrdersPromise(user.id);
    const payment = await getPaymentPromise(orders[0].id);
    return { user, orders, payment };
  } catch (error) {
    handleError(error);
    throw error;
  }
}
```

`async`/`await` is syntax built on promises; it does not replace the Promise model or make operations synchronous.

### Convert an error-first callback to a Promise

```javascript
function readUserPromise(id) {
  return new Promise((resolve, reject) => {
    readUser(id, (error, user) => {
      if (error) reject(error);
      else resolve(user);
    });
  });
}

readUserPromise(1)
  .then(console.log)
  .catch(console.error);
```

Settle once and `return` after rejecting when later callback code could continue. Use the reusable `promisify` helper later in this chapter for standard Node-style functions.

---

## XMLHttpRequest and Fetch

Both APIs perform HTTP requests in browsers. `XMLHttpRequest` is the older event/callback-based API. `fetch` is the modern promise-based API.

### Old approach: `XMLHttpRequest` with callbacks

```javascript
function getJsonXHR(url, callback) {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.responseType = 'json';

  xhr.onload = () => {
    if (xhr.status >= 200 && xhr.status < 300) {
      callback(null, xhr.response);
    } else {
      callback(new Error(`HTTP ${xhr.status}`));
    }
  };

  xhr.onerror = () => callback(new TypeError('Network request failed'));
  xhr.ontimeout = () => callback(new Error('Request timed out'));
  xhr.timeout = 5_000;
  xhr.send();
}

getJsonXHR('/api/users/1', (error, user) => {
  if (error) return console.error(error);
  console.log(user);
});
```

`XMLHttpRequest` provides upload/download progress events and explicit timeout support, but its event-driven interface is harder to compose.

### Wrap `XMLHttpRequest` in a Promise

```javascript
function getJsonXHRPromise(url, { signal, timeout = 5_000 } = {}) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.responseType = 'json';
    xhr.timeout = timeout;

    const abort = () => xhr.abort();
    signal?.addEventListener('abort', abort, { once: true });
    const cleanup = () => signal?.removeEventListener('abort', abort);

    xhr.onload = () => {
      cleanup();
      if (xhr.status >= 200 && xhr.status < 300) resolve(xhr.response);
      else reject(new Error(`HTTP ${xhr.status}`));
    };
    xhr.onerror = () => {
      cleanup();
      reject(new TypeError('Network request failed'));
    };
    xhr.ontimeout = () => {
      cleanup();
      reject(new Error('Request timed out'));
    };
    xhr.onabort = () => {
      cleanup();
      reject(signal?.reason ?? new DOMException('Aborted', 'AbortError'));
    };

    if (signal?.aborted) {
      cleanup();
      reject(signal.reason ?? new DOMException('Aborted', 'AbortError'));
      return;
    }

    xhr.send();
  });
}
```

This is an adapter: the underlying XHR remains event-based, while callers receive a composable promise.

### Modern approach: `fetch`

```javascript
async function getJson(url, { signal } = {}) {
  const response = await fetch(url, {
    method: 'GET',
    headers: { Accept: 'application/json' },
    signal
  });

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.json();
}

const controller = new AbortController();

getJson('/api/users/1', { signal: controller.signal })
  .then(console.log)
  .catch((error) => {
    if (error.name !== 'AbortError') console.error(error);
  });
```

Important `fetch` behavior:

- It rejects for network failures, malformed URLs, and cancellation—not merely because the server returned `404` or `500`.
- Check `response.ok` or `response.status` explicitly for HTTP errors.
- Body readers such as `response.json()` are asynchronous and can reject, including for invalid JSON.
- A response body is normally consumed once; clone the response first if it must be read more than once.
- Cancellation uses `AbortController`; a timeout wrapper alone does not stop the request.
- Browser same-origin and CORS rules apply to both XHR and `fetch`.

### Sequential versus concurrent Fetch requests

```javascript
// Sequential: the second URL depends on the first result.
const user = await getJson('/api/users/1');
const orders = await getJson(`/api/users/${user.id}/orders`);

// Concurrent: requests are independent.
const [profile, settings] = await Promise.all([
  getJson('/api/profile'),
  getJson('/api/settings')
]);
```

Start independent requests together. Await them sequentially only when there is a dependency, ordering requirement, or resource constraint.

### XHR versus Fetch

| Feature | `XMLHttpRequest` | `fetch` |
|---|---|---|
| Programming model | Events and callbacks | Promises |
| HTTP 4xx/5xx | `load` event; inspect status | Fulfilled promise; inspect `ok`/status |
| Cancellation | `xhr.abort()` | `AbortController` |
| Built-in timeout | `xhr.timeout` | Use abort/timer logic |
| Upload progress | Supported | Not generally exposed by standard Fetch upload APIs |
| Streaming response | Limited/event-based | `ReadableStream` where supported |
| Service Worker integration | No | Yes |

---

## Promise Essentials

A promise is **pending**, **fulfilled** with a value, or **rejected** with a reason. Fulfillment and rejection are both settlement; settlement happens once and cannot be reversed.

```javascript
const promise = new Promise((resolve) => {
  console.log('executor');        // synchronous
  resolve(42);
});

promise.then(console.log);        // handler runs in a microtask
console.log('after constructor');
// executor, after constructor, 42
```

Key rules:

- The executor runs immediately and synchronously. Throwing inside it rejects the promise.
- `.then`, `.catch`, and `.finally` handlers run asynchronously as microtasks.
- `.then` returns a new promise. Returning a value fulfills it; throwing rejects it; returning a promise or thenable makes it adopt that result.
- A **thenable** is an object with a callable `then`. `Promise.resolve(thenable)` safely assimilates it.
- Each link should be returned or awaited. Missing `return` commonly breaks sequencing and error propagation.

---

## Promise Combinators

### `myPromiseAll(iterable)`

Fulfills when all inputs fulfill, preserves input order, and rejects on the first rejection.

```javascript
function myPromiseAll(iterable) {
  return new Promise((resolve, reject) => {
    let values;
    try {
      values = Array.from(iterable);
    } catch (error) {
      reject(error);
      return;
    }

    if (values.length === 0) {
      resolve([]);
      return;
    }

    const results = new Array(values.length);
    let remaining = values.length;

    values.forEach((value, index) => {
      Promise.resolve(value).then(
        (result) => {
          results[index] = result;
          if (--remaining === 0) resolve(results);
        },
        reject
      );
    });
  });
}
```

```javascript
const slow = new Promise((resolve) => setTimeout(() => resolve('slow'), 20));
const fast = Promise.resolve('fast');

myPromiseAll(new Set([slow, fast, 3])).then(console.log);
// ['slow', 'fast', 3] — input order, not completion order

myPromiseAll([]).then(console.log); // []
```

**Edges:** empty input fulfills with `[]`; plain values and thenables are accepted; any rejection rejects the aggregate.

### `myPromiseAllSettled(iterable)`

Waits for every input and never rejects because an input rejects.

```javascript
function myPromiseAllSettled(iterable) {
  return new Promise((resolve, reject) => {
    let values;
    try {
      values = Array.from(iterable);
    } catch (error) {
      reject(error);
      return;
    }

    if (values.length === 0) {
      resolve([]);
      return;
    }

    const results = new Array(values.length);
    let remaining = values.length;
    const record = (index, result) => {
      results[index] = result;
      if (--remaining === 0) resolve(results);
    };

    values.forEach((value, index) => {
      Promise.resolve(value).then(
        (fulfilledValue) => record(index, { status: 'fulfilled', value: fulfilledValue }),
        (reason) => record(index, { status: 'rejected', reason })
      );
    });
  });
}
```

```javascript
myPromiseAllSettled([Promise.resolve(1), Promise.reject('no')])
  .then(console.log);
// [{ status: 'fulfilled', value: 1 }, { status: 'rejected', reason: 'no' }]
```

**Edges:** empty input fulfills with `[]`; iteration itself can still reject before inputs are processed.

### `myPromiseRace(iterable)`

Adopts the first input to settle, whether fulfilled or rejected.

```javascript
function myPromiseRace(iterable) {
  return new Promise((resolve, reject) => {
    let values;
    try {
      values = Array.from(iterable);
    } catch (error) {
      reject(error);
      return;
    }

    for (const value of values) {
      Promise.resolve(value).then(resolve, reject);
    }
  });
}
```

```javascript
myPromiseRace([
  new Promise((resolve) => setTimeout(() => resolve('later'), 20)),
  Promise.resolve('first')
]).then(console.log); // first
```

**Edge:** an empty iterable leaves the returned promise pending forever.

### `myPromiseAny(iterable)`

Fulfills on the first fulfillment. It rejects only when every input rejects.

```javascript
function myPromiseAny(iterable) {
  return new Promise((resolve, reject) => {
    let values;
    try {
      values = Array.from(iterable);
    } catch (error) {
      reject(error);
      return;
    }

    if (values.length === 0) {
      reject(new AggregateError([], 'All promises were rejected'));
      return;
    }

    const errors = new Array(values.length);
    let remaining = values.length;

    values.forEach((value, index) => {
      Promise.resolve(value).then(resolve, (error) => {
        errors[index] = error;
        if (--remaining === 0) {
          reject(new AggregateError(errors, 'All promises were rejected'));
        }
      });
    });
  });
}
```

```javascript
myPromiseAny([Promise.reject('A'), Promise.resolve('B')])
  .then(console.log); // B

myPromiseAny([]).catch((error) => {
  console.log(error instanceof AggregateError, error.errors);
}); // true []
```

**Edges:** rejection reasons in `AggregateError.errors` preserve input order; empty input rejects with an `AggregateError`.

---

## Resolution and Timing Helpers

### `customPromiseResolve(value)` and `customPromiseReject(reason)`

```javascript
function customPromiseResolve(value) {
  return new Promise((resolve) => resolve(value));
}

function customPromiseReject(reason) {
  return new Promise((_, reject) => reject(reason));
}
```

```javascript
customPromiseResolve({ then: (resolve) => resolve(7) }).then(console.log); // 7
customPromiseReject(new Error('failed')).catch((error) => console.log(error.message));
// failed
```

The wrapper assimilates thenables, but unlike native `Promise.resolve`, it does **not** preserve identity:

```javascript
const original = Promise.resolve(1);
console.log(customPromiseResolve(original) === original); // false
console.log(Promise.resolve(original) === original);       // true
```

Exact native identity behavior requires constructor-aware logic: the input's promise constructor must match the receiver constructor. This educational wrapper intentionally omits that machinery.

### `delay(ms, value, { signal })`

```javascript
function abortReason(signal) {
  return signal.reason ?? new DOMException('Aborted', 'AbortError');
}

function delay(ms, value, { signal } = {}) {
  return new Promise((resolve, reject) => {
    if (signal?.aborted) {
      reject(abortReason(signal));
      return;
    }

    const timer = setTimeout(() => {
      signal?.removeEventListener('abort', onAbort);
      resolve(value);
    }, ms);

    function onAbort() {
      clearTimeout(timer);
      reject(abortReason(signal));
    }

    signal?.addEventListener('abort', onAbort, { once: true });
  });
}
```

```javascript
await delay(20, 'ready'); // 'ready'

const controller = new AbortController();
const waiting = delay(1000, 'late', { signal: controller.signal });
controller.abort(new Error('stopped'));
waiting.catch((error) => console.log(error.message)); // stopped
```

### `withTimeout(promise, ms)`

```javascript
function withTimeout(promise, ms) {
  let timer;
  const timeout = new Promise((_, reject) => {
    timer = setTimeout(() => reject(new Error(`Timed out after ${ms}ms`)), ms);
  });

  return Promise.race([Promise.resolve(promise), timeout])
    .finally(() => clearTimeout(timer));
}
```

```javascript
await withTimeout(delay(10, 'done'), 100); // done
```

The timer is cleaned up after either outcome. A timeout rejects only the wrapper; it does **not** cancel the underlying operation. Pass an `AbortSignal` to that operation when real cancellation is required.

---

## Retries, Cancellation, and Adapters

### `retry(operation, options)`

```javascript
async function retry(operation, {
  attempts = 3,
  baseDelay = 100,
  factor = 2,
  signal,
  shouldRetry = () => true
} = {}) {
  if (!Number.isInteger(attempts) || attempts < 1) {
    throw new RangeError('attempts must be a positive integer');
  }

  for (let attempt = 1; attempt <= attempts; attempt++) {
    if (signal?.aborted) throw abortReason(signal);

    try {
      return await Promise.resolve().then(() => operation({ attempt, signal }));
    } catch (error) {
      if (signal?.aborted) throw abortReason(signal);
      if (attempt === attempts || !shouldRetry(error, attempt)) throw error;
      await delay(baseDelay * factor ** (attempt - 1), undefined, { signal });
    }
  }
}
```

```javascript
let calls = 0;
const value = await retry(
  () => {
    calls++;
    if (calls < 3) throw new Error('temporary');
    return 'ok';
  },
  { attempts: 4, baseDelay: 10 }
);
console.log(value, calls); // ok 3
```

Retry only intended **transient** failures and operations that are idempotent or protected by idempotency keys. Production retry policies normally add randomized jitter, cap the delay, classify errors, and honor server retry hints.

### `promisify(nodeStyleFunction)`

```javascript
function promisify(nodeStyleFunction) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      nodeStyleFunction.call(this, ...args, (error, value) => {
        if (error) reject(error);
        else resolve(value);
      });
    });
  };
}
```

```javascript
const service = {
  prefix: 'ID-',
  lookup(id, callback) {
    callback(null, this.prefix + id);
  },
  lookupAsync: null
};
service.lookupAsync = promisify(service.lookup);
console.log(await service.lookupAsync(7)); // ID-7
```

Returning a normal function and using `.call(this, ...)` preserves the receiver when the promisified function is called as a method. This compact version assumes the callback returns one success value; some Node APIs have custom promisification behavior or multiple success values.

---

## Concurrency, Sequencing, and Caching

### `mapWithConcurrency(items, limit, mapper)`

```javascript
async function mapWithConcurrency(items, limit, mapper) {
  if (!Number.isInteger(limit) || limit < 1) {
    throw new RangeError('limit must be a positive integer');
  }

  const values = Array.from(items);
  const results = new Array(values.length);
  let nextIndex = 0;

  async function worker() {
    while (true) {
      const index = nextIndex++;
      if (index >= values.length) return;
      results[index] = await mapper(values[index], index);
    }
  }

  const workerCount = Math.min(limit, values.length);
  await Promise.all(Array.from({ length: workerCount }, worker));
  return results;
}
```

```javascript
const results = await mapWithConcurrency([30, 10, 20], 2, async (ms) => {
  await delay(ms);
  return ms / 10;
});
console.log(results); // [3, 1, 2]
```

At most `limit` mapper calls are in flight. Results preserve input order, and synchronous mapper throws become rejections through the async worker. Already-started work is not automatically cancelled after one worker fails.

### `sequential(tasks)`

```javascript
async function sequential(tasks) {
  const results = [];
  for (const task of Array.from(tasks)) {
    results.push(await task());
  }
  return results;
}
```

```javascript
const results = await sequential([
  async () => 1,
  async () => 2
]);
console.log(results); // [1, 2]
```

Tasks start one at a time. The first failure stops the sequence.

### `deferred()` / `withResolvers`-style helper

```javascript
function deferred() {
  let resolve;
  let reject;
  const promise = new Promise((res, rej) => {
    resolve = res;
    reject = rej;
  });
  return { promise, resolve, reject };
}
```

```javascript
const gate = deferred();
gate.promise.then(console.log);
gate.resolve('open'); // open
```

Prefer native `Promise.withResolvers()` where available. Deferred objects are useful at integration boundaries, but exposing settlement broadly can make ownership and control flow unclear.

### `memoizeAsync(fn)`

```javascript
function memoizeAsync(fn, keyFor = (...args) => JSON.stringify(args)) {
  const cache = new Map();

  return function (...args) {
    const key = keyFor(...args);
    if (cache.has(key)) return cache.get(key);

    const promise = Promise.resolve().then(() => fn.apply(this, args));
    cache.set(key, promise);
    promise.catch(() => {
      if (cache.get(key) === promise) cache.delete(key);
    });
    return promise;
  };
}
```

```javascript
let loads = 0;
const load = memoizeAsync(async (id) => {
  loads++;
  return { id };
});

const [a, b] = await Promise.all([load(1), load(1)]);
console.log(a, b, loads); // { id: 1 } { id: 1 } 1
```

The same in-flight promise is shared, and rejected promises are evicted so a later call can retry. Real caches need stable collision-free keys, receiver-aware keys when relevant, size/TTL eviction, and a policy for mutable results.

---

## How `finally` Passes Through

`finally` runs for either outcome. Its callback receives no value or reason. Unless that callback throws or returns a rejected thenable, the original outcome passes through.

```javascript
function promiseFinally(promise, onFinally) {
  return Promise.resolve(promise).then(
    (value) => Promise.resolve(onFinally()).then(() => value),
    (reason) => Promise.resolve(onFinally()).then(() => { throw reason; })
  );
}
```

```javascript
await promiseFinally(Promise.resolve(5), () => console.log('cleanup')); // 5
await promiseFinally(Promise.reject('original'), () => Promise.reject('cleanup'))
  .catch(console.log); // cleanup
```

A cleanup failure replaces the original outcome, matching native `finally` pass-through behavior.

---

## Educational `CustomPromise`

> **Learning exercise only:** use native promises in production. This compact class demonstrates single settlement, thenable assimilation, asynchronous handlers, multiple subscribers, chaining, and chaining-cycle rejection. It is not a complete ECMAScript `Promise` implementation.

```javascript
class CustomPromise {
  #state = 'pending';
  #value;
  #handlers = [];
  #locked = false;

  constructor(executor) {
    if (typeof executor !== 'function') {
      throw new TypeError('executor must be a function');
    }

    const resolve = (value) => {
      if (this.#locked) return;
      this.#locked = true;
      this.#resolve(value);
    };
    const reject = (reason) => {
      if (this.#locked) return;
      this.#locked = true;
      this.#settle('rejected', reason);
    };

    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }

  #resolve(value) {
    if (this.#state !== 'pending') return;
    if (value === this) {
      this.#settle('rejected', new TypeError('Chaining cycle detected'));
      return;
    }

    if (value !== null && (typeof value === 'object' || typeof value === 'function')) {
      let then;
      try {
        then = value.then;
      } catch (error) {
        this.#settle('rejected', error);
        return;
      }

      if (typeof then === 'function') {
        let called = false;
        try {
          then.call(
            value,
            (next) => {
              if (called) return;
              called = true;
              this.#resolve(next);
            },
            (reason) => {
              if (called) return;
              called = true;
              this.#settle('rejected', reason);
            }
          );
        } catch (error) {
          if (!called) {
            called = true;
            this.#settle('rejected', error);
          }
        }
        return;
      }
    }

    this.#settle('fulfilled', value);
  }

  #settle(state, value) {
    if (this.#state !== 'pending') return;
    this.#state = state;
    this.#value = value;
    const handlers = this.#handlers;
    this.#handlers = [];
    for (const handler of handlers) this.#run(handler);
  }

  #run(handler) {
    queueMicrotask(() => {
      const callback = this.#state === 'fulfilled'
        ? handler.onFulfilled
        : handler.onRejected;

      if (typeof callback !== 'function') {
        (this.#state === 'fulfilled' ? handler.resolve : handler.reject)(this.#value);
        return;
      }

      try {
        handler.resolve(callback(this.#value));
      } catch (error) {
        handler.reject(error);
      }
    });
  }

  then(onFulfilled, onRejected) {
    return new CustomPromise((resolve, reject) => {
      const handler = { onFulfilled, onRejected, resolve, reject };
      if (this.#state === 'pending') this.#handlers.push(handler);
      else this.#run(handler);
    });
  }

  catch(onRejected) {
    return this.then(undefined, onRejected);
  }

  finally(onFinally) {
    const callback = typeof onFinally === 'function' ? onFinally : () => undefined;
    return this.then(
      (value) => CustomPromise.resolve(callback()).then(() => value),
      (reason) => CustomPromise.resolve(callback()).then(() => { throw reason; })
    );
  }

  static resolve(value) {
    if (value instanceof CustomPromise) return value;
    return new CustomPromise((resolve) => resolve(value));
  }

  static reject(reason) {
    return new CustomPromise((_, reject) => reject(reason));
  }

  static all(iterable) {
    return new CustomPromise((resolve, reject) => {
      let values;
      try {
        values = Array.from(iterable);
      } catch (error) {
        reject(error);
        return;
      }

      if (values.length === 0) {
        resolve([]);
        return;
      }

      const results = new Array(values.length);
      let remaining = values.length;
      values.forEach((value, index) => {
        CustomPromise.resolve(value).then((result) => {
          results[index] = result;
          if (--remaining === 0) resolve(results);
        }, reject);
      });
    });
  }

  static race(iterable) {
    return new CustomPromise((resolve, reject) => {
      let values;
      try {
        values = Array.from(iterable);
      } catch (error) {
        reject(error);
        return;
      }

      for (const value of values) {
        CustomPromise.resolve(value).then(resolve, reject);
      }
    });
  }
}
```

```javascript
const base = new CustomPromise((resolve) => resolve(2));
base.then((value) => value * 3).then(console.log); // 6
base.then((value) => console.log('subscriber A', value));
base.then((value) => console.log('subscriber B', value));

CustomPromise.resolve({ then: (resolve) => resolve(9) }).then(console.log); // 9
CustomPromise.all([CustomPromise.resolve(1), 2]).then(console.log); // [1, 2]

let cycle;
cycle = CustomPromise.resolve('x').then(() => cycle);
cycle.catch((error) => console.log(error instanceof TypeError)); // true
```

Important omissions include subclass species behavior, constructor-generic static methods, exact ECMAScript job/realm semantics, host unhandled-rejection tracking, native branding, and every iterator-closing/species edge case. The class uses `queueMicrotask`, which provides the intended learning-level asynchronous reaction behavior but is not the specification's internal PromiseJobs implementation.

---

## Output-Order Questions

Assume each example runs independently.

### 1. Executor versus handler

```javascript
console.log('A');
new Promise((resolve) => {
  console.log('B');
  resolve();
}).then(() => console.log('C'));
console.log('D');
```

**Exact output:**

```text
A
B
D
C
```

The executor is synchronous; the reaction is a microtask.

### 2. Microtasks before timers

```javascript
setTimeout(() => console.log('timer'), 0);
Promise.resolve().then(() => console.log('promise'));
queueMicrotask(() => console.log('microtask'));
console.log('sync');
```

**Exact output:**

```text
sync
promise
microtask
timer
```

Microtasks run FIFO before the next timer task.

### 3. A chained reaction is queued later

```javascript
Promise.resolve()
  .then(() => console.log('A'))
  .then(() => console.log('B'));
Promise.resolve().then(() => console.log('C'));
```

**Exact output:**

```text
A
C
B
```

The handler for `B` is queued only after the first handler settles its returned promise.

### 4. Missing return

```javascript
Promise.resolve(1)
  .then((value) => {
    Promise.resolve(value + 1);
  })
  .then((value) => console.log(value));
```

**Exact output:**

```text
undefined
```

The first handler returns `undefined`; the inner promise is detached.

### 5. `await` yields to the microtask queue

```javascript
async function run() {
  console.log('A');
  await 0;
  console.log('B');
}

console.log('C');
run();
Promise.resolve().then(() => console.log('D'));
console.log('E');
```

**Exact output:**

```text
C
A
E
B
D
```

The `await` continuation is queued before the later `.then` reaction.

### 6. `finally` pass-through

```javascript
Promise.resolve('value')
  .finally(() => console.log('cleanup'))
  .then(console.log);
console.log('sync');
```

**Exact output:**

```text
sync
cleanup
value
```

The fulfillment value passes through after cleanup completes.

---

## Decision Table

| Need | Choose | Empty iterable | Failure behavior | Ordering |
|---|---|---|---|---|
| Every result, fail fast | `Promise.all` | Fulfills with `[]` | First rejection rejects | Input order |
| Every outcome for reporting | `Promise.allSettled` | Fulfills with `[]` | Input rejections become records | Input order |
| First settlement | `Promise.race` | Remains pending | First settlement wins | Not applicable |
| First successful result | `Promise.any` | Rejects with `AggregateError` | Rejects after all reject | Errors in input order |
| One task at a time | `sequential` | Fulfills with `[]` | Stops at first failure | Input order |
| Bounded concurrent mapping | `mapWithConcurrency` | Fulfills with `[]` | Rejects on a mapper failure | Results in input order |
| Deadline wrapper | `withTimeout` | Not applicable | Timeout rejects wrapper only | First settlement |
| Transient failure recovery | `retry` | Not applicable | Final/intended non-retryable error escapes | Attempt order |
| Deduplicate identical async work | `memoizeAsync` | Not applicable | Rejected entries are evicted | Not applicable |

---

## Common Mistakes

1. **Using `forEach(async () => ...)` and awaiting nothing.** Use `Promise.all(items.map(...))`, `for...of`, or bounded workers.
2. **Forgetting `return` in `.then`.** The next link receives `undefined` and does not wait.
3. **Wrapping an existing promise unnecessarily.** `new Promise((resolve, reject) => existing.then(resolve, reject))` adds complexity without value.
4. **Assuming `race` or a timeout cancels losers.** Cancellation requires cooperation, commonly through `AbortController`.
5. **Retrying every error.** Authentication, validation, and most permanent failures should fail immediately.
6. **Retrying unsafe writes.** Use idempotent operations or idempotency keys to avoid duplicate effects.
7. **Losing input order in combinators.** Store each result by its original index.
8. **Ignoring thenables.** Normalize with `Promise.resolve` rather than checking only `instanceof Promise`.
9. **Not handling empty iterables.** `all`/`allSettled` fulfill, `any` rejects, and `race` stays pending.
10. **Forgetting synchronous throws.** Start user callbacks through an async function or `Promise.resolve().then(...)` when normalization matters.
11. **Leaking listeners or timers.** Remove abort listeners and clear timeout handles after settlement.
12. **Caching rejected promises forever.** Evict failures when later retries should be possible.
13. **Creating unbounded concurrency.** A large `Promise.all` can overload memory, sockets, APIs, or databases.
14. **Swallowing errors in `catch`.** A returned value recovers the chain; rethrow when recovery is not intended.
15. **Calling a custom Promise implementation production-ready.** Full conformance involves many constructor, realm, iterator, host-hook, and subclass details.

### Explicit async `forEach` anti-pattern

```javascript
// Wrong: the outer function does not wait for these callbacks.
items.forEach(async (item) => {
  await save(item);
});

// Concurrent and awaited:
await Promise.all(items.map((item) => save(item)));

// Sequential and awaited:
for (const item of items) {
  await save(item);
}
```

---

## Interview Questions and Answers

These are high-frequency, MNC-style questions; no claim is made that a specific company asks an exact question.

### 1. How is a Promise better than a callback?

A promise standardizes one future outcome, composition, chaining, and error propagation. Callbacks remain useful for repeated events, but deeply nested one-shot callbacks are harder to compose and reason about.

### 2. When does a Promise executor run?

Synchronously during construction. Only registered reactions run asynchronously.

### 3. What is the microtask queue?

It holds promise reactions, `queueMicrotask` callbacks, and related jobs. The runtime drains microtasks after the current stack and before moving to the next task such as a timer.

### 4. What does `await` do?

It pauses only the containing async function, assimilates the awaited value like `Promise.resolve`, and schedules the continuation as a microtask. It does not block the thread.

### 5. Why prefer chaining over nesting?

Chaining keeps one linear sequence and one error path. Nesting can create detached work and require separate error handling.

### 6. What happens when a `.then` handler returns a value?

The promise returned by `.then` fulfills with that value. If it returns a promise or thenable, the new promise adopts its eventual outcome.

### 7. What happens when a `.then` handler omits `return`?

It returns `undefined`, so the next promise fulfills with `undefined` without waiting for any detached async operation started inside.

### 8. How do errors propagate through a chain?

A thrown error or rejection skips fulfillment handlers until a rejection handler handles it. A rejection handler that throws creates a new rejection.

### 9. Does `.catch` always leave the chain rejected?

No. `.catch(handler)` is `.then(undefined, handler)`. Returning normally recovers the chain; throwing or returning a rejected thenable keeps it rejected.

### 10. What are `finally` semantics?

It runs after fulfillment or rejection, receives no outcome argument, and normally passes the original outcome through. A throw or rejected return from cleanup replaces that outcome.

### 11. Compare `all`, `race`, `allSettled`, and `any`.

`all` needs every fulfillment; `race` takes the first settlement; `allSettled` records every outcome; `any` takes the first fulfillment and rejects only if all reject.

### 12. What happens for empty combinator input?

`all([])` and `allSettled([])` fulfill with `[]`; `any([])` rejects with an `AggregateError`; `race([])` remains pending.

### 13. Do combinator results use completion order?

`all`, `allSettled`, and `AggregateError.errors` from `any` retain input order. `race` and successful `any` are determined by settlement timing.

### 14. How can Promise-based work be cancelled?

Promises have no universal cancel operation. Pass an `AbortSignal` into a cooperative API, listen for abort, stop the underlying work, clean resources, and reject with the signal's reason or an `AbortError`.

### 15. What is concurrency versus parallelism?

Concurrency means tasks overlap in progress; parallelism means work literally executes simultaneously. JavaScript can coordinate concurrent I/O on one main thread while workers or native subsystems may run work in parallel.

### 16. Why does `forEach(async ...)` not wait?

`forEach` ignores callback return values and returns `undefined`. Use `Promise.all` over `map` for concurrency or `for...of` with `await` for sequencing.

### 17. When should work be sequential rather than concurrent?

Use sequencing for dependencies, ordering constraints, transactions, rate limits, or shared mutable state. Use bounded concurrency for independent operations when unlimited concurrency would overload resources.

### 18. Does `Promise.race` implement cancellation?

No. Losing operations continue unless separately cancelled. This is why a timeout race alone does not stop a network request.

### 19. What is an unhandled rejection?

It is a rejection with no timely rejection handler. Hosts may log warnings, emit events, or terminate according to runtime policy; handle or deliberately propagate every promise.

### 20. What is a thenable?

Any object or function with a callable `then`. Promise resolution reads and invokes it defensively, adopting the first resolve/reject call and ignoring later calls.

### 21. When is retry safe?

For selected transient failures and operations that are naturally idempotent or use an idempotency key. Blindly retrying writes can duplicate side effects.

### 22. Why use exponential backoff?

Increasing delays reduce pressure on a failing dependency. A common delay is `baseDelay * factor ** (attempt - 1)`, usually with a maximum cap.

### 23. Why add jitter to retries?

Randomization prevents many clients from retrying simultaneously, reducing synchronized retry storms. Full, equal, and decorrelated jitter are common policies.

### 24. What memory risks arise from async memoization?

Unbounded keys retain promises and results indefinitely; large argument serialization also costs memory and CPU. Add TTL/LRU limits, stable keys, rejection eviction, and explicit invalidation.

### 25. What is the Promise constructor anti-pattern?

Wrapping an API that already returns a promise in `new Promise` unnecessarily. Return or await the existing promise; use the constructor mainly to adapt callback/event APIs.

### 26. What does an async function always return?

A native promise. A returned value fulfills it, a thrown error rejects it, and a returned thenable is assimilated.

### 27. Why track an original index in `Promise.all`?

Completion order is nondeterministic, but the API promises input-order results. Each reaction must write into its assigned slot.

### 28. How does a custom Promise prevent multiple settlement?

It transitions only from pending and ignores later attempts. Thenable assimilation also uses a per-thenable guard because hostile thenables may call both callbacks or throw after resolving.

### 29. How is a chaining cycle detected?

If resolving a promise with itself, reject with a `TypeError`. In a `.then` chain, returning the same promise being created reaches that self-resolution check.

### 30. Why schedule custom handlers asynchronously?

Promises guarantee that reactions do not run in the current call stack. Scheduling also allows handlers registered before and after settlement to behave consistently.

### 31. Why is implementing native `Promise.resolve` exactly difficult?

Identity reuse depends on constructor matching, and full behavior involves receiver constructors, subclassing/species-related semantics, realms, and precise resolution jobs. A simple wrapper can assimilate thenables but cannot reproduce every native detail.

### 32. What design choices matter in a concurrency mapper?

Validate the limit, allocate only bounded workers, claim each index once, await synchronous or async mapper results, retain result order, define failure/cancellation behavior, and avoid launching more work after a known failure when strict stopping is required.

### 33. Why can `Promise.all` cause resource pressure?

It attaches to all supplied operations immediately; if those operations were eagerly created, they may all be running. A worker pool limits in-flight work and provides backpressure.

### 34. Should rejection reasons always be `Error` objects?

JavaScript allows any reason, but `Error` objects preserve stack and metadata and are preferable. When wrapping, use `new Error(message, { cause })` where adding context without losing the original cause is useful.

### 35. What is callback hell?

It is deeply nested asynchronous callback code with duplicated error handling and difficult control flow. Named functions can reduce nesting, while promises and `async`/`await` provide stronger composition and centralized propagation.

### 36. What is an error-first callback?

It is the Node.js convention `callback(error, result)`. On failure, `error` is usually an `Error`; on success, it is `null` and the result follows. The callback should be invoked exactly once.

### 37. Are callbacks obsolete after Promises?

No. Callbacks are natural for repeated events, visitors, array operations, and streaming notifications. Promises specifically model one eventual settlement and are better for composing one-shot asynchronous results.

### 38. Does `fetch` reject for HTTP `404` or `500` responses?

Normally no. The promise fulfills with a `Response`; inspect `response.ok` or `response.status` and throw when the application treats that status as failure.

### 39. How do XHR and Fetch differ?

XHR is event/callback-based and has built-in timeout and progress events. Fetch is promise-based, supports modern request/response and streaming APIs, integrates with service workers, and uses `AbortController` for cancellation.

### 40. How should a callback API be converted to a Promise?

Create one promise, invoke the callback API inside its executor, reject for the callback error, and resolve with the result. Preserve the receiver when required, guard unusual multi-callback APIs, and do not promisify APIs that represent repeated events as though they had one result.

### 41. Why can `response.json()` fail after `fetch` fulfills?

HTTP headers may have arrived successfully while reading the body later fails, or the body may not contain valid JSON. `response.json()` returns a separate promise and must be awaited or returned.

### 42. Does racing a Fetch request against a timeout cancel Fetch?

No. `Promise.race` settles only the wrapper. Abort the underlying request with an `AbortController` and clear the timer during cleanup.

---

[← Previous: Top 200 Output-Based Questions](./32-top-200-output-based-questions.md) · [Home](./README.md)
