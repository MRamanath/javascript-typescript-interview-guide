# 32. Top 200 JavaScript & TypeScript Output Questions

> High-frequency MNC-style output-prediction practice for modern JavaScript and strict TypeScript.

> **Interaction note:** GitHub and VS Code Markdown previews do not persist checkbox or radio state. HTML radio controls may also be disabled or sanitized by some renderers; record answers separately if needed.

Assume modern ECMAScript. JavaScript snippets run as scripts unless they explicitly state module or strict mode. TypeScript snippets use `strict: true` and current stable TypeScript unless stated otherwise.

## Categories

- [Coercion and equality (1–20)](#coercion-and-equality-120)
- [Scope, hoisting, and closures (21–40)](#scope-hoisting-and-closures-2140)
- [Functions and `this` (41–60)](#functions-and-this-4160)
- [Arrays and objects (61–80)](#arrays-and-objects-6180)
- [Prototypes and classes (81–95)](#prototypes-and-classes-8195)
- [Async, event loop, and promises (96–115)](#async-event-loop-and-promises-96115)
- [Modern JavaScript (116–130)](#modern-javascript-116130)
- [Errors, modules, browser, and runtime (131–140)](#errors-modules-browser-and-runtime-131140)
- [TypeScript basics and inference (141–155)](#typescript-basics-and-inference-141155)
- [Narrowing and unions (156–170)](#narrowing-and-unions-156170)
- [Generics, `keyof`, and utility types (171–185)](#generics-keyof-and-utility-types-171185)
- [Advanced TypeScript and classes (186–200)](#advanced-typescript-and-classes-186200)

## Scoring guide

| Score | Readiness |
|---:|---|
| 180–200 | Excellent: explain edge cases aloud |
| 150–179 | Strong: review missed categories |
| 110–149 | Developing: revisit core chapters |
| 0–109 | Foundation: study by topic, then retry |

Give one point per correct answer. Do not count an answer unless both the option and reasoning are correct.

---

## Coercion and equality (1–20)

### 1. [ ] What is logged?
```javascript
console.log('5' + 2);
```
<label><input type="radio" name="q1"> A. 7</label><br>
<label><input type="radio" name="q1"> B. 52</label><br>
<label><input type="radio" name="q1"> C. "52"</label><br>
<label><input type="radio" name="q1"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**C. Output:** `"52"`. With a string operand, `+` performs concatenation.
</details>

### 2. [ ] What is logged?
```javascript
console.log('5' - 2);
```
<label><input type="radio" name="q2"> A. 3</label><br>
<label><input type="radio" name="q2"> B. "3"</label><br>
<label><input type="radio" name="q2"> C. NaN</label><br>
<label><input type="radio" name="q2"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `3`. Subtraction converts the string to a number.
</details>

### 3. [ ] What is logged?
```javascript
console.log(Boolean('0'), Boolean(''));
```
<label><input type="radio" name="q3"> A. false false</label><br>
<label><input type="radio" name="q3"> B. true false</label><br>
<label><input type="radio" name="q3"> C. true true</label><br>
<label><input type="radio" name="q3"> D. false true</label><br>
<details><summary>Show answer</summary>

**B. Output:** `true false`. Every nonempty string is truthy; the empty string is falsy.
</details>

### 4. [ ] What is logged?
```javascript
console.log(null == undefined, null === undefined);
```
<label><input type="radio" name="q4"> A. true false</label><br>
<label><input type="radio" name="q4"> B. false false</label><br>
<label><input type="radio" name="q4"> C. true true</label><br>
<label><input type="radio" name="q4"> D. false true</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true false`. Loose equality specially equates them, while strict equality does not.
</details>

### 5. [ ] What is logged?
```javascript
console.log(NaN === NaN, Object.is(NaN, NaN));
```
<label><input type="radio" name="q5"> A. true true</label><br>
<label><input type="radio" name="q5"> B. false true</label><br>
<label><input type="radio" name="q5"> C. false false</label><br>
<label><input type="radio" name="q5"> D. true false</label><br>
<details><summary>Show answer</summary>

**B. Output:** `false true`. `NaN` is unequal to itself under `===`, but `Object.is` treats it as the same value.
</details>

### 6. [ ] What is logged?
```javascript
console.log([] == false);
```
<label><input type="radio" name="q6"> A. true</label><br>
<label><input type="radio" name="q6"> B. false</label><br>
<label><input type="radio" name="q6"> C. undefined</label><br>
<label><input type="radio" name="q6"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true`. Loose equality converts both operands through primitive/number coercion to `0`.
</details>

### 7. [ ] What is logged?
```javascript
console.log(typeof null);
```
<label><input type="radio" name="q7"> A. "null"</label><br>
<label><input type="radio" name="q7"> B. "undefined"</label><br>
<label><input type="radio" name="q7"> C. "object"</label><br>
<label><input type="radio" name="q7"> D. "number"</label><br>
<details><summary>Show answer</summary>

**C. Output:** `"object"`. This is a historical JavaScript behavior.
</details>

### 8. [ ] What is logged?
```javascript
console.log(0.1 + 0.2 === 0.3);
```
<label><input type="radio" name="q8"> A. true</label><br>
<label><input type="radio" name="q8"> B. false</label><br>
<label><input type="radio" name="q8"> C. NaN</label><br>
<label><input type="radio" name="q8"> D. RangeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `false`. Binary floating-point cannot represent these decimals exactly.
</details>

### 9. [ ] What is logged?
```javascript
console.log(Number(''), Number('x'));
```
<label><input type="radio" name="q9"> A. 0 NaN</label><br>
<label><input type="radio" name="q9"> B. NaN NaN</label><br>
<label><input type="radio" name="q9"> C. 0 0</label><br>
<label><input type="radio" name="q9"> D. undefined NaN</label><br>
<details><summary>Show answer</summary>

**A. Output:** `0 NaN`. An empty string converts to zero; a nonnumeric string does not.
</details>

### 10. [ ] What is logged?
```javascript
console.log(1 < 2 < 3, 3 > 2 > 1);
```
<label><input type="radio" name="q10"> A. true true</label><br>
<label><input type="radio" name="q10"> B. true false</label><br>
<label><input type="radio" name="q10"> C. false true</label><br>
<label><input type="radio" name="q10"> D. false false</label><br>
<details><summary>Show answer</summary>

**B. Output:** `true false`. Comparisons are left-associative, and intermediate booleans coerce to `1` or `0`.
</details>

### 11. [ ] What is logged?
```javascript
console.log('10' > '2', '10' > 2);
```
<label><input type="radio" name="q11"> A. true true</label><br>
<label><input type="radio" name="q11"> B. false true</label><br>
<label><input type="radio" name="q11"> C. false false</label><br>
<label><input type="radio" name="q11"> D. true false</label><br>
<details><summary>Show answer</summary>

**B. Output:** `false true`. Two strings compare lexicographically; the mixed comparison is numeric.
</details>

### 12. [ ] What is logged?
```javascript
console.log(+true, +null, +undefined);
```
<label><input type="radio" name="q12"> A. 1 0 NaN</label><br>
<label><input type="radio" name="q12"> B. true null undefined</label><br>
<label><input type="radio" name="q12"> C. 1 NaN NaN</label><br>
<label><input type="radio" name="q12"> D. 0 0 NaN</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1 0 NaN`. Unary plus applies numeric conversion.
</details>

### 13. [ ] What is logged?
```javascript
console.log(0 || 'x', 0 ?? 'x');
```
<label><input type="radio" name="q13"> A. "x" "x"</label><br>
<label><input type="radio" name="q13"> B. 0 0</label><br>
<label><input type="radio" name="q13"> C. "x" 0</label><br>
<label><input type="radio" name="q13"> D. 0 "x"</label><br>
<details><summary>Show answer</summary>

**C. Output:** `"x" 0`. `||` reacts to falsiness; `??` only reacts to `null` or `undefined`.
</details>

### 14. [ ] What is logged?
```javascript
console.log(Object.is(0, -0), 0 === -0);
```
<label><input type="radio" name="q14"> A. true true</label><br>
<label><input type="radio" name="q14"> B. false true</label><br>
<label><input type="radio" name="q14"> C. false false</label><br>
<label><input type="radio" name="q14"> D. true false</label><br>
<details><summary>Show answer</summary>

**B. Output:** `false true`. `Object.is` distinguishes signed zero; strict equality does not.
</details>

### 15. [ ] What is logged?
```javascript
console.log(parseInt('12px', 10), Number('12px'));
```
<label><input type="radio" name="q15"> A. 12 12</label><br>
<label><input type="radio" name="q15"> B. NaN NaN</label><br>
<label><input type="radio" name="q15"> C. 12 NaN</label><br>
<label><input type="radio" name="q15"> D. NaN 12</label><br>
<details><summary>Show answer</summary>

**C. Output:** `12 NaN`. `parseInt` accepts a numeric prefix; `Number` requires the whole string to be numeric.
</details>

### 16. [ ] What is logged?
```javascript
console.log([] + [], [] + {});
```
<label><input type="radio" name="q16"> A. "" "[object Object]"</label><br>
<label><input type="radio" name="q16"> B. [] {}</label><br>
<label><input type="radio" name="q16"> C. 0 NaN</label><br>
<label><input type="radio" name="q16"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"" "[object Object]"`. In expression context, arrays stringify to `""` and the object to its default string.
</details>

### 17. [ ] What is logged?
```javascript
console.log(false == '0', false === '0');
```
<label><input type="radio" name="q17"> A. true false</label><br>
<label><input type="radio" name="q17"> B. false false</label><br>
<label><input type="radio" name="q17"> C. true true</label><br>
<label><input type="radio" name="q17"> D. false true</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true false`. Loose equality converts both to zero; their types differ under strict equality.
</details>

### 18. [ ] What is logged?
```javascript
console.log(2 ** 3 ** 2);
```
<label><input type="radio" name="q18"> A. 64</label><br>
<label><input type="radio" name="q18"> B. 512</label><br>
<label><input type="radio" name="q18"> C. 36</label><br>
<label><input type="radio" name="q18"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `512`. Exponentiation is right-associative: `2 ** (3 ** 2)`.
</details>

### 19. [ ] What is logged?
```javascript
console.log(5 + null, 5 + undefined);
```
<label><input type="radio" name="q19"> A. 5 NaN</label><br>
<label><input type="radio" name="q19"> B. 5 5</label><br>
<label><input type="radio" name="q19"> C. "5null" "5undefined"</label><br>
<label><input type="radio" name="q19"> D. NaN NaN</label><br>
<details><summary>Show answer</summary>

**A. Output:** `5 NaN`. Numeric coercion turns `null` into `0` and `undefined` into `NaN`.
</details>

### 20. [ ] What is logged?
```javascript
console.log(1n == 1, 1n === 1);
```
<label><input type="radio" name="q20"> A. true false</label><br>
<label><input type="radio" name="q20"> B. false false</label><br>
<label><input type="radio" name="q20"> C. true true</label><br>
<label><input type="radio" name="q20"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true false`. Loose equality can compare equivalent BigInt and Number values; strict equality requires the same type.
</details>

---

## Scope, hoisting, and closures (21–40)

### 21. [ ] What is logged?
```javascript
console.log(x);
var x = 4;
```
<label><input type="radio" name="q21"> A. 4</label><br>
<label><input type="radio" name="q21"> B. undefined</label><br>
<label><input type="radio" name="q21"> C. ReferenceError</label><br>
<label><input type="radio" name="q21"> D. null</label><br>
<details><summary>Show answer</summary>

**B. Output:** `undefined`. The `var` declaration is hoisted, but its assignment is not.
</details>

### 22. [ ] What is the result?
```javascript
console.log(x);
let x = 4;
```
<label><input type="radio" name="q22"> A. 4</label><br>
<label><input type="radio" name="q22"> B. undefined</label><br>
<label><input type="radio" name="q22"> C. ReferenceError</label><br>
<label><input type="radio" name="q22"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**C. Result:** `ReferenceError`. `x` is in its temporal dead zone before initialization.
</details>

### 23. [ ] What is logged?
```javascript
var x = 1;
function f() { console.log(x); var x = 2; }
f();
```
<label><input type="radio" name="q23"> A. 1</label><br>
<label><input type="radio" name="q23"> B. 2</label><br>
<label><input type="radio" name="q23"> C. undefined</label><br>
<label><input type="radio" name="q23"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**C. Output:** `undefined`. The local `var x` shadows the outer variable and is initialized to `undefined` on entry.
</details>

### 24. [ ] What is logged?
```javascript
let x = 1;
{ let x = 2; console.log(x); }
console.log(x);
```
<label><input type="radio" name="q24"> A. 2 then 1</label><br>
<label><input type="radio" name="q24"> B. 2 then 2</label><br>
<label><input type="radio" name="q24"> C. 1 then 1</label><br>
<label><input type="radio" name="q24"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `2` then `1`. Block-scoped `let` creates a separate inner binding.
</details>

### 25. [ ] What is logged?
```javascript
function outer() {
  let n = 0;
  return () => ++n;
}
const c = outer();
console.log(c(), c());
```
<label><input type="radio" name="q25"> A. 0 1</label><br>
<label><input type="radio" name="q25"> B. 1 1</label><br>
<label><input type="radio" name="q25"> C. 1 2</label><br>
<label><input type="radio" name="q25"> D. 2 2</label><br>
<details><summary>Show answer</summary>

**C. Output:** `1 2`. The returned closure retains and updates `n`.
</details>

### 26. [ ] What is logged?
```javascript
const a = (() => { let n = 0; return () => ++n; })();
const b = (() => { let n = 0; return () => ++n; })();
console.log(a(), b(), a());
```
<label><input type="radio" name="q26"> A. 1 1 2</label><br>
<label><input type="radio" name="q26"> B. 1 2 3</label><br>
<label><input type="radio" name="q26"> C. 0 0 1</label><br>
<label><input type="radio" name="q26"> D. 1 1 1</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1 1 2`. Each IIFE creates independent closed-over state.
</details>

### 27. [ ] What is logged?
```javascript
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i), 0);
```
<label><input type="radio" name="q27"> A. 0 1 2</label><br>
<label><input type="radio" name="q27"> B. 3 3 3</label><br>
<label><input type="radio" name="q27"> C. 2 2 2</label><br>
<label><input type="radio" name="q27"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `3` three times. All callbacks close over the same function/global-scoped `var i`.
</details>

### 28. [ ] What is logged?
```javascript
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i), 0);
```
<label><input type="radio" name="q28"> A. 0 1 2</label><br>
<label><input type="radio" name="q28"> B. 3 3 3</label><br>
<label><input type="radio" name="q28"> C. 2 2 2</label><br>
<label><input type="radio" name="q28"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `0 1 2`. A `for` loop with `let` creates a fresh binding per iteration.
</details>

### 29. [ ] What is logged?
```javascript
foo();
function foo() { console.log('ok'); }
```
<label><input type="radio" name="q29"> A. "ok"</label><br>
<label><input type="radio" name="q29"> B. undefined</label><br>
<label><input type="radio" name="q29"> C. ReferenceError</label><br>
<label><input type="radio" name="q29"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"ok"`. Function declarations are instantiated before execution.
</details>

### 30. [ ] What is the result?
```javascript
foo();
const foo = () => console.log('ok');
```
<label><input type="radio" name="q30"> A. "ok"</label><br>
<label><input type="radio" name="q30"> B. undefined</label><br>
<label><input type="radio" name="q30"> C. ReferenceError</label><br>
<label><input type="radio" name="q30"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**C. Result:** `ReferenceError`. The `const` binding is accessed in its temporal dead zone.
</details>

### 31. [ ] What is logged?
```javascript
function f(a = 1) { var a = 2; console.log(a); }
f();
```
<label><input type="radio" name="q31"> A. 1</label><br>
<label><input type="radio" name="q31"> B. 2</label><br>
<label><input type="radio" name="q31"> C. undefined</label><br>
<label><input type="radio" name="q31"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. `var a` shares the parameter binding and the assignment updates it.
</details>

### 32. [ ] What is logged?
```javascript
let x = 1;
function f() { return () => x; }
const g = f();
x = 9;
console.log(g());
```
<label><input type="radio" name="q32"> A. 1</label><br>
<label><input type="radio" name="q32"> B. 9</label><br>
<label><input type="radio" name="q32"> C. undefined</label><br>
<label><input type="radio" name="q32"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `9`. Closures capture bindings, not frozen snapshots of values.
</details>

### 33. [ ] What is logged?
```javascript
function f() {
  if (true) var x = 3;
  console.log(x);
}
f();
```
<label><input type="radio" name="q33"> A. 3</label><br>
<label><input type="radio" name="q33"> B. undefined</label><br>
<label><input type="radio" name="q33"> C. ReferenceError</label><br>
<label><input type="radio" name="q33"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `3`. `var` is function-scoped rather than block-scoped.
</details>

### 34. [ ] What is logged?
```javascript
const x = 1;
function f(x) { console.log(x); }
f();
```
<label><input type="radio" name="q34"> A. 1</label><br>
<label><input type="radio" name="q34"> B. undefined</label><br>
<label><input type="radio" name="q34"> C. null</label><br>
<label><input type="radio" name="q34"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `undefined`. The omitted parameter still creates a local binding that shadows outer `x`.
</details>

### 35. [ ] What is logged?
```javascript
function make() {
  const value = 7;
  return function valueOf() { return value; };
}
console.log(make()());
```
<label><input type="radio" name="q35"> A. 7</label><br>
<label><input type="radio" name="q35"> B. "valueOf"</label><br>
<label><input type="radio" name="q35"> C. undefined</label><br>
<label><input type="radio" name="q35"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `7`. The function closes over the lexical `value` binding.
</details>

### 36. [ ] What is logged?
```javascript
let n = 1;
{
  let n = n + 1;
  console.log(n);
}
```
<label><input type="radio" name="q36"> A. 1</label><br>
<label><input type="radio" name="q36"> B. 2</label><br>
<label><input type="radio" name="q36"> C. ReferenceError</label><br>
<label><input type="radio" name="q36"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**C. Result:** `ReferenceError`. The inner `n` shadows the outer one throughout its block and is read in its own TDZ.
</details>

### 37. [ ] What is logged?
```javascript
function f() {
  let n = 0;
  return { inc: () => ++n, get: () => n };
}
const x = f();
console.log(x.inc(), x.get());
```
<label><input type="radio" name="q37"> A. 0 0</label><br>
<label><input type="radio" name="q37"> B. 1 0</label><br>
<label><input type="radio" name="q37"> C. 1 1</label><br>
<label><input type="radio" name="q37"> D. 2 1</label><br>
<details><summary>Show answer</summary>

**C. Output:** `1 1`. Both closures share the same lexical environment.
</details>

### 38. [ ] What is logged?
```javascript
var x = 1;
(function (x) { x = 2; })(x);
console.log(x);
```
<label><input type="radio" name="q38"> A. 1</label><br>
<label><input type="radio" name="q38"> B. 2</label><br>
<label><input type="radio" name="q38"> C. undefined</label><br>
<label><input type="radio" name="q38"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1`. Reassigning the primitive parameter does not change the outer binding.
</details>

### 39. [ ] What is logged?
```javascript
function f() {
  try { throw 1; } catch (e) { var x = e; }
  console.log(x);
}
f();
```
<label><input type="radio" name="q39"> A. 1</label><br>
<label><input type="radio" name="q39"> B. undefined</label><br>
<label><input type="radio" name="q39"> C. ReferenceError</label><br>
<label><input type="radio" name="q39"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1`. The catch parameter is block-scoped, while `var x` belongs to the function.
</details>

### 40. [ ] What is logged?
```javascript
function once(fn) {
  let done = false;
  return () => done ? 'skip' : (done = true, fn());
}
const f = once(() => 'run');
console.log(f(), f());
```
<label><input type="radio" name="q40"> A. "run" "run"</label><br>
<label><input type="radio" name="q40"> B. "run" "skip"</label><br>
<label><input type="radio" name="q40"> C. true "skip"</label><br>
<label><input type="radio" name="q40"> D. undefined "skip"</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"run" "skip"`. The closure preserves the `done` flag between calls.
</details>

---

## Functions and this (41–60)

### 41. [ ] What is logged?
```javascript
function add(a, b = 2) { return a + b; }
console.log(add(3));
```
<label><input type="radio" name="q41"> A. 3</label><br>
<label><input type="radio" name="q41"> B. 5</label><br>
<label><input type="radio" name="q41"> C. NaN</label><br>
<label><input type="radio" name="q41"> D. undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `5`. The default applies because `b` is `undefined`.
</details>

### 42. [ ] What is logged?
```javascript
function f(...xs) { return xs.length; }
console.log(f(1, 2, 3));
```
<label><input type="radio" name="q42"> A. 1</label><br>
<label><input type="radio" name="q42"> B. 2</label><br>
<label><input type="radio" name="q42"> C. 3</label><br>
<label><input type="radio" name="q42"> D. undefined</label><br>
<details><summary>Show answer</summary>

**C. Output:** `3`. Rest parameters collect all arguments into an array.
</details>

### 43. [ ] What is logged?
```javascript
const obj = { n: 4, get() { return this.n; } };
console.log(obj.get());
```
<label><input type="radio" name="q43"> A. 4</label><br>
<label><input type="radio" name="q43"> B. undefined</label><br>
<label><input type="radio" name="q43"> C. null</label><br>
<label><input type="radio" name="q43"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `4`. A method call binds `this` to the receiver `obj`.
</details>

### 44. [ ] What is logged?
```javascript
'use strict';
function f() { return this; }
console.log(f());
```
<label><input type="radio" name="q44"> A. global object</label><br>
<label><input type="radio" name="q44"> B. undefined</label><br>
<label><input type="radio" name="q44"> C. null</label><br>
<label><input type="radio" name="q44"> D. f</label><br>
<details><summary>Show answer</summary>

**B. Output:** `undefined`. A plain strict-mode function call does not substitute the global object.
</details>

### 45. [ ] What is logged?
```javascript
const obj = {
  n: 3,
  f() { return () => this.n; }
};
console.log(obj.f()());
```
<label><input type="radio" name="q45"> A. 3</label><br>
<label><input type="radio" name="q45"> B. undefined</label><br>
<label><input type="radio" name="q45"> C. NaN</label><br>
<label><input type="radio" name="q45"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `3`. The arrow captures `this` from the method invocation.
</details>

### 46. [ ] What is logged?
```javascript
function f() { return this.n; }
const g = f.bind({ n: 5 });
console.log(g.call({ n: 9 }));
```
<label><input type="radio" name="q46"> A. 5</label><br>
<label><input type="radio" name="q46"> B. 9</label><br>
<label><input type="radio" name="q46"> C. undefined</label><br>
<label><input type="radio" name="q46"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `5`. A bound function's `this` cannot be replaced by `call`.
</details>

### 47. [ ] What is logged?
```javascript
function f(a, b) { return this.n + a + b; }
console.log(f.apply({ n: 1 }, [2, 3]));
```
<label><input type="radio" name="q47"> A. 5</label><br>
<label><input type="radio" name="q47"> B. 6</label><br>
<label><input type="radio" name="q47"> C. "123"</label><br>
<label><input type="radio" name="q47"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `6`. `apply` supplies both the receiver and an argument array.
</details>

### 48. [ ] What is logged?
```javascript
function f(a, b) {}
const g = f.bind(null, 1);
console.log(f.length, g.length);
```
<label><input type="radio" name="q48"> A. 2 2</label><br>
<label><input type="radio" name="q48"> B. 2 1</label><br>
<label><input type="radio" name="q48"> C. 1 1</label><br>
<label><input type="radio" name="q48"> D. 0 0</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2 1`. Binding one leading argument reduces the reported remaining parameter count.
</details>

### 49. [ ] What is logged?
```javascript
function f(a = 1, b) {}
console.log(f.length);
```
<label><input type="radio" name="q49"> A. 0</label><br>
<label><input type="radio" name="q49"> B. 1</label><br>
<label><input type="radio" name="q49"> C. 2</label><br>
<label><input type="radio" name="q49"> D. undefined</label><br>
<details><summary>Show answer</summary>

**A. Output:** `0`. `length` counts parameters only before the first default parameter.
</details>

### 50. [ ] What is logged?
```javascript
const f = (a, b) => ({ sum: a + b });
console.log(f(2, 3).sum);
```
<label><input type="radio" name="q50"> A. 5</label><br>
<label><input type="radio" name="q50"> B. undefined</label><br>
<label><input type="radio" name="q50"> C. "23"</label><br>
<label><input type="radio" name="q50"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `5`. Parentheses make the object literal the arrow's expression result.
</details>

### 51. [ ] What is logged?
```javascript
function f() { return arguments[0] + arguments.length; }
console.log(f(4, 5));
```
<label><input type="radio" name="q51"> A. 6</label><br>
<label><input type="radio" name="q51"> B. 9</label><br>
<label><input type="radio" name="q51"> C. 42</label><br>
<label><input type="radio" name="q51"> D. undefined</label><br>
<details><summary>Show answer</summary>

**A. Output:** `6`. The first argument is `4` and there are two arguments.
</details>

### 52. [ ] What is logged?
```javascript
function f(x) { return x * 2; }
console.log(f.call(null, 6));
```
<label><input type="radio" name="q52"> A. 6</label><br>
<label><input type="radio" name="q52"> B. 12</label><br>
<label><input type="radio" name="q52"> C. NaN</label><br>
<label><input type="radio" name="q52"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `12`. `call` passes `6` as the function argument; `this` is unused.
</details>

### 53. [ ] What is logged?
```javascript
const obj = { n: 1, inc() { return ++this.n; } };
const other = { n: 8, inc: obj.inc };
console.log(other.inc());
```
<label><input type="radio" name="q53"> A. 2</label><br>
<label><input type="radio" name="q53"> B. 8</label><br>
<label><input type="radio" name="q53"> C. 9</label><br>
<label><input type="radio" name="q53"> D. undefined</label><br>
<details><summary>Show answer</summary>

**C. Output:** `9`. `this` is determined by the call-site receiver, not where the function was defined.
</details>

### 54. [ ] What is logged?
```javascript
function Person(name) { this.name = name; }
const Bound = Person.bind({ name: 'x' });
console.log(new Bound('Ada').name);
```
<label><input type="radio" name="q54"> A. "x"</label><br>
<label><input type="radio" name="q54"> B. "Ada"</label><br>
<label><input type="radio" name="q54"> C. undefined</label><br>
<label><input type="radio" name="q54"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"Ada"`. Construction ignores the bound `this` and creates a new instance.
</details>

### 55. [ ] What is logged?
```javascript
const f = function named() { return named.name; };
console.log(f());
```
<label><input type="radio" name="q55"> A. "f"</label><br>
<label><input type="radio" name="q55"> B. "named"</label><br>
<label><input type="radio" name="q55"> C. undefined</label><br>
<label><input type="radio" name="q55"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"named"`. A named function expression exposes its internal name within its own body.
</details>

### 56. [ ] What is logged?
```javascript
function f({ x = 2 } = {}) { return x; }
console.log(f(), f({ x: 5 }));
```
<label><input type="radio" name="q56"> A. 2 5</label><br>
<label><input type="radio" name="q56"> B. undefined 5</label><br>
<label><input type="radio" name="q56"> C. 2 2</label><br>
<label><input type="radio" name="q56"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `2 5`. The parameter and property defaults cover the missing argument/property.
</details>

### 57. [ ] What is logged?
```javascript
function f(a, ...rest) { return [a, rest.length]; }
console.log(f(1, 2, 3).join(':'));
```
<label><input type="radio" name="q57"> A. "1:2"</label><br>
<label><input type="radio" name="q57"> B. "1:3"</label><br>
<label><input type="radio" name="q57"> C. "2:3"</label><br>
<label><input type="radio" name="q57"> D. "1:2:3"</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"1:2"`. `a` consumes one argument and `rest` receives the remaining two.
</details>

### 58. [ ] What is logged?
```javascript
const obj = { n: 2 };
function f(x) { return this.n * x; }
const g = f.bind(obj, 3);
console.log(g());
```
<label><input type="radio" name="q58"> A. 5</label><br>
<label><input type="radio" name="q58"> B. 6</label><br>
<label><input type="radio" name="q58"> C. undefined</label><br>
<label><input type="radio" name="q58"> D. NaN</label><br>
<details><summary>Show answer</summary>

**B. Output:** `6`. `bind` fixes both `this` and the leading argument.
</details>

### 59. [ ] What is logged?
```javascript
function f() { return new.target ? 'new' : 'plain'; }
console.log(f(), new f());
```
<label><input type="radio" name="q59"> A. "plain" then an f instance</label><br>
<label><input type="radio" name="q59"> B. "plain" "new"</label><br>
<label><input type="radio" name="q59"> C. "new" "new"</label><br>
<label><input type="radio" name="q59"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** one log entry containing `"plain"` followed by a newly created `f` instance. The instance's exact console representation depends on the runtime. Constructor calls ignore primitive return values, so `new f()` returns the instance.
</details>

### 60. [ ] What is logged?
```javascript
const sum = (a) => (b) => a + b;
console.log(sum(2)(5));
```
<label><input type="radio" name="q60"> A. 2</label><br>
<label><input type="radio" name="q60"> B. 5</label><br>
<label><input type="radio" name="q60"> C. 7</label><br>
<label><input type="radio" name="q60"> D. NaN</label><br>
<details><summary>Show answer</summary>

**C. Output:** `7`. The inner function closes over `a` and adds `b`.
</details>

---

## Arrays and objects (61–80)

### 61. [ ] What is logged?
```javascript
const a = [1, 2, 3];
console.log(a.map(x => x * 2).join(','));
```
<label><input type="radio" name="q61"> A. "1,2,3"</label><br>
<label><input type="radio" name="q61"> B. "2,4,6"</label><br>
<label><input type="radio" name="q61"> C. "6"</label><br>
<label><input type="radio" name="q61"> D. undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"2,4,6"`. `map` creates transformed elements and `join` combines them.
</details>

### 62. [ ] What is logged?
```javascript
console.log([1, 2, 3].filter(Boolean).length);
```
<label><input type="radio" name="q62"> A. 0</label><br>
<label><input type="radio" name="q62"> B. 1</label><br>
<label><input type="radio" name="q62"> C. 2</label><br>
<label><input type="radio" name="q62"> D. 3</label><br>
<details><summary>Show answer</summary>

**D. Output:** `3`. All three nonzero numbers are truthy.
</details>

### 63. [ ] What is logged?
```javascript
console.log([1, 2, 3].reduce((a, x) => a + x, 0));
```
<label><input type="radio" name="q63"> A. 3</label><br>
<label><input type="radio" name="q63"> B. 5</label><br>
<label><input type="radio" name="q63"> C. 6</label><br>
<label><input type="radio" name="q63"> D. "123"</label><br>
<details><summary>Show answer</summary>

**C. Output:** `6`. The reducer accumulates the numeric sum from zero.
</details>

### 64. [ ] What is logged?
```javascript
const a = [1, 2];
const b = a;
b.push(3);
console.log(a.length);
```
<label><input type="radio" name="q64"> A. 2</label><br>
<label><input type="radio" name="q64"> B. 3</label><br>
<label><input type="radio" name="q64"> C. undefined</label><br>
<label><input type="radio" name="q64"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `3`. Both variables reference the same array.
</details>

### 65. [ ] What is logged?
```javascript
const a = [1, 2];
const b = [...a];
b[0] = 9;
console.log(a[0], b[0]);
```
<label><input type="radio" name="q65"> A. 1 9</label><br>
<label><input type="radio" name="q65"> B. 9 9</label><br>
<label><input type="radio" name="q65"> C. 1 1</label><br>
<label><input type="radio" name="q65"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1 9`. Array spread makes a shallow copy.
</details>

### 66. [ ] What is logged?
```javascript
const a = [[1]];
const b = [...a];
b[0].push(2);
console.log(a[0].length);
```
<label><input type="radio" name="q66"> A. 1</label><br>
<label><input type="radio" name="q66"> B. 2</label><br>
<label><input type="radio" name="q66"> C. 3</label><br>
<label><input type="radio" name="q66"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. Spread is shallow, so the nested array remains shared.
</details>

### 67. [ ] What is logged?
```javascript
const { a = 4 } = { a: undefined };
console.log(a);
```
<label><input type="radio" name="q67"> A. 4</label><br>
<label><input type="radio" name="q67"> B. undefined</label><br>
<label><input type="radio" name="q67"> C. null</label><br>
<label><input type="radio" name="q67"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `4`. A destructuring default applies when the property value is `undefined`.
</details>

### 68. [ ] What is logged?
```javascript
const { a = 4 } = { a: null };
console.log(a);
```
<label><input type="radio" name="q68"> A. 4</label><br>
<label><input type="radio" name="q68"> B. undefined</label><br>
<label><input type="radio" name="q68"> C. null</label><br>
<label><input type="radio" name="q68"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**C. Output:** `null`. Destructuring defaults do not replace `null`.
</details>

### 69. [ ] What is logged?
```javascript
const o = { a: 1, a: 2 };
console.log(o.a);
```
<label><input type="radio" name="q69"> A. 1</label><br>
<label><input type="radio" name="q69"> B. 2</label><br>
<label><input type="radio" name="q69"> C. undefined</label><br>
<label><input type="radio" name="q69"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. In an ordinary object literal, the later property value overwrites the earlier one.
</details>

### 70. [ ] What is logged?
```javascript
const key = 'x';
const o = { [key]: 7 };
console.log(o.x);
```
<label><input type="radio" name="q70"> A. "x"</label><br>
<label><input type="radio" name="q70"> B. 7</label><br>
<label><input type="radio" name="q70"> C. undefined</label><br>
<label><input type="radio" name="q70"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `7`. The computed property name evaluates `key`.
</details>

### 71. [ ] What is logged?
```javascript
const o = { a: 1 };
console.log('a' in o, Object.hasOwn(o, 'toString'));
```
<label><input type="radio" name="q71"> A. true true</label><br>
<label><input type="radio" name="q71"> B. true false</label><br>
<label><input type="radio" name="q71"> C. false true</label><br>
<label><input type="radio" name="q71"> D. false false</label><br>
<details><summary>Show answer</summary>

**B. Output:** `true false`. `a` exists, while inherited `toString` is not an own property.
</details>

### 72. [ ] What is logged?
```javascript
const a = Array(3);
console.log(a.length, a.map(() => 1).join('-'));
```
<label><input type="radio" name="q72"> A. 3 "1-1-1"</label><br>
<label><input type="radio" name="q72"> B. 3 "--"</label><br>
<label><input type="radio" name="q72"> C. 0 ""</label><br>
<label><input type="radio" name="q72"> D. 3 ""</label><br>
<details><summary>Show answer</summary>

**B. Output:** `3 "--"`. `map` skips holes, and `join` inserts separators for the three empty slots.
</details>

### 73. [ ] What is logged?
```javascript
const a = [1, 2, 3];
console.log(a.splice(1, 1)[0], a.join(','));
```
<label><input type="radio" name="q73"> A. 1 "2,3"</label><br>
<label><input type="radio" name="q73"> B. 2 "1,3"</label><br>
<label><input type="radio" name="q73"> C. 2 "1,2,3"</label><br>
<label><input type="radio" name="q73"> D. 3 "1,2"</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2 "1,3"`. `splice` removes and returns the element at index one, mutating `a`.
</details>

### 74. [ ] What is logged?
```javascript
const a = [1, 2, 3];
console.log(a.slice(1).join(','), a.length);
```
<label><input type="radio" name="q74"> A. "2,3" 3</label><br>
<label><input type="radio" name="q74"> B. "2,3" 2</label><br>
<label><input type="radio" name="q74"> C. "1,2" 3</label><br>
<label><input type="radio" name="q74"> D. "1,2,3" 3</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"2,3" 3`. `slice` returns a copy and does not mutate the source.
</details>

### 75. [ ] What is logged?
```javascript
const o = { a: 1 };
const p = { ...o, a: 2 };
console.log(o.a, p.a);
```
<label><input type="radio" name="q75"> A. 1 2</label><br>
<label><input type="radio" name="q75"> B. 2 2</label><br>
<label><input type="radio" name="q75"> C. 1 1</label><br>
<label><input type="radio" name="q75"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1 2`. The later property overrides the copied value without changing `o`.
</details>

### 76. [ ] What is logged?
```javascript
const o = Object.freeze({ inner: { n: 1 } });
o.inner.n = 2;
console.log(o.inner.n);
```
<label><input type="radio" name="q76"> A. 1</label><br>
<label><input type="radio" name="q76"> B. 2</label><br>
<label><input type="radio" name="q76"> C. TypeError</label><br>
<label><input type="radio" name="q76"> D. undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. `Object.freeze` is shallow, so the nested object remains mutable.
</details>

### 77. [ ] What is logged?
```javascript
console.log(Object.keys({ 2: 'b', 1: 'a', x: 'c' }).join(','));
```
<label><input type="radio" name="q77"> A. "2,1,x"</label><br>
<label><input type="radio" name="q77"> B. "1,2,x"</label><br>
<label><input type="radio" name="q77"> C. "x,1,2"</label><br>
<label><input type="radio" name="q77"> D. Order is unspecified</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"1,2,x"`. Own integer-index keys precede other string keys in defined enumeration order.
</details>

### 78. [ ] What is logged?
```javascript
const m = new Map([[{}, 1]]);
console.log(m.get({}));
```
<label><input type="radio" name="q78"> A. 1</label><br>
<label><input type="radio" name="q78"> B. 0</label><br>
<label><input type="radio" name="q78"> C. undefined</label><br>
<label><input type="radio" name="q78"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**C. Output:** `undefined`. The lookup uses a different object identity from the stored key.
</details>

### 79. [ ] What is logged?
```javascript
console.log(new Set([1, 1, '1']).size);
```
<label><input type="radio" name="q79"> A. 1</label><br>
<label><input type="radio" name="q79"> B. 2</label><br>
<label><input type="radio" name="q79"> C. 3</label><br>
<label><input type="radio" name="q79"> D. undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. Duplicate numbers collapse, but number `1` and string `"1"` differ.
</details>

### 80. [ ] What is logged?
```javascript
const [a, , b] = [1, 2, 3];
console.log(a, b);
```
<label><input type="radio" name="q80"> A. 1 2</label><br>
<label><input type="radio" name="q80"> B. 1 3</label><br>
<label><input type="radio" name="q80"> C. 2 3</label><br>
<label><input type="radio" name="q80"> D. undefined 3</label><br>
<details><summary>Show answer</summary>

**B. Output:** `1 3`. The elision skips the second array element.
</details>

---

## Prototypes and classes (81–95)

### 81. [ ] What is logged?
```javascript
const p = { n: 1 };
const o = Object.create(p);
console.log(o.n, Object.hasOwn(o, 'n'));
```
<label><input type="radio" name="q81"> A. 1 true</label><br>
<label><input type="radio" name="q81"> B. 1 false</label><br>
<label><input type="radio" name="q81"> C. undefined false</label><br>
<label><input type="radio" name="q81"> D. undefined true</label><br>
<details><summary>Show answer</summary>

**B. Output:** `1 false`. Property lookup reaches the prototype, but `n` is not own.
</details>

### 82. [ ] What is logged?
```javascript
function A() {}
const a = new A();
console.log(a instanceof A);
```
<label><input type="radio" name="q82"> A. true</label><br>
<label><input type="radio" name="q82"> B. false</label><br>
<label><input type="radio" name="q82"> C. undefined</label><br>
<label><input type="radio" name="q82"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true`. `A.prototype` is in the instance's prototype chain.
</details>

### 83. [ ] What is logged?
```javascript
function A() {}
A.prototype.n = 1;
const a = new A();
a.n = 2;
console.log(a.n, A.prototype.n);
```
<label><input type="radio" name="q83"> A. 1 1</label><br>
<label><input type="radio" name="q83"> B. 2 2</label><br>
<label><input type="radio" name="q83"> C. 2 1</label><br>
<label><input type="radio" name="q83"> D. 1 2</label><br>
<details><summary>Show answer</summary>

**C. Output:** `2 1`. Assignment creates an own property that shadows the prototype property.
</details>

### 84. [ ] What is logged?
```javascript
class A { static x = 3; }
const a = new A();
console.log(A.x, a.x);
```
<label><input type="radio" name="q84"> A. 3 3</label><br>
<label><input type="radio" name="q84"> B. 3 undefined</label><br>
<label><input type="radio" name="q84"> C. undefined 3</label><br>
<label><input type="radio" name="q84"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `3 undefined`. Static fields belong to the class constructor, not instances.
</details>

### 85. [ ] What is logged?
```javascript
class A { get x() { return 4; } }
console.log(new A().x);
```
<label><input type="radio" name="q85"> A. 4</label><br>
<label><input type="radio" name="q85"> B. function</label><br>
<label><input type="radio" name="q85"> C. undefined</label><br>
<label><input type="radio" name="q85"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `4`. Property access invokes the getter.
</details>

### 86. [ ] What is logged?
```javascript
class A { x = 1; }
class B extends A { x = 2; }
console.log(new B().x);
```
<label><input type="radio" name="q86"> A. 1</label><br>
<label><input type="radio" name="q86"> B. 2</label><br>
<label><input type="radio" name="q86"> C. undefined</label><br>
<label><input type="radio" name="q86"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. Derived instance fields initialize after the base constructor and overwrite `x`.
</details>

### 87. [ ] What is logged?
```javascript
class A { greet() { return 'A'; } }
class B extends A { greet() { return super.greet() + 'B'; } }
console.log(new B().greet());
```
<label><input type="radio" name="q87"> A. "A"</label><br>
<label><input type="radio" name="q87"> B. "B"</label><br>
<label><input type="radio" name="q87"> C. "AB"</label><br>
<label><input type="radio" name="q87"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**C. Output:** `"AB"`. `super.greet()` calls the base prototype method.
</details>

### 88. [ ] What is the result?
```javascript
class A { #x = 1; }
const a = new A();
console.log(a.#x);
```
<label><input type="radio" name="q88"> A. 1</label><br>
<label><input type="radio" name="q88"> B. undefined</label><br>
<label><input type="radio" name="q88"> C. SyntaxError</label><br>
<label><input type="radio" name="q88"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**C. Result:** `SyntaxError`. A private field cannot be referenced outside its declaring class body.
</details>

### 89. [ ] What is logged?
```javascript
class A { constructor() { this.n = 1; } }
class B extends A { constructor() { super(); this.n++; } }
console.log(new B().n);
```
<label><input type="radio" name="q89"> A. 1</label><br>
<label><input type="radio" name="q89"> B. 2</label><br>
<label><input type="radio" name="q89"> C. undefined</label><br>
<label><input type="radio" name="q89"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. `super()` initializes the instance before the derived constructor increments `n`.
</details>

### 90. [ ] What is logged?
```javascript
class A {}
console.log(typeof A, typeof A.prototype);
```
<label><input type="radio" name="q90"> A. "class" "object"</label><br>
<label><input type="radio" name="q90"> B. "function" "object"</label><br>
<label><input type="radio" name="q90"> C. "object" "object"</label><br>
<label><input type="radio" name="q90"> D. "function" "function"</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"function" "object"`. A class is a special constructor function with a prototype object.
</details>

### 91. [ ] What is logged?
```javascript
class A {}
const a = new A();
console.log(Object.getPrototypeOf(a) === A.prototype);
```
<label><input type="radio" name="q91"> A. true</label><br>
<label><input type="radio" name="q91"> B. false</label><br>
<label><input type="radio" name="q91"> C. undefined</label><br>
<label><input type="radio" name="q91"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true`. Construction links the instance to the constructor's `prototype`.
</details>

### 92. [ ] What is logged?
```javascript
function A() {}
A.prototype = { n: 1 };
const a = new A();
console.log(a.constructor === A);
```
<label><input type="radio" name="q92"> A. true</label><br>
<label><input type="radio" name="q92"> B. false</label><br>
<label><input type="radio" name="q92"> C. undefined</label><br>
<label><input type="radio" name="q92"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `false`. Replacing the prototype with a plain object loses the usual own `constructor` link to `A`.
</details>

### 93. [ ] What is logged?
```javascript
class A { m() {} }
const a = new A();
console.log(Object.hasOwn(a, 'm'));
```
<label><input type="radio" name="q93"> A. true</label><br>
<label><input type="radio" name="q93"> B. false</label><br>
<label><input type="radio" name="q93"> C. undefined</label><br>
<label><input type="radio" name="q93"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `false`. Class methods are stored on the prototype.
</details>

### 94. [ ] What is logged?
```javascript
class A { static { this.n = 5; } }
console.log(A.n);
```
<label><input type="radio" name="q94"> A. 5</label><br>
<label><input type="radio" name="q94"> B. undefined</label><br>
<label><input type="radio" name="q94"> C. null</label><br>
<label><input type="radio" name="q94"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `5`. The static initialization block runs while the class is defined.
</details>

### 95. [ ] What is logged?
```javascript
class A { constructor() { return { n: 9 }; } }
console.log(new A().n);
```
<label><input type="radio" name="q95"> A. 9</label><br>
<label><input type="radio" name="q95"> B. undefined</label><br>
<label><input type="radio" name="q95"> C. TypeError</label><br>
<label><input type="radio" name="q95"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `9`. A base constructor may explicitly return an object, replacing the created instance.
</details>

---

## Async, event loop, and promises (96–115)

### 96. [ ] What is the log order?
```javascript
console.log('A');
Promise.resolve().then(() => console.log('B'));
console.log('C');
```
<label><input type="radio" name="q96"> A. A B C</label><br>
<label><input type="radio" name="q96"> B. A C B</label><br>
<label><input type="radio" name="q96"> C. B A C</label><br>
<label><input type="radio" name="q96"> D. C A B</label><br>
<details><summary>Show answer</summary>

**B. Output:** `A`, `C`, `B`. Promise reactions run as microtasks after the current synchronous job.
</details>

### 97. [ ] What is the log order?
```javascript
setTimeout(() => console.log('T'), 0);
Promise.resolve().then(() => console.log('P'));
console.log('S');
```
<label><input type="radio" name="q97"> A. S P T</label><br>
<label><input type="radio" name="q97"> B. S T P</label><br>
<label><input type="radio" name="q97"> C. P S T</label><br>
<label><input type="radio" name="q97"> D. T P S</label><br>
<details><summary>Show answer</summary>

**A. Output:** `S`, `P`, `T`. Synchronous code runs first, then microtasks, then the timer task.
</details>

### 98. [ ] What is logged?
```javascript
async function f() { return 4; }
f().then(console.log);
```
<label><input type="radio" name="q98"> A. 4</label><br>
<label><input type="radio" name="q98"> B. Promise { 4 }</label><br>
<label><input type="radio" name="q98"> C. undefined</label><br>
<label><input type="radio" name="q98"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `4`. An async function wraps its return value in a fulfilled promise.
</details>

### 99. [ ] What is the log order?
```javascript
async function f() {
  console.log('A');
  await 0;
  console.log('B');
}
f();
console.log('C');
```
<label><input type="radio" name="q99"> A. A B C</label><br>
<label><input type="radio" name="q99"> B. A C B</label><br>
<label><input type="radio" name="q99"> C. C A B</label><br>
<label><input type="radio" name="q99"> D. C B A</label><br>
<details><summary>Show answer</summary>

**B. Output:** `A`, `C`, `B`. Execution after `await` resumes in a microtask.
</details>

### 100. [ ] What is logged?
```javascript
Promise.resolve(2)
  .then(x => x * 3)
  .then(console.log);
```
<label><input type="radio" name="q100"> A. 2</label><br>
<label><input type="radio" name="q100"> B. 3</label><br>
<label><input type="radio" name="q100"> C. 5</label><br>
<label><input type="radio" name="q100"> D. 6</label><br>
<details><summary>Show answer</summary>

**D. Output:** `6`. Each `then` receives the previous reaction's returned value.
</details>

### 101. [ ] What is logged?
```javascript
Promise.reject('x')
  .catch(() => 'ok')
  .then(console.log);
```
<label><input type="radio" name="q101"> A. "x"</label><br>
<label><input type="radio" name="q101"> B. "ok"</label><br>
<label><input type="radio" name="q101"> C. undefined</label><br>
<label><input type="radio" name="q101"> D. Unhandled rejection</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"ok"`. The rejection handler recovers by returning a fulfillment value.
</details>

### 102. [ ] What is logged?
```javascript
Promise.resolve('a')
  .finally(() => 'b')
  .then(console.log);
```
<label><input type="radio" name="q102"> A. "a"</label><br>
<label><input type="radio" name="q102"> B. "b"</label><br>
<label><input type="radio" name="q102"> C. undefined</label><br>
<label><input type="radio" name="q102"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"a"`. A normal return from `finally` does not replace the settled value.
</details>

### 103. [ ] What is logged?
```javascript
Promise.all([Promise.resolve(1), 2]).then(x => console.log(x.join(',')));
```
<label><input type="radio" name="q103"> A. "1,2"</label><br>
<label><input type="radio" name="q103"> B. "2,1"</label><br>
<label><input type="radio" name="q103"> C. 3</label><br>
<label><input type="radio" name="q103"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"1,2"`. `Promise.all` preserves input order and wraps non-promises.
</details>

### 104. [ ] What is logged?
```javascript
Promise.race([
  Promise.resolve('first'),
  Promise.resolve('second')
]).then(console.log);
```
<label><input type="radio" name="q104"> A. "first"</label><br>
<label><input type="radio" name="q104"> B. "second"</label><br>
<label><input type="radio" name="q104"> C. ["first", "second"]</label><br>
<label><input type="radio" name="q104"> D. Nondeterministic</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"first"`. Reactions are enqueued in iterable order, so the first already-fulfilled input wins.
</details>

### 105. [ ] What is logged?
```javascript
const p = Promise.resolve(1);
console.log(p === Promise.resolve(p));
```
<label><input type="radio" name="q105"> A. true</label><br>
<label><input type="radio" name="q105"> B. false</label><br>
<label><input type="radio" name="q105"> C. undefined</label><br>
<label><input type="radio" name="q105"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true`. `Promise.resolve` returns an input promise unchanged when its constructor matches.
</details>

### 106. [ ] What is logged?
```javascript
new Promise(resolve => {
  console.log('A');
  resolve();
}).then(() => console.log('B'));
console.log('C');
```
<label><input type="radio" name="q106"> A. A B C</label><br>
<label><input type="radio" name="q106"> B. A C B</label><br>
<label><input type="radio" name="q106"> C. C A B</label><br>
<label><input type="radio" name="q106"> D. C B A</label><br>
<details><summary>Show answer</summary>

**B. Output:** `A`, `C`, `B`. The executor is synchronous; the reaction is a microtask.
</details>

### 107. [ ] What is logged?
```javascript
Promise.resolve()
  .then(() => { throw new Error('x'); })
  .catch(e => console.log(e.message));
```
<label><input type="radio" name="q107"> A. "x"</label><br>
<label><input type="radio" name="q107"> B. "Error"</label><br>
<label><input type="radio" name="q107"> C. undefined</label><br>
<label><input type="radio" name="q107"> D. Uncaught exception</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"x"`. A throw inside a reaction rejects the promise returned by `then`.
</details>

### 108. [ ] What is logged?
```javascript
async function f() { throw 'bad'; }
f().catch(console.log);
```
<label><input type="radio" name="q108"> A. "bad"</label><br>
<label><input type="radio" name="q108"> B. undefined</label><br>
<label><input type="radio" name="q108"> C. Synchronous uncaught throw</label><br>
<label><input type="radio" name="q108"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"bad"`. Throwing in an async function rejects its returned promise.
</details>

### 109. [ ] What is logged?
```javascript
async function f() {
  return await Promise.resolve(7);
}
f().then(console.log);
```
<label><input type="radio" name="q109"> A. 7</label><br>
<label><input type="radio" name="q109"> B. Promise { 7 }</label><br>
<label><input type="radio" name="q109"> C. undefined</label><br>
<label><input type="radio" name="q109"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `7`. `await` unwraps the fulfillment value before the async function fulfills.
</details>

### 110. [ ] What is the log order?
```javascript
queueMicrotask(() => console.log('M1'));
Promise.resolve().then(() => console.log('M2'));
console.log('S');
```
<label><input type="radio" name="q110"> A. S M1 M2</label><br>
<label><input type="radio" name="q110"> B. S M2 M1</label><br>
<label><input type="radio" name="q110"> C. M1 M2 S</label><br>
<label><input type="radio" name="q110"> D. S only</label><br>
<details><summary>Show answer</summary>

**A. Output:** `S`, `M1`, `M2`. Both enqueue microtasks, processed FIFO after synchronous code.
</details>

### 111. [ ] What is logged?
```javascript
Promise.allSettled([Promise.resolve(1), Promise.reject(2)])
  .then(r => console.log(r[0].status, r[1].status));
```
<label><input type="radio" name="q111"> A. fulfilled rejected</label><br>
<label><input type="radio" name="q111"> B. resolved rejected</label><br>
<label><input type="radio" name="q111"> C. fulfilled fulfilled</label><br>
<label><input type="radio" name="q111"> D. Unhandled rejection</label><br>
<details><summary>Show answer</summary>

**A. Output:** `fulfilled rejected`. `allSettled` reports every input's settlement status.
</details>

### 112. [ ] What is logged?
```javascript
Promise.any([Promise.reject('x'), Promise.resolve('y')])
  .then(console.log);
```
<label><input type="radio" name="q112"> A. "x"</label><br>
<label><input type="radio" name="q112"> B. "y"</label><br>
<label><input type="radio" name="q112"> C. AggregateError</label><br>
<label><input type="radio" name="q112"> D. undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"y"`. `Promise.any` fulfills with the first fulfilled input.
</details>

### 113. [ ] What is logged?
```javascript
Promise.resolve(1)
  .then(x => Promise.resolve(x + 1))
  .then(console.log);
```
<label><input type="radio" name="q113"> A. 1</label><br>
<label><input type="radio" name="q113"> B. 2</label><br>
<label><input type="radio" name="q113"> C. Promise { 2 }</label><br>
<label><input type="radio" name="q113"> D. undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. Promise chaining adopts the returned promise's state and value.
</details>

### 114. [ ] What is the log order?
```javascript
Promise.resolve().then(() => {
  console.log('A');
  queueMicrotask(() => console.log('B'));
});
Promise.resolve().then(() => console.log('C'));
```
<label><input type="radio" name="q114"> A. A B C</label><br>
<label><input type="radio" name="q114"> B. A C B</label><br>
<label><input type="radio" name="q114"> C. C A B</label><br>
<label><input type="radio" name="q114"> D. B A C</label><br>
<details><summary>Show answer</summary>

**B. Output:** `A`, `C`, `B`. The nested microtask is appended after the already queued second reaction.
</details>

### 115. [ ] What is logged?
```javascript
(async () => {
  const x = await 3;
  console.log(x);
})();
```
<label><input type="radio" name="q115"> A. 3</label><br>
<label><input type="radio" name="q115"> B. Promise { 3 }</label><br>
<label><input type="radio" name="q115"> C. undefined</label><br>
<label><input type="radio" name="q115"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `3`. `await` treats a non-promise as an already-fulfilled value and resumes asynchronously.
</details>

---

## Modern JavaScript (116–130)

### 116. [ ] What is logged?
```javascript
const o = { a: { b: 2 } };
console.log(o.a?.b, o.x?.b);
```
<label><input type="radio" name="q116"> A. 2 undefined</label><br>
<label><input type="radio" name="q116"> B. 2 null</label><br>
<label><input type="radio" name="q116"> C. undefined undefined</label><br>
<label><input type="radio" name="q116"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `2 undefined`. Optional chaining short-circuits the missing `x` property.
</details>

### 117. [ ] What is logged?
```javascript
const x = null ?? 0 ?? 5;
console.log(x);
```
<label><input type="radio" name="q117"> A. null</label><br>
<label><input type="radio" name="q117"> B. 0</label><br>
<label><input type="radio" name="q117"> C. 5</label><br>
<label><input type="radio" name="q117"> D. undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `0`. Nullish coalescing keeps zero because it is neither `null` nor `undefined`.
</details>

### 118. [ ] What is logged?
```javascript
let x = 0;
x ||= 5;
let y = 0;
y ??= 5;
console.log(x, y);
```
<label><input type="radio" name="q118"> A. 0 0</label><br>
<label><input type="radio" name="q118"> B. 5 5</label><br>
<label><input type="radio" name="q118"> C. 5 0</label><br>
<label><input type="radio" name="q118"> D. 0 5</label><br>
<details><summary>Show answer</summary>

**C. Output:** `5 0`. `||=` replaces falsy zero; `??=` does not replace non-nullish zero.
</details>

### 119. [ ] What is logged?
```javascript
const s = Symbol('x');
console.log(s === Symbol('x'));
```
<label><input type="radio" name="q119"> A. true</label><br>
<label><input type="radio" name="q119"> B. false</label><br>
<label><input type="radio" name="q119"> C. undefined</label><br>
<label><input type="radio" name="q119"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `false`. Each `Symbol()` call creates a unique symbol.
</details>

### 120. [ ] What is logged?
```javascript
console.log(Symbol.for('x') === Symbol.for('x'));
```
<label><input type="radio" name="q120"> A. true</label><br>
<label><input type="radio" name="q120"> B. false</label><br>
<label><input type="radio" name="q120"> C. undefined</label><br>
<label><input type="radio" name="q120"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true`. `Symbol.for` reuses the global symbol registry entry for the key.
</details>

### 121. [ ] What is logged?
```javascript
function* g() { yield 1; return 2; }
const it = g();
console.log(it.next().value, it.next().value);
```
<label><input type="radio" name="q121"> A. 1 1</label><br>
<label><input type="radio" name="q121"> B. 1 2</label><br>
<label><input type="radio" name="q121"> C. 2 undefined</label><br>
<label><input type="radio" name="q121"> D. undefined undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `1 2`. The first call receives the yielded value; the second receives the return value.
</details>

### 122. [ ] What is logged?
```javascript
function* g() { yield 1; return 2; }
console.log([...g()].join(','));
```
<label><input type="radio" name="q122"> A. "1"</label><br>
<label><input type="radio" name="q122"> B. "1,2"</label><br>
<label><input type="radio" name="q122"> C. "2"</label><br>
<label><input type="radio" name="q122"> D. ""</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"1"`. Iteration consumes yielded values but excludes the generator's return value.
</details>

### 123. [ ] What is logged?
```javascript
const r = /a/g;
console.log(r.test('a'), r.test('a'));
```
<label><input type="radio" name="q123"> A. true true</label><br>
<label><input type="radio" name="q123"> B. true false</label><br>
<label><input type="radio" name="q123"> C. false true</label><br>
<label><input type="radio" name="q123"> D. false false</label><br>
<details><summary>Show answer</summary>

**B. Output:** `true false`. A global regular expression updates `lastIndex`, so the second search starts after the match.
</details>

### 124. [ ] What is logged?
```javascript
console.log('abc'.replaceAll('b', 'X'));
```
<label><input type="radio" name="q124"> A. "abc"</label><br>
<label><input type="radio" name="q124"> B. "aXc"</label><br>
<label><input type="radio" name="q124"> C. "Xabc"</label><br>
<label><input type="radio" name="q124"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"aXc"`. `replaceAll` replaces every literal occurrence of `b`.
</details>

### 125. [ ] What is logged?
```javascript
console.log([1, [2, [3]]].flat(2).join(','));
```
<label><input type="radio" name="q125"> A. "1,2,3"</label><br>
<label><input type="radio" name="q125"> B. "1,2,3,"</label><br>
<label><input type="radio" name="q125"> C. "1,2"</label><br>
<label><input type="radio" name="q125"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"1,2,3"`. A depth of two flattens both nested array levels.
</details>

### 126. [ ] What is logged?
```javascript
console.log([1, 2].flatMap(x => [x, x]).join(','));
```
<label><input type="radio" name="q126"> A. "1,2"</label><br>
<label><input type="radio" name="q126"> B. "1,1,2,2"</label><br>
<label><input type="radio" name="q126"> C. "2,4"</label><br>
<label><input type="radio" name="q126"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"1,1,2,2"`. `flatMap` maps each element and flattens one level.
</details>

### 127. [ ] What is logged?
```javascript
const n = 1_000_000;
console.log(n / 1_000);
```
<label><input type="radio" name="q127"> A. 100</label><br>
<label><input type="radio" name="q127"> B. 1000</label><br>
<label><input type="radio" name="q127"> C. 1000000</label><br>
<label><input type="radio" name="q127"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `1000`. Numeric separators affect readability, not the numeric value.
</details>

### 128. [ ] What is logged?
```javascript
const o = { x: 1, ...null, ...undefined };
console.log(o.x);
```
<label><input type="radio" name="q128"> A. 1</label><br>
<label><input type="radio" name="q128"> B. undefined</label><br>
<label><input type="radio" name="q128"> C. null</label><br>
<label><input type="radio" name="q128"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1`. Object spread ignores `null` and `undefined` sources.
</details>

### 129. [ ] What is logged?
```javascript
const a = [3, 1, 2];
const b = a.toSorted();
console.log(a.join(''), b.join(''));
```
<label><input type="radio" name="q129"> A. "123" "123"</label><br>
<label><input type="radio" name="q129"> B. "312" "123"</label><br>
<label><input type="radio" name="q129"> C. "312" "312"</label><br>
<label><input type="radio" name="q129"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"312" "123"`. `toSorted` returns a sorted copy without mutating the original.
</details>

### 130. [ ] What is logged?
```javascript
const o = { x: 2, m() { return `${this.x}`; } };
console.log(o.m());
```
<label><input type="radio" name="q130"> A. "2"</label><br>
<label><input type="radio" name="q130"> B. 2</label><br>
<label><input type="radio" name="q130"> C. "this.x"</label><br>
<label><input type="radio" name="q130"> D. undefined</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"2"`. Template interpolation converts the numeric expression to text.
</details>

---

## Errors, modules, browser, and runtime (131–140)

### 131. [ ] What is logged?
```javascript
try { throw new Error('x'); }
catch (e) { console.log(e.message); }
```
<label><input type="radio" name="q131"> A. "Error"</label><br>
<label><input type="radio" name="q131"> B. "x"</label><br>
<label><input type="radio" name="q131"> C. undefined</label><br>
<label><input type="radio" name="q131"> D. Uncaught error</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"x"`. The catch block receives the thrown `Error` object.
</details>

### 132. [ ] What is logged?
```javascript
function f() {
  try { return 1; }
  finally { return 2; }
}
console.log(f());
```
<label><input type="radio" name="q132"> A. 1</label><br>
<label><input type="radio" name="q132"> B. 2</label><br>
<label><input type="radio" name="q132"> C. undefined</label><br>
<label><input type="radio" name="q132"> D. SyntaxError</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. A return from `finally` overrides the pending return from `try`.
</details>

### 133. [ ] What is logged?
```javascript
try { JSON.parse('{'); }
catch (e) { console.log(e instanceof SyntaxError); }
```
<label><input type="radio" name="q133"> A. true</label><br>
<label><input type="radio" name="q133"> B. false</label><br>
<label><input type="radio" name="q133"> C. undefined</label><br>
<label><input type="radio" name="q133"> D. No error</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true`. Invalid JSON text causes `JSON.parse` to throw a `SyntaxError`.
</details>

### 134. [ ] What is logged?
```javascript
console.log(JSON.stringify({ a: undefined, b: 1 }));
```
<label><input type="radio" name="q134"> A. "{\"a\":undefined,\"b\":1}"</label><br>
<label><input type="radio" name="q134"> B. "{\"a\":null,\"b\":1}"</label><br>
<label><input type="radio" name="q134"> C. "{\"b\":1}"</label><br>
<label><input type="radio" name="q134"> D. undefined</label><br>
<details><summary>Show answer</summary>

**C. Output:** `"{\"b\":1}"`. Object properties whose values are `undefined` are omitted from JSON.
</details>

### 135. [ ] What is logged?
```javascript
console.log(JSON.stringify([undefined, 1]));
```
<label><input type="radio" name="q135"> A. "[undefined,1]"</label><br>
<label><input type="radio" name="q135"> B. "[null,1]"</label><br>
<label><input type="radio" name="q135"> C. "[1]"</label><br>
<label><input type="radio" name="q135"> D. undefined</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"[null,1]"`. Unsupported array values become `null` placeholders in JSON.
</details>

### 136. [ ] In an ES module, what is logged?
```javascript
console.log(this);
```
<label><input type="radio" name="q136"> A. globalThis</label><br>
<label><input type="radio" name="q136"> B. undefined</label><br>
<label><input type="radio" name="q136"> C. module.exports</label><br>
<label><input type="radio" name="q136"> D. null</label><br>
<details><summary>Show answer</summary>

**B. Output:** `undefined`. Top-level `this` in an ECMAScript module is `undefined`.
</details>

### 137. [ ] In any modern JavaScript host, what is logged?
```javascript
console.log(globalThis === globalThis.globalThis);
```
<label><input type="radio" name="q137"> A. true</label><br>
<label><input type="radio" name="q137"> B. false</label><br>
<label><input type="radio" name="q137"> C. undefined</label><br>
<label><input type="radio" name="q137"> D. ReferenceError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true`. `globalThis` exposes the standard global object reference, which has the same property.
</details>

### 138. [ ] In a browser, what is logged?
```javascript
const el = document.createElement('div');
el.dataset.userId = '42';
console.log(el.getAttribute('data-user-id'));
```
<label><input type="radio" name="q138"> A. "42"</label><br>
<label><input type="radio" name="q138"> B. 42</label><br>
<label><input type="radio" name="q138"> C. null</label><br>
<label><input type="radio" name="q138"> D. undefined</label><br>
<details><summary>Show answer</summary>

**A. Output:** `"42"`. The camel-case dataset property maps to the hyphenated string-valued attribute.
</details>

### 139. [ ] In a browser, what is logged?
```javascript
const el = document.createElement('input');
el.setAttribute('disabled', 'false');
console.log(el.disabled);
```
<label><input type="radio" name="q139"> A. true</label><br>
<label><input type="radio" name="q139"> B. false</label><br>
<label><input type="radio" name="q139"> C. "false"</label><br>
<label><input type="radio" name="q139"> D. undefined</label><br>
<details><summary>Show answer</summary>

**A. Output:** `true`. A boolean HTML attribute is true when present, regardless of its text value.
</details>

### 140. [ ] What is logged?
```javascript
const e = new TypeError('bad');
console.log(e.name, e.message);
```
<label><input type="radio" name="q140"> A. "Error" "bad"</label><br>
<label><input type="radio" name="q140"> B. "TypeError" "bad"</label><br>
<label><input type="radio" name="q140"> C. "TypeError" undefined</label><br>
<label><input type="radio" name="q140"> D. undefined "bad"</label><br>
<details><summary>Show answer</summary>

**B. Output:** `"TypeError" "bad"`. The constructor sets the standard error name and supplied message.
</details>

---

## TypeScript basics and inference (141–155)

### 141. [ ] Does this compile under strict TypeScript?
```typescript
let x = 1;
x = 'one';
```
<label><input type="radio" name="q141"> A. Compiles; x is any</label><br>
<label><input type="radio" name="q141"> B. Compiles; x is string | number</label><br>
<label><input type="radio" name="q141"> C. Error: string is not assignable to number</label><br>
<label><input type="radio" name="q141"> D. Error: x is readonly</label><br>
<details><summary>Show answer</summary>

**C. Compile result:** error—`string` is not assignable to inferred `number`. The initializer determines `x`'s variable type.
</details>

### 142. [ ] What type is inferred for `x`?
```typescript
const x = 'ready';
```
<label><input type="radio" name="q142"> A. string</label><br>
<label><input type="radio" name="q142"> B. "ready"</label><br>
<label><input type="radio" name="q142"> C. any</label><br>
<label><input type="radio" name="q142"> D. unknown</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `x` has literal type `"ready"`. A primitive `const` binding preserves its literal type.
</details>

### 143. [ ] What type is inferred for `x`?
```typescript
let x = 'ready';
```
<label><input type="radio" name="q143"> A. string</label><br>
<label><input type="radio" name="q143"> B. "ready"</label><br>
<label><input type="radio" name="q143"> C. any</label><br>
<label><input type="radio" name="q143"> D. never</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** `x` is inferred as `string`. A mutable `let` initializer is widened here.
</details>

### 144. [ ] Does this compile?
```typescript
const xs: number[] = [1, 2];
xs.push(3);
```
<label><input type="radio" name="q144"> A. Compiles</label><br>
<label><input type="radio" name="q144"> B. Error: const arrays are immutable</label><br>
<label><input type="radio" name="q144"> C. Error: push accepts strings only</label><br>
<label><input type="radio" name="q144"> D. Error: tuple length is fixed</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles, and runtime array becomes `[1, 2, 3]`. `const` prevents rebinding, not mutation.
</details>

### 145. [ ] Does this compile?
```typescript
const xs: readonly number[] = [1, 2];
xs.push(3);
```
<label><input type="radio" name="q145"> A. Compiles</label><br>
<label><input type="radio" name="q145"> B. Error: push does not exist on readonly number[]</label><br>
<label><input type="radio" name="q145"> C. Error: numbers cannot be readonly</label><br>
<label><input type="radio" name="q145"> D. Runtime TypeError only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—mutating `push` is unavailable on `readonly number[]`. Readonly is enforced statically.
</details>

### 146. [ ] What is logged after successful compilation?
```typescript
const pair: [string, number] = ['age', 7];
console.log(pair[1]);
```
<label><input type="radio" name="q146"> A. "age"</label><br>
<label><input type="radio" name="q146"> B. 7</label><br>
<label><input type="radio" name="q146"> C. undefined</label><br>
<label><input type="radio" name="q146"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**B. Output:** `7`; it compiles. The tuple's second element has type `number`.
</details>

### 147. [ ] Does this compile?
```typescript
let value: unknown = 'x';
console.log(value.length);
```
<label><input type="radio" name="q147"> A. Compiles; outputs 1</label><br>
<label><input type="radio" name="q147"> B. Error: value is of type unknown</label><br>
<label><input type="radio" name="q147"> C. Error: unknown is never</label><br>
<label><input type="radio" name="q147"> D. Runtime TypeError only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—an `unknown` value must be narrowed before property access.
</details>

### 148. [ ] Does this compile?
```typescript
let value: any = 'x';
console.log(value.length);
```
<label><input type="radio" name="q148"> A. Compiles; outputs 1</label><br>
<label><input type="radio" name="q148"> B. Error: value is unknown</label><br>
<label><input type="radio" name="q148"> C. Error: length is private</label><br>
<label><input type="radio" name="q148"> D. Compiles; outputs undefined</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1`; it compiles. `any` disables static checking for the access.
</details>

### 149. [ ] What is the compile result?
```typescript
function fail(): never { throw new Error('x'); }
```
<label><input type="radio" name="q149"> A. Compiles</label><br>
<label><input type="radio" name="q149"> B. Error: never functions cannot throw</label><br>
<label><input type="radio" name="q149"> C. Error: missing return value</label><br>
<label><input type="radio" name="q149"> D. Error: Error is not never</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. A function that always throws never returns normally, matching `never`.
</details>

### 150. [ ] Does this compile?
```typescript
interface User { name: string; age?: number }
const u: User = { name: 'Ada' };
```
<label><input type="radio" name="q150"> A. Compiles</label><br>
<label><input type="radio" name="q150"> B. Error: age is required</label><br>
<label><input type="radio" name="q150"> C. Error: interfaces need classes</label><br>
<label><input type="radio" name="q150"> D. Error: object is readonly</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. The `?` marks `age` as optional.
</details>

### 151. [ ] Does this compile?
```typescript
interface User { name: string }
const u: User = { name: 'Ada', age: 30 };
```
<label><input type="radio" name="q151"> A. Compiles by structural typing</label><br>
<label><input type="radio" name="q151"> B. Error: excess property age on fresh literal</label><br>
<label><input type="radio" name="q151"> C. Error: name must be readonly</label><br>
<label><input type="radio" name="q151"> D. Runtime error only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—fresh object literals receive excess-property checking, and `age` is not declared.
</details>

### 152. [ ] What is logged?
```typescript
enum Direction { Up, Down }
console.log(Direction.Down);
```
<label><input type="radio" name="q152"> A. 0</label><br>
<label><input type="radio" name="q152"> B. 1</label><br>
<label><input type="radio" name="q152"> C. "Down"</label><br>
<label><input type="radio" name="q152"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**B. Output:** `1`. Numeric enum members auto-increment from zero.
</details>

### 153. [ ] Does this compile?
```typescript
let x: string | undefined;
console.log(x.toUpperCase());
```
<label><input type="radio" name="q153"> A. Compiles</label><br>
<label><input type="radio" name="q153"> B. Error: x is possibly undefined</label><br>
<label><input type="radio" name="q153"> C. Error: unions cannot have methods</label><br>
<label><input type="radio" name="q153"> D. Runtime output is undefined</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—strict null checking requires handling `undefined` first.
</details>

### 154. [ ] What is logged?
```typescript
const x = 5 as const;
console.log(x);
```
<label><input type="radio" name="q154"> A. 5</label><br>
<label><input type="radio" name="q154"> B. "5"</label><br>
<label><input type="radio" name="q154"> C. undefined</label><br>
<label><input type="radio" name="q154"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**A. Output:** `5`; its static type is literal `5`. Type assertions are erased at runtime.
</details>

### 155. [ ] Does this compile?
```typescript
type Point = { readonly x: number };
const p: Point = { x: 1 };
p.x = 2;
```
<label><input type="radio" name="q155"> A. Compiles; outputs 2</label><br>
<label><input type="radio" name="q155"> B. Error: cannot assign to readonly x</label><br>
<label><input type="radio" name="q155"> C. Error: type aliases cannot describe objects</label><br>
<label><input type="radio" name="q155"> D. Runtime TypeError only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—`readonly` prevents assignment through this type.
</details>

---

## Narrowing and unions (156–170)

### 156. [ ] What is logged?
```typescript
function f(x: string | number) {
  return typeof x === 'string' ? x.length : x * 2;
}
console.log(f('abc'));
```
<label><input type="radio" name="q156"> A. 3</label><br>
<label><input type="radio" name="q156"> B. 6</label><br>
<label><input type="radio" name="q156"> C. "abcabc"</label><br>
<label><input type="radio" name="q156"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**A. Output:** `3`. The `typeof` check narrows `x` to `string` in that branch.
</details>

### 157. [ ] Does this compile?
```typescript
function f(x: string | number) {
  return x.toUpperCase();
}
```
<label><input type="radio" name="q157"> A. Compiles</label><br>
<label><input type="radio" name="q157"> B. Error: method not present on number</label><br>
<label><input type="radio" name="q157"> C. Error: string has no methods</label><br>
<label><input type="radio" name="q157"> D. Error: union must include null</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—`toUpperCase` is not safe for the `number` member of the union.
</details>

### 158. [ ] What is logged?
```typescript
type A = { kind: 'a'; value: number };
type B = { kind: 'b'; value: string };
function f(x: A | B) { return x.kind === 'a' ? x.value + 1 : x.value.length; }
console.log(f({ kind: 'a', value: 2 }));
```
<label><input type="radio" name="q158"> A. 2</label><br>
<label><input type="radio" name="q158"> B. 3</label><br>
<label><input type="radio" name="q158"> C. undefined</label><br>
<label><input type="radio" name="q158"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**B. Output:** `3`. The discriminant narrows `x` to `A`, so numeric addition is valid.
</details>

### 159. [ ] Does this compile?
```typescript
function f(x: string | null) {
  if (x) return x.length;
  return 0;
}
```
<label><input type="radio" name="q159"> A. Compiles</label><br>
<label><input type="radio" name="q159"> B. Error: x may be null in the if branch</label><br>
<label><input type="radio" name="q159"> C. Error: empty strings are impossible</label><br>
<label><input type="radio" name="q159"> D. Error: return types differ</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. Truthiness narrowing removes `null` (and the empty-string value) in the branch.
</details>

### 160. [ ] What is logged?
```typescript
function f(x: Date | string) {
  return x instanceof Date ? x.getFullYear() : x.length;
}
console.log(f('year'));
```
<label><input type="radio" name="q160"> A. 4</label><br>
<label><input type="radio" name="q160"> B. "year"</label><br>
<label><input type="radio" name="q160"> C. Current year</label><br>
<label><input type="radio" name="q160"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**A. Output:** `4`. The `instanceof` guard leaves `string` in the other branch.
</details>

### 161. [ ] What is logged?
```typescript
type X = { a: number } | { b: string };
function f(x: X) { return 'a' in x ? x.a : x.b.length; }
console.log(f({ b: 'ok' }));
```
<label><input type="radio" name="q161"> A. 1</label><br>
<label><input type="radio" name="q161"> B. 2</label><br>
<label><input type="radio" name="q161"> C. "ok"</label><br>
<label><input type="radio" name="q161"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**B. Output:** `2`. The `in` check narrows to the member containing `a`; the else member contains `b`.
</details>

### 162. [ ] Does this compile?
```typescript
function isString(x: unknown): x is string {
  return typeof x === 'string';
}
const x: unknown = 'hi';
if (isString(x)) console.log(x.length);
```
<label><input type="radio" name="q162"> A. Compiles; outputs 2</label><br>
<label><input type="radio" name="q162"> B. Error: unknown cannot be passed</label><br>
<label><input type="radio" name="q162"> C. Error: predicates cannot use unknown</label><br>
<label><input type="radio" name="q162"> D. Compiles; outputs undefined</label><br>
<details><summary>Show answer</summary>

**A. Output:** `2`; it compiles. The user-defined type predicate narrows `x` to `string`.
</details>

### 163. [ ] What is the inferred return type?
```typescript
function f(x: boolean) {
  return x ? 'yes' : 0;
}
```
<label><input type="radio" name="q163"> A. string</label><br>
<label><input type="radio" name="q163"> B. number</label><br>
<label><input type="radio" name="q163"> C. string | number</label><br>
<label><input type="radio" name="q163"> D. any</label><br>
<details><summary>Show answer</summary>

**C. Compile result:** return type `string | number` (with widened branch literals). Both possible return values contribute to inference.
</details>

### 164. [ ] Does this compile?
```typescript
type Status = 'open' | 'closed';
const s: Status = 'pending';
```
<label><input type="radio" name="q164"> A. Compiles</label><br>
<label><input type="radio" name="q164"> B. Error: pending is not assignable to Status</label><br>
<label><input type="radio" name="q164"> C. Error: literal unions require enums</label><br>
<label><input type="radio" name="q164"> D. Runtime error only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—`"pending"` is not a member of the declared literal union.
</details>

### 165. [ ] What is logged?
```typescript
function f(x?: number) { return x ?? 10; }
console.log(f(0), f());
```
<label><input type="radio" name="q165"> A. 10 10</label><br>
<label><input type="radio" name="q165"> B. 0 10</label><br>
<label><input type="radio" name="q165"> C. 0 undefined</label><br>
<label><input type="radio" name="q165"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**B. Output:** `0 10`. An optional parameter includes `undefined`; `??` preserves zero and replaces `undefined`.
</details>

### 166. [ ] Does this compile?
```typescript
function lengthOf(x: string | undefined) { return x!.length; }
console.log(lengthOf(undefined));
```
<label><input type="radio" name="q166"> A. Compile error: x may be undefined</label><br>
<label><input type="radio" name="q166"> B. Compiles, then runtime TypeError</label><br>
<label><input type="radio" name="q166"> C. Compiles; outputs 0</label><br>
<label><input type="radio" name="q166"> D. Compiles; outputs undefined</label><br>
<details><summary>Show answer</summary>

**B. Compile/runtime result:** it compiles, then throws `TypeError` at runtime. The non-null assertion removes the static warning but emits no check.
</details>

### 167. [ ] Does this compile?
```typescript
type A = { a: number };
type B = { b: string };
const x: A & B = { a: 1, b: 'x' };
```
<label><input type="radio" name="q167"> A. Compiles</label><br>
<label><input type="radio" name="q167"> B. Error: intersections are impossible</label><br>
<label><input type="radio" name="q167"> C. Error: only a is allowed</label><br>
<label><input type="radio" name="q167"> D. Error: only b is allowed</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. The intersection requires both object shapes, which the literal supplies.
</details>

### 168. [ ] What is the result of exhaustiveness checking?
```typescript
type S = 'a' | 'b';
function f(s: S) {
  if (s === 'a') return 1;
  const rest: never = s;
  return rest;
}
```
<label><input type="radio" name="q168"> A. Compiles</label><br>
<label><input type="radio" name="q168"> B. Error: string is not assignable to never</label><br>
<label><input type="radio" name="q168"> C. Error: never cannot be returned</label><br>
<label><input type="radio" name="q168"> D. Runtime TypeError</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—after excluding `"a"`, `s` is still `"b"`, which is not assignable to `never`.
</details>

### 169. [ ] Does this compile?
```typescript
function f(x: string | number) {
  if (typeof x === 'string') return x.toUpperCase();
  return x.toFixed(1);
}
```
<label><input type="radio" name="q169"> A. Compiles</label><br>
<label><input type="radio" name="q169"> B. Error: x is never in second branch</label><br>
<label><input type="radio" name="q169"> C. Error: return types differ</label><br>
<label><input type="radio" name="q169"> D. Error: typeof cannot narrow</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. Control-flow analysis narrows the second branch to `number`; both branches return strings.
</details>

### 170. [ ] What is logged?
```typescript
type R = { ok: true; value: number } | { ok: false; error: string };
function show(r: R) { return r.ok ? r.value : r.error.length; }
console.log(show({ ok: true, value: 4 }));
```
<label><input type="radio" name="q170"> A. 4</label><br>
<label><input type="radio" name="q170"> B. true</label><br>
<label><input type="radio" name="q170"> C. undefined</label><br>
<label><input type="radio" name="q170"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**A. Output:** `4`. The boolean literal discriminant selects the successful union member.
</details>

---

## Generics, keyof, and utility types (171–185)

### 171. [ ] What is logged?
```typescript
function id<T>(x: T): T { return x; }
console.log(id(5));
```
<label><input type="radio" name="q171"> A. 5</label><br>
<label><input type="radio" name="q171"> B. T</label><br>
<label><input type="radio" name="q171"> C. undefined</label><br>
<label><input type="radio" name="q171"> D. Compile error</label><br>
<details><summary>Show answer</summary>

**A. Output:** `5`. `T` is inferred as `number` and erased from emitted JavaScript.
</details>

### 172. [ ] Does this compile?
```typescript
function len<T extends { length: number }>(x: T) { return x.length; }
len(42);
```
<label><input type="radio" name="q172"> A. Compiles; returns 0</label><br>
<label><input type="radio" name="q172"> B. Error: number lacks length</label><br>
<label><input type="radio" name="q172"> C. Error: generics cannot be constrained</label><br>
<label><input type="radio" name="q172"> D. Runtime TypeError only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—`number` does not satisfy the constraint requiring a numeric `length` property.
</details>

### 173. [ ] What is `keyof User`?
```typescript
type User = { id: number; name: string };
type K = keyof User;
```
<label><input type="radio" name="q173"> A. string</label><br>
<label><input type="radio" name="q173"> B. "id" | "name"</label><br>
<label><input type="radio" name="q173"> C. number | string</label><br>
<label><input type="radio" name="q173"> D. never</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `K` is `"id" | "name"`. `keyof` produces the known property-key union.
</details>

### 174. [ ] Does this compile?
```typescript
function get<T, K extends keyof T>(o: T, k: K): T[K] { return o[k]; }
get({ id: 1 }, 'name');
```
<label><input type="radio" name="q174"> A. Compiles; returns undefined</label><br>
<label><input type="radio" name="q174"> B. Error: name is not a key</label><br>
<label><input type="radio" name="q174"> C. Error: indexed access is invalid</label><br>
<label><input type="radio" name="q174"> D. Error: T cannot be inferred</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—`"name"` does not satisfy the key constraint `"id"`.
</details>

### 175. [ ] Does this compile?
```typescript
type User = { id: number; name: string };
const u: Partial<User> = { id: 1 };
```
<label><input type="radio" name="q175"> A. Compiles</label><br>
<label><input type="radio" name="q175"> B. Error: name is required</label><br>
<label><input type="radio" name="q175"> C. Error: Partial makes fields readonly</label><br>
<label><input type="radio" name="q175"> D. Error: id must be string</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. `Partial` makes every property optional.
</details>

### 176. [ ] Does this compile?
```typescript
type User = { id?: number };
const u: Required<User> = {};
```
<label><input type="radio" name="q176"> A. Compiles</label><br>
<label><input type="radio" name="q176"> B. Error: id is required</label><br>
<label><input type="radio" name="q176"> C. Error: Required removes id</label><br>
<label><input type="radio" name="q176"> D. Runtime error only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—`Required` removes the optional modifier, so `id` must be present.
</details>

### 177. [ ] Does this compile?
```typescript
type User = { id: number; name: string };
const u: Pick<User, 'id'> = { id: 1 };
```
<label><input type="radio" name="q177"> A. Compiles</label><br>
<label><input type="radio" name="q177"> B. Error: name is required</label><br>
<label><input type="radio" name="q177"> C. Error: Pick requires all keys</label><br>
<label><input type="radio" name="q177"> D. Error: id becomes string</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. `Pick` retains only the selected `id` property.
</details>

### 178. [ ] Does this compile?
```typescript
type User = { id: number; name: string };
const u: Omit<User, 'name'> = { id: 1 };
```
<label><input type="radio" name="q178"> A. Compiles</label><br>
<label><input type="radio" name="q178"> B. Error: name is required</label><br>
<label><input type="radio" name="q178"> C. Error: Omit removes id</label><br>
<label><input type="radio" name="q178"> D. Error: Omit accepts no key</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. `Omit` removes `name` from the object type.
</details>

### 179. [ ] Does this compile?
```typescript
const counts: Record<'a' | 'b', number> = { a: 1 };
```
<label><input type="radio" name="q179"> A. Compiles</label><br>
<label><input type="radio" name="q179"> B. Error: property b is missing</label><br>
<label><input type="radio" name="q179"> C. Error: Record keys must be numbers</label><br>
<label><input type="radio" name="q179"> D. Runtime error only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—`Record` requires both `a` and `b` with numeric values.
</details>

### 180. [ ] What is `R`?
```typescript
type R = ReturnType<() => string>;
```
<input type="radio" name="q180"> A. `() => string`<br>
<label><input type="radio" name="q180"> B. string</label><br>
<label><input type="radio" name="q180"> C. unknown</label><br>
<label><input type="radio" name="q180"> D. never</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `R` is `string`. `ReturnType` extracts a function's return type.
</details>

### 181. [ ] What is `P`?
```typescript
type P = Parameters<(x: number, y: string) => void>;
```
<label><input type="radio" name="q181"> A. [number, string]</label><br>
<label><input type="radio" name="q181"> B. number | string</label><br>
<label><input type="radio" name="q181"> C. void</label><br>
<label><input type="radio" name="q181"> D. unknown[]</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** `P` is tuple `[x: number, y: string]`, structurally `[number, string]`. `Parameters` extracts the parameter list.
</details>

### 182. [ ] What is `R`?
```typescript
type R = Exclude<'a' | 'b' | 'c', 'b'>;
```
<label><input type="radio" name="q182"> A. "b"</label><br>
<label><input type="radio" name="q182"> B. "a" | "c"</label><br>
<label><input type="radio" name="q182"> C. never</label><br>
<label><input type="radio" name="q182"> D. string</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `R` is `"a" | "c"`. `Exclude` removes union members assignable to its second argument.
</details>

### 183. [ ] What is `R`?
```typescript
type R = Extract<'a' | 'b' | 1, string>;
```
<label><input type="radio" name="q183"> A. "a" | "b"</label><br>
<label><input type="radio" name="q183"> B. 1</label><br>
<label><input type="radio" name="q183"> C. string</label><br>
<label><input type="radio" name="q183"> D. never</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** `R` is `"a" | "b"`. `Extract` keeps union members assignable to `string`.
</details>

### 184. [ ] What is `R`?
```typescript
type R = NonNullable<string | null | undefined>;
```
<label><input type="radio" name="q184"> A. string</label><br>
<label><input type="radio" name="q184"> B. null | undefined</label><br>
<label><input type="radio" name="q184"> C. string | null</label><br>
<label><input type="radio" name="q184"> D. never</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** `R` is `string`. `NonNullable` removes `null` and `undefined`.
</details>

### 185. [ ] What is `AwaitedValue`?
```typescript
type AwaitedValue = Awaited<Promise<Promise<number>>>;
```
<input type="radio" name="q185"> A. `Promise<number>`<br>
<label><input type="radio" name="q185"> B. number</label><br>
<label><input type="radio" name="q185"> C. unknown</label><br>
<label><input type="radio" name="q185"> D. never</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `AwaitedValue` is `number`. `Awaited` recursively unwraps promise-like types.
</details>

---

## Advanced TypeScript and classes (186–200)

### 186. [ ] What is `X`?
```typescript
type R<T> = T extends string ? 'S' : 'N';
type X = R<number>;
```
<label><input type="radio" name="q186"> A. "S"</label><br>
<label><input type="radio" name="q186"> B. "N"</label><br>
<label><input type="radio" name="q186"> C. boolean</label><br>
<label><input type="radio" name="q186"> D. never</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `X` is `"N"`. `number` does not extend `string`, so the false branch is selected.
</details>

### 187. [ ] What is `X`?
```typescript
type R<T> = T extends string ? T[] : never;
type X = R<'a' | 1>;
```
<label><input type="radio" name="q187"> A. "a"[]</label><br>
<label><input type="radio" name="q187"> B. 1[]</label><br>
<label><input type="radio" name="q187"> C. ("a" | 1)[]</label><br>
<label><input type="radio" name="q187"> D. never</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** `X` is `"a"[]`. The naked type parameter makes the conditional distribute, and the numeric branch becomes `never`.
</details>

### 188. [ ] What is `X`?
```typescript
type Elem<T> = T extends (infer U)[] ? U : never;
type X = Elem<string[]>;
```
<label><input type="radio" name="q188"> A. string</label><br>
<label><input type="radio" name="q188"> B. string[]</label><br>
<label><input type="radio" name="q188"> C. unknown</label><br>
<label><input type="radio" name="q188"> D. never</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** `X` is `string`. `infer U` captures the array element type.
</details>

### 189. [ ] Does this compile?
```typescript
type Flags<T> = { [K in keyof T]: boolean };
type F = Flags<{ a: number; b: string }>;
const f: F = { a: true, b: false };
```
<label><input type="radio" name="q189"> A. Compiles</label><br>
<label><input type="radio" name="q189"> B. Error: a must be number</label><br>
<label><input type="radio" name="q189"> C. Error: b must be string</label><br>
<label><input type="radio" name="q189"> D. Error: mapped types cannot create objects</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. The mapped type preserves keys while replacing every value type with `boolean`.
</details>

### 190. [ ] What is `HandlerName`?
```typescript
type Name = 'click' | 'focus';
type HandlerName = `on${Capitalize<Name>}`;
```
<label><input type="radio" name="q190"> A. "onclick" | "onfocus"</label><br>
<label><input type="radio" name="q190"> B. "onClick" | "onFocus"</label><br>
<label><input type="radio" name="q190"> C. string</label><br>
<label><input type="radio" name="q190"> D. never</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `HandlerName` is `"onClick" | "onFocus"`. The template literal distributes over the union and capitalizes each name.
</details>

### 191. [ ] Does this compile?
```typescript
class A { private x = 1; }
const a = new A();
console.log(a.x);
```
<label><input type="radio" name="q191"> A. Compiles; outputs 1</label><br>
<label><input type="radio" name="q191"> B. Error: x is private</label><br>
<label><input type="radio" name="q191"> C. Compiles; outputs undefined</label><br>
<label><input type="radio" name="q191"> D. Runtime SyntaxError only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—TypeScript `private` restricts access to class `A`'s body.
</details>

### 192. [ ] Does this compile?
```typescript
abstract class A { abstract f(): number; }
class B extends A {}
```
<label><input type="radio" name="q192"> A. Compiles</label><br>
<label><input type="radio" name="q192"> B. Error: B does not implement f</label><br>
<label><input type="radio" name="q192"> C. Error: abstract classes cannot be extended</label><br>
<label><input type="radio" name="q192"> D. Runtime error only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—non-abstract `B` must implement inherited abstract member `f`.
</details>

### 193. [ ] What is logged?
```typescript
class A {
  constructor(public x: number) {}
}
console.log(new A(3).x);
```
<label><input type="radio" name="q193"> A. 3</label><br>
<label><input type="radio" name="q193"> B. undefined</label><br>
<label><input type="radio" name="q193"> C. Compile error</label><br>
<label><input type="radio" name="q193"> D. TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `3`. The parameter property declares and initializes public instance field `x`.
</details>

### 194. [ ] Does this compile?
```typescript
interface HasName { name: string }
class User implements HasName {
  constructor(public name: string) {}
}
```
<label><input type="radio" name="q194"> A. Compiles</label><br>
<label><input type="radio" name="q194"> B. Error: classes cannot implement interfaces</label><br>
<label><input type="radio" name="q194"> C. Error: name is not declared</label><br>
<label><input type="radio" name="q194"> D. Error: constructor must be private</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** compiles. The public parameter property satisfies the interface structurally.
</details>

### 195. [ ] Does this compile?
```typescript
class Box<T> { constructor(public value: T) {} }
const b = new Box(4);
b.value = 'x';
```
<label><input type="radio" name="q195"> A. Compiles; T becomes string | number</label><br>
<label><input type="radio" name="q195"> B. Error: string is not assignable to number</label><br>
<label><input type="radio" name="q195"> C. Error: generic classes need explicit T</label><br>
<label><input type="radio" name="q195"> D. Error: value is readonly</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—construction infers `Box<number>`, so `value` accepts numbers only.
</details>

### 196. [ ] What is `X`?
```typescript
const config = { mode: 'dark' } as const;
type X = typeof config.mode;
```
<label><input type="radio" name="q196"> A. string</label><br>
<label><input type="radio" name="q196"> B. "dark"</label><br>
<label><input type="radio" name="q196"> C. { mode: string }</label><br>
<label><input type="radio" name="q196"> D. never</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `X` is literal type `"dark"`. The const assertion prevents property widening.
</details>

### 197. [ ] Does this compile?
```typescript
type Config = { mode: 'dark' | 'light' };
const c = { mode: 'dark', debug: true } satisfies Config;
console.log(c.debug);
```
<label><input type="radio" name="q197"> A. Compiles; outputs true</label><br>
<label><input type="radio" name="q197"> B. Error: debug is an excess property</label><br>
<label><input type="radio" name="q197"> C. Error: satisfies removes debug</label><br>
<label><input type="radio" name="q197"> D. Compiles; outputs undefined</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** error—`satisfies` contextually checks the fresh object literal, and `debug` is not in `Config`.
</details>

### 198. [ ] Which overload result is inferred for `x`?
```typescript
function f(x: string): number;
function f(x: number): string;
function f(x: string | number) { return typeof x === 'string' ? x.length : String(x); }
const x = f('abc');
```
<label><input type="radio" name="q198"> A. number</label><br>
<label><input type="radio" name="q198"> B. string</label><br>
<label><input type="radio" name="q198"> C. string | number</label><br>
<label><input type="radio" name="q198"> D. any</label><br>
<details><summary>Show answer</summary>

**A. Compile result:** `x` is `number` (runtime value `3`). The string overload is selected at the call site.
</details>

### 199. [ ] Does this compile?
```typescript
class Base { protected x = 1; }
class Child extends Base { value() { return this.x; } }
console.log(new Child().value());
```
<label><input type="radio" name="q199"> A. Compiles; outputs 1</label><br>
<label><input type="radio" name="q199"> B. Error: protected members are never accessible</label><br>
<label><input type="radio" name="q199"> C. Compiles; outputs undefined</label><br>
<label><input type="radio" name="q199"> D. Runtime TypeError</label><br>
<details><summary>Show answer</summary>

**A. Output:** `1`; it compiles. A subclass may access an inherited protected member internally.
</details>

### 200. [ ] What is the compile result?
```typescript
type ReadonlyUser = Readonly<{ name: string }>;
const u: ReadonlyUser = { name: 'Ada' };
u.name = 'Grace';
```
<label><input type="radio" name="q200"> A. Compiles; name becomes Grace</label><br>
<label><input type="radio" name="q200"> B. TypeScript error: cannot assign to name because it is read-only</label><br>
<label><input type="radio" name="q200"> C. Error: Readonly removes name</label><br>
<label><input type="radio" name="q200"> D. Runtime TypeError only</label><br>
<details><summary>Show answer</summary>

**B. Compile result:** `Cannot assign to 'name' because it is a read-only property.` The `Readonly<T>` utility makes each property read-only at compile time. It does not call `Object.freeze()`; if JavaScript were emitted despite the type error, the assignment would succeed at runtime because TypeScript types are erased.
</details>

---

[← Previous: Most Asked Interview Questions](./31-most-asked-interview-questions.md) · [Home](./README.md)
