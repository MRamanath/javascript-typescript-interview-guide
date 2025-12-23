# 20. TypeScript Advanced Types

> Advanced TypeScript types enable sophisticated type modeling, better code safety, and powerful abstractions.

## 📋 Overview

TypeScript's advanced type system goes beyond basic types to provide utility types, conditional types, mapped types, and template literal types. These features enable precise type modeling, better API design, and compile-time guarantees that prevent runtime errors.

**What You'll Learn:**
- ✅ Utility types (Partial, Required, Pick, Omit, etc.)
- ✅ Mapped types and key remapping
- ✅ Conditional types and type inference
- ✅ Template literal types
- ✅ Recursive types and type constraints
- ✅ Index access types and keyof operator
- ✅ Type guards and assertion functions
- ✅ Branded types and nominal typing
- ✅ Higher-kinded types patterns
- ✅ Type-level programming techniques
- ✅ Performance considerations with complex types
- ✅ Real-world type modeling patterns

---

## 🎯 Key Concepts

### 1. Utility Types

```typescript
// Built-in utility types
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
  isActive: boolean;
}

// Partial - makes all properties optional
type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string; age?: number; isActive?: boolean; }

function updateUser(id: number, updates: Partial<User>): User {
  // Implementation
  return { id, name: '', email: '', age: 0, isActive: true, ...updates };
}

// Required - makes all properties required
interface OptionalConfig {
  host?: string;
  port?: number;
  ssl?: boolean;
}

type RequiredConfig = Required<OptionalConfig>;
// { host: string; port: number; ssl: boolean; }

// Pick - select specific properties
type UserSummary = Pick<User, 'id' | 'name' | 'email'>;
// { id: number; name: string; email: string; }

// Omit - exclude specific properties
type UserWithoutId = Omit<User, 'id'>;
// { name: string; email: string; age: number; isActive: boolean; }

// Record - create object type with specific keys and values
type UserRoles = Record<'admin' | 'user' | 'guest', string[]>;
// { admin: string[]; user: string[]; guest: string[]; }

const permissions: UserRoles = {
  admin: ['read', 'write', 'delete'],
  user: ['read', 'write'],
  guest: ['read']
};

// Exclude and Extract with union types
type Status = 'pending' | 'approved' | 'rejected' | 'cancelled';
type ActiveStatus = Exclude<Status, 'cancelled'>; // 'pending' | 'approved' | 'rejected'
type FinalStatus = Extract<Status, 'approved' | 'rejected'>; // 'approved' | 'rejected'

// NonNullable - remove null and undefined
type MaybeString = string | null | undefined;
type DefinitelyString = NonNullable<MaybeString>; // string
```

---

### 2. Mapped Types

```typescript
// Custom mapped types
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type Optional<T> = {
  [P in keyof T]?: T[P];
};

// Key remapping with template literal types
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type Setters<T> = {
  [K in keyof T as `set${Capitalize<string & K>}`]: (value: T[K]) => void;
};

interface Person {
  name: string;
  age: number;
}

type PersonGetters = Getters<Person>;
// { getName: () => string; getAge: () => number; }

type PersonSetters = Setters<Person>;
// { setName: (value: string) => void; setAge: (value: number) => void; }

// Conditional property mapping
type ApiResponse<T> = {
  [K in keyof T]: T[K] extends string 
    ? string | null 
    : T[K] extends number 
    ? number | null 
    : T[K];
};

type UserResponse = ApiResponse<User>;
// All string and number properties become nullable

// Advanced mapped types
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

// Filter properties by type
type StringPropertyNames<T> = {
  [K in keyof T]: T[K] extends string ? K : never;
}[keyof T];

type StringProperties<T> = Pick<T, StringPropertyNames<T>>;

type UserStrings = StringProperties<User>; // { name: string; email: string; }
```

---

### 3. Conditional Types

```typescript
// Basic conditional types
type IsArray<T> = T extends any[] ? true : false;

type Test1 = IsArray<string[]>; // true
type Test2 = IsArray<string>;   // false

// Conditional types with infer
type ArrayElementType<T> = T extends (infer U)[] ? U : never;

type StringFromArray = ArrayElementType<string[]>; // string
type NumberFromArray = ArrayElementType<number[]>; // number

// Extract function parameters
type Parameters<T extends (...args: any) => any> = 
  T extends (...args: infer P) => any ? P : never;

type ReturnType<T extends (...args: any) => any> = 
  T extends (...args: any) => infer R ? R : any;

function greet(name: string, age: number): string {
  return `Hello ${name}, you are ${age}`;
}

type GreetParams = Parameters<typeof greet>; // [string, number]
type GreetReturn = ReturnType<typeof greet>; // string

// Distributive conditional types
type ToArray<T> = T extends any ? T[] : never;

type StringOrNumberArray = ToArray<string | number>; // string[] | number[]

// Non-distributive version
type ToArrayNonDistributive<T> = [T] extends [any] ? T[] : never;

type TupleArray = ToArrayNonDistributive<string | number>; // (string | number)[]

// Nested conditional types
type Flatten<T> = T extends any[] 
  ? T[0] extends any[]
    ? Flatten<T[0]>
    : T[0]
  : T;

type NestedArray = string[][][];
type FlattenedType = Flatten<NestedArray>; // string
```

---

### 4. Template Literal Types

```typescript
// Basic template literal types
type World = "world";
type Greeting = `hello ${World}`; // "hello world"

// Combining with unions
type EmailDomain = "gmail.com" | "yahoo.com" | "hotmail.com";
type EmailAddress = `user@${EmailDomain}`;
// "user@gmail.com" | "user@yahoo.com" | "user@hotmail.com"

// Dynamic property names
type EventName<T extends string> = `on${Capitalize<T>}`;

type ClickEvent = EventName<"click">; // "onClick"
type HoverEvent = EventName<"hover">; // "onHover"

// Path building
type ApiPath<T extends string> = `/api/${T}`;
type UserPath = ApiPath<"users">; // "/api/users"

// CSS-in-JS style properties
type CSSProperties = {
  [K in 
    | "margin" 
    | "padding" 
    | "border"
  as `${K}${"Top" | "Right" | "Bottom" | "Left"}`]?: string;
};

// margin-top, margin-right, etc. as camelCase properties

// Route parameters extraction
type ExtractRouteParams<T extends string> = 
  T extends `${infer Start}/:${infer Param}/${infer Rest}`
    ? { [K in Param]: string } & ExtractRouteParams<`${Start}/${Rest}`>
    : T extends `${infer Start}/:${infer Param}`
    ? { [K in Param]: string }
    : {};

type UserRoute = ExtractRouteParams<"/users/:id/posts/:postId">;
// { id: string; postId: string; }

// SQL query builder types
type SQLOperator = "=" | ">" | "<" | ">=" | "<=" | "!=";
type WhereClause<T extends string, U extends string> = 
  `WHERE ${T} ${SQLOperator} ${U}`;

type UserQuery = WhereClause<"age", "25">; // "WHERE age = 25" (and other operators)
```

---

### 5. Advanced Type Patterns

```typescript
// Branded/Nominal types
declare const __brand: unique symbol;
type Brand<T, TBrand extends string> = T & { [__brand]: TBrand };

type UserId = Brand<number, "UserId">;
type ProductId = Brand<number, "ProductId">;

function getUser(id: UserId): User | null {
  // Implementation
  return null;
}

function createUserId(id: number): UserId {
  return id as UserId;
}

// This prevents mixing up IDs
const userId = createUserId(123);
const productId = 456 as ProductId;

getUser(userId);    // ✅ Works
// getUser(productId); // ❌ Type error
// getUser(789);      // ❌ Type error

// Builder pattern with types
interface QueryBuilder<T = {}> {
  select<K extends string>(fields: K[]): QueryBuilder<T & Record<K, unknown>>;
  where<K extends string>(field: K, value: unknown): QueryBuilder<T>;
  build(): T;
}

// Fluent API
const query = createQueryBuilder()
  .select(['name', 'email'])
  .where('active', true)
  .build(); // Type is { name: unknown, email: unknown }

// Recursive types
type JSON = 
  | string 
  | number 
  | boolean 
  | null 
  | JSON[] 
  | { [key: string]: JSON };

// Tree structures
interface TreeNode<T> {
  value: T;
  children: TreeNode<T>[];
}

// Deep property access
type DeepKeyOf<T> = T extends object ? {
  [K in keyof T]: K extends string | number
    ? T[K] extends object
      ? `${K}` | `${K}.${DeepKeyOf<T[K]>}`
      : `${K}`
    : never;
}[keyof T] : never;

interface NestedObject {
  user: {
    profile: {
      name: string;
      settings: {
        theme: string;
      };
    };
  };
}

type NestedKeys = DeepKeyOf<NestedObject>; 
// "user" | "user.profile" | "user.profile.name" | "user.profile.settings" | "user.profile.settings.theme"

// Function overload generation
type OverloadedFunction<T extends Record<string, any[]>> = {
  [K in keyof T]: (...args: T[K]) => K;
}[keyof T];

type MathFunctions = {
  add: [number, number];
  multiply: [number, number];
  divide: [number, number, boolean?]; // optional precision flag
};

declare const mathOp: OverloadedFunction<MathFunctions>;
// Type: 
// ((...args: [number, number]) => "add") & 
// ((...args: [number, number]) => "multiply") & 
// ((...args: [number, number, boolean?]) => "divide")
```

---

## 💡 Real-World Examples

### Example 1: Type-Safe Event System

```typescript
// Event system with type safety
interface EventMap {
  'user:login': { userId: string; timestamp: Date };
  'user:logout': { userId: string };
  'product:view': { productId: string; userId: string };
  'order:placed': { orderId: string; amount: number; items: string[] };
}

class TypedEventEmitter<T extends Record<string, any> = EventMap> {
  private listeners: {
    [K in keyof T]?: Array<(data: T[K]) => void>;
  } = {};

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

  off<K extends keyof T>(event: K, listener: (data: T[K]) => void): void {
    const eventListeners = this.listeners[event];
    if (eventListeners) {
      const index = eventListeners.indexOf(listener);
      if (index > -1) {
        eventListeners.splice(index, 1);
      }
    }
  }
}

// Usage with full type safety
const eventEmitter = new TypedEventEmitter<EventMap>();

eventEmitter.on('user:login', (data) => {
  // data is typed as { userId: string; timestamp: Date }
  console.log(`User ${data.userId} logged in at ${data.timestamp}`);
});

eventEmitter.emit('user:login', {
  userId: 'user123',
  timestamp: new Date()
});

// Type error - missing required properties
// eventEmitter.emit('user:login', { userId: 'user123' }); // ❌

// Type error - wrong event name
// eventEmitter.on('user:signin', (data) => {}); // ❌
```

### Example 2: API Client with Type-Safe Routes

```typescript
// API route definitions
interface ApiRoutes {
  'GET /users': {
    params: {};
    query: { page?: number; limit?: number };
    response: { users: User[]; total: number };
  };
  'GET /users/:id': {
    params: { id: string };
    query: {};
    response: User;
  };
  'POST /users': {
    params: {};
    query: {};
    body: Omit<User, 'id'>;
    response: User;
  };
  'PUT /users/:id': {
    params: { id: string };
    query: {};
    body: Partial<User>;
    response: User;
  };
  'DELETE /users/:id': {
    params: { id: string };
    query: {};
    response: { success: boolean };
  };
}

// Extract HTTP method and path
type ExtractMethod<T extends string> = T extends `${infer Method} ${string}` ? Method : never;
type ExtractPath<T extends string> = T extends `${string} ${infer Path}` ? Path : never;

// Build parameter types from path
type ExtractPathParams<T extends string> = 
  T extends `${infer Start}/:${infer Param}/${infer Rest}`
    ? { [K in Param]: string } & ExtractPathParams<`${Start}/${Rest}`>
    : T extends `${infer Start}/:${infer Param}`
    ? { [K in Param]: string }
    : {};

// Type-safe API client
class TypedApiClient<T extends Record<string, any> = ApiRoutes> {
  constructor(private baseUrl: string) {}

  async request<K extends keyof T>(
    route: K,
    options: {
      params?: T[K] extends { params: infer P } ? P : {};
      query?: T[K] extends { query: infer Q } ? Q : {};
      body?: T[K] extends { body: infer B } ? B : never;
    } = {}
  ): Promise<T[K] extends { response: infer R } ? R : unknown> {
    const method = (route as string).split(' ')[0];
    let path = (route as string).split(' ')[1];
    
    // Replace path parameters
    if (options.params) {
      Object.entries(options.params).forEach(([key, value]) => {
        path = path.replace(`:${key}`, String(value));
      });
    }

    // Add query parameters
    const queryString = options.query 
      ? '?' + new URLSearchParams(options.query as any).toString()
      : '';

    const response = await fetch(`${this.baseUrl}${path}${queryString}`, {
      method,
      headers: {
        'Content-Type': 'application/json',
      },
      body: options.body ? JSON.stringify(options.body) : undefined,
    });

    return response.json();
  }
}

// Usage with full type safety
const api = new TypedApiClient<ApiRoutes>('https://api.example.com');

// ✅ Type-safe requests
const users = await api.request('GET /users', {
  query: { page: 1, limit: 10 }
});

const user = await api.request('GET /users/:id', {
  params: { id: '123' }
});

const newUser = await api.request('POST /users', {
  body: { name: 'John', email: 'john@example.com', age: 30, isActive: true }
});

// ❌ Type errors
// await api.request('GET /users/:id', { params: { userId: '123' } }); // Wrong param name
// await api.request('POST /users', { body: { name: 'John' } }); // Missing required fields
```

### Example 3: Form Validation with Advanced Types

```typescript
// Form field types
type FieldType = 'string' | 'number' | 'email' | 'url' | 'date' | 'boolean';

interface FieldConfig<T extends FieldType = FieldType> {
  type: T;
  required?: boolean;
  min?: T extends 'number' | 'date' ? number : T extends 'string' ? number : never;
  max?: T extends 'number' | 'date' ? number : T extends 'string' ? number : never;
  pattern?: T extends 'string' ? RegExp : never;
  options?: T extends 'string' ? string[] : never;
}

// Convert field config to TypeScript types
type InferFieldType<T extends FieldConfig> = 
  T extends { type: 'string'; required: true }
    ? string
  : T extends { type: 'string' }
    ? string | undefined
  : T extends { type: 'number'; required: true }
    ? number
  : T extends { type: 'number' }
    ? number | undefined
  : T extends { type: 'boolean'; required: true }
    ? boolean
  : T extends { type: 'boolean' }
    ? boolean | undefined
  : T extends { type: 'email' | 'url'; required: true }
    ? string
  : T extends { type: 'email' | 'url' }
    ? string | undefined
  : T extends { type: 'date'; required: true }
    ? Date
  : T extends { type: 'date' }
    ? Date | undefined
  : unknown;

// Form schema to TypeScript type
type InferFormType<T extends Record<string, FieldConfig>> = {
  [K in keyof T]: InferFieldType<T[K]>;
};

// Validation result types
type ValidationResult = 
  | { valid: true }
  | { valid: false; errors: string[] };

type FormValidationResult<T extends Record<string, FieldConfig>> = {
  valid: boolean;
  errors: Partial<Record<keyof T, string[]>>;
};

// Type-safe form validator
class FormValidator<T extends Record<string, FieldConfig>> {
  constructor(private schema: T) {}

  validate(data: Partial<InferFormType<T>>): FormValidationResult<T> {
    const result: FormValidationResult<T> = {
      valid: true,
      errors: {}
    };

    for (const [fieldName, config] of Object.entries(this.schema)) {
      const value = data[fieldName as keyof T];
      const fieldErrors = this.validateField(fieldName, value, config);
      
      if (!fieldErrors.valid) {
        result.valid = false;
        result.errors[fieldName as keyof T] = fieldErrors.errors;
      }
    }

    return result;
  }

  private validateField(
    fieldName: string,
    value: any,
    config: FieldConfig
  ): ValidationResult {
    const errors: string[] = [];

    // Required validation
    if (config.required && (value === undefined || value === null || value === '')) {
      errors.push(`${fieldName} is required`);
      return { valid: false, errors };
    }

    // Skip other validations if value is empty and not required
    if (!config.required && (value === undefined || value === null || value === '')) {
      return { valid: true };
    }

    // Type-specific validations
    switch (config.type) {
      case 'string':
        if (typeof value !== 'string') {
          errors.push(`${fieldName} must be a string`);
        } else {
          if (config.min && value.length < config.min) {
            errors.push(`${fieldName} must be at least ${config.min} characters`);
          }
          if (config.max && value.length > config.max) {
            errors.push(`${fieldName} must be at most ${config.max} characters`);
          }
          if (config.pattern && !config.pattern.test(value)) {
            errors.push(`${fieldName} format is invalid`);
          }
          if (config.options && !config.options.includes(value)) {
            errors.push(`${fieldName} must be one of: ${config.options.join(', ')}`);
          }
        }
        break;

      case 'number':
        if (typeof value !== 'number' || isNaN(value)) {
          errors.push(`${fieldName} must be a number`);
        } else {
          if (config.min !== undefined && value < config.min) {
            errors.push(`${fieldName} must be at least ${config.min}`);
          }
          if (config.max !== undefined && value > config.max) {
            errors.push(`${fieldName} must be at most ${config.max}`);
          }
        }
        break;

      case 'email':
        if (typeof value !== 'string' || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
          errors.push(`${fieldName} must be a valid email address`);
        }
        break;

      // ... other validations
    }

    return errors.length > 0 ? { valid: false, errors } : { valid: true };
  }
}

// Usage
const userFormSchema = {
  name: { type: 'string' as const, required: true, min: 2, max: 50 },
  email: { type: 'email' as const, required: true },
  age: { type: 'number' as const, min: 18, max: 120 },
  website: { type: 'url' as const },
  isSubscribed: { type: 'boolean' as const }
} satisfies Record<string, FieldConfig>;

type UserFormData = InferFormType<typeof userFormSchema>;
// {
//   name: string;
//   email: string;
//   age: number | undefined;
//   website: string | undefined;
//   isSubscribed: boolean | undefined;
// }

const validator = new FormValidator(userFormSchema);

const formData: Partial<UserFormData> = {
  name: 'John Doe',
  email: 'john@example.com',
  age: 25
};

const validation = validator.validate(formData);
if (validation.valid) {
  console.log('Form is valid!');
} else {
  console.log('Validation errors:', validation.errors);
}
```

---

## 🎤 Interview Questions

### Q1: What's the difference between mapped types and conditional types?

**Answer:**

**Mapped types** transform properties of a type, while **conditional types** choose between types based on conditions:

```typescript
// Mapped type - transforms properties
type Optional<T> = { [K in keyof T]?: T[K] };

// Conditional type - chooses based on condition
type IsArray<T> = T extends any[] ? true : false;

// Can be combined
type OptionalArrays<T> = {
  [K in keyof T]: T[K] extends any[] ? T[K] | undefined : T[K];
};
```

---

### Q2: How do template literal types work with unions?

**Answer:**

Template literal types distribute over unions, creating all possible combinations:

```typescript
type Size = 'small' | 'medium' | 'large';
type Color = 'red' | 'blue';
type Variant = `${Size}-${Color}`;
// 'small-red' | 'small-blue' | 'medium-red' | 'medium-blue' | 'large-red' | 'large-blue'

// Useful for CSS classes, API endpoints, etc.
type ApiEndpoint = `/api/${'users' | 'products'}/${'create' | 'update' | 'delete'}`;
```

---

### Q3: What are branded types and when would you use them?

**Answer:**

Branded types add compile-time distinction to primitive types that are otherwise the same:

```typescript
type UserId = number & { __brand: 'UserId' };
type ProductId = number & { __brand: 'ProductId' };

function getUser(id: UserId) { /* ... */ }

// Prevents accidentally passing wrong type of ID
const userId = 123 as UserId;
const productId = 456 as ProductId;

getUser(userId);    // ✅
// getUser(productId); // ❌ Type error
```

---

### Q4: How do recursive types work in TypeScript?

**Answer:**

Recursive types reference themselves, useful for tree structures and nested data:

```typescript
// JSON type
type Json = string | number | boolean | null | Json[] | { [key: string]: Json };

// Tree structure
interface TreeNode<T> {
  value: T;
  children: TreeNode<T>[];
}

// Deep readonly
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};
```

---

### Q5: What's the `infer` keyword used for?

**Answer:**

`infer` extracts types from other types within conditional types:

```typescript
// Extract array element type
type ElementType<T> = T extends (infer U)[] ? U : never;

// Extract function return type
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// Extract promise resolved type
type Awaited<T> = T extends Promise<infer U> ? U : T;

type StringArray = ElementType<string[]>; // string
type NumberReturn = ReturnType<() => number>; // number
```

---

### Q6: How do you create type-safe function overloads?

**Answer:**

Use conditional types and mapped types to generate overloads from type definitions:

```typescript
type ApiEndpoints = {
  '/users': { method: 'GET'; response: User[] };
  '/users/:id': { method: 'GET'; params: { id: string }; response: User };
  '/users': { method: 'POST'; body: CreateUser; response: User };
};

type RequestFn = <K extends keyof ApiEndpoints>(
  endpoint: K,
  ...args: ApiEndpoints[K] extends { params: infer P; body: infer B }
    ? [params: P, body: B]
    : ApiEndpoints[K] extends { body: infer B }
    ? [body: B]
    : ApiEndpoints[K] extends { params: infer P }
    ? [params: P]
    : []
) => Promise<ApiEndpoints[K] extends { response: infer R } ? R : unknown>;
```

---

## 📚 Summary

**Key Takeaways:**
- Utility types like `Partial`, `Pick`, and `Record` solve common type transformation needs
- Mapped types transform object properties with flexible key remapping
- Conditional types enable type-level programming with `extends` and `infer`
- Template literal types create sophisticated string-based type systems
- Branded types add type safety to primitive values
- Recursive types model complex nested structures
- Advanced patterns enable type-safe APIs, form validation, and event systems
- Performance matters with complex types - avoid excessive depth and computation

---

**Next Topic:** [21. Interfaces](./21-interfaces.md)