# 18. Functional Programming in JavaScript

> Functional programming emphasizes immutability, pure functions, and composability for more predictable and maintainable code.

## 📋 Overview

Functional programming (FP) is a programming paradigm that treats computation as the evaluation of mathematical functions. It emphasizes immutability, pure functions, and function composition, leading to more predictable, testable, and maintainable code. JavaScript supports many FP concepts natively.

**What You'll Learn:**
- ✅ Pure functions and side effects
- ✅ Immutability and avoiding mutations
- ✅ Higher-order functions (map, filter, reduce)
- ✅ Function composition and piping
- ✅ Currying and partial application
- ✅ Closures for data encapsulation
- ✅ Recursion patterns and tail call optimization
- ✅ Functional array methods and transformations
- ✅ Monads and functional error handling
- ✅ Performance considerations in FP
- ✅ Combining FP with object-oriented patterns

---

## 🎯 Key Concepts

### 1. Pure Functions

```javascript
// Pure function - same input always produces same output, no side effects
function add(a, b) {
  return a + b;
}

function multiply(x, y) {
  return x * y;
}

// Impure function - has side effects
let total = 0;
function addToTotal(value) {
  total += value; // modifies external state
  return total;
}

// Impure function - depends on external state
function getCurrentTime() {
  return new Date().getTime(); // different result each call
}

// Making impure functions pure
function addToValue(currentTotal, value) {
  return currentTotal + value; // pure version
}

function formatDate(timestamp) {
  return new Date(timestamp).toISOString(); // pure when timestamp provided
}
```

---

### 2. Immutability

```javascript
// Avoid mutations - create new objects instead
const originalUser = { name: 'John', age: 30 };

// Bad - mutates original
originalUser.age = 31;

// Good - creates new object
const updatedUser = { ...originalUser, age: 31 };

// Working with arrays immutably
const numbers = [1, 2, 3, 4, 5];

// Bad - mutates original
numbers.push(6);
numbers.sort((a, b) => b - a);

// Good - creates new arrays
const withSix = [...numbers, 6];
const sorted = [...numbers].sort((a, b) => b - a);

// Immutable array operations
const fruits = ['apple', 'banana', 'orange'];

// Add item
const withGrape = [...fruits, 'grape'];

// Remove item
const withoutBanana = fruits.filter(fruit => fruit !== 'banana');

// Update item
const updatedFruits = fruits.map(fruit => 
  fruit === 'apple' ? 'green apple' : fruit
);

// Deep immutability helper
function deepFreeze(obj) {
  Object.getOwnPropertyNames(obj).forEach(prop => {
    if (obj[prop] !== null && typeof obj[prop] === 'object') {
      deepFreeze(obj[prop]);
    }
  });
  return Object.freeze(obj);
}

const immutableConfig = deepFreeze({
  api: { url: 'https://api.example.com', timeout: 5000 },
  features: { darkMode: true, notifications: false }
});
```

---

### 3. Higher-Order Functions

```javascript
// Functions that take or return other functions

// Array methods are higher-order functions
const numbers = [1, 2, 3, 4, 5, 6];

// map - transforms each element
const doubled = numbers.map(n => n * 2);
console.log(doubled); // [2, 4, 6, 8, 10, 12]

// filter - selects elements based on predicate
const evens = numbers.filter(n => n % 2 === 0);
console.log(evens); // [2, 4, 6]

// reduce - combines elements into single value
const sum = numbers.reduce((acc, n) => acc + n, 0);
console.log(sum); // 21

// Custom higher-order functions
function createMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// Function that takes a function as argument
function repeat(times, fn) {
  for (let i = 0; i < times; i++) {
    fn(i);
  }
}

repeat(3, i => console.log(`Iteration ${i}`));

// Returning functions from functions
function createValidator(rules) {
  return function(data) {
    return rules.every(rule => rule(data));
  };
}

const userValidator = createValidator([
  user => user.name && user.name.length > 0,
  user => user.email && user.email.includes('@'),
  user => user.age && user.age >= 18
]);

console.log(userValidator({ name: 'John', email: 'john@example.com', age: 25 })); // true
```

---

### 4. Function Composition

```javascript
// Combining simple functions to build complex operations

// Basic composition
const add1 = x => x + 1;
const multiply2 = x => x * 2;
const square = x => x * x;

// Manual composition
const addThenMultiply = x => multiply2(add1(x));
console.log(addThenMultiply(3)); // 8

// Compose utility function
function compose(...fns) {
  return function(value) {
    return fns.reduceRight((acc, fn) => fn(acc), value);
  };
}

// Pipe utility function (left to right)
function pipe(...fns) {
  return function(value) {
    return fns.reduce((acc, fn) => fn(acc), value);
  };
}

// Using compose (right to left)
const transform1 = compose(square, multiply2, add1);
console.log(transform1(3)); // 64 ((3 + 1) * 2)²

// Using pipe (left to right) 
const transform2 = pipe(add1, multiply2, square);
console.log(transform2(3)); // 64 same result, more readable

// Real-world example: data processing pipeline
const users = [
  { name: 'john doe', age: 25, active: true },
  { name: 'jane smith', age: 30, active: false },
  { name: 'bob johnson', age: 35, active: true }
];

const processUsers = pipe(
  users => users.filter(user => user.active),
  users => users.map(user => ({ ...user, name: user.name.toUpperCase() })),
  users => users.sort((a, b) => a.age - b.age)
);

console.log(processUsers(users));
```

---

### 5. Currying and Partial Application

```javascript
// Currying - converting function with multiple arguments into sequence of functions

// Regular function
function add(a, b, c) {
  return a + b + c;
}

// Curried version
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

// ES6 arrow function version
const curriedAddArrow = a => b => c => a + b + c;

// Usage
console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAddArrow(1)(2)(3)); // 6

// Partial application
const add5 = curriedAdd(5);
const add5and3 = add5(3);
console.log(add5and3(2)); // 10

// Generic curry function
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...nextArgs) {
      return curried.apply(this, [...args, ...nextArgs]);
    };
  };
}

// Example usage
const multiply = (a, b, c) => a * b * c;
const curriedMultiply = curry(multiply);

console.log(curriedMultiply(2)(3)(4)); // 24
console.log(curriedMultiply(2, 3)(4)); // 24
console.log(curriedMultiply(2)(3, 4)); // 24

// Practical example: API configuration
const apiCall = curry((baseUrl, endpoint, params) => {
  return fetch(`${baseUrl}${endpoint}?${new URLSearchParams(params)}`);
});

const myApiCall = apiCall('https://api.example.com');
const getUsersCall = myApiCall('/users');
const getActiveUsers = getUsersCall({ active: true });
```

---

### 6. Functional Array Methods

```javascript
// Advanced array processing patterns

const orders = [
  { id: 1, customer: 'John', items: [{ name: 'laptop', price: 1000, qty: 1 }], status: 'completed' },
  { id: 2, customer: 'Jane', items: [{ name: 'mouse', price: 25, qty: 2 }, { name: 'keyboard', price: 75, qty: 1 }], status: 'pending' },
  { id: 3, customer: 'Bob', items: [{ name: 'monitor', price: 300, qty: 1 }], status: 'completed' }
];

// Complex data transformations
const orderSummary = orders
  .filter(order => order.status === 'completed')
  .map(order => ({
    id: order.id,
    customer: order.customer,
    total: order.items.reduce((sum, item) => sum + (item.price * item.qty), 0),
    itemCount: order.items.reduce((count, item) => count + item.qty, 0)
  }))
  .sort((a, b) => b.total - a.total);

console.log(orderSummary);

// flatMap - map and flatten in one step
const allItems = orders.flatMap(order => order.items);

// find and findIndex
const expensiveOrder = orders.find(order => 
  order.items.some(item => item.price > 500)
);

// some and every
const allCompleted = orders.every(order => order.status === 'completed');
const hasExpensiveItems = orders.some(order => 
  order.items.some(item => item.price > 500)
);

// Custom array methods using reduce
Array.prototype.myMap = function(callback) {
  return this.reduce((acc, item, index) => {
    acc.push(callback(item, index, this));
    return acc;
  }, []);
};

Array.prototype.myFilter = function(predicate) {
  return this.reduce((acc, item, index) => {
    if (predicate(item, index, this)) {
      acc.push(item);
    }
    return acc;
  }, []);
};
```

---

### 7. Recursion Patterns

```javascript
// Functional approach to repetitive tasks

// Basic recursion
function factorial(n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
}

// Tail recursion (optimized)
function factorialTail(n, accumulator = 1) {
  if (n <= 1) return accumulator;
  return factorialTail(n - 1, n * accumulator);
}

// Tree traversal
const tree = {
  value: 1,
  children: [
    { value: 2, children: [{ value: 4, children: [] }, { value: 5, children: [] }] },
    { value: 3, children: [{ value: 6, children: [] }] }
  ]
};

function traverseTree(node, callback) {
  callback(node.value);
  node.children.forEach(child => traverseTree(child, callback));
}

// Flatten nested arrays
function flatten(arr) {
  return arr.reduce((acc, item) => {
    return acc.concat(Array.isArray(item) ? flatten(item) : item);
  }, []);
}

console.log(flatten([1, [2, [3, 4]], 5])); // [1, 2, 3, 4, 5]

// Deep object operations
function deepMap(obj, transform) {
  if (Array.isArray(obj)) {
    return obj.map(item => deepMap(item, transform));
  }
  
  if (obj !== null && typeof obj === 'object') {
    const result = {};
    for (const [key, value] of Object.entries(obj)) {
      result[key] = deepMap(value, transform);
    }
    return result;
  }
  
  return transform(obj);
}

const data = { a: 1, b: [2, { c: 3 }] };
const doubled = deepMap(data, x => typeof x === 'number' ? x * 2 : x);
console.log(doubled); // { a: 2, b: [4, { c: 6 }] }
```

---

## 💡 Real-World Examples

### Example 1: Data Validation Pipeline

```javascript
// Functional validation system
const validators = {
  required: (field) => (value) => {
    if (!value || (typeof value === 'string' && value.trim() === '')) {
      return { valid: false, error: `${field} is required` };
    }
    return { valid: true };
  },
  
  minLength: (field, min) => (value) => {
    if (typeof value === 'string' && value.length < min) {
      return { valid: false, error: `${field} must be at least ${min} characters` };
    }
    return { valid: true };
  },
  
  email: (field) => (value) => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (typeof value === 'string' && !emailRegex.test(value)) {
      return { valid: false, error: `${field} must be a valid email` };
    }
    return { valid: true };
  },
  
  range: (field, min, max) => (value) => {
    const num = Number(value);
    if (isNaN(num) || num < min || num > max) {
      return { valid: false, error: `${field} must be between ${min} and ${max}` };
    }
    return { valid: true };
  }
};

function validateField(fieldName, value, ...validatorFns) {
  return validatorFns.reduce((acc, validator) => {
    if (!acc.valid) return acc;
    return validator(value);
  }, { valid: true });
}

function validateObject(obj, schema) {
  return Object.entries(schema).reduce((acc, [field, validatorFns]) => {
    const result = validateField(field, obj[field], ...validatorFns);
    if (!result.valid) {
      acc.errors[field] = result.error;
      acc.valid = false;
    }
    return acc;
  }, { valid: true, errors: {} });
}

// Usage
const userSchema = {
  name: [validators.required('Name'), validators.minLength('Name', 2)],
  email: [validators.required('Email'), validators.email('Email')],
  age: [validators.required('Age'), validators.range('Age', 13, 120)]
};

const userData = { name: 'John', email: 'john@example.com', age: 25 };
const validation = validateObject(userData, userSchema);
console.log(validation); // { valid: true, errors: {} }
```

### Example 2: Event Stream Processing

```javascript
// Functional event processing system
class EventStream {
  constructor() {
    this.listeners = [];
  }
  
  // Higher-order function that returns configured listener
  static filter(predicate) {
    return function(next) {
      return function(event) {
        if (predicate(event)) {
          next(event);
        }
      };
    };
  }
  
  static map(transform) {
    return function(next) {
      return function(event) {
        next(transform(event));
      };
    };
  }
  
  static debounce(ms) {
    return function(next) {
      let timeout;
      return function(event) {
        clearTimeout(timeout);
        timeout = setTimeout(() => next(event), ms);
      };
    };
  }
  
  static throttle(ms) {
    return function(next) {
      let lastCall = 0;
      return function(event) {
        const now = Date.now();
        if (now - lastCall >= ms) {
          lastCall = now;
          next(event);
        }
      };
    };
  }
  
  pipe(...processors) {
    const finalHandler = processors.reduceRight(
      (next, processor) => processor(next),
      (event) => this.listeners.forEach(listener => listener(event))
    );
    
    return {
      emit: finalHandler,
      subscribe: (listener) => {
        this.listeners.push(listener);
        return () => {
          const index = this.listeners.indexOf(listener);
          if (index > -1) this.listeners.splice(index, 1);
        };
      }
    };
  }
}

// Usage
const eventStream = new EventStream();

const processedStream = eventStream.pipe(
  EventStream.filter(event => event.type === 'user_action'),
  EventStream.map(event => ({ ...event, timestamp: Date.now() })),
  EventStream.debounce(300)
);

processedStream.subscribe(event => {
  console.log('Processed event:', event);
});

// Emit events
processedStream.emit({ type: 'user_action', action: 'click', target: 'button' });
```

### Example 3: Functional State Management

```javascript
// Redux-like state management with pure functions
function createStore(reducer, initialState) {
  let state = initialState;
  let listeners = [];
  
  return {
    getState: () => state,
    
    dispatch: (action) => {
      state = reducer(state, action);
      listeners.forEach(listener => listener(state));
    },
    
    subscribe: (listener) => {
      listeners.push(listener);
      return () => {
        listeners = listeners.filter(l => l !== listener);
      };
    }
  };
}

// Pure reducer functions
const todosReducer = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [...state, {
        id: Date.now(),
        text: action.text,
        completed: false
      }];
      
    case 'TOGGLE_TODO':
      return state.map(todo =>
        todo.id === action.id
          ? { ...todo, completed: !todo.completed }
          : todo
      );
      
    case 'REMOVE_TODO':
      return state.filter(todo => todo.id !== action.id);
      
    default:
      return state;
  }
};

// Combine multiple reducers
function combineReducers(reducers) {
  return function(state = {}, action) {
    return Object.keys(reducers).reduce((nextState, key) => {
      nextState[key] = reducers[key](state[key], action);
      return nextState;
    }, {});
  };
}

const rootReducer = combineReducers({
  todos: todosReducer,
  user: (state = null, action) => {
    switch (action.type) {
      case 'SET_USER':
        return action.user;
      default:
        return state;
    }
  }
});

// Usage
const store = createStore(rootReducer, { todos: [], user: null });

store.subscribe(state => {
  console.log('State updated:', state);
});

store.dispatch({ type: 'ADD_TODO', text: 'Learn functional programming' });
store.dispatch({ type: 'SET_USER', user: { name: 'John' } });
```

---

## 🎤 Interview Questions

### Q1: What makes a function "pure"?

**Answer:**

A pure function:
1. Always returns the same output for the same input
2. Has no side effects (doesn't modify external state)
3. Doesn't depend on external mutable state

```javascript
// Pure
const add = (a, b) => a + b;

// Impure - side effect
let count = 0;
const increment = () => ++count;

// Impure - depends on external state
const getTime = () => new Date().getTime();
```

---

### Q2: What's the difference between `map()` and `forEach()`?

**Answer:**

`map()` creates a new array with transformed elements (functional), while `forEach()` executes a function for side effects and returns undefined.

```javascript
const numbers = [1, 2, 3];

// map - functional, returns new array
const doubled = numbers.map(n => n * 2); // [2, 4, 6]

// forEach - imperative, for side effects
numbers.forEach(n => console.log(n)); // undefined
```

---

### Q3: How does currying help in functional programming?

**Answer:**

Currying enables function reuse, partial application, and better composition:

```javascript
const multiply = a => b => a * b;

const double = multiply(2);
const triple = multiply(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// Enables point-free style
const numbers = [1, 2, 3];
const doubled = numbers.map(double);
```

---

### Q4: What's function composition and why is it useful?

**Answer:**

Function composition combines simple functions to build complex operations, promoting reusability and modularity:

```javascript
const pipe = (...fns) => value => fns.reduce((acc, fn) => fn(acc), value);

const addOne = x => x + 1;
const double = x => x * 2;
const square = x => x * x;

const transform = pipe(addOne, double, square);
console.log(transform(3)); // 64
```

---

### Q5: How do you handle errors functionally?

**Answer:**

Use functional patterns like Maybe/Option or Either monads, or functional error handling:

```javascript
// Functional error handling
const safeOperation = (fn) => (value) => {
  try {
    return { success: true, data: fn(value) };
  } catch (error) {
    return { success: false, error: error.message };
  }
};

const safeDivide = safeOperation(x => {
  if (x === 0) throw new Error('Division by zero');
  return 10 / x;
});

const result = safeDivide(2); // { success: true, data: 5 }
```

---

### Q6: What are the benefits and drawbacks of functional programming?

**Answer:**

**Benefits:**
- More predictable code (pure functions)
- Easier testing and debugging
- Better composability and reusability
- Immutability prevents many bugs

**Drawbacks:**
- Can be less performant (creating new objects)
- Steeper learning curve
- Not always intuitive for procedural problems
- Memory overhead from immutability

---

## 📚 Summary

**Key Takeaways:**
- Pure functions are predictable and testable
- Immutability prevents bugs and enables safe concurrent programming
- Higher-order functions (map, filter, reduce) enable powerful data transformations
- Function composition builds complex operations from simple parts
- Currying enables partial application and better reusability
- Recursion provides elegant solutions for tree-like data structures
- Functional programming works well with JavaScript's first-class function support

---

**Next Topic:** [19. TypeScript Basics](./19-typescript-basics.md)