# 02. Operators in JavaScript

> Operators are symbols that perform operations on operands (values and variables).

## 📋 Overview

JavaScript operators are essential building blocks that allow you to manipulate data, perform calculations, compare values, and control program flow. Understanding operators is crucial for writing efficient and expressive code.

**What You'll Learn:**
- ✅ Arithmetic operators for mathematical operations
- ✅ Comparison operators for value comparison
- ✅ Logical operators for boolean logic
- ✅ Assignment operators and shortcuts
- ✅ Bitwise operators for binary operations
- ✅ Special operators (typeof, delete, void, etc.)
- ✅ Operator precedence and associativity

---

## 🎯 Key Concepts

### 1. Arithmetic Operators

Perform mathematical operations on numbers.

```javascript
// Basic arithmetic
let a = 10;
let b = 3;

console.log(a + b);  // Addition: 13
console.log(a - b);  // Subtraction: 7
console.log(a * b);  // Multiplication: 30
console.log(a / b);  // Division: 3.333...
console.log(a % b);  // Modulus (remainder): 1
console.log(a ** b); // Exponentiation: 1000 (10^3)

// Increment and Decrement
let count = 5;

console.log(count++); // Post-increment: returns 5, then increments to 6
console.log(count);   // 6

console.log(++count); // Pre-increment: increments to 7, then returns 7
console.log(count);   // 7

console.log(count--); // Post-decrement: returns 7, then decrements to 6
console.log(count);   // 6

console.log(--count); // Pre-decrement: decrements to 5, then returns 5
console.log(count);   // 5

// Unary plus and minus
let x = "5";
console.log(+x);     // 5 (converts string to number)
console.log(-x);     // -5

let y = true;
console.log(+y);     // 1 (converts boolean to number)
```

---

### 2. Comparison Operators

Compare two values and return a boolean result.

```javascript
let num1 = 10;
let num2 = 20;
let str1 = "10";

// Equality operators
console.log(num1 == str1);   // true (loose equality - type coercion)
console.log(num1 === str1);  // false (strict equality - no type coercion)
console.log(num1 != str1);   // false (loose inequality)
console.log(num1 !== str1);  // true (strict inequality)

// Relational operators
console.log(num1 < num2);    // true
console.log(num1 > num2);    // false
console.log(num1 <= 10);     // true
console.log(num2 >= 20);     // true

// String comparison (lexicographical)
console.log("apple" < "banana");    // true (alphabetical order)
console.log("Apple" < "apple");     // true (uppercase < lowercase)
console.log("2" < "10");            // false (string comparison, not numeric)

// Special cases
console.log(null == undefined);     // true
console.log(null === undefined);    // false
console.log(NaN == NaN);           // false (NaN is not equal to anything)
console.log(NaN === NaN);          // false
console.log(Object.is(NaN, NaN));  // true (Object.is for precise comparison)

// Comparing objects
let obj1 = { name: "John" };
let obj2 = { name: "John" };
let obj3 = obj1;

console.log(obj1 == obj2);   // false (different references)
console.log(obj1 === obj2);  // false
console.log(obj1 === obj3);  // true (same reference)
```

---

### 3. Logical Operators

Combine or invert boolean values.

```javascript
// AND (&&) - returns first falsy value or last value
console.log(true && true);        // true
console.log(true && false);       // false
console.log(false && true);       // false
console.log("Hello" && "World");  // "World"
console.log("Hello" && 0);        // 0
console.log(0 && "Hello");        // 0

// OR (||) - returns first truthy value or last value
console.log(true || false);       // true
console.log(false || true);       // true
console.log(false || false);      // false
console.log("" || "Default");     // "Default"
console.log("User" || "Guest");   // "User"
console.log(null || 0 || "" || "Found"); // "Found"

// NOT (!) - inverts boolean value
console.log(!true);               // false
console.log(!false);              // true
console.log(!0);                  // true
console.log(!"Hello");            // false
console.log(!!"Hello");           // true (double negation converts to boolean)

// Nullish Coalescing (??) - returns right operand when left is null or undefined
let value1 = null ?? "default";        // "default"
let value2 = undefined ?? "default";   // "default"
let value3 = 0 ?? "default";          // 0 (0 is not null/undefined)
let value4 = "" ?? "default";         // "" (empty string is not null/undefined)

console.log(value1); // "default"
console.log(value2); // "default"
console.log(value3); // 0
console.log(value4); // ""

// Practical examples
function greetUser(name) {
    // Using ||
    let userName = name || "Guest";
    console.log(`Hello, ${userName}!`);
}

greetUser("Alice");  // Hello, Alice!
greetUser("");       // Hello, Guest! (empty string is falsy)
greetUser(null);     // Hello, Guest!

function greetUserFixed(name) {
    // Using ?? (better for this case)
    let userName = name ?? "Guest";
    console.log(`Hello, ${userName}!`);
}

greetUserFixed("Alice");  // Hello, Alice!
greetUserFixed("");       // Hello, ! (empty string is valid)
greetUserFixed(null);     // Hello, Guest!

// Short-circuit evaluation
let count = 0;
let result1 = count++ && console.log("This won't execute"); // count is 0 (falsy)
console.log(count); // 1

let result2 = count++ || console.log("This won't execute"); // count is 1 (truthy)
console.log(count); // 2
```

---

### 4. Assignment Operators

Assign values to variables with optional operations.

```javascript
let x = 10;

// Compound assignment operators
x += 5;   // x = x + 5;    x is now 15
x -= 3;   // x = x - 3;    x is now 12
x *= 2;   // x = x * 2;    x is now 24
x /= 4;   // x = x / 4;    x is now 6
x %= 5;   // x = x % 5;    x is now 1
x **= 3;  // x = x ** 3;   x is now 1

// Logical assignment operators (ES2021)
let a = null;
a ||= 10;  // a = a || 10;  a is now 10

let b = 5;
b ||= 20;  // a = a || 20;  b is still 5 (short-circuit)

let c = null;
c &&= 10;  // c = c && 10;  c is still null (short-circuit)

let d = 5;
d &&= 10;  // d = d && 10;  d is now 10

let e = null;
e ??= 10;  // e = e ?? 10;  e is now 10

let f = 0;
f ??= 20;  // f = f ?? 20;  f is still 0 (0 is not null/undefined)

// Destructuring assignment
let [first, second] = [1, 2];
console.log(first);  // 1
console.log(second); // 2

let { name, age } = { name: "John", age: 30 };
console.log(name);   // "John"
console.log(age);    // 30
```

---

### 5. Bitwise Operators

Perform operations on binary representations of numbers.

```javascript
// Bitwise operators work on 32-bit integers
let a = 5;      // 00000000000000000000000000000101
let b = 3;      // 00000000000000000000000000000011

console.log(a & b);   // AND: 1  (00000000000000000000000000000001)
console.log(a | b);   // OR:  7  (00000000000000000000000000000111)
console.log(a ^ b);   // XOR: 6  (00000000000000000000000000000110)
console.log(~a);      // NOT: -6 (inverts all bits)

// Bit shift operators
console.log(5 << 1);  // Left shift: 10   (multiply by 2)
console.log(5 << 2);  // Left shift: 20   (multiply by 4)
console.log(20 >> 1); // Right shift: 10  (divide by 2)
console.log(20 >> 2); // Right shift: 5   (divide by 4)

// Zero-fill right shift
console.log(-5 >>> 1);  // 2147483645 (treats as unsigned)

// Practical uses of bitwise operators
// 1. Check if number is even or odd
function isEven(num) {
    return (num & 1) === 0;
}
console.log(isEven(4));  // true
console.log(isEven(7));  // false

// 2. Fast multiplication/division by powers of 2
let num = 10;
console.log(num << 1);   // 20 (multiply by 2)
console.log(num << 2);   // 40 (multiply by 4)
console.log(num >> 1);   // 5  (divide by 2)

// 3. Swap two numbers without temp variable
let x = 5, y = 10;
x = x ^ y;
y = x ^ y;
x = x ^ y;
console.log(x, y);  // 10, 5

// 4. Toggle boolean flags
let flags = 0;
const FLAG_A = 1 << 0;  // 1
const FLAG_B = 1 << 1;  // 2
const FLAG_C = 1 << 2;  // 4

flags |= FLAG_A;        // Set FLAG_A
flags |= FLAG_C;        // Set FLAG_C
console.log(flags);     // 5

console.log((flags & FLAG_A) !== 0);  // true (FLAG_A is set)
console.log((flags & FLAG_B) !== 0);  // false (FLAG_B is not set)

flags ^= FLAG_A;        // Toggle FLAG_A
console.log(flags);     // 4 (FLAG_C only)
```

---

### 6. Special Operators

```javascript
// typeof - returns type of operand
console.log(typeof 42);          // "number"
console.log(typeof "Hello");     // "string"
console.log(typeof true);        // "boolean"
console.log(typeof undefined);   // "undefined"
console.log(typeof null);        // "object" (historical bug)
console.log(typeof {});          // "object"
console.log(typeof []);          // "object"
console.log(typeof function(){}); // "function"
console.log(typeof Symbol());    // "symbol"

// instanceof - checks if object is instance of class
class Person {}
let person = new Person();

console.log(person instanceof Person);  // true
console.log(person instanceof Object);  // true
console.log([] instanceof Array);       // true
console.log([] instanceof Object);      // true

// delete - removes property from object
let obj = { name: "John", age: 30 };
delete obj.age;
console.log(obj);  // { name: "John" }

// void - evaluates expression and returns undefined
console.log(void 0);         // undefined
console.log(void "hello");   // undefined

// Ternary operator (? :) - conditional operator
let age = 20;
let status = age >= 18 ? "Adult" : "Minor";
console.log(status);  // "Adult"

// Nested ternary (use sparingly)
let score = 85;
let grade = score >= 90 ? "A" : score >= 80 ? "B" : score >= 70 ? "C" : "F";
console.log(grade);  // "B"

// Comma operator - evaluates multiple expressions, returns last
let result = (1 + 2, 3 + 4, 5 + 6);
console.log(result);  // 11

// Optional chaining (?.) - safely access nested properties
let user = {
    name: "John",
    address: {
        city: "New York"
    }
};

console.log(user?.name);                  // "John"
console.log(user?.address?.city);         // "New York"
console.log(user?.address?.zipCode);      // undefined (no error)
console.log(user?.phone?.number);         // undefined (no error)

// Optional chaining with methods
console.log(user?.getName?.());           // undefined (method doesn't exist)

// Optional chaining with arrays
let users = [{ name: "Alice" }, { name: "Bob" }];
console.log(users?.[0]?.name);            // "Alice"
console.log(users?.[5]?.name);            // undefined

// in operator - checks if property exists in object
console.log("name" in user);              // true
console.log("phone" in user);             // false
console.log("toString" in user);          // true (inherited from Object)
```

---

### 7. Operator Precedence

Operators have different precedence levels that determine evaluation order.

```javascript
// Precedence examples
console.log(2 + 3 * 4);      // 14 (not 20) - multiplication before addition
console.log((2 + 3) * 4);    // 20 (parentheses override precedence)

console.log(10 - 5 - 2);     // 3 (left-to-right for same precedence)
console.log(10 - (5 - 2));   // 7 (parentheses change order)

// Complex expression
let result = 10 + 5 * 2 ** 3 / 4 - 1;
// Step 1: 2 ** 3 = 8        (exponentiation highest)
// Step 2: 5 * 8 = 40         (multiplication)
// Step 3: 40 / 4 = 10        (division)
// Step 4: 10 + 10 = 20       (addition)
// Step 5: 20 - 1 = 19        (subtraction)
console.log(result);  // 19

// Precedence order (high to low):
// 1. Grouping: ()
// 2. Member access: . []
// 3. Function call: ()
// 4. Postfix increment/decrement: ++ --
// 5. Logical NOT, unary +/-, prefix ++/--, typeof, delete
// 6. Exponentiation: **
// 7. Multiplication, division, modulus: * / %
// 8. Addition, subtraction: + -
// 9. Bitwise shifts: << >> >>>
// 10. Relational: < <= > >= in instanceof
// 11. Equality: == != === !==
// 12. Bitwise AND: &
// 13. Bitwise XOR: ^
// 14. Bitwise OR: |
// 15. Logical AND: &&
// 16. Logical OR: ||
// 17. Nullish coalescing: ??
// 18. Ternary: ? :
// 19. Assignment: = += -= *= etc.
// 20. Comma: ,

// Associativity examples
// Left-to-right
console.log(10 - 5 - 2);     // 3  (10 - 5) - 2

// Right-to-left (exponentiation, assignment)
console.log(2 ** 3 ** 2);    // 512  (2 ** (3 ** 2))
let a = b = c = 5;           // c = 5, b = 5, a = 5 (right-to-left)
```

---

## 💡 Examples

### Example 1: Calculator Function

```javascript
function calculator(num1, operator, num2) {
    switch (operator) {
        case '+':
            return num1 + num2;
        case '-':
            return num1 - num2;
        case '*':
            return num1 * num2;
        case '/':
            return num2 !== 0 ? num1 / num2 : 'Cannot divide by zero';
        case '%':
            return num1 % num2;
        case '**':
            return num1 ** num2;
        default:
            return 'Invalid operator';
    }
}

console.log(calculator(10, '+', 5));   // 15
console.log(calculator(10, '-', 5));   // 5
console.log(calculator(10, '*', 5));   // 50
console.log(calculator(10, '/', 5));   // 2
console.log(calculator(10, '%', 3));   // 1
console.log(calculator(2, '**', 3));   // 8
```

### Example 2: Value Validation

```javascript
function validateUser(user) {
    // Using logical operators for validation
    const hasName = user?.name && user.name.length > 0;
    const hasEmail = user?.email && user.email.includes('@');
    const isAdult = user?.age >= 18;
    
    // Using ternary for conditional messages
    const nameStatus = hasName ? 'Valid' : 'Invalid';
    const emailStatus = hasEmail ? 'Valid' : 'Invalid';
    const ageStatus = isAdult ? 'Adult' : 'Minor';
    
    return {
        name: nameStatus,
        email: emailStatus,
        age: ageStatus,
        isValid: hasName && hasEmail && isAdult
    };
}

console.log(validateUser({ name: "John", email: "john@email.com", age: 25 }));
// { name: 'Valid', email: 'Valid', age: 'Adult', isValid: true }

console.log(validateUser({ name: "", email: "invalid", age: 15 }));
// { name: 'Invalid', email: 'Invalid', age: 'Minor', isValid: false }
```

### Example 3: Default Values

```javascript
// Using || for default values (legacy approach)
function greet1(name) {
    name = name || 'Guest';
    return `Hello, ${name}!`;
}

console.log(greet1('Alice'));  // Hello, Alice!
console.log(greet1(''));       // Hello, Guest! (empty string is falsy)
console.log(greet1(0));        // Hello, Guest! (0 is falsy - bug!)

// Using ?? for default values (modern approach)
function greet2(name) {
    name = name ?? 'Guest';
    return `Hello, ${name}!`;
}

console.log(greet2('Alice'));  // Hello, Alice!
console.log(greet2(''));       // Hello, ! (empty string is valid)
console.log(greet2(null));     // Hello, Guest! (null uses default)

// Default parameters (best approach)
function greet3(name = 'Guest') {
    return `Hello, ${name}!`;
}

console.log(greet3('Alice'));     // Hello, Alice!
console.log(greet3());            // Hello, Guest!
console.log(greet3(undefined));   // Hello, Guest!
console.log(greet3(null));        // Hello, null! (null is not undefined)
```

### Example 4: Chaining Comparisons

```javascript
// Range checking
function isInRange(value, min, max) {
    return value >= min && value <= max;
}

console.log(isInRange(5, 1, 10));   // true
console.log(isInRange(15, 1, 10));  // false

// Multiple conditions
function canVote(age, citizenship, registered) {
    return age >= 18 && citizenship === 'US' && registered === true;
}

console.log(canVote(20, 'US', true));   // true
console.log(canVote(16, 'US', true));   // false
console.log(canVote(20, 'UK', true));   // false
```

---

## 🎤 Interview Questions

### Q1: What's the difference between `==` and `===`?

**Answer:**
- `==` (loose equality) performs type coercion before comparison
- `===` (strict equality) compares both value and type without coercion

```javascript
console.log(5 == "5");     // true (string coerced to number)
console.log(5 === "5");    // false (different types)

console.log(0 == false);   // true (false coerced to 0)
console.log(0 === false);  // false (different types)

console.log(null == undefined);   // true (special case)
console.log(null === undefined);  // false (different types)
```

**Best Practice:** Always use `===` and `!==` unless you specifically need type coercion.

---

### Q2: What is the difference between `||` and `??`?

**Answer:**
- `||` returns the first truthy value or the last value
- `??` returns the right operand only when the left is `null` or `undefined`

```javascript
// || treats 0, "", false as falsy
console.log(0 || "default");        // "default"
console.log("" || "default");       // "default"
console.log(false || "default");    // "default"

// ?? only checks for null/undefined
console.log(0 ?? "default");        // 0
console.log("" ?? "default");       // ""
console.log(false ?? "default");    // false
console.log(null ?? "default");     // "default"
console.log(undefined ?? "default"); // "default"
```

**Use Case:** Use `??` when you want to allow falsy values like 0, "", or false but still provide defaults for null/undefined.

---

### Q3: Explain short-circuit evaluation.

**Answer:**
Short-circuit evaluation means logical operators stop evaluating as soon as the result is determined.

```javascript
// AND (&&) short-circuits on first falsy value
let x = false && expensiveOperation();  // expensiveOperation() never called

// OR (||) short-circuits on first truthy value
let y = true || expensiveOperation();   // expensiveOperation() never called

// Practical use: conditional execution
let isLoggedIn = true;
isLoggedIn && console.log("User is logged in");  // Executes

let hasError = false;
hasError || console.log("No errors");  // Executes

// Guard pattern
function processUser(user) {
    user && user.name && console.log(user.name);
}
```

---

### Q4: What is operator precedence and associativity?

**Answer:**
- **Precedence** determines which operator is evaluated first
- **Associativity** determines evaluation order for operators with same precedence

```javascript
// Precedence
console.log(2 + 3 * 4);      // 14 (* has higher precedence than +)

// Associativity (left-to-right for most operators)
console.log(10 - 5 - 2);     // 3 ((10 - 5) - 2)

// Associativity (right-to-left for assignment)
let a, b, c;
a = b = c = 5;               // c = 5, then b = 5, then a = 5

// Exponentiation (right-to-left)
console.log(2 ** 3 ** 2);    // 512 (2 ** (3 ** 2))
```

---

### Q5: Explain the comma operator.

**Answer:**
The comma operator evaluates each expression from left to right and returns the last expression's value.

```javascript
let x = (1, 2, 3);
console.log(x);  // 3

// Common use: for loops
for (let i = 0, j = 10; i < j; i++, j--) {
    console.log(i, j);
}

// In expressions
let a = 1;
let b = (a++, a + 5);
console.log(b);  // 7 (a is 2, then 2 + 5)

// Return value
function foo() {
    return (bar(), baz(), 42);  // Returns 42
}
```

**Note:** Rarely used outside for loops; can make code harder to read.

---

### Q6: What are the use cases for bitwise operators?

**Answer:**
Bitwise operators are useful for:

1. **Performance**: Faster than arithmetic for certain operations
2. **Flags**: Managing multiple boolean flags efficiently
3. **Permissions**: File/user permissions systems
4. **Color manipulation**: Working with RGB values

```javascript
// 1. Fast even/odd check
function isEven(n) {
    return (n & 1) === 0;
}

// 2. Flag management
const READ = 1 << 0;     // 1
const WRITE = 1 << 1;    // 2
const EXECUTE = 1 << 2;  // 4

let permissions = READ | WRITE;  // 3
console.log((permissions & READ) !== 0);    // true
console.log((permissions & EXECUTE) !== 0); // false

// 3. RGB color manipulation
let color = 0xFF5733;  // RGB color
let red = (color >> 16) & 0xFF;    // 255
let green = (color >> 8) & 0xFF;   // 87
let blue = color & 0xFF;           // 51

// 4. Fast multiplication/division by powers of 2
console.log(5 << 1);   // 10 (5 * 2)
console.log(20 >> 2);  // 5 (20 / 4)
```

---

### Q7: How does the `typeof` operator work with different values?

**Answer:**
`typeof` returns a string indicating the type of the operand.

```javascript
// Primitives
console.log(typeof 42);           // "number"
console.log(typeof "hello");      // "string"
console.log(typeof true);         // "boolean"
console.log(typeof undefined);    // "undefined"
console.log(typeof Symbol());     // "symbol"
console.log(typeof 123n);         // "bigint"

// Special cases
console.log(typeof null);         // "object" (bug in JavaScript)
console.log(typeof NaN);          // "number"
console.log(typeof Infinity);     // "number"

// Objects
console.log(typeof {});           // "object"
console.log(typeof []);           // "object" (arrays are objects)
console.log(typeof function(){}); // "function"
console.log(typeof new Date());   // "object"

// Better type checking
console.log(Array.isArray([]));   // true
console.log(Object.prototype.toString.call([])); // "[object Array]"
```

---

### Q8: What is the nullish coalescing assignment operator?

**Answer:**
The `??=` operator assigns a value only if the current value is `null` or `undefined`.

```javascript
let x = null;
x ??= 10;      // x = x ?? 10;
console.log(x); // 10

let y = 5;
y ??= 20;      // y is not null/undefined, no assignment
console.log(y); // 5

let z = 0;
z ??= 30;      // 0 is not null/undefined, no assignment
console.log(z); // 0

// Practical use: lazy initialization
let cache = null;

function getValue() {
    cache ??= expensiveComputation();
    return cache;
}

// Similar operators
let a = 0;
a ||= 10;  // a is falsy, assigns 10
console.log(a); // 10

let b = 5;
b &&= 20;  // b is truthy, assigns 20
console.log(b); // 20
```

---

### Q9: How does optional chaining work?

**Answer:**
Optional chaining (`?.`) safely accesses nested properties without throwing errors if intermediate values are null/undefined.

```javascript
let user = {
    name: "John",
    address: {
        city: "New York"
    }
};

// Without optional chaining
// console.log(user.contact.phone); // Error: Cannot read property 'phone'

// With optional chaining
console.log(user?.contact?.phone); // undefined (no error)

// With functions
console.log(user?.getName?.());   // undefined (method doesn't exist)

// With arrays
let users = [{ name: "Alice" }];
console.log(users?.[0]?.name);    // "Alice"
console.log(users?.[5]?.name);    // undefined

// Combining with nullish coalescing
let displayName = user?.profile?.displayName ?? "Anonymous";
console.log(displayName); // "Anonymous"

// Short-circuit behavior
let count = 0;
let result = user?.missingMethod?.(count++);
console.log(count); // 0 (function never called)
```

---

### Q10: What's the difference between prefix and postfix increment?

**Answer:**
- **Prefix (`++x`)**: Increments then returns the new value
- **Postfix (`x++`)**: Returns the current value then increments

```javascript
let a = 5;
console.log(++a);  // 6 (a becomes 6, returns 6)
console.log(a);    // 6

let b = 5;
console.log(b++);  // 5 (returns 5, then b becomes 6)
console.log(b);    // 6

// In expressions
let x = 10;
let y = x++ + 5;   // y = 10 + 5 = 15, then x becomes 11
console.log(y, x); // 15, 11

let m = 10;
let n = ++m + 5;   // m becomes 11, n = 11 + 5 = 16
console.log(n, m); // 16, 11

// Common pitfall
let arr = [1, 2, 3];
let i = 0;
console.log(arr[i++]); // 1 (accesses arr[0], then i becomes 1)
console.log(arr[i]);   // 2 (accesses arr[1])
```

---

## ✅ Best Practices

### 1. Use Strict Equality
```javascript
// ❌ Bad: Loose equality can cause unexpected coercion
if (value == null) { }

// ✅ Good: Strict equality is more predictable
if (value === null || value === undefined) { }

// ✅ Better: Use nullish check
if (value == null) { } // Only acceptable for null/undefined check
```

### 2. Use Nullish Coalescing for Defaults
```javascript
// ❌ Bad: || treats 0, "", false as needing defaults
function setHeight(height) {
    return height || 100;
}
console.log(setHeight(0)); // 100 (bug! 0 is valid)

// ✅ Good: ?? only defaults null/undefined
function setHeight(height) {
    return height ?? 100;
}
console.log(setHeight(0)); // 0 (correct!)
```

### 3. Use Optional Chaining
```javascript
// ❌ Bad: Manual null checks
if (user && user.address && user.address.city) {
    console.log(user.address.city);
}

// ✅ Good: Optional chaining
console.log(user?.address?.city);
```

### 4. Avoid Complex Ternary Expressions
```javascript
// ❌ Bad: Hard to read nested ternary
let status = age >= 18 ? canVote ? isRegistered ? "Can vote" : "Not registered" : "Cannot vote" : "Too young";

// ✅ Good: Use if/else or separate logic
let status;
if (age < 18) {
    status = "Too young";
} else if (!canVote) {
    status = "Cannot vote";
} else if (!isRegistered) {
    status = "Not registered";
} else {
    status = "Can vote";
}
```

### 5. Be Careful with Operator Precedence
```javascript
// ❌ Bad: Unclear precedence
let result = a && b || c && d;

// ✅ Good: Use parentheses for clarity
let result = (a && b) || (c && d);
```

---

## 📚 Resources

### Documentation
- [MDN: Expressions and Operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators)
- [MDN: Operator Precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)
- [JavaScript.info: Operators](https://javascript.info/operators)

### Tools
- [Operator Precedence Table](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#table)
- [Bitwise Calculator](https://www.rapidtables.com/convert/number/decimal-to-binary.html)

---

## 📝 Summary

- **Arithmetic operators** perform mathematical operations (`+`, `-`, `*`, `/`, `%`, `**`)
- **Comparison operators** compare values and return booleans (`==`, `===`, `<`, `>`, etc.)
- **Logical operators** work with boolean values (`&&`, `||`, `!`, `??`)
- **Assignment operators** assign and modify values (`=`, `+=`, `-=`, `||=`, etc.)
- **Bitwise operators** work on binary representations of numbers
- **Special operators** include `typeof`, `instanceof`, `?.`, `??`, ternary
- **Operator precedence** determines evaluation order
- Always use `===` for equality checks
- Use `??` for default values when you want to allow falsy values
- Use optional chaining (`?.`) to safely access nested properties

---

<div align="center">

**[⬅️ Previous: Variables & Data Types](./01-variables-and-types.md)** | **[🏠 Home](./README.md)** | **[Next: Control Flow ➡️](./03-control-flow.md)**

</div>
