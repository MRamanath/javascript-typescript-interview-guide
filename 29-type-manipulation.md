# 29. Type Manipulation in TypeScript

## **What You'll Learn:**
- Advanced type manipulation techniques using utility types
- Creating custom utility types and type transformations
- Working with mapped types, conditional types, and template literal types
- Understanding type inference and type narrowing patterns
- Building complex type systems for better code safety
- Leveraging TypeScript's type system for runtime and compile-time guarantees
- Advanced patterns like recursive types and type-level programming

## Overview

Type manipulation is one of TypeScript's most powerful features, allowing you to create sophisticated type systems that provide excellent developer experience and compile-time safety. This advanced topic goes beyond basic types to explore how you can transform, combine, and manipulate types to create flexible and reusable type definitions.

Through utility types, mapped types, conditional types, and template literal types, TypeScript enables you to build type systems that adapt to your code's structure and provide precise type checking. These techniques are essential for building libraries, frameworks, and complex applications where type safety and developer experience are paramount.

Mastering type manipulation will help you write more maintainable code, catch errors at compile time, and create APIs that are both flexible and type-safe.

## Table of Contents
- [Built-in Utility Types](#built-in-utility-types)
- [Mapped Types](#mapped-types)
- [Conditional Types](#conditional-types)
- [Template Literal Types](#template-literal-types)
- [Type Inference](#type-inference)
- [Recursive Types](#recursive-types)
- [Custom Utility Types](#custom-utility-types)
- [Type Narrowing](#type-narrowing)
- [Advanced Type Patterns](#advanced-type-patterns)
- [Type-Level Programming](#type-level-programming)
- [Real-World Examples](#real-world-examples)
- [Best Practices](#best-practices)

## Built-in Utility Types

### Partial and Required

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    age: number;
}

// Make all properties optional
type PartialUser = Partial<User>;
// Equivalent to: { id?: number; name?: string; email?: string; age?: number; }

// Make all properties required
type RequiredUser = Required<Partial<User>>;

// Usage examples
function updateUser(id: number, updates: PartialUser): User {
    // Implementation
    return {} as User;
}

function createUser(userData: RequiredUser): User {
    return userData; // All properties must be provided
}

// Practical example with API
interface ApiUser {
    id?: number;
    name?: string;
    email?: string;
    createdAt?: Date;
}

type CreateUserRequest = Required<Pick<ApiUser, 'name' | 'email'>>;
type UpdateUserRequest = Partial<Pick<ApiUser, 'name' | 'email'>>;
```

### Pick and Omit

```typescript
interface Product {
    id: number;
    name: string;
    description: string;
    price: number;
    category: string;
    inStock: boolean;
    tags: string[];
}

// Pick specific properties
type ProductSummary = Pick<Product, 'id' | 'name' | 'price'>;
// { id: number; name: string; price: number; }

// Omit specific properties
type ProductWithoutId = Omit<Product, 'id'>;
// All properties except 'id'

// Combine Pick and Omit
type ProductForUpdate = Omit<Product, 'id' | 'createdAt'>;
type ProductDisplay = Pick<Product, 'name' | 'price' | 'inStock'>;

// Advanced usage
type PublicProduct = Omit<Product, 'inStock'> & {
    available: boolean;
};
```

### Record

```typescript
// Create object type with specific keys and values
type Status = 'pending' | 'approved' | 'rejected';
type StatusMessages = Record<Status, string>;
// { pending: string; approved: string; rejected: string; }

const messages: StatusMessages = {
    pending: 'Your request is being processed',
    approved: 'Your request has been approved',
    rejected: 'Your request has been rejected'
};

// Dynamic keys with Record
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE';
type ApiEndpoints = Record<HttpMethod, string>;

const endpoints: ApiEndpoints = {
    GET: '/api/users',
    POST: '/api/users',
    PUT: '/api/users/:id',
    DELETE: '/api/users/:id'
};

// Record with complex values
type ComponentProps = Record<string, {
    required: boolean;
    type: string;
    default?: any;
}>;

const buttonProps: ComponentProps = {
    label: { required: true, type: 'string' },
    disabled: { required: false, type: 'boolean', default: false },
    onClick: { required: true, type: 'function' }
};
```

### Extract and Exclude

```typescript
type AllColors = 'red' | 'green' | 'blue' | 'yellow' | 'purple';
type PrimaryColors = 'red' | 'green' | 'blue';

// Extract specific types
type ExtractedPrimary = Extract<AllColors, PrimaryColors>;
// 'red' | 'green' | 'blue'

// Exclude specific types
type SecondaryColors = Exclude<AllColors, PrimaryColors>;
// 'yellow' | 'purple'

// Working with function types
type EventHandler = (event: MouseEvent) => void;
type KeyboardHandler = (event: KeyboardEvent) => void;
type Handler = EventHandler | KeyboardHandler | string;

type FunctionHandlers = Extract<Handler, Function>;
// EventHandler | KeyboardHandler

type NonFunctionHandlers = Exclude<Handler, Function>;
// string
```

### ReturnType and Parameters

```typescript
// Extract return type from function
function getUserData(id: number): { name: string; email: string } {
    return { name: 'John', email: 'john@example.com' };
}

type UserData = ReturnType<typeof getUserData>;
// { name: string; email: string }

// Extract parameters type
type GetUserParams = Parameters<typeof getUserData>;
// [id: number]

// Advanced usage with generic functions
function apiCall<T>(endpoint: string, data: T): Promise<T> {
    return Promise.resolve(data);
}

type ApiCallParams<T> = Parameters<typeof apiCall<T>>;
// [endpoint: string, data: T]

type ApiCallReturn<T> = ReturnType<typeof apiCall<T>>;
// Promise<T>
```

## Mapped Types

### Basic Mapped Types

```typescript
// Create new types by mapping over properties
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

type Optional<T> = {
    [P in keyof T]?: T[P];
};

// Custom mapped type
type Stringify<T> = {
    [K in keyof T]: string;
};

interface User {
    id: number;
    name: string;
    active: boolean;
}

type StringifiedUser = Stringify<User>;
// { id: string; name: string; active: string; }
```

### Advanced Mapped Types

```typescript
// Mapped types with conditions
type NonNullable<T> = {
    [P in keyof T]: T[P] extends null | undefined ? never : T[P];
};

// Mapped types with template literals
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

// Combine getters and setters
type AccessorPattern<T> = Getters<T> & Setters<T>;
type PersonAccessors = AccessorPattern<Person>;
```

### Key Remapping

```typescript
// Remove properties with specific pattern
type RemovePrivate<T> = {
    [K in keyof T as K extends `_${string}` ? never : K]: T[K];
};

interface UserWithPrivate {
    id: number;
    name: string;
    _password: string;
    _internalId: number;
}

type PublicUser = RemovePrivate<UserWithPrivate>;
// { id: number; name: string; }

// Transform property names
type PrefixKeys<T, P extends string> = {
    [K in keyof T as `${P}${string & K}`]: T[K];
};

type PrefixedUser = PrefixKeys<User, 'user_'>;
// { user_id: number; user_name: string; user_active: boolean; }
```

## Conditional Types

### Basic Conditional Types

```typescript
// T extends U ? X : Y
type IsString<T> = T extends string ? true : false;

type Test1 = IsString<string>; // true
type Test2 = IsString<number>; // false

// More complex conditional type
type ArrayElement<T> = T extends (infer U)[] ? U : T;

type StringArray = ArrayElement<string[]>; // string
type NumberArray = ArrayElement<number[]>; // number
type NotArray = ArrayElement<boolean>; // boolean
```

### Conditional Types with infer

```typescript
// Extract function parameters
type Head<T extends readonly any[]> = T extends readonly [infer H, ...any[]] ? H : never;
type Tail<T extends readonly any[]> = T extends readonly [any, ...infer T] ? T : [];

type FirstParam = Head<[string, number, boolean]>; // string
type RestParams = Tail<[string, number, boolean]>; // [number, boolean]

// Extract promise type
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type StringPromise = UnwrapPromise<Promise<string>>; // string
type DirectString = UnwrapPromise<string>; // string

// Extract function return type
type MyReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type FunctionReturn = MyReturnType<(a: number, b: string) => boolean>; // boolean
```

### Distributive Conditional Types

```typescript
// Conditional types distribute over union types
type ToArray<T> = T extends any ? T[] : never;

type StringOrNumberArray = ToArray<string | number>;
// string[] | number[] (distributed)

// Non-distributive version
type ToArrayNonDistributive<T> = [T] extends [any] ? T[] : never;

type NonDistributed = ToArrayNonDistributive<string | number>;
// (string | number)[] (not distributed)

// Practical example: Filter union types
type NonNull<T> = T extends null | undefined ? never : T;

type FilteredUnion = NonNull<string | number | null | undefined>;
// string | number
```

## Template Literal Types

### Basic Template Literals

```typescript
// Combine string literals
type Greeting = `Hello, ${string}!`;

const greeting1: Greeting = "Hello, World!"; // ✅
const greeting2: Greeting = "Hi, World!"; // ❌

// With specific literals
type Color = 'red' | 'green' | 'blue';
type CSSProperty = `color-${Color}`;
// 'color-red' | 'color-green' | 'color-blue'

// Multiple interpolations
type EventName<T extends string> = `on${Capitalize<T>}`;
type ClickEvent = EventName<'click'>; // 'onClick'
```

### Advanced Template Literal Patterns

```typescript
// URL pattern matching
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE';
type APIEndpoint = `/api/${string}`;
type HTTPRequest = `${HttpMethod} ${APIEndpoint}`;

type ValidRequest = HTTPRequest;
// 'GET /api/users' | 'POST /api/users' | etc.

// SQL query building
type SQLOperator = 'SELECT' | 'INSERT' | 'UPDATE' | 'DELETE';
type TableName = string;
type SQLQuery = `${SQLOperator} * FROM ${TableName}`;

// CSS class generation
type Size = 'sm' | 'md' | 'lg';
type Color = 'primary' | 'secondary' | 'danger';
type ButtonClass = `btn-${Size}-${Color}`;
// 'btn-sm-primary' | 'btn-sm-secondary' | etc.
```

### Template Literal Utilities

```typescript
// String manipulation utilities
type Uppercase<S extends string> = intrinsic;
type Lowercase<S extends string> = intrinsic;
type Capitalize<S extends string> = intrinsic;
type Uncapitalize<S extends string> = intrinsic;

// Custom string utilities using template literals
type Split<S extends string, D extends string> = 
    S extends `${infer T}${D}${infer U}` ? [T, ...Split<U, D>] : [S];

type SplitResult = Split<'a,b,c', ','>; // ['a', 'b', 'c']

// Path parsing
type ParsePath<T extends string> = T extends `${infer Segment}/${infer Rest}` 
    ? [Segment, ...ParsePath<Rest>] 
    : [T];

type PathSegments = ParsePath<'users/123/posts/456'>; 
// ['users', '123', 'posts', '456']
```

## Type Inference

### infer Keyword

```typescript
// Basic inference
type FirstElement<T> = T extends [infer First, ...any[]] ? First : never;
type LastElement<T> = T extends [...any[], infer Last] ? Last : never;

type First = FirstElement<[1, 2, 3]>; // 1
type Last = LastElement<[1, 2, 3]>; // 3

// Function signature inference
type GetParameters<T> = T extends (...args: infer P) => any ? P : never;
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type ParamsOf<T> = T extends (...args: infer P) => any ? P : never;

function example(a: string, b: number, c: boolean): void {}
type ExampleParams = ParamsOf<typeof example>; // [string, number, boolean]
```

### Advanced Inference Patterns

```typescript
// Nested inference
type DeepReadonly<T> = {
    readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

// Promise chain inference
type PromiseChain<T> = T extends Promise<infer U> 
    ? U extends Promise<any> 
        ? PromiseChain<U> 
        : U 
    : T;

type NestedPromise = Promise<Promise<Promise<string>>>;
type Resolved = PromiseChain<NestedPromise>; // string

// Object key inference
type KeysOfUnion<T> = T extends any ? keyof T : never;

type Keys = KeysOfUnion<{ a: 1; b: 2 } | { b: 2; c: 3 }>; // 'a' | 'b' | 'c'
```

## Recursive Types

### Basic Recursive Types

```typescript
// JSON type definition
type JSON = 
    | string 
    | number 
    | boolean 
    | null 
    | JSON[] 
    | { [key: string]: JSON };

// Tree structure
interface TreeNode<T> {
    value: T;
    children: TreeNode<T>[];
}

// File system
type FileSystemEntry = {
    name: string;
    type: 'file' | 'directory';
    children?: FileSystemEntry[];
};
```

### Advanced Recursive Patterns

```typescript
// Deep flatten array type
type Flatten<T extends readonly any[]> = T extends readonly [infer First, ...infer Rest]
    ? First extends readonly any[]
        ? [...Flatten<First>, ...Flatten<Rest>]
        : [First, ...Flatten<Rest>]
    : [];

type NestedArray = [[1, 2], [3, [4, 5]], 6];
type FlatArray = Flatten<NestedArray>; // [1, 2, 3, 4, 5, 6]

// Deep merge types
type DeepMerge<T, U> = {
    [K in keyof T | keyof U]: K extends keyof U
        ? K extends keyof T
            ? T[K] extends object
                ? U[K] extends object
                    ? DeepMerge<T[K], U[K]>
                    : U[K]
                : U[K]
            : U[K]
        : K extends keyof T
            ? T[K]
            : never;
};
```

## Custom Utility Types

### Practical Utility Types

```typescript
// Make specific properties optional
type PartialBy<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

interface User {
    id: number;
    name: string;
    email: string;
    age: number;
}

type UserWithOptionalAge = PartialBy<User, 'age'>;
// { id: number; name: string; email: string; age?: number; }

// Make specific properties required
type RequiredBy<T, K extends keyof T> = T & Required<Pick<T, K>>;

// Deep partial
type DeepPartial<T> = {
    [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

// Mutable (opposite of Readonly)
type Mutable<T> = {
    -readonly [P in keyof T]: T[P];
};
```

### Validation Utility Types

```typescript
// Ensure all keys are present
type EnsureKeys<T, K extends string> = T & Record<K, any>;

// Validate object structure
type ValidateShape<T, Shape> = T extends Shape 
    ? Shape extends T 
        ? T 
        : never 
    : never;

// Require at least one property
type RequireAtLeastOne<T> = {
    [K in keyof T]-?: Required<Pick<T, K>> & Partial<Pick<T, Exclude<keyof T, K>>>;
}[keyof T];

interface SearchOptions {
    byName?: string;
    byEmail?: string;
    byAge?: number;
}

type ValidSearchOptions = RequireAtLeastOne<SearchOptions>;
// Must have at least one of: byName, byEmail, or byAge
```

## Type Narrowing

### Type Guards

```typescript
// Custom type guards
function isString(value: any): value is string {
    return typeof value === 'string';
}

function isNumber(value: any): value is number {
    return typeof value === 'number';
}

// Generic type guard
function isArrayOf<T>(
    value: any,
    guard: (item: any) => item is T
): value is T[] {
    return Array.isArray(value) && value.every(guard);
}

// Usage
const value: unknown = ['hello', 'world'];
if (isArrayOf(value, isString)) {
    // value is now string[]
    console.log(value.map(s => s.toUpperCase()));
}
```

### Discriminated Unions

```typescript
// Action pattern with discriminated unions
type Action = 
    | { type: 'LOADING' }
    | { type: 'SUCCESS'; data: any }
    | { type: 'ERROR'; error: string };

function handleAction(action: Action) {
    switch (action.type) {
        case 'LOADING':
            // action is { type: 'LOADING' }
            break;
        case 'SUCCESS':
            // action is { type: 'SUCCESS'; data: any }
            console.log(action.data);
            break;
        case 'ERROR':
            // action is { type: 'ERROR'; error: string }
            console.error(action.error);
            break;
    }
}

// Shape discrimination
type Shape = 
    | { kind: 'circle'; radius: number }
    | { kind: 'rectangle'; width: number; height: number }
    | { kind: 'square'; size: number };

function calculateArea(shape: Shape): number {
    switch (shape.kind) {
        case 'circle':
            return Math.PI * shape.radius ** 2;
        case 'rectangle':
            return shape.width * shape.height;
        case 'square':
            return shape.size ** 2;
    }
}
```

## Advanced Type Patterns

### Builder Pattern with Types

```typescript
// Type-safe builder pattern
class QueryBuilder<T extends Record<string, any> = {}> {
    private query: T;

    constructor(query: T = {} as T) {
        this.query = query;
    }

    where<K extends string, V>(key: K, value: V): QueryBuilder<T & Record<K, V>> {
        return new QueryBuilder({ ...this.query, [key]: value });
    }

    select<K extends string>(fields: K[]): QueryBuilder<T & { select: K[] }> {
        return new QueryBuilder({ ...this.query, select: fields });
    }

    build(): T {
        return this.query;
    }
}

// Usage
const query = new QueryBuilder()
    .where('status', 'active')
    .where('age', 25)
    .select(['name', 'email'])
    .build();
// Type: { status: string; age: number; select: string[] }
```

### Event System with Types

```typescript
// Type-safe event system
interface EventMap {
    'user:login': { userId: string; timestamp: Date };
    'user:logout': { userId: string };
    'order:created': { orderId: string; amount: number };
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
}

// Usage
const emitter = new TypedEventEmitter();

emitter.on('user:login', (data) => {
    // data is properly typed as { userId: string; timestamp: Date }
    console.log(`User ${data.userId} logged in at ${data.timestamp}`);
});
```

## Type-Level Programming

### Type-Level Arithmetic

```typescript
// Type-level number operations
type Length<T extends readonly any[]> = T['length'];

type Add<A extends number, B extends number> = 
    [...Array<A>, ...Array<B>]['length'] extends number ? 
    [...Array<A>, ...Array<B>]['length'] : never;

type Subtract<A extends number, B extends number> = 
    Array<A> extends [...Array<B>, ...infer Rest] ? 
    Rest['length'] : never;

// Usage (limited to small numbers due to TypeScript limitations)
type Three = Add<1, 2>; // 3
type Two = Subtract<5, 3>; // 2
```

### Type-Level Logic

```typescript
// Boolean logic at type level
type Not<B extends boolean> = B extends true ? false : true;
type And<A extends boolean, B extends boolean> = A extends true ? B : false;
type Or<A extends boolean, B extends boolean> = A extends true ? true : B;

// Type-level comparison
type Equal<X, Y> = 
    (<T>() => T extends X ? 1 : 2) extends 
    (<T>() => T extends Y ? 1 : 2) ? true : false;

type IsEqual1 = Equal<string, string>; // true
type IsEqual2 = Equal<string, number>; // false

// Type-level array operations
type Includes<T extends readonly any[], U> = 
    T extends readonly [infer First, ...infer Rest] 
        ? Equal<First, U> extends true 
            ? true 
            : Includes<Rest, U>
        : false;

type HasString = Includes<[1, 2, 'hello', 4], string>; // true
```

## Real-World Examples

### API Response Types

```typescript
// Generic API response wrapper
type ApiResponse<T> = {
    data: T;
    status: number;
    message: string;
};

// Paginated response
type PaginatedResponse<T> = ApiResponse<{
    items: T[];
    pagination: {
        page: number;
        limit: number;
        total: number;
        hasNext: boolean;
    };
}>;

// Error response
type ErrorResponse = {
    error: string;
    details?: Record<string, string[]>;
    status: number;
};

// Union for all possible responses
type APIResult<T> = ApiResponse<T> | PaginatedResponse<T> | ErrorResponse;

// Type guard for error checking
function isErrorResponse(response: APIResult<any>): response is ErrorResponse {
    return 'error' in response;
}
```

### Form Validation Types

```typescript
// Form field configuration
type FieldConfig<T> = {
    [K in keyof T]: {
        required?: boolean;
        validator?: (value: T[K]) => string | null;
        transform?: (value: any) => T[K];
    };
};

// Form state
type FormState<T> = {
    values: Partial<T>;
    errors: Partial<Record<keyof T, string>>;
    touched: Partial<Record<keyof T, boolean>>;
    isValid: boolean;
};

// Form validation result
type ValidationResult<T> = {
    isValid: boolean;
    errors: Partial<Record<keyof T, string>>;
};

// Example usage
interface UserForm {
    email: string;
    password: string;
    confirmPassword: string;
}

const userFormConfig: FieldConfig<UserForm> = {
    email: {
        required: true,
        validator: (value) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value) ? null : 'Invalid email'
    },
    password: {
        required: true,
        validator: (value) => value.length >= 8 ? null : 'Password must be at least 8 characters'
    },
    confirmPassword: {
        required: true,
        validator: (value) => value === 'password' ? null : 'Passwords do not match'
    }
};
```

### Database Query Types

```typescript
// Database model definition
interface User {
    id: number;
    name: string;
    email: string;
    createdAt: Date;
    updatedAt: Date;
}

// Query builder types
type WhereClause<T> = {
    [K in keyof T]?: T[K] | { $gt?: T[K]; $lt?: T[K]; $in?: T[K][] };
};

type SelectClause<T> = (keyof T)[] | '*';

type OrderByClause<T> = {
    [K in keyof T]?: 'ASC' | 'DESC';
};

type QueryOptions<T> = {
    where?: WhereClause<T>;
    select?: SelectClause<T>;
    orderBy?: OrderByClause<T>;
    limit?: number;
    offset?: number;
};

// Query result type based on select clause
type QueryResult<T, S extends SelectClause<T>> = S extends '*' 
    ? T 
    : S extends (keyof T)[]
        ? Pick<T, S[number]>
        : T;

// Example usage
function findUsers<S extends SelectClause<User>>(
    options: QueryOptions<User> & { select: S }
): Promise<QueryResult<User, S>[]> {
    // Implementation
    return Promise.resolve([] as QueryResult<User, S>[]);
}

// Usage with proper type inference
const users = await findUsers({
    where: { name: 'John' },
    select: ['id', 'name', 'email'],
    orderBy: { name: 'ASC' }
});
// users is Array<{ id: number; name: string; email: string }>
```

## Best Practices

### 1. Keep Types Simple and Readable

```typescript
// ✅ Good: Clear and simple
type UserWithRole = User & { role: 'admin' | 'user' };

// ❌ Avoid: Overly complex one-liners
type ComplexType<T> = T extends { [K in keyof T]: infer U } 
    ? U extends string 
        ? { [P in keyof T]: T[P] extends string ? `prefix_${T[P]}` : T[P] }
        : T 
    : never;
```

### 2. Use Meaningful Names

```typescript
// ✅ Good: Descriptive names
type DatabaseEntity<T> = T & {
    id: string;
    createdAt: Date;
    updatedAt: Date;
};

type ApiEndpoint<TRequest, TResponse> = {
    request: TRequest;
    response: TResponse;
};

// ❌ Avoid: Generic or unclear names
type Helper<T, U> = T & U;
type Thing<X> = { data: X };
```

### 3. Document Complex Types

```typescript
/**
 * Recursively makes all properties of T optional, including nested objects.
 * Useful for patch operations where any subset of an object can be updated.
 * 
 * @example
 * type User = { name: string; profile: { age: number; bio: string } };
 * type PartialUser = DeepPartial<User>;
 * // { name?: string; profile?: { age?: number; bio?: string } }
 */
type DeepPartial<T> = {
    [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};
```

### 4. Provide Helper Types

```typescript
// Create helper types for common patterns
type NonEmptyArray<T> = [T, ...T[]];

type Optional<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

type WithId<T> = T & { id: string };

type Timestamped<T> = T & {
    createdAt: Date;
    updatedAt: Date;
};

// Usage
type CreateUserRequest = Optional<User, 'id' | 'createdAt' | 'updatedAt'>;
type DatabaseUser = WithId<Timestamped<User>>;
```

### 5. Use Type Assertions Sparingly

```typescript
// ✅ Good: Type narrowing with type guards
function processValue(value: unknown) {
    if (typeof value === 'string') {
        // TypeScript knows value is string here
        return value.toUpperCase();
    }
}

// ❌ Avoid: Excessive type assertions
function processValueBad(value: unknown) {
    return (value as string).toUpperCase(); // Unsafe!
}
```

### 6. Leverage Generic Constraints

```typescript
// ✅ Good: Constrained generics
type KeysOfType<T, U> = {
    [K in keyof T]: T[K] extends U ? K : never;
}[keyof T];

interface Example {
    name: string;
    age: number;
    active: boolean;
    count: number;
}

type NumberKeys = KeysOfType<Example, number>; // 'age' | 'count'
type StringKeys = KeysOfType<Example, string>; // 'name'
```

## Summary

Type manipulation in TypeScript is a powerful feature that enables you to:

**Key Concepts:**
- **Utility Types**: Built-in types for common transformations
- **Mapped Types**: Transform properties across object types
- **Conditional Types**: Create types based on conditions
- **Template Literals**: Build string literal types dynamically
- **Type Inference**: Extract types from existing structures

**Advanced Patterns:**
- **Recursive Types**: Handle nested structures
- **Custom Utilities**: Build reusable type transformations
- **Type Guards**: Narrow types at runtime
- **Type-Level Programming**: Perform logic at the type level

**Best Practices:**
- Keep types readable and well-documented
- Use meaningful names and helper types
- Leverage generic constraints for better type safety
- Prefer type narrowing over type assertions

Mastering these concepts will help you build more robust, type-safe applications and create better APIs with excellent developer experience.