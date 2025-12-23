# 22. TypeScript Type Aliases

> Type aliases create new names for types, enabling union types, intersection types, and complex type compositions.

## 📋 Overview

Type aliases in TypeScript provide a way to create new names for existing types or define complex type combinations. Unlike interfaces, type aliases can represent any type including primitives, unions, intersections, and conditional types, making them essential for advanced type modeling.

**What You'll Learn:**
- ✅ Basic type alias syntax and usage
- ✅ Union types and discriminated unions
- ✅ Intersection types and type composition
- ✅ Generic type aliases and constraints
- ✅ Conditional types and type inference
- ✅ Recursive type aliases
- ✅ Template literal types
- ✅ Mapped types with type aliases
- ✅ Type aliases vs interfaces comparison
- ✅ Advanced type manipulation patterns
- ✅ Performance considerations
- ✅ Real-world type modeling scenarios

---

## 🎯 Key Concepts

### 1. Basic Type Aliases

```typescript
// Simple type aliases
type ID = string | number;
type Status = 'pending' | 'approved' | 'rejected';
type Handler = () => void;

// Using type aliases
let userId: ID = "user_123";
let orderId: ID = 42;
let currentStatus: Status = 'pending';

// Object type aliases
type User = {
  id: ID;
  name: string;
  email: string;
  status: Status;
};

type UserPreferences = {
  theme: 'light' | 'dark';
  language: string;
  notifications: boolean;
};

// Function type aliases
type EventHandler<T> = (event: T) => void;
type AsyncOperation<T, R> = (input: T) => Promise<R>;
type Predicate<T> = (value: T) => boolean;

// Array and tuple type aliases
type StringArray = string[];
type NumberTuple = [number, number];
type Coordinates = [x: number, y: number, z?: number];
type NameValuePair = [name: string, value: any];

// Using in function signatures
function handleClick(handler: EventHandler<MouseEvent>): void {
  document.addEventListener('click', handler);
}

function processAsync<T, R>(
  data: T, 
  operation: AsyncOperation<T, R>
): Promise<R> {
  return operation(data);
}
```

---

### 2. Union Types

```typescript
// Basic union types
type StringOrNumber = string | number;
type Theme = 'light' | 'dark' | 'auto';
type Size = 'small' | 'medium' | 'large';

// Union with null/undefined
type NullableString = string | null;
type OptionalNumber = number | undefined;
type MaybeUser = User | null | undefined;

// Complex union types
type ApiResponse<T> = 
  | { success: true; data: T; error?: never }
  | { success: false; data?: never; error: string };

// Discriminated unions
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
    default:
      // TypeScript ensures exhaustiveness
      const _exhaustive: never = shape;
      return _exhaustive;
  }
}

// Tagged unions for state management
type LoadingState = { type: 'loading' };
type SuccessState<T> = { type: 'success'; data: T };
type ErrorState = { type: 'error'; message: string };

type AsyncState<T> = LoadingState | SuccessState<T> | ErrorState;

function handleAsyncState<T>(state: AsyncState<T>) {
  switch (state.type) {
    case 'loading':
      console.log('Loading...');
      break;
    case 'success':
      console.log('Data:', state.data);
      break;
    case 'error':
      console.log('Error:', state.message);
      break;
  }
}

// Union with type guards
type NetworkResponse = 
  | { status: 'success'; data: any }
  | { status: 'error'; code: number; message: string };

function isSuccessResponse(response: NetworkResponse): response is { status: 'success'; data: any } {
  return response.status === 'success';
}

function handleResponse(response: NetworkResponse) {
  if (isSuccessResponse(response)) {
    console.log(response.data); // TypeScript knows this is success response
  } else {
    console.log(`Error ${response.code}: ${response.message}`);
  }
}
```

---

### 3. Intersection Types

```typescript
// Basic intersection types
type HasId = { id: string };
type HasName = { name: string };
type HasTimestamps = { createdAt: Date; updatedAt: Date };

type Entity = HasId & HasName & HasTimestamps;

// Intersection with different object types
type PersonalInfo = {
  firstName: string;
  lastName: string;
  email: string;
};

type WorkInfo = {
  company: string;
  position: string;
  salary: number;
};

type Employee = PersonalInfo & WorkInfo & {
  employeeId: string;
  startDate: Date;
};

// Mixin pattern with intersections
type Timestamped<T> = T & {
  timestamp: Date;
};

type Versioned<T> = T & {
  version: number;
};

type AuditLog<T> = Timestamped<Versioned<T>> & {
  action: 'create' | 'update' | 'delete';
  userId: string;
};

// Function intersection types
type Logger = {
  log: (message: string) => void;
};

type ErrorHandler = {
  handleError: (error: Error) => void;
};

type ServiceWithLogging = Logger & ErrorHandler & {
  process: (data: any) => Promise<any>;
};

// Intersection with conditional types
type OptionalKeys<T> = {
  [K in keyof T]: T extends Record<K, T[K]> ? never : K;
}[keyof T];

type RequiredKeys<T> = {
  [K in keyof T]: T extends Record<K, T[K]> ? K : never;
}[keyof T];

type Split<T> = {
  optional: Pick<T, OptionalKeys<T>>;
  required: Pick<T, RequiredKeys<T>>;
};

// Merging conflicting types
type A = { x: string };
type B = { x: number };
type Conflict = A & B; // x is never (string & number)

// Useful intersection with functions
type EventEmitter = {
  on(event: string, handler: Function): void;
  emit(event: string, ...args: any[]): void;
};

type CustomEventEmitter = EventEmitter & {
  once(event: string, handler: Function): void;
  off(event: string, handler?: Function): void;
};
```

---

### 4. Generic Type Aliases

```typescript
// Basic generic type aliases
type Container<T> = {
  value: T;
  isEmpty: boolean;
};

type Result<T, E = Error> = 
  | { success: true; data: T }
  | { success: false; error: E };

type EventMap<T> = {
  [K in keyof T]: (payload: T[K]) => void;
};

// Generic constraints
type Serializable<T extends string | number | boolean | object> = {
  serialize(): string;
  deserialize(data: string): T;
};

type KeyOf<T> = keyof T;
type ValueOf<T> = T[keyof T];

type PickByType<T, U> = {
  [K in keyof T as T[K] extends U ? K : never]: T[K];
};

// Advanced generic patterns
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

type DeepRequired<T> = {
  [P in keyof T]-?: T[P] extends object ? DeepRequired<T[P]> : T[P];
};

type Paths<T, K extends keyof T = keyof T> = 
  K extends string | number ? 
    T[K] extends Record<string, any> ? 
      K | `${K}.${Paths<T[K]>}` : K : never;

// Generic utility types
type NonNullable<T> = T extends null | undefined ? never : T;
type NonEmptyArray<T> = [T, ...T[]];

type PromiseType<T> = T extends Promise<infer U> ? U : T;
type ArrayElement<T> = T extends (infer U)[] ? U : never;

// Example usage
type UserPaths = Paths<User>; // "id" | "name" | "email" | "preferences.theme" | ...
type ApiResult<T> = Result<T, { code: number; message: string }>;

const userResult: ApiResult<User> = {
  success: true,
  data: { id: '1', name: 'John', email: 'john@example.com', status: 'approved' }
};

// Generic type factories
type EventHandlers<T> = {
  [K in keyof T as `on${Capitalize<string & K>}`]: (value: T[K]) => void;
};

type GettersAndSetters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
} & {
  [K in keyof T as `set${Capitalize<string & K>}`]: (value: T[K]) => void;
};

type UserEventHandlers = EventHandlers<User>;
// { onId: (value: string) => void; onName: (value: string) => void; ... }
```

---

### 5. Conditional Types

```typescript
// Basic conditional types
type IsString<T> = T extends string ? true : false;
type IsArray<T> = T extends any[] ? true : false;
type IsFunction<T> = T extends (...args: any[]) => any ? true : false;

// Conditional types with infer
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
type Parameters<T> = T extends (...args: infer P) => any ? P : never;
type InstanceType<T> = T extends new (...args: any[]) => infer R ? R : any;

// Distributive conditional types
type ToArray<T> = T extends any ? T[] : never;
type StringOrNumberArray = ToArray<string | number>; // string[] | number[]

// Exclude and Extract
type Exclude<T, U> = T extends U ? never : T;
type Extract<T, U> = T extends U ? T : never;

type WithoutNull<T> = Exclude<T, null | undefined>;
type StringsOnly<T> = Extract<T, string>;

// Complex conditional logic
type FlattenArray<T> = T extends (infer U)[]
  ? U extends (infer V)[]
    ? FlattenArray<V[]>
    : U
  : T;

type Deep = string[][][];
type Flattened = FlattenArray<Deep>; // string

// Conditional types for API modeling
type ApiEndpoint<T> = T extends 'GET'
  ? { method: 'GET'; params?: Record<string, string> }
  : T extends 'POST'
  ? { method: 'POST'; body: any }
  : T extends 'PUT'
  ? { method: 'PUT'; body: any; params: Record<string, string> }
  : never;

type GetRequest = ApiEndpoint<'GET'>;
type PostRequest = ApiEndpoint<'POST'>;

// Recursive conditional types
type Flatten<T> = T extends any[]
  ? Flatten<T[0]>
  : T;

type DeepReadonly<T> = T extends (infer U)[]
  ? readonly DeepReadonly<U>[]
  : T extends object
  ? { readonly [K in keyof T]: DeepReadonly<T[K]> }
  : T;

// Template literal conditional types
type ExtractRouteParams<T> = T extends `${infer Start}/:${infer Param}/${infer Rest}`
  ? { [K in Param]: string } & ExtractRouteParams<`${Start}/${Rest}`>
  : T extends `${infer Start}/:${infer Param}`
  ? { [K in Param]: string }
  : {};

type UserRouteParams = ExtractRouteParams<'/users/:id/posts/:postId'>;
// { id: string; postId: string }
```

---

### 6. Recursive Type Aliases

```typescript
// JSON type (self-referencing)
type Json = 
  | null
  | boolean 
  | number 
  | string 
  | Json[] 
  | { [key: string]: Json };

// Tree structure
type TreeNode<T> = {
  value: T;
  children: TreeNode<T>[];
  parent?: TreeNode<T>;
};

// Nested object paths
type NestedKeyOf<ObjectType extends object> = {
  [Key in keyof ObjectType & (string | number)]: ObjectType[Key] extends object
    ? `${Key}` | `${Key}.${NestedKeyOf<ObjectType[Key]>}`
    : `${Key}`;
}[keyof ObjectType & (string | number)];

// Recursive array flattening
type FlatArray<Arr, Depth extends number = 1> = {
  done: Arr;
  recur: Arr extends ReadonlyArray<infer InnerArr>
    ? FlatArray<InnerArr, [-1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20][Depth]>
    : Arr;
}[Depth extends -1 ? 'done' : 'recur'];

// File system tree
type FileSystemNode = 
  | { type: 'file'; name: string; size: number }
  | { type: 'directory'; name: string; children: FileSystemNode[] };

// Expression tree for a simple language
type Expression = 
  | { type: 'literal'; value: number }
  | { type: 'variable'; name: string }
  | { type: 'binary'; operator: '+' | '-' | '*' | '/'; left: Expression; right: Expression }
  | { type: 'unary'; operator: '+' | '-'; operand: Expression };

// Recursive validation
type Validator<T> = {
  validate: (value: unknown) => value is T;
  rules?: ValidatorRule<T>[];
};

type ValidatorRule<T> = 
  | { type: 'required' }
  | { type: 'min'; value: number }
  | { type: 'max'; value: number }
  | { type: 'custom'; validate: (value: T) => boolean };

// Recursive data transformation
type DeepStringify<T> = T extends string | number | boolean | null | undefined
  ? string
  : T extends (infer U)[]
  ? DeepStringify<U>[]
  : T extends object
  ? { [K in keyof T]: DeepStringify<T[K]> }
  : string;

// Recursive type with depth limit
type RecursiveType<T, Depth extends number[]> = 
  Depth['length'] extends 10 
    ? T
    : T extends object
    ? { [K in keyof T]: RecursiveType<T[K], [...Depth, 1]> }
    : T;
```

---

## 💡 Real-World Examples

### Example 1: Redux State Management Types

```typescript
// Action types
type BaseAction = {
  type: string;
  payload?: any;
  meta?: any;
  error?: boolean;
};

type ActionType<T extends string, P = void> = P extends void
  ? { type: T }
  : { type: T; payload: P };

// Specific action types
type UserActions = 
  | ActionType<'USER_LOGIN_REQUEST'>
  | ActionType<'USER_LOGIN_SUCCESS', { user: User; token: string }>
  | ActionType<'USER_LOGIN_FAILURE', { error: string }>
  | ActionType<'USER_LOGOUT'>
  | ActionType<'USER_UPDATE_PROFILE', Partial<User>>;

type TodoActions =
  | ActionType<'TODO_ADD', { text: string }>
  | ActionType<'TODO_TOGGLE', { id: string }>
  | ActionType<'TODO_DELETE', { id: string }>
  | ActionType<'TODO_UPDATE', { id: string; updates: Partial<Todo> }>;

type AppActions = UserActions | TodoActions;

// State types
type AsyncState<T, E = string> = 
  | { status: 'idle'; data?: never; error?: never }
  | { status: 'loading'; data?: never; error?: never }
  | { status: 'success'; data: T; error?: never }
  | { status: 'error'; data?: never; error: E };

type UserState = {
  current: User | null;
  auth: AsyncState<{ user: User; token: string }>;
  preferences: UserPreferences;
};

type TodoState = {
  items: Record<string, Todo>;
  filter: 'all' | 'active' | 'completed';
  selectedIds: string[];
};

type RootState = {
  user: UserState;
  todos: TodoState;
  ui: {
    theme: Theme;
    loading: boolean;
    notifications: Notification[];
  };
};

// Reducer types
type Reducer<S, A> = (state: S, action: A) => S;

type UserReducer = Reducer<UserState, UserActions>;
type TodoReducer = Reducer<TodoState, TodoActions>;
type RootReducer = Reducer<RootState, AppActions>;

// Selector types
type Selector<S, R> = (state: S) => R;
type ParametricSelector<S, P, R> = (state: S, props: P) => R;

// Utility types for Redux
type ActionCreator<A> = A extends ActionType<infer T, infer P>
  ? P extends void
    ? () => ActionType<T>
    : (payload: P) => ActionType<T, P>
  : never;

type ActionCreators<T> = {
  [K in T['type']]: ActionCreator<Extract<T, { type: K }>>;
};

// Store type
type Store<S, A> = {
  getState(): S;
  dispatch(action: A): A;
  subscribe(listener: () => void): () => void;
  replaceReducer(reducer: Reducer<S, A>): void;
};

type AppStore = Store<RootState, AppActions>;

// Thunk types for async actions
type ThunkAction<R, S, E, A> = (
  dispatch: (action: A) => A,
  getState: () => S,
  extraArgument: E
) => R;

type AppThunk<R = void> = ThunkAction<R, RootState, unknown, AppActions>;

// Example usage
const loginUser = (credentials: LoginCredentials): AppThunk<Promise<void>> => 
  async (dispatch, getState) => {
    dispatch({ type: 'USER_LOGIN_REQUEST' });
    try {
      const response = await authAPI.login(credentials);
      dispatch({ 
        type: 'USER_LOGIN_SUCCESS', 
        payload: { user: response.user, token: response.token }
      });
    } catch (error) {
      dispatch({ 
        type: 'USER_LOGIN_FAILURE', 
        payload: { error: error.message }
      });
    }
  };
```

### Example 2: GraphQL Schema Types

```typescript
// GraphQL scalar types
type GraphQLScalarType = 
  | 'String'
  | 'Int' 
  | 'Float' 
  | 'Boolean' 
  | 'ID';

// GraphQL type modifiers
type NonNull<T> = T & { __nonNull: true };
type ListType<T> = T & { __list: true };
type GraphQLType<T> = T | NonNull<T> | ListType<T> | ListType<NonNull<T>>;

// Schema definition types
type FieldDefinition<T = any> = {
  type: GraphQLType<T>;
  args?: Record<string, { type: GraphQLType<any>; defaultValue?: any }>;
  resolve?: (parent: any, args: any, context: any, info: any) => T | Promise<T>;
  description?: string;
};

type ObjectTypeDefinition = {
  name: string;
  description?: string;
  fields: Record<string, FieldDefinition>;
  interfaces?: string[];
};

type InputTypeDefinition = {
  name: string;
  description?: string;
  fields: Record<string, { type: GraphQLType<any>; defaultValue?: any }>;
};

// Query and mutation builders
type QueryDefinition = Record<string, FieldDefinition>;
type MutationDefinition = Record<string, FieldDefinition>;
type SubscriptionDefinition = Record<string, FieldDefinition>;

// Context types
type GraphQLContext = {
  user?: User;
  dataSources: {
    userAPI: UserAPI;
    todoAPI: TodoAPI;
  };
  cache: Record<string, any>;
};

// Resolver types
type FieldResolver<TSource, TArgs, TContext, TResult> = (
  source: TSource,
  args: TArgs,
  context: TContext,
  info: any
) => TResult | Promise<TResult>;

type ResolverMap<T> = {
  [K in keyof T]: T[K] extends FieldDefinition<infer R>
    ? FieldResolver<any, any, GraphQLContext, R>
    : never;
};

// Example schema types
type UserType = {
  id: NonNull<'ID'>;
  name: NonNull<'String'>;
  email: NonNull<'String'>;
  todos: ListType<NonNull<TodoType>>;
  createdAt: NonNull<'String'>;
};

type TodoType = {
  id: NonNull<'ID'>;
  text: NonNull<'String'>;
  completed: NonNull<'Boolean'>;
  user: NonNull<UserType>;
  createdAt: NonNull<'String'>;
};

// Input types
type CreateUserInput = {
  name: NonNull<'String'>;
  email: NonNull<'String'>;
  password: NonNull<'String'>;
};

type UpdateTodoInput = {
  id: NonNull<'ID'>;
  text?: 'String';
  completed?: 'Boolean';
};

// Schema type
type Schema = {
  query: QueryDefinition;
  mutation?: MutationDefinition;
  subscription?: SubscriptionDefinition;
  types: Record<string, ObjectTypeDefinition | InputTypeDefinition>;
};

// Type-safe query builder
type QueryBuilder<T extends Record<string, any>> = {
  [K in keyof T]: T[K] extends FieldDefinition<infer R>
    ? R extends Record<string, any>
      ? QueryBuilder<R> | true
      : true
    : never;
};

type UserQuery = QueryBuilder<UserType>;
// Can build: { id: true, name: true, todos: { id: true, text: true } }

// Execution result types
type ExecutionResult<T> = 
  | { data: T; errors?: never }
  | { data?: never; errors: Array<{ message: string; path?: string[] }> };

// Example resolver implementation
const userResolvers: ResolverMap<UserType> = {
  id: (user) => user.id,
  name: (user) => user.name,
  email: (user) => user.email,
  todos: async (user, args, context) => {
    return context.dataSources.todoAPI.findByUserId(user.id);
  },
  createdAt: (user) => user.createdAt.toISOString()
};
```

### Example 3: Form Builder Type System

```typescript
// Field type definitions
type FieldType = 
  | 'text' 
  | 'email' 
  | 'password' 
  | 'number' 
  | 'select' 
  | 'checkbox' 
  | 'radio' 
  | 'textarea'
  | 'file'
  | 'date';

// Base field configuration
type BaseFieldConfig = {
  label: string;
  name: string;
  required?: boolean;
  disabled?: boolean;
  placeholder?: string;
  helpText?: string;
  validation?: ValidationRule[];
};

// Field-specific configurations
type TextFieldConfig = BaseFieldConfig & {
  type: 'text' | 'email' | 'password';
  minLength?: number;
  maxLength?: number;
  pattern?: RegExp;
};

type NumberFieldConfig = BaseFieldConfig & {
  type: 'number';
  min?: number;
  max?: number;
  step?: number;
};

type SelectFieldConfig = BaseFieldConfig & {
  type: 'select';
  options: Array<{ value: string; label: string }>;
  multiple?: boolean;
};

type CheckboxFieldConfig = BaseFieldConfig & {
  type: 'checkbox';
  defaultChecked?: boolean;
};

type FileFieldConfig = BaseFieldConfig & {
  type: 'file';
  accept?: string;
  multiple?: boolean;
  maxSize?: number;
};

// Union of all field configurations
type FieldConfig = 
  | TextFieldConfig
  | NumberFieldConfig
  | SelectFieldConfig
  | CheckboxFieldConfig
  | FileFieldConfig;

// Extract field value type based on configuration
type FieldValueType<T extends FieldConfig> = 
  T extends TextFieldConfig ? string :
  T extends NumberFieldConfig ? number :
  T extends CheckboxFieldConfig ? boolean :
  T extends SelectFieldConfig
    ? T['multiple'] extends true 
      ? string[] 
      : string
  : T extends FileFieldConfig
    ? T['multiple'] extends true
      ? File[]
      : File
  : unknown;

// Form schema type
type FormSchema = Record<string, FieldConfig>;

// Infer form data type from schema
type FormData<T extends FormSchema> = {
  [K in keyof T]: T[K]['required'] extends true
    ? FieldValueType<T[K]>
    : FieldValueType<T[K]> | undefined;
};

// Validation rules
type ValidationRule = 
  | { type: 'required'; message?: string }
  | { type: 'minLength'; value: number; message?: string }
  | { type: 'maxLength'; value: number; message?: string }
  | { type: 'pattern'; value: RegExp; message?: string }
  | { type: 'custom'; validate: (value: any) => boolean | Promise<boolean>; message?: string };

// Form state
type FieldState<T = any> = {
  value: T;
  error?: string;
  touched: boolean;
  dirty: boolean;
  validating: boolean;
};

type FormState<T extends FormSchema> = {
  fields: { [K in keyof T]: FieldState<FieldValueType<T[K]>> };
  isValid: boolean;
  isSubmitting: boolean;
  submitCount: number;
};

// Form actions
type FormAction<T extends FormSchema> = 
  | { type: 'SET_FIELD_VALUE'; field: keyof T; value: any }
  | { type: 'SET_FIELD_ERROR'; field: keyof T; error: string }
  | { type: 'CLEAR_FIELD_ERROR'; field: keyof T }
  | { type: 'SET_FIELD_TOUCHED'; field: keyof T; touched: boolean }
  | { type: 'RESET_FORM' }
  | { type: 'SUBMIT_START' }
  | { type: 'SUBMIT_SUCCESS' }
  | { type: 'SUBMIT_ERROR'; errors: Partial<Record<keyof T, string>> };

// Form builder interface
type FormBuilder<T extends FormSchema> = {
  schema: T;
  initialValues?: Partial<FormData<T>>;
  onSubmit: (data: FormData<T>) => void | Promise<void>;
  validate?: (data: Partial<FormData<T>>) => Record<keyof T, string> | Promise<Record<keyof T, string>>;
};

// Example usage
const registrationSchema = {
  email: {
    type: 'email' as const,
    name: 'email',
    label: 'Email Address',
    required: true,
    validation: [
      { type: 'required' as const, message: 'Email is required' },
      { type: 'pattern' as const, value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/, message: 'Invalid email' }
    ]
  },
  password: {
    type: 'password' as const,
    name: 'password',
    label: 'Password',
    required: true,
    minLength: 8,
    validation: [
      { type: 'required' as const },
      { type: 'minLength' as const, value: 8, message: 'Password must be at least 8 characters' }
    ]
  },
  age: {
    type: 'number' as const,
    name: 'age',
    label: 'Age',
    min: 18,
    max: 120
  },
  terms: {
    type: 'checkbox' as const,
    name: 'terms',
    label: 'I agree to the terms and conditions',
    required: true
  }
} satisfies FormSchema;

type RegistrationData = FormData<typeof registrationSchema>;
// {
//   email: string;
//   password: string;
//   age: number | undefined;
//   terms: boolean;
// }

const form: FormBuilder<typeof registrationSchema> = {
  schema: registrationSchema,
  initialValues: { terms: false },
  onSubmit: async (data: RegistrationData) => {
    console.log('Submitting:', data);
  }
};
```

---

## 🎤 Interview Questions

### Q1: When should you use type aliases instead of interfaces?

**Answer:**

Use **type aliases** for:
- Union types: `type Status = 'pending' | 'approved'`
- Intersection types: `type Combined = A & B`
- Computed/conditional types
- Primitive aliases: `type ID = string`

Use **interfaces** for:
- Object shapes that might be extended
- When you need declaration merging
- Class contracts

```typescript
// Type - better for unions
type Status = 'active' | 'inactive';

// Interface - better for extensible object shapes
interface User {
  name: string;
}
interface User { age: number; } // Declaration merging works
```

---

### Q2: How do discriminated unions help with type safety?

**Answer:**

Discriminated unions use a common property to distinguish between types, enabling exhaustive type checking:

```typescript
type ApiResponse<T> = 
  | { success: true; data: T }
  | { success: false; error: string };

function handleResponse<T>(response: ApiResponse<T>) {
  if (response.success) {
    console.log(response.data); // TypeScript knows data exists
  } else {
    console.log(response.error); // TypeScript knows error exists
  }
}

// Exhaustiveness checking with never
type Shape = 
  | { kind: 'circle'; radius: number }
  | { kind: 'square'; size: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case 'circle': return Math.PI * shape.radius ** 2;
    case 'square': return shape.size ** 2;
    default:
      const exhaustive: never = shape; // Error if new shape added
      return exhaustive;
  }
}
```

---

### Q3: What are the limitations of recursive type aliases?

**Answer:**

Recursive types have depth limits and performance implications:

```typescript
// ✅ Good - simple recursion
type Json = string | number | boolean | Json[] | { [key: string]: Json };

// ❌ Problematic - too deep/complex
type DeepNested<T, D extends number[]> = 
  D['length'] extends 50 
    ? never 
    : { [K in keyof T]: DeepNested<T[K], [...D, 1]> };

// Solutions:
// 1. Limit recursion depth
type LimitedRecursion<T, Depth = 0> = 
  Depth extends 5 ? any : T extends object ? { [K in keyof T]: LimitedRecursion<T[K], [any, ...Depth]> } : T;

// 2. Use conditional types to terminate
type SafeDeepPartial<T> = T extends object ? { [P in keyof T]?: SafeDeepPartial<T[P]> } : T;
```

---

### Q4: How do conditional types work with distribution?

**Answer:**

Conditional types distribute over union types automatically:

```typescript
// Distributive - T is distributed over union
type ToArray<T> = T extends any ? T[] : never;
type Result1 = ToArray<string | number>; // string[] | number[]

// Non-distributive - wrap in tuple to prevent distribution
type ToTuple<T> = [T] extends [any] ? T[] : never;
type Result2 = ToTuple<string | number>; // (string | number)[]

// Practical example
type NonNullable<T> = T extends null | undefined ? never : T;
type Clean = NonNullable<string | null | number>; // string | number
```

---

### Q5: What are mapped types and how do they work with type aliases?

**Answer:**

Mapped types transform properties of existing types:

```typescript
// Basic mapped type
type Partial<T> = { [P in keyof T]?: T[P] };
type Required<T> = { [P in keyof T]-?: T[P] };

// Key remapping with template literals
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type EventHandlers<T> = {
  [K in keyof T as `on${Capitalize<string & K>}Changed`]: (value: T[K]) => void;
};

// Conditional property inclusion
type PickByType<T, U> = {
  [P in keyof T as T[P] extends U ? P : never]: T[P];
};

type StringProps<T> = PickByType<T, string>;
```

---

### Q6: How do you create type-safe builder patterns with type aliases?

**Answer:**

Use method chaining with progressive type refinement:

```typescript
type QueryBuilder<T = {}> = {
  select<K extends string>(fields: K[]): QueryBuilder<T & Record<K, unknown>>;
  where<K extends string>(field: K, value: unknown): QueryBuilder<T>;
  orderBy<K extends string>(field: K, direction?: 'ASC' | 'DESC'): QueryBuilder<T>;
  build(): T;
};

// Usage
const query = new QueryBuilderImpl()
  .select(['name', 'email'])  // T becomes { name: unknown, email: unknown }
  .where('active', true)
  .orderBy('name')
  .build(); // Returns { name: unknown, email: unknown }

// The type system ensures you can only build after selecting fields
```

---

## 📚 Summary

**Key Takeaways:**
- Type aliases create new names for types and enable complex type compositions
- Union types represent "one of many" choices; intersection types combine multiple types
- Discriminated unions provide type-safe pattern matching with exhaustiveness checking
- Generic type aliases enable reusable, parameterized type definitions
- Conditional types enable type-level programming and computed types
- Recursive types model self-referential data structures with depth considerations
- Type aliases are more flexible than interfaces for unions, intersections, and computed types
- Performance matters with complex conditional and recursive types

---

**Next Topic:** [23. Functions in TypeScript](./23-functions-typescript.md)