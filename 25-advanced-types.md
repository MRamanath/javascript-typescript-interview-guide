# 25. Advanced TypeScript Types

> Advanced types in TypeScript enable sophisticated type manipulations, conditional logic, and complex type relationships for building robust, type-safe applications.

## 📋 Overview

Advanced types in TypeScript provide powerful tools for creating complex type relationships, conditional logic, and sophisticated type manipulations. These features enable you to build highly type-safe applications with minimal runtime overhead while expressing complex business logic through the type system itself.

**What You'll Learn:**
- ✅ Conditional types and type inference
- ✅ Template literal types and string manipulation
- ✅ Mapped types and key remapping
- ✅ Recursive types and type recursion
- ✅ Distributive conditional types
- ✅ Variance and type relationships
- ✅ Higher-order types and type operators
- ✅ Brand types and phantom types
- ✅ Module augmentation and declaration merging
- ✅ Advanced utility type creation
- ✅ Type-level programming patterns
- ✅ Performance considerations with complex types

---

## 🎯 Key Concepts

### 1. Conditional Types

```typescript
// Basic conditional types
type IsString<T> = T extends string ? true : false;

type Test1 = IsString<string>;  // true
type Test2 = IsString<number>;  // false

// Conditional types with infer
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type FuncReturn = ReturnType<() => string>;  // string
type AsyncReturn = ReturnType<() => Promise<number>>;  // Promise<number>

// Nested conditional types
type Flatten<T> = T extends (infer U)[] ? U : T;

type StringArray = Flatten<string[]>;    // string
type NumberType = Flatten<number>;       // number

// Advanced inference patterns
type ExtractArrayType<T> = T extends readonly (infer U)[] ? U : never;
type ExtractPromiseType<T> = T extends Promise<infer U> ? U : never;
type ExtractFunctionArgs<T> = T extends (...args: infer A) => any ? A : never;

type ArrayElement = ExtractArrayType<readonly string[]>;  // string
type PromiseValue = ExtractPromiseType<Promise<number>>;  // number
type FunctionParams = ExtractFunctionArgs<(a: string, b: number) => void>;  // [string, number]
```

### 2. Template Literal Types

```typescript
// Basic template literal types
type Greeting = `Hello, ${string}!`;
type EventName<T extends string> = `on${Capitalize<T>}`;

type ClickEvent = EventName<"click">;     // "onClick"
type MouseEvent = EventName<"mouseOver">; // "onMouseOver"

// Advanced string manipulation
type CamelCase<S extends string> = S extends `${infer P1}_${infer P2}${infer P3}`
  ? `${P1}${Uppercase<P2>}${CamelCase<P3>}`
  : S;

type CamelCased = CamelCase<"hello_world_foo">; // "helloWorldFoo"

// Path parameter extraction
type ExtractParams<T extends string> = 
  T extends `${string}:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof ExtractParams<Rest>]: string }
    : T extends `${string}:${infer Param}`
      ? { [K in Param]: string }
      : {};

type RouteParams = ExtractParams<"/users/:id/posts/:postId">; // { id: string; postId: string }

// SQL-like query builder types
type SelectClause<T, K extends keyof T> = `SELECT ${K & string} FROM ${T & string}`;
type Query = SelectClause<"users", "name" | "email">; // "SELECT name FROM users" | "SELECT email FROM users"
```

### 3. Advanced Mapped Types

```typescript
// Basic mapped types with key remapping
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type User = { name: string; age: number };
type UserGetters = Getters<User>; // { getName: () => string; getAge: () => number }

// Conditional mapped types
type OptionalKeys<T> = {
  [K in keyof T]: undefined extends T[K] ? K : never;
}[keyof T];

type RequiredKeys<T> = {
  [K in keyof T]: undefined extends T[K] ? never : K;
}[keyof T];

interface UserProfile {
  id: string;
  name: string;
  email?: string;
  avatar?: string;
}

type Optional = OptionalKeys<UserProfile>; // "email" | "avatar"
type Required = RequiredKeys<UserProfile>; // "id" | "name"

// Deep readonly transformation
type DeepReadonly<T> = {
  readonly [K in keyof T]: T[K] extends object ? DeepReadonly<T[K]> : T[K];
};

type ReadonlyUser = DeepReadonly<{
  profile: { name: string; settings: { theme: string } };
}>; // All nested properties become readonly

// Recursive mapped types for nested objects
type DeepPartial<T> = {
  [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};

type PartialUserProfile = DeepPartial<{
  user: { name: string; address: { street: string; city: string } };
}>; // All properties optional, including nested ones
```

### 4. Recursive Types and Type Recursion

```typescript
// JSON type definition using recursion
type JSONValue = 
  | string
  | number
  | boolean
  | null
  | JSONValue[]
  | { [key: string]: JSONValue };

// Tree structure with recursive types
interface TreeNode<T> {
  value: T;
  children: TreeNode<T>[];
}

// Deep flatten array type
type FlattenArray<T> = T extends readonly (infer U)[]
  ? U extends readonly any[]
    ? FlattenArray<U>
    : U
  : T;

type Nested = [[[string]]];
type Flattened = FlattenArray<Nested>; // string

// Path type for nested objects
type Path<T> = T extends object
  ? {
      [K in keyof T]: K extends string
        ? T[K] extends object
          ? K | `${K}.${Path<T[K]>}`
          : K
        : never;
    }[keyof T]
  : never;

type UserPaths = Path<{
  user: { profile: { name: string; age: number }; settings: { theme: string } };
}>; // "user" | "user.profile" | "user.profile.name" | "user.profile.age" | "user.settings" | "user.settings.theme"

// Recursive type for function composition
type Compose<F extends readonly Function[]> = F extends readonly [
  (...args: any[]) => infer R,
  ...infer Rest
]
  ? Rest extends readonly Function[]
    ? (arg: R) => Compose<Rest>
    : never
  : never;
```

### 5. Distributive Conditional Types

```typescript
// Understanding distributive behavior
type ToArray<T> = T extends any ? T[] : never;

type StringOrNumber = string | number;
type ArrayTypes = ToArray<StringOrNumber>; // string[] | number[]

// Non-distributive conditional types
type ToArrayNonDistributive<T> = [T] extends [any] ? T[] : never;
type NonDistributiveResult = ToArrayNonDistributive<string | number>; // (string | number)[]

// Practical example: Filtering union types
type NonNullable<T> = T extends null | undefined ? never : T;
type FilteredUnion = NonNullable<string | null | number | undefined>; // string | number

// Extract function types from union
type ExtractFunctions<T> = T extends Function ? T : never;
type FunctionTypes = ExtractFunctions<string | (() => void) | number | ((x: number) => string)>;
// (() => void) | ((x: number) => string)
```

### 6. Brand Types and Phantom Types

```typescript
// Brand types for type safety
type Brand<K, T> = K & { __brand: T };

type UserId = Brand<number, 'UserId'>;
type ProductId = Brand<number, 'ProductId'>;

function createUserId(id: number): UserId {
  return id as UserId;
}

function createProductId(id: number): ProductId {
  return id as ProductId;
}

function getUser(id: UserId): void {
  console.log(`Getting user ${id}`);
}

const userId = createUserId(123);
const productId = createProductId(456);

getUser(userId);     // OK
// getUser(productId); // Error: ProductId is not assignable to UserId

// Email validation with brands
type ValidEmail = Brand<string, 'ValidEmail'>;

function validateEmail(email: string): ValidEmail | null {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email) ? (email as ValidEmail) : null;
}

function sendEmail(to: ValidEmail): void {
  console.log(`Sending email to ${to}`);
}

const email = validateEmail("user@example.com");
if (email) {
  sendEmail(email); // Type-safe: we know the email is valid
}

// Phantom types for measurements
interface Meters {
  readonly _brand: unique symbol;
  readonly value: number;
}

interface Kilometers {
  readonly _brand: unique symbol;
  readonly value: number;
}

function meters(value: number): Meters {
  return { value } as Meters;
}

function kilometers(value: number): Kilometers {
  return { value } as Kilometers;
}

function addMeters(a: Meters, b: Meters): Meters {
  return meters(a.value + b.value);
}

const distance1 = meters(100);
const distance2 = kilometers(1);
const sum = addMeters(distance1, distance1); // OK
// const invalid = addMeters(distance1, distance2); // Error: can't mix units
```

### 7. Higher-Order Types and Type Operators

```typescript
// Type-level functions
type Curry<T> = T extends (arg: infer A, ...rest: infer R) => infer Return
  ? R extends []
    ? (arg: A) => Return
    : (arg: A) => Curry<(...args: R) => Return>
  : never;

declare function curry<T extends Function>(fn: T): Curry<T>;

const add = (x: number, y: number, z: number) => x + y + z;
const curriedAdd = curry(add); // (arg: number) => (arg: number) => (arg: number) => number

// Advanced function composition types
type Pipe<T extends readonly Function[]> = T extends readonly [
  (...args: any[]) => infer R,
  (arg: R) => infer S,
  ...infer Rest
]
  ? Rest extends readonly Function[]
    ? Pipe<[(arg: R) => S, ...Rest]>
    : (arg: R) => S
  : never;

declare function pipe<T extends readonly Function[]>(...fns: T): Pipe<T>;

const transform = pipe(
  (x: number) => x.toString(),
  (s: string) => s.length,
  (n: number) => n > 0
); // (x: number) => boolean

// Type-level arithmetic (limited)
type Increment<T extends number> = T extends 0 ? 1
  : T extends 1 ? 2
  : T extends 2 ? 3
  : T extends 3 ? 4
  : T extends 4 ? 5
  : number; // fallback for larger numbers

type Next = Increment<2>; // 3

// Length of tuple type
type Length<T extends readonly any[]> = T['length'];
type ArrayLength = Length<[1, 2, 3, 4]>; // 4
```

### 8. Module Augmentation and Declaration Merging

```typescript
// Extending existing modules
declare module 'express' {
  interface Request {
    user?: { id: string; name: string };
  }
}

// Global augmentation
declare global {
  interface Window {
    myCustomProperty: string;
  }
}

// Interface merging
interface User {
  name: string;
}

interface User {
  age: number;
}

interface User {
  email: string;
}

// Merged interface has all properties
const user: User = {
  name: "John",
  age: 30,
  email: "john@example.com"
};

// Namespace merging
namespace MyLibrary {
  export function utility1() {}
}

namespace MyLibrary {
  export function utility2() {}
}

// Both utilities are available
MyLibrary.utility1();
MyLibrary.utility2();

// Module and namespace merging
declare module "./myModule" {
  namespace MyNamespace {
    interface Config {
      additionalOption: boolean;
    }
  }
}
```

## 💻 Advanced Examples

### Type-Safe Event Emitter

```typescript
type EventMap = {
  'user:created': { id: string; name: string };
  'user:updated': { id: string; changes: Partial<{ name: string; email: string }> };
  'user:deleted': { id: string };
};

class TypedEventEmitter<T extends Record<string, any>> {
  private listeners: Partial<{
    [K in keyof T]: Array<(data: T[K]) => void>;
  }> = {};

  on<K extends keyof T>(event: K, listener: (data: T[K]) => void): void {
    if (!this.listeners[event]) {
      this.listeners[event] = [];
    }
    this.listeners[event]!.push(listener);
  }

  emit<K extends keyof T>(event: K, data: T[K]): void {
    const eventListeners = this.listeners[event];
    if (eventListeners) {
      eventListeners.forEach(listener => listener(data));
    }
  }
}

const emitter = new TypedEventEmitter<EventMap>();

emitter.on('user:created', (data) => {
  console.log(`User created: ${data.name} (ID: ${data.id})`);
});

emitter.emit('user:created', { id: '123', name: 'John' }); // Type-safe!
// emitter.emit('user:created', { id: '123' }); // Error: missing 'name'
```

### Advanced Form Validation Types

```typescript
type ValidationRule<T> = {
  required?: boolean;
  min?: T extends number ? number : T extends string ? number : never;
  max?: T extends number ? number : T extends string ? number : never;
  pattern?: T extends string ? RegExp : never;
  custom?: (value: T) => boolean | string;
};

type FormSchema<T> = {
  [K in keyof T]: ValidationRule<T[K]>;
};

type ValidationErrors<T> = Partial<{
  [K in keyof T]: string[];
}>;

type InferFormType<T> = T extends FormSchema<infer U> ? U : never;

class FormValidator<T extends Record<string, any>> {
  constructor(private schema: FormSchema<T>) {}

  validate(data: T): ValidationErrors<T> {
    const errors: ValidationErrors<T> = {};

    for (const field in this.schema) {
      const value = data[field];
      const rules = this.schema[field];
      const fieldErrors: string[] = [];

      if (rules.required && (value === undefined || value === null || value === '')) {
        fieldErrors.push(`${field} is required`);
      }

      if (value !== undefined && value !== null) {
        if (rules.min !== undefined) {
          if (typeof value === 'string' && value.length < rules.min) {
            fieldErrors.push(`${field} must be at least ${rules.min} characters`);
          } else if (typeof value === 'number' && value < rules.min) {
            fieldErrors.push(`${field} must be at least ${rules.min}`);
          }
        }

        if (rules.max !== undefined) {
          if (typeof value === 'string' && value.length > rules.max) {
            fieldErrors.push(`${field} must be at most ${rules.max} characters`);
          } else if (typeof value === 'number' && value > rules.max) {
            fieldErrors.push(`${field} must be at most ${rules.max}`);
          }
        }

        if (rules.pattern && typeof value === 'string' && !rules.pattern.test(value)) {
          fieldErrors.push(`${field} format is invalid`);
        }

        if (rules.custom) {
          const result = rules.custom(value);
          if (result !== true) {
            fieldErrors.push(typeof result === 'string' ? result : `${field} is invalid`);
          }
        }
      }

      if (fieldErrors.length > 0) {
        errors[field] = fieldErrors;
      }
    }

    return errors;
  }
}

// Usage
const userSchema = {
  name: { required: true, min: 2, max: 50 },
  email: { required: true, pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/ },
  age: { required: true, min: 18, max: 120 }
} satisfies FormSchema<{ name: string; email: string; age: number }>;

const validator = new FormValidator(userSchema);
const errors = validator.validate({
  name: "Jo",
  email: "invalid-email",
  age: 15
});
// errors will be type-safe and contain validation messages
```

## 🎯 Best Practices

1. **Keep Complex Types Readable**
   ```typescript
   // Bad: Complex inline type
   type ComplexType = T extends (infer U)[] ? U extends object ? { [K in keyof U]: string } : never : never;
   
   // Good: Break down into smaller types
   type ExtractArrayElement<T> = T extends (infer U)[] ? U : never;
   type ObjectToStringRecord<T> = T extends object ? { [K in keyof T]: string } : never;
   type ComplexType<T> = ObjectToStringRecord<ExtractArrayElement<T>>;
   ```

2. **Use Type Comments for Complex Logic**
   ```typescript
   // Extract the return type of a function, handling async functions
   type ReturnType<T> = T extends (...args: any[]) => infer R 
     ? R extends Promise<infer U> 
       ? U  // For async functions, extract the resolved value
       : R  // For sync functions, use the return type directly
     : never;
   ```

3. **Avoid Excessive Type Recursion**
   ```typescript
   // Bad: Can cause infinite recursion
   type BadRecursive<T> = T extends object ? BadRecursive<T[keyof T]> : T;
   
   // Good: Add depth limits or base cases
   type SafeRecursive<T, Depth extends number = 5> = 
     Depth extends 0 ? T :
     T extends object ? SafeRecursive<T[keyof T], Prev<Depth>> : T;
   ```

4. **Use Branded Types for Domain Safety**
   ```typescript
   type UserId = string & { readonly brand: unique symbol };
   type Email = string & { readonly brand: unique symbol };
   
   function sendNotification(userId: UserId, email: Email): void {
     // Implementation
   }
   ```

## 📚 Additional Resources

- [TypeScript Handbook - Advanced Types](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html)
- [Template Literal Types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)
- [Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)
- [Mapped Types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)

---

[← Previous: Generics](./24-generics.md) | [Next: Classes in TypeScript →](./26-classes-typescript.md) | [Home](./README.md)