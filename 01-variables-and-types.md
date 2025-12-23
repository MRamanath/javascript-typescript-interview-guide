# 01. Variables and Data Types

## 📚 Overview

JavaScript variables and data types are the foundation of programming. Understanding how to declare variables and work with different data types is essential for writing effective JavaScript code.

## 🎯 Key Concepts

### Variable Declaration

```
var   - Function-scoped, hoisted, can be redeclared
let   - Block-scoped, not hoisted, cannot be redeclared
const - Block-scoped, immutable reference, must be initialized
```

### Primitive Data Types

```
string    - Text data
number    - Numeric values (integers and floats)
boolean   - true or false
null      - Intentional absence of value
undefined - Variable declared but not assigned
symbol    - Unique identifier (ES6+)
bigint    - Large integers (ES2020+)
```

## 💻 Examples

### Variable Declarations

```javascript
// var - function scoped
var name = 'John';
var name = 'Jane'; // Can be redeclared
console.log(name); // 'Jane'

// let - block scoped
let age = 25;
// let age = 30; // Error: Cannot redeclare
age = 30; // OK - can be reassigned
console.log(age); // 30

// const - block scoped, immutable reference
const PI = 3.14159;
// PI = 3.14; // Error: Cannot reassign
// const value; // Error: Must be initialized
console.log(PI); // 3.14159

// const with objects (reference is immutable, not content)
const person = { name: 'John' };
person.name = 'Jane'; // OK - modifying property
person.age = 30; // OK - adding property
// person = {}; // Error: Cannot reassign reference
console.log(person); // { name: 'Jane', age: 30 }
```

### String Type

```javascript
// String creation
const single = 'Single quotes';
const double = "Double quotes";
const template = `Template literal`;

// String concatenation
const firstName = 'John';
const lastName = 'Doe';
const fullName = firstName + ' ' + lastName;
console.log(fullName); // 'John Doe'

// Template literals
const name = 'Alice';
const age = 25;
const greeting = `Hello, my name is ${name} and I'm ${age} years old.`;
console.log(greeting);

// Multi-line strings
const multiLine = `
  This is a
  multi-line
  string
`;

// String methods
const text = 'JavaScript';
console.log(text.length); // 10
console.log(text.toUpperCase()); // 'JAVASCRIPT'
console.log(text.toLowerCase()); // 'javascript'
console.log(text.charAt(0)); // 'J'
console.log(text.substring(0, 4)); // 'Java'
console.log(text.includes('Script')); // true
console.log(text.split('')); // ['J', 'a', 'v', 'a', ...]
console.log(text.replace('Java', 'Type')); // 'TypeScript'
```

### Number Type

```javascript
// Integer and float
const integer = 42;
const float = 3.14;
const negative = -10;

// Special numeric values
console.log(Infinity); // Infinity
console.log(-Infinity); // -Infinity
console.log(NaN); // Not a Number

// Number methods
const num = 123.456;
console.log(num.toFixed(2)); // '123.46'
console.log(num.toPrecision(4)); // '123.5'
console.log(parseInt('123')); // 123
console.log(parseFloat('123.45')); // 123.45
console.log(Number.isInteger(123)); // true
console.log(Number.isNaN(NaN)); // true

// Math operations
console.log(10 + 5); // 15
console.log(10 - 5); // 5
console.log(10 * 5); // 50
console.log(10 / 5); // 2
console.log(10 % 3); // 1 (modulus)
console.log(2 ** 3); // 8 (exponentiation)

// Math object
console.log(Math.PI); // 3.141592653589793
console.log(Math.round(4.7)); // 5
console.log(Math.ceil(4.1)); // 5
console.log(Math.floor(4.9)); // 4
console.log(Math.max(1, 5, 3)); // 5
console.log(Math.min(1, 5, 3)); // 1
console.log(Math.random()); // Random number 0-1
console.log(Math.abs(-5)); // 5
```

### Boolean Type

```javascript
// Boolean values
const isTrue = true;
const isFalse = false;

// Boolean conversion
console.log(Boolean(1)); // true
console.log(Boolean(0)); // false
console.log(Boolean('hello')); // true
console.log(Boolean('')); // false
console.log(Boolean(null)); // false
console.log(Boolean(undefined)); // false

// Comparison operators
console.log(5 > 3); // true
console.log(5 < 3); // false
console.log(5 >= 5); // true
console.log(5 <= 5); // true
console.log(5 == 5); // true (loose equality)
console.log(5 === 5); // true (strict equality)
console.log(5 != 3); // true
console.log(5 !== '5'); // true

// Logical operators
console.log(true && true); // true (AND)
console.log(true || false); // true (OR)
console.log(!true); // false (NOT)

// Truthy and falsy values
// Falsy: false, 0, '', null, undefined, NaN
// Everything else is truthy
if ('hello') console.log('Truthy!'); // Executes
if (0) console.log('Falsy!'); // Does not execute
```

### Null and Undefined

```javascript
// undefined - variable declared but not assigned
let x;
console.log(x); // undefined
console.log(typeof x); // 'undefined'

// null - intentional absence of value
let y = null;
console.log(y); // null
console.log(typeof y); // 'object' (JavaScript quirk)

// Difference
console.log(undefined == null); // true (loose equality)
console.log(undefined === null); // false (strict equality)

// Common use cases
function findUser(id) {
  // Return null if not found
  return id === 1 ? { id: 1, name: 'John' } : null;
}

let config = {
  timeout: 3000,
  retries: undefined // Explicitly undefined
};
```

### Symbol Type

```javascript
// Unique identifiers
const sym1 = Symbol();
const sym2 = Symbol('description');
const sym3 = Symbol('description');

console.log(sym2 === sym3); // false (always unique)
console.log(typeof sym1); // 'symbol'

// Symbol as object key
const id = Symbol('id');
const user = {
  name: 'John',
  [id]: 123
};

console.log(user[id]); // 123
console.log(Object.keys(user)); // ['name'] - symbols not included

// Well-known symbols
const arr = [1, 2, 3];
console.log(arr[Symbol.iterator]); // [Function: values]
```

### BigInt Type

```javascript
// Large integers
const bigInt1 = 9007199254740991n;
const bigInt2 = BigInt('9007199254740991');

console.log(typeof bigInt1); // 'bigint'
console.log(bigInt1 + 1n); // 9007199254740992n

// Cannot mix BigInt with Number
// console.log(bigInt1 + 1); // Error
console.log(bigInt1 + BigInt(1)); // OK

// BigInt methods
console.log(BigInt.asIntN(64, 2n ** 63n - 1n));
console.log(BigInt.asUintN(64, 2n ** 64n - 1n));
```

### Type Checking

```javascript
// typeof operator
console.log(typeof 'hello'); // 'string'
console.log(typeof 42); // 'number'
console.log(typeof true); // 'boolean'
console.log(typeof undefined); // 'undefined'
console.log(typeof null); // 'object' (quirk)
console.log(typeof Symbol()); // 'symbol'
console.log(typeof 10n); // 'bigint'
console.log(typeof {}); // 'object'
console.log(typeof []); // 'object'
console.log(typeof function() {}); // 'function'

// More accurate type checking
console.log(Array.isArray([])); // true
console.log(Number.isInteger(42)); // true
console.log(Number.isNaN(NaN)); // true
console.log(Object.prototype.toString.call([])); // '[object Array]'
```

### Type Conversion

```javascript
// String conversion
console.log(String(123)); // '123'
console.log(String(true)); // 'true'
console.log(String(null)); // 'null'
console.log(123 + ''); // '123'

// Number conversion
console.log(Number('123')); // 123
console.log(Number('123.45')); // 123.45
console.log(Number(true)); // 1
console.log(Number(false)); // 0
console.log(Number(null)); // 0
console.log(Number(undefined)); // NaN
console.log(+'123'); // 123

// Boolean conversion
console.log(Boolean(1)); // true
console.log(Boolean(0)); // false
console.log(Boolean('hello')); // true
console.log(Boolean('')); // false
console.log(!!'hello'); // true

// Implicit conversion
console.log('5' + 3); // '53' (string concatenation)
console.log('5' - 3); // 2 (numeric subtraction)
console.log('5' * '2'); // 10 (numeric multiplication)
console.log('5' / '2'); // 2.5 (numeric division)
```

### Variable Scope

```javascript
// Global scope
var globalVar = 'global';

function demo() {
  // Function scope (var)
  var functionVar = 'function';
  
  if (true) {
    // Block scope (let/const)
    let blockLet = 'block';
    const blockConst = 'block';
    var functionVar2 = 'function'; // Still function-scoped
    
    console.log(blockLet); // 'block'
  }
  
  // console.log(blockLet); // Error: not defined
  console.log(functionVar2); // 'function' - accessible
}

// Hoisting
console.log(hoistedVar); // undefined
var hoistedVar = 'I am hoisted';

// console.log(notHoisted); // Error: Cannot access before initialization
let notHoisted = 'Not hoisted';
```

## 🎤 Interview Questions

### Q1: What's the difference between var, let, and const?
**Answer:**
- **var**: Function-scoped, hoisted, can be redeclared
- **let**: Block-scoped, not hoisted (temporal dead zone), cannot be redeclared
- **const**: Block-scoped, immutable reference, must be initialized

### Q2: What are the primitive data types in JavaScript?
**Answer:** string, number, boolean, null, undefined, symbol, bigint (7 types)

### Q3: What's the difference between null and undefined?
**Answer:**
- **undefined**: Variable declared but not assigned
- **null**: Intentional absence of value (must be explicitly assigned)

### Q4: What is type coercion?
**Answer:** Automatic or implicit conversion of values from one type to another. Example: `'5' + 3` becomes `'53'` (string concatenation)

### Q5: What are truthy and falsy values?
**Answer:**
- **Falsy**: false, 0, '', null, undefined, NaN
- **Truthy**: Everything else

### Q6: What's the difference between == and ===?
**Answer:**
- **==**: Loose equality, performs type coercion
- **===**: Strict equality, no type coercion

### Q7: Why does typeof null return 'object'?
**Answer:** It's a JavaScript bug that can't be fixed for backward compatibility reasons.

### Q8: What is the temporal dead zone?
**Answer:** The period between entering scope and variable declaration where let/const variables cannot be accessed.

### Q9: Can you modify a const object?
**Answer:** Yes, you can modify properties but cannot reassign the reference:
```javascript
const obj = { a: 1 };
obj.a = 2; // OK
// obj = {}; // Error
```

### Q10: What is the purpose of Symbol type?
**Answer:** Create unique identifiers that won't collide with other property names, useful for object keys and well-known symbols for language behavior.

## 🎯 Best Practices

1. **Use const by default, let when needed**
   ```javascript
   const name = 'John';
   let age = 25;
   ```

2. **Avoid var in modern JavaScript**
   ```javascript
   // Bad
   var x = 10;
   
   // Good
   let x = 10;
   ```

3. **Use strict equality (===)**
   ```javascript
   if (value === 10) { }
   ```

4. **Be explicit with type conversion**
   ```javascript
   const num = Number(str);
   const bool = Boolean(value);
   ```

5. **Use meaningful variable names**
   ```javascript
   // Bad
   const d = new Date();
   
   // Good
   const currentDate = new Date();
   ```

## 📚 Additional Resources

- [MDN - Variables](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types)
- [MDN - Data Types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)
- [JavaScript.info - Variables](https://javascript.info/variables)

---

[Next: Operators →](./02-operators.md) | [Home](./README.md)
