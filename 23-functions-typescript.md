# 23. Functions in TypeScript

> TypeScript enhances JavaScript functions with static typing, enabling better parameter validation, return type checking, and function signature documentation.

## 📋 Overview

Functions are first-class citizens in TypeScript, just like JavaScript. TypeScript adds static typing capabilities that help catch errors at compile time, provide better IDE support, and serve as living documentation for function contracts. Understanding function typing is crucial for building robust TypeScript applications.

**What You'll Learn:**
- ✅ Function type annotations and inference
- ✅ Parameter types and optional parameters
- ✅ Rest parameters and spread syntax
- ✅ Return type annotations and inference
- ✅ Function overloads and signature matching
- ✅ Generic functions and constraints
- ✅ Higher-order function typing
- ✅ Function as types and callbacks
- ✅ This parameter typing
- ✅ Assertion functions and type guards
- ✅ Async function typing
- ✅ Advanced function patterns

---

## 🎯 Key Concepts

### 1. Basic Function Typing

```typescript
// Function declarations with type annotations
function add(a: number, b: number): number {
  return a + b;
}

function greet(name: string): string {
  return `Hello, ${name}!`;
}

// Function expressions
const multiply = function(x: number, y: number): number {
  return x * y;
};

// Arrow functions
const subtract = (a: number, b: number): number => a - b;
const square = (n: number): number => n * n;

// Void return type
function logMessage(message: string): void {
  console.log(message);
  // No return statement or return undefined
}

// Never return type (functions that never return)
function throwError(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {
    // This function never returns
  }
}

// Type inference
const divide = (a: number, b: number) => a / b; // Inferred as (a: number, b: number) => number

// Function type annotations
let calculator: (x: number, y: number) => number;
calculator = add;
calculator = multiply;
// calculator = greet; // Error: Type mismatch

// Functions as parameters
function applyOperation(a: number, b: number, operation: (x: number, y: number) => number): number {
  return operation(a, b);
}

const result = applyOperation(5, 3, add); // 8
```

---

### 2. Optional and Default Parameters

```typescript
// Optional parameters (must come after required parameters)
function createUser(name: string, age?: number, email?: string): User {
  return {
    id: Math.random().toString(),
    name,
    age: age ?? 0,
    email: email ?? `${name.toLowerCase()}@example.com`
  };
}

const user1 = createUser("John");
const user2 = createUser("Jane", 25);
const user3 = createUser("Bob", 30, "bob@email.com");

// Default parameters
function formatMessage(text: string, prefix: string = "INFO", timestamp: boolean = true): string {
  const time = timestamp ? `[${new Date().toISOString()}] ` : '';
  return `${time}${prefix}: ${text}`;
}

formatMessage("Hello"); // Uses defaults
formatMessage("Error occurred", "ERROR", false); // Overrides defaults

// Default parameters with type inference
function createArray<T>(size: number, defaultValue: T = undefined as T): T[] {
  return Array(size).fill(defaultValue);
}

const numbers = createArray(5, 0); // number[]
const strings = createArray(3, ""); // string[]

// Optional parameters in object destructuring
function configureServer({ 
  host = "localhost", 
  port = 3000, 
  ssl = false 
}: { host?: string; port?: number; ssl?: boolean } = {}): void {
  console.log(`Server: ${ssl ? 'https' : 'http'}://${host}:${port}`);
}

configureServer(); // Uses all defaults
configureServer({ port: 8080, ssl: true }); // Partial override

// Conditional default parameters
function processData<T>(
  data: T[], 
  processor?: (item: T) => T
): T[] {
  const defaultProcessor = (item: T) => item; // Identity function
  const proc = processor ?? defaultProcessor;
  return data.map(proc);
}
```

---

### 3. Rest Parameters and Spread

```typescript
// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

function combineStrings(separator: string, ...strings: string[]): string {
  return strings.join(separator);
}

sum(1, 2, 3, 4, 5); // 15
combineStrings(" ", "Hello", "World", "!"); // "Hello World !"

// Rest parameters with different types
function logWithTimestamp(level: string, ...messages: (string | number | boolean)[]): void {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${level}:`, ...messages);
}

logWithTimestamp("INFO", "User logged in", 123, true);

// Spread syntax in function calls
const numbers = [1, 2, 3, 4, 5];
const total = sum(...numbers);

// Rest parameters with object destructuring
function createApiClient(
  baseUrl: string,
  { timeout = 5000, retries = 3, ...options }: {
    timeout?: number;
    retries?: number;
    [key: string]: any;
  } = {}
): ApiClient {
  return new ApiClient(baseUrl, { timeout, retries, ...options });
}

// Tuple rest parameters
function processCoordinates(...coords: [number, number, number?]): void {
  const [x, y, z = 0] = coords;
  console.log(`Position: (${x}, ${y}, ${z})`);
}

processCoordinates(10, 20); // Valid
processCoordinates(10, 20, 30); // Valid
// processCoordinates(10); // Error: Expected 2-3 arguments

// Generic rest parameters
function merge<T extends object>(...objects: T[]): T {
  return objects.reduce((result, obj) => ({ ...result, ...obj }), {} as T);
}

const merged = merge({ a: 1 }, { b: 2 }, { c: 3 }); // { a: number, b: number, c: number }
```

---

### 4. Function Overloads

```typescript
// Function overloads for different parameter combinations
function combine(a: string, b: string): string;
function combine(a: number, b: number): number;
function combine(a: string[], b: string[]): string[];
function combine(a: any, b: any): any {
  if (typeof a === 'string') {
    return a + b;
  }
  if (typeof a === 'number') {
    return a + b;
  }
  if (Array.isArray(a)) {
    return [...a, ...b];
  }
}

const str = combine("Hello", " World"); // string
const num = combine(5, 3); // number
const arr = combine(["a", "b"], ["c", "d"]); // string[]

// Overloads with different return types
function format(value: string): string;
function format(value: number): string;
function format(value: boolean): string;
function format(value: Date): string;
function format(value: unknown): string {
  if (value instanceof Date) {
    return value.toISOString();
  }
  return String(value);
}

// Conditional overloads
function getElementById(id: string): Element | null;
function getElementById<T extends Element>(id: string, type: new () => T): T | null;
function getElementById<T extends Element>(id: string, type?: new () => T): Element | T | null {
  const element = document.getElementById(id);
  if (!element) return null;
  if (type && !(element instanceof type)) return null;
  return element as T | Element;
}

const element = getElementById("myDiv"); // Element | null
const canvas = getElementById("myCanvas", HTMLCanvasElement); // HTMLCanvasElement | null

// Method overloads in classes
class DataProcessor {
  process(data: string): string;
  process(data: number): number;
  process(data: boolean): boolean;
  process<T>(data: T[]): T[];
  process(data: unknown): unknown {
    if (Array.isArray(data)) {
      return data.map(item => this.processItem(item));
    }
    return this.processItem(data);
  }

  private processItem(item: unknown): unknown {
    // Implementation details
    return item;
  }
}

// Generic overloads
interface Repository<T> {
  find(id: string): Promise<T | null>;
  find(query: Partial<T>): Promise<T[]>;
  find(predicate: (item: T) => boolean): Promise<T[]>;
}

class UserRepository implements Repository<User> {
  async find(id: string): Promise<User | null>;
  async find(query: Partial<User>): Promise<User[]>;
  async find(predicate: (item: User) => boolean): Promise<User[]>;
  async find(criteria: string | Partial<User> | ((item: User) => boolean)): Promise<User | User[] | null> {
    if (typeof criteria === 'string') {
      // Find by ID
      return this.findById(criteria);
    } else if (typeof criteria === 'function') {
      // Find by predicate
      return this.findByPredicate(criteria);
    } else {
      // Find by query object
      return this.findByQuery(criteria);
    }
  }

  private async findById(id: string): Promise<User | null> { /* implementation */ return null; }
  private async findByQuery(query: Partial<User>): Promise<User[]> { /* implementation */ return []; }
  private async findByPredicate(predicate: (item: User) => boolean): Promise<User[]> { /* implementation */ return []; }
}
```

---

### 5. Generic Functions

```typescript
// Basic generic functions
function identity<T>(value: T): T {
  return value;
}

const num = identity(42); // number
const str = identity("hello"); // string

// Multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const stringNumber = pair("hello", 42); // [string, number]
const booleanArray = pair(true, [1, 2, 3]); // [boolean, number[]]

// Generic constraints
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(item: T): T {
  console.log(`Length: ${item.length}`);
  return item;
}

logLength("hello"); // string
logLength([1, 2, 3]); // number[]
// logLength(42); // Error: number doesn't have length property

// Keyof constraints
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "John", age: 30, email: "john@example.com" };
const name = getProperty(person, "name"); // string
const age = getProperty(person, "age"); // number
// const invalid = getProperty(person, "invalid"); // Error

// Generic factory functions
function createArray<T>(length: number, value: T): T[] {
  return Array(length).fill(value);
}

function createMap<K extends string | number | symbol, V>(
  entries: Array<[K, V]>
): Map<K, V> {
  return new Map(entries);
}

// Generic higher-order functions
function memoize<Args extends any[], Return>(
  fn: (...args: Args) => Return
): (...args: Args) => Return {
  const cache = new Map();
  
  return (...args: Args): Return => {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key);
    }
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

const expensiveFunction = memoize((a: number, b: number) => {
  console.log('Computing...');
  return a * b;
});

// Conditional generic types in functions
function processValue<T>(
  value: T
): T extends string ? string : T extends number ? number : never {
  if (typeof value === 'string') {
    return value.toUpperCase() as any;
  }
  if (typeof value === 'number') {
    return (value * 2) as any;
  }
  throw new Error('Unsupported type');
}

const processed1 = processValue("hello"); // string
const processed2 = processValue(42); // number
```

---

### 6. Higher-Order Functions

```typescript
// Functions that return functions
function createMultiplier(factor: number): (value: number) => number {
  return (value: number) => value * factor;
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(4)); // 12

// Generic higher-order functions
function createValidator<T>(
  predicate: (value: T) => boolean,
  errorMessage: string
): (value: T) => { isValid: boolean; error?: string } {
  return (value: T) => {
    const isValid = predicate(value);
    return isValid ? { isValid } : { isValid, error: errorMessage };
  };
}

const emailValidator = createValidator(
  (email: string) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email),
  "Invalid email format"
);

// Curry function implementation
function curry<A, B, R>(fn: (a: A, b: B) => R): (a: A) => (b: B) => R;
function curry<A, B, C, R>(fn: (a: A, b: B, c: C) => R): (a: A) => (b: B) => (c: C) => R;
function curry(fn: Function): Function {
  return function curried(...args: any[]) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...nextArgs: any[]) {
      return curried.apply(this, [...args, ...nextArgs]);
    };
  };
}

const add = (a: number, b: number, c: number) => a + b + c;
const curriedAdd = curry(add);

const add5 = curriedAdd(5);
const add5and3 = add5(3);
const result = add5and3(2); // 10

// Function composition
function compose<A, B, C>(
  f: (b: B) => C,
  g: (a: A) => B
): (a: A) => C {
  return (a: A) => f(g(a));
}

function pipe<A, B, C>(
  g: (a: A) => B,
  f: (b: B) => C
): (a: A) => C {
  return (a: A) => f(g(a));
}

const addOne = (x: number) => x + 1;
const multiplyByTwo = (x: number) => x * 2;

const addThenMultiply = pipe(addOne, multiplyByTwo);
const multiplyThenAdd = compose(addOne, multiplyByTwo);

console.log(addThenMultiply(3)); // 8 ((3 + 1) * 2)
console.log(multiplyThenAdd(3)); // 7 ((3 * 2) + 1)

// Partial application
function partial<T extends any[], U extends any[], R>(
  fn: (...args: [...T, ...U]) => R,
  ...partialArgs: T
): (...remainingArgs: U) => R {
  return (...remainingArgs: U) => fn(...partialArgs, ...remainingArgs);
}

const greetWithPrefix = (prefix: string, name: string, punctuation: string) => 
  `${prefix} ${name}${punctuation}`;

const greetFormally = partial(greetWithPrefix, "Good morning");
console.log(greetFormally("John", "!")); // "Good morning John!"
```

---

### 7. This Parameter and Context Typing

```typescript
// Explicit 'this' parameter
interface Counter {
  count: number;
  increment(this: Counter, step?: number): void;
  decrement(this: Counter, step?: number): void;
  reset(this: Counter): void;
}

const counter: Counter = {
  count: 0,
  increment(this: Counter, step: number = 1) {
    this.count += step;
  },
  decrement(this: Counter, step: number = 1) {
    this.count -= step;
  },
  reset(this: Counter) {
    this.count = 0;
  }
};

// Arrow functions don't have their own 'this'
class EventHandler {
  private listeners: Map<string, Function[]> = new Map();

  // Method with explicit 'this' parameter
  addEventListener(this: EventHandler, event: string, handler: Function): void {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, []);
    }
    this.listeners.get(event)!.push(handler);
  }

  // Arrow function maintains lexical 'this'
  removeEventListener = (event: string, handler: Function): void => {
    const handlers = this.listeners.get(event);
    if (handlers) {
      const index = handlers.indexOf(handler);
      if (index > -1) {
        handlers.splice(index, 1);
      }
    }
  };

  // Method that can be safely bound
  emit(this: EventHandler, event: string, ...args: any[]): void {
    const handlers = this.listeners.get(event) || [];
    handlers.forEach(handler => handler(...args));
  }
}

// Function with callable and constructible signatures
interface CallableConstructor {
  new (value: string): { value: string };
  (value: string): string;
}

const StringProcessor: CallableConstructor = function(this: any, value: string) {
  if (new.target) {
    // Called as constructor
    this.value = value;
  } else {
    // Called as function
    return value.toUpperCase();
  }
} as CallableConstructor;

// ThisType utility for object literals
interface ThisContext {
  name: string;
  greet(): string;
}

const obj: ThisContext & ThisType<ThisContext> = {
  name: "World",
  greet() {
    return `Hello, ${this.name}!`; // 'this' is properly typed
  }
};

// Binding helper function
function bindMethods<T extends Record<string, any>>(
  obj: T,
  ...methods: Array<keyof T>
): T {
  const bound = { ...obj };
  for (const method of methods) {
    if (typeof obj[method] === 'function') {
      bound[method] = obj[method].bind(obj);
    }
  }
  return bound;
}
```

---

## 💡 Real-World Examples

### Example 1: Type-Safe Event System

```typescript
// Event type definitions
interface EventMap {
  'user:login': { user: User; timestamp: Date };
  'user:logout': { userId: string };
  'data:update': { id: string; changes: any };
  'error:occurred': { error: Error; context: string };
}

// Event handler type
type EventHandler<T> = (payload: T) => void | Promise<void>;

// Event emitter with type safety
class TypedEventEmitter<TEventMap extends Record<string, any> = EventMap> {
  private listeners = new Map<keyof TEventMap, Set<EventHandler<any>>>();

  // Add event listener with proper typing
  on<K extends keyof TEventMap>(
    event: K,
    handler: EventHandler<TEventMap[K]>
  ): () => void {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, new Set());
    }
    
    this.listeners.get(event)!.add(handler);
    
    // Return unsubscribe function
    return () => {
      this.listeners.get(event)?.delete(handler);
    };
  }

  // One-time event listener
  once<K extends keyof TEventMap>(
    event: K,
    handler: EventHandler<TEventMap[K]>
  ): () => void {
    const onceWrapper: EventHandler<TEventMap[K]> = (payload) => {
      handler(payload);
      this.off(event, onceWrapper);
    };
    
    return this.on(event, onceWrapper);
  }

  // Remove specific handler
  off<K extends keyof TEventMap>(
    event: K,
    handler: EventHandler<TEventMap[K]>
  ): void {
    this.listeners.get(event)?.delete(handler);
  }

  // Emit event with type checking
  async emit<K extends keyof TEventMap>(
    event: K,
    payload: TEventMap[K]
  ): Promise<void> {
    const handlers = this.listeners.get(event);
    if (!handlers) return;

    const promises: Promise<void>[] = [];
    
    for (const handler of handlers) {
      try {
        const result = handler(payload);
        if (result instanceof Promise) {
          promises.push(result);
        }
      } catch (error) {
        console.error(`Error in event handler for ${String(event)}:`, error);
      }
    }

    if (promises.length > 0) {
      await Promise.all(promises);
    }
  }

  // Remove all listeners for an event
  removeAllListeners<K extends keyof TEventMap>(event?: K): void {
    if (event) {
      this.listeners.delete(event);
    } else {
      this.listeners.clear();
    }
  }

  // Get listener count
  listenerCount<K extends keyof TEventMap>(event: K): number {
    return this.listeners.get(event)?.size || 0;
  }

  // List all events with listeners
  eventNames(): Array<keyof TEventMap> {
    return Array.from(this.listeners.keys());
  }
}

// Usage example
const eventEmitter = new TypedEventEmitter<EventMap>();

// Type-safe event handling
const unsubscribe = eventEmitter.on('user:login', ({ user, timestamp }) => {
  console.log(`User ${user.name} logged in at ${timestamp.toISOString()}`);
});

eventEmitter.once('error:occurred', ({ error, context }) => {
  console.error(`Error in ${context}:`, error.message);
});

// Type-safe event emission
await eventEmitter.emit('user:login', {
  user: { id: '1', name: 'John', email: 'john@example.com', status: 'active' },
  timestamp: new Date()
});

// Compilation error for wrong payload type
// eventEmitter.emit('user:login', { wrongProperty: true });
```

### Example 2: Database Query Builder

```typescript
// Query builder types
interface QueryOperators {
  $eq: any;
  $ne: any;
  $gt: number | Date;
  $gte: number | Date;
  $lt: number | Date;
  $lte: number | Date;
  $in: any[];
  $nin: any[];
  $exists: boolean;
  $regex: RegExp;
}

type QueryCondition<T> = {
  [K in keyof T]?: T[K] | Partial<QueryOperators>;
};

type SortDirection = 1 | -1 | 'asc' | 'desc';
type SortOptions<T> = {
  [K in keyof T]?: SortDirection;
};

// Generic query builder
class QueryBuilder<T extends Record<string, any>, TSelect = T> {
  private conditions: QueryCondition<T>[] = [];
  private sortOptions: SortOptions<T>[] = [];
  private limitValue?: number;
  private skipValue?: number;
  private selectedFields?: Array<keyof T>;

  // Where conditions with type safety
  where(condition: QueryCondition<T>): QueryBuilder<T, TSelect> {
    this.conditions.push(condition);
    return this;
  }

  // Fluent where methods
  whereEquals<K extends keyof T>(field: K, value: T[K]): QueryBuilder<T, TSelect> {
    return this.where({ [field]: { $eq: value } } as QueryCondition<T>);
  }

  whereIn<K extends keyof T>(field: K, values: T[K][]): QueryBuilder<T, TSelect> {
    return this.where({ [field]: { $in: values } } as QueryCondition<T>);
  }

  whereGreaterThan<K extends keyof T>(
    field: K,
    value: T[K] extends number | Date ? T[K] : never
  ): QueryBuilder<T, TSelect> {
    return this.where({ [field]: { $gt: value } } as QueryCondition<T>);
  }

  // Select specific fields
  select<K extends keyof T>(...fields: K[]): QueryBuilder<T, Pick<T, K>> {
    this.selectedFields = fields;
    return this as any;
  }

  // Sorting
  sort(options: SortOptions<T>): QueryBuilder<T, TSelect> {
    this.sortOptions.push(options);
    return this;
  }

  sortBy<K extends keyof T>(field: K, direction: SortDirection = 1): QueryBuilder<T, TSelect> {
    return this.sort({ [field]: direction } as SortOptions<T>);
  }

  // Pagination
  limit(count: number): QueryBuilder<T, TSelect> {
    this.limitValue = count;
    return this;
  }

  skip(count: number): QueryBuilder<T, TSelect> {
    this.skipValue = count;
    return this;
  }

  offset(count: number): QueryBuilder<T, TSelect> {
    return this.skip(count);
  }

  // Execute query
  async execute(): Promise<TSelect[]> {
    const query = {
      conditions: this.conditions,
      sort: this.sortOptions,
      limit: this.limitValue,
      skip: this.skipValue,
      select: this.selectedFields
    };

    // This would interface with actual database
    console.log('Executing query:', query);
    return [] as TSelect[];
  }

  // Execute and get first result
  async first(): Promise<TSelect | null> {
    const results = await this.limit(1).execute();
    return results[0] || null;
  }

  // Count results
  async count(): Promise<number> {
    // Implementation would return count
    return 0;
  }

  // Execute and return if exists
  async exists(): Promise<boolean> {
    return (await this.count()) > 0;
  }
}

// Repository pattern with query builder
class Repository<T extends Record<string, any>> {
  constructor(private collection: string) {}

  // Create new query builder
  query(): QueryBuilder<T> {
    return new QueryBuilder<T>();
  }

  // Find methods
  async findById(id: string): Promise<T | null> {
    return this.query().whereEquals('id' as keyof T, id as any).first();
  }

  async findOne(condition: QueryCondition<T>): Promise<T | null> {
    return this.query().where(condition).first();
  }

  async findMany(condition: QueryCondition<T>): Promise<T[]> {
    return this.query().where(condition).execute();
  }

  async findAll(): Promise<T[]> {
    return this.query().execute();
  }

  // Generic find with builder function
  async find(builder: (query: QueryBuilder<T>) => QueryBuilder<T, any>): Promise<any[]> {
    return builder(this.query()).execute();
  }

  // CRUD operations
  async create(data: Omit<T, 'id'>): Promise<T> {
    // Implementation would create record
    return { id: Math.random().toString(), ...data } as T;
  }

  async update(id: string, updates: Partial<T>): Promise<T | null> {
    // Implementation would update record
    return null;
  }

  async delete(id: string): Promise<boolean> {
    // Implementation would delete record
    return true;
  }
}

// Usage example
interface User {
  id: string;
  name: string;
  email: string;
  age: number;
  isActive: boolean;
  createdAt: Date;
}

const userRepository = new Repository<User>('users');

// Type-safe querying
const activeUsers = await userRepository
  .query()
  .where({ isActive: true })
  .whereGreaterThan('age', 18)
  .sortBy('name', 'asc')
  .limit(10)
  .execute();

// Select specific fields
const userSummaries = await userRepository
  .query()
  .select('id', 'name', 'email')
  .where({ isActive: true })
  .execute(); // Returns Pick<User, 'id' | 'name' | 'email'>[]

// Complex query using builder function
const results = await userRepository.find(query => 
  query
    .where({ isActive: true })
    .whereIn('age', [25, 30, 35])
    .sort({ createdAt: -1 })
    .limit(50)
);
```

### Example 3: Functional Pipeline with Type Safety

```typescript
// Pipeline step types
type PipelineStep<TInput, TOutput> = (input: TInput) => TOutput;
type AsyncPipelineStep<TInput, TOutput> = (input: TInput) => Promise<TOutput>;

// Pipeline builder for synchronous operations
class Pipeline<T> {
  constructor(private steps: Array<(input: any) => any> = []) {}

  // Add transformation step
  pipe<U>(step: PipelineStep<T, U>): Pipeline<U> {
    return new Pipeline<U>([...this.steps, step]);
  }

  // Add filtering step
  filter(predicate: (input: T) => boolean): Pipeline<T> {
    return this.pipe((input: T) => predicate(input) ? input : undefined)
      .pipe((input: T | undefined) => {
        if (input === undefined) {
          throw new Error('Filtered out');
        }
        return input;
      });
  }

  // Add validation step
  validate(validator: (input: T) => boolean, errorMessage?: string): Pipeline<T> {
    return this.pipe((input: T) => {
      if (!validator(input)) {
        throw new Error(errorMessage || 'Validation failed');
      }
      return input;
    });
  }

  // Add side effect step
  tap(sideEffect: (input: T) => void): Pipeline<T> {
    return this.pipe((input: T) => {
      sideEffect(input);
      return input;
    });
  }

  // Execute pipeline
  execute(input: any): T {
    return this.steps.reduce((current, step) => step(current), input);
  }

  // Execute with error handling
  safeExecute(input: any): { success: true; result: T } | { success: false; error: Error } {
    try {
      const result = this.execute(input);
      return { success: true, result };
    } catch (error) {
      return { success: false, error: error instanceof Error ? error : new Error(String(error)) };
    }
  }
}

// Async pipeline builder
class AsyncPipeline<T> {
  constructor(private steps: Array<(input: any) => Promise<any>> = []) {}

  pipe<U>(step: AsyncPipelineStep<T, U>): AsyncPipeline<U> {
    return new AsyncPipeline<U>([...this.steps, step]);
  }

  pipeBatch<U>(
    step: (inputs: T[]) => Promise<U[]>,
    batchSize: number = 10
  ): AsyncPipeline<U[]> {
    return this.pipe(async (input: T) => {
      // For single input, wrap in array and unwrap result
      if (!Array.isArray(input)) {
        const results = await step([input]);
        return results[0];
      }
      
      // Process in batches
      const batches = [];
      for (let i = 0; i < input.length; i += batchSize) {
        batches.push(input.slice(i, i + batchSize));
      }
      
      const batchResults = await Promise.all(
        batches.map(batch => step(batch))
      );
      
      return batchResults.flat();
    });
  }

  filter(predicate: (input: T) => boolean | Promise<boolean>): AsyncPipeline<T> {
    return this.pipe(async (input: T) => {
      const shouldKeep = await predicate(input);
      if (!shouldKeep) {
        throw new Error('Filtered out');
      }
      return input;
    });
  }

  validate(
    validator: (input: T) => boolean | Promise<boolean>,
    errorMessage?: string
  ): AsyncPipeline<T> {
    return this.pipe(async (input: T) => {
      const isValid = await validator(input);
      if (!isValid) {
        throw new Error(errorMessage || 'Validation failed');
      }
      return input;
    });
  }

  tap(sideEffect: (input: T) => void | Promise<void>): AsyncPipeline<T> {
    return this.pipe(async (input: T) => {
      await sideEffect(input);
      return input;
    });
  }

  async execute(input: any): Promise<T> {
    let current = input;
    for (const step of this.steps) {
      current = await step(current);
    }
    return current;
  }

  async safeExecute(input: any): Promise<{ success: true; result: T } | { success: false; error: Error }> {
    try {
      const result = await this.execute(input);
      return { success: true, result };
    } catch (error) {
      return { success: false, error: error instanceof Error ? error : new Error(String(error)) };
    }
  }

  // Parallel execution for arrays
  async executeParallel(inputs: any[], concurrency: number = 5): Promise<T[]> {
    const results: T[] = [];
    const executing: Promise<void>[] = [];

    for (const input of inputs) {
      if (executing.length >= concurrency) {
        await Promise.race(executing);
      }

      const promise = this.execute(input).then(result => {
        results.push(result);
        executing.splice(executing.indexOf(promise), 1);
      });

      executing.push(promise);
    }

    await Promise.all(executing);
    return results;
  }
}

// Pipeline factory functions
function createPipeline<T>(initialStep?: PipelineStep<any, T>): Pipeline<T> {
  return new Pipeline<T>(initialStep ? [initialStep] : []);
}

function createAsyncPipeline<T>(initialStep?: AsyncPipelineStep<any, T>): AsyncPipeline<T> {
  return new AsyncPipeline<T>(initialStep ? [initialStep] : []);
}

// Usage example
interface RawUserData {
  name: string;
  email: string;
  age: string; // String from form input
  interests: string; // Comma-separated
}

interface ProcessedUser {
  id: string;
  name: string;
  email: string;
  age: number;
  interests: string[];
  createdAt: Date;
}

// Synchronous pipeline
const userProcessingPipeline = createPipeline<RawUserData>()
  .validate(data => data.name.trim().length > 0, 'Name is required')
  .validate(data => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(data.email), 'Invalid email')
  .pipe((data: RawUserData) => ({
    ...data,
    name: data.name.trim(),
    age: parseInt(data.age, 10),
    interests: data.interests.split(',').map(i => i.trim()).filter(i => i.length > 0)
  }))
  .validate(data => data.age >= 13, 'Must be at least 13 years old')
  .pipe((data): ProcessedUser => ({
    id: Math.random().toString(36),
    name: data.name,
    email: data.email.toLowerCase(),
    age: data.age,
    interests: data.interests,
    createdAt: new Date()
  }))
  .tap(user => console.log('Processed user:', user.name));

// Async pipeline for database operations
const userPersistencePipeline = createAsyncPipeline<ProcessedUser>()
  .validate(async (user) => {
    const existing = await checkEmailExists(user.email);
    return !existing;
  }, 'Email already exists')
  .pipe(async (user) => {
    const saved = await saveUser(user);
    return saved;
  })
  .tap(async (user) => {
    await sendWelcomeEmail(user.email);
    await logUserRegistration(user.id);
  });

// Combined pipeline usage
async function registerUser(rawData: RawUserData): Promise<ProcessedUser | null> {
  // Process raw data synchronously
  const processResult = userProcessingPipeline.safeExecute(rawData);
  if (!processResult.success) {
    console.error('Processing failed:', processResult.error.message);
    return null;
  }

  // Persist processed data asynchronously
  const persistResult = await userPersistencePipeline.safeExecute(processResult.result);
  if (!persistResult.success) {
    console.error('Persistence failed:', persistResult.error.message);
    return null;
  }

  return persistResult.result;
}

// Helper functions (would be implemented)
async function checkEmailExists(email: string): Promise<boolean> { return false; }
async function saveUser(user: ProcessedUser): Promise<ProcessedUser> { return user; }
async function sendWelcomeEmail(email: string): Promise<void> {}
async function logUserRegistration(userId: string): Promise<void> {}
```

---

## 🎤 Interview Questions

### Q1: What's the difference between function declarations and function expressions in TypeScript?

**Answer:**

**Function declarations** are hoisted and can be called before they're defined, while **function expressions** are not hoisted:

```typescript
// Function declaration - hoisted
console.log(add(2, 3)); // Works: 5

function add(a: number, b: number): number {
  return a + b;
}

// Function expression - not hoisted
// console.log(multiply(2, 3)); // Error: Cannot access before initialization

const multiply = function(a: number, b: number): number {
  return a * b;
};

// Arrow function - also not hoisted
const subtract = (a: number, b: number): number => a - b;
```

---

### Q2: How do function overloads work in TypeScript?

**Answer:**

Function overloads allow multiple function signatures for the same function name, with TypeScript choosing the best match:

```typescript
// Overload signatures
function format(value: string): string;
function format(value: number): string;
function format(value: Date): string;

// Implementation signature (must be compatible with all overloads)
function format(value: string | number | Date): string {
  if (value instanceof Date) {
    return value.toISOString();
  }
  return String(value);
}

// TypeScript picks the right overload based on argument type
const str = format("hello");    // Uses string overload
const num = format(42);         // Uses number overload
const date = format(new Date()); // Uses Date overload
```

---

### Q3: How do you type the 'this' parameter in functions?

**Answer:**

Use explicit `this` parameter as the first parameter (doesn't count as a real parameter):

```typescript
interface Counter {
  count: number;
  increment(this: Counter): void;
}

const counter: Counter = {
  count: 0,
  increment(this: Counter) {
    this.count++; // TypeScript knows 'this' is Counter
  }
};

// Arrow functions don't have their own 'this'
class Service {
  private data = "service data";

  // Method - has its own 'this'
  method(this: Service) {
    return this.data;
  }

  // Arrow function - inherits lexical 'this'
  arrowMethod = () => {
    return this.data; // Always refers to Service instance
  };
}
```

---

### Q4: What are generic constraints in functions and when do you use them?

**Answer:**

Generic constraints limit what types can be used with generics using `extends`:

```typescript
// Constraint to ensure type has length property
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(item: T): T {
  console.log(`Length: ${item.length}`);
  return item;
}

logLength("hello");     // ✅ string has length
logLength([1, 2, 3]);   // ✅ array has length
// logLength(42);       // ❌ number doesn't have length

// Keyof constraint for safe property access
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "John", age: 30 };
const name = getProperty(person, "name"); // string
// const invalid = getProperty(person, "invalid"); // ❌ Error
```

---

### Q5: How do you create type-safe higher-order functions?

**Answer:**

Use generics to maintain type information through function composition:

```typescript
// Type-safe memoization
function memoize<Args extends any[], Return>(
  fn: (...args: Args) => Return
): (...args: Args) => Return {
  const cache = new Map<string, Return>();
  
  return (...args: Args): Return => {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key)!;
    }
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

// Type-safe function composition
function compose<A, B, C>(
  f: (b: B) => C,
  g: (a: A) => B
): (a: A) => C {
  return (a: A) => f(g(a));
}

const addOne = (x: number) => x + 1;
const toString = (x: number) => x.toString();
const addOneAndStringify = compose(toString, addOne);

const result = addOneAndStringify(5); // string: "6"
```

---

### Q6: What are assertion functions and how do they work?

**Answer:**

Assertion functions throw errors if conditions aren't met, and TypeScript understands this for type narrowing:

```typescript
// Assertion function signature
function assert(condition: any, message?: string): asserts condition {
  if (!condition) {
    throw new Error(message || 'Assertion failed');
  }
}

// Type predicate assertion
function assertIsNumber(value: unknown): asserts value is number {
  if (typeof value !== 'number') {
    throw new Error('Expected number');
  }
}

function processValue(input: unknown) {
  assertIsNumber(input);
  // TypeScript now knows input is number
  return input.toFixed(2);
}

// Custom assertion with generic
function assertIsDefined<T>(value: T | null | undefined): asserts value is T {
  if (value === null || value === undefined) {
    throw new Error('Value is null or undefined');
  }
}

function useValue(value: string | null) {
  assertIsDefined(value);
  // TypeScript now knows value is string
  console.log(value.toUpperCase());
}
```

---

## 📚 Summary

**Key Takeaways:**
- TypeScript enhances functions with parameter and return type safety
- Optional parameters must come after required ones; default parameters provide fallback values
- Rest parameters enable variable-length argument lists with type safety
- Function overloads allow multiple signatures for the same function
- Generic functions enable reusable, type-safe code with constraints
- Higher-order functions maintain type information through composition
- Explicit `this` parameters provide context typing for methods
- Assertion functions help with runtime validation and type narrowing
- Proper function typing improves code reliability and developer experience

---

**Next Topic:** [24. Generics](./24-generics.md)