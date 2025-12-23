# 24. TypeScript Generics

> Generics enable writing reusable, type-safe code that works with multiple types while maintaining type information throughout the system.

## 📋 Overview

Generics are one of TypeScript's most powerful features, allowing you to create flexible, reusable components that work with multiple types while preserving type safety. They enable abstraction over types, making code more maintainable and reducing duplication while catching errors at compile time.

**What You'll Learn:**
- ✅ Generic functions and type parameters
- ✅ Generic interfaces and classes
- ✅ Type constraints and bounded generics
- ✅ Default generic parameters
- ✅ Generic utility types and transformations
- ✅ Conditional generics and type inference
- ✅ Variance and type relationships
- ✅ Generic decorators and metaprogramming
- ✅ Performance considerations with generics
- ✅ Advanced generic patterns
- ✅ Real-world generic architectures
- ✅ Best practices and common pitfalls

---

## 🎯 Key Concepts

### 1. Basic Generic Functions

```typescript
// Simple generic function
function identity<T>(arg: T): T {
  return arg;
}

// Usage with explicit type
const numberResult = identity<number>(42);
const stringResult = identity<string>("hello");

// Type inference (recommended)
const inferredNumber = identity(42);      // T inferred as number
const inferredString = identity("hello"); // T inferred as string

// Generic function with array
function firstElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

const firstNumber = firstElement([1, 2, 3]);      // number | undefined
const firstString = firstElement(["a", "b", "c"]); // string | undefined

// Multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const numberStringPair = pair(42, "hello");        // [number, string]
const booleanArrayPair = pair(true, [1, 2, 3]);    // [boolean, number[]]

// Generic function with conditional return
function wrapInArray<T>(value: T): T[] {
  return Array.isArray(value) ? value : [value];
}

// Generic factory function
function createContainer<T>(initialValue: T) {
  return {
    value: initialValue,
    getValue(): T { return this.value; },
    setValue(newValue: T): void { this.value = newValue; },
    map<U>(fn: (value: T) => U): { value: U; getValue(): U; setValue(newValue: U): void; map<V>(fn: (value: U) => V): any } {
      return createContainer(fn(this.value));
    }
  };
}

const numberContainer = createContainer(42);
const stringContainer = numberContainer.map(n => n.toString());
```

---

### 2. Generic Interfaces and Types

```typescript
// Generic interface
interface Container<T> {
  value: T;
  isEmpty(): boolean;
  map<U>(fn: (value: T) => U): Container<U>;
}

// Generic type alias
type Result<T, E = Error> = 
  | { success: true; data: T }
  | { success: false; error: E };

type AsyncResult<T> = Promise<Result<T>>;

// Generic interface with multiple parameters
interface KeyValueStore<K, V> {
  get(key: K): V | undefined;
  set(key: K, value: V): void;
  has(key: K): boolean;
  delete(key: K): boolean;
  keys(): K[];
  values(): V[];
  entries(): Array<[K, V]>;
  size: number;
}

// Generic interface extending another
interface SortedStore<K, V> extends KeyValueStore<K, V> {
  sort(compareFn?: (a: [K, V], b: [K, V]) => number): Array<[K, V]>;
  findRange(start: K, end: K): Array<[K, V]>;
}

// Conditional generic interfaces
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
  meta: T extends any[] ? { total: number; page: number } : { timestamp: Date };
}

// Generic interface with computed properties
type EventHandlers<T> = {
  [K in keyof T as `on${Capitalize<string & K>}`]: (value: T[K]) => void;
};

interface User {
  name: string;
  email: string;
  age: number;
}

type UserEventHandlers = EventHandlers<User>;
// Result: { onName: (value: string) => void; onEmail: (value: string) => void; onAge: (value: number) => void; }

// Generic discriminated unions
type NetworkState<T> = 
  | { state: 'loading' }
  | { state: 'success'; data: T }
  | { state: 'error'; error: string };

function handleNetworkState<T>(state: NetworkState<T>): T | null {
  switch (state.state) {
    case 'loading':
      console.log('Loading...');
      return null;
    case 'success':
      return state.data;
    case 'error':
      console.error(state.error);
      return null;
  }
}
```

---

### 3. Generic Classes

```typescript
// Basic generic class
class Stack<T> {
  private items: T[] = [];

  push(item: T): void {
    this.items.push(item);
  }

  pop(): T | undefined {
    return this.items.pop();
  }

  peek(): T | undefined {
    return this.items[this.items.length - 1];
  }

  isEmpty(): boolean {
    return this.items.length === 0;
  }

  size(): number {
    return this.items.length;
  }

  toArray(): T[] {
    return [...this.items];
  }
}

const numberStack = new Stack<number>();
numberStack.push(1);
numberStack.push(2);
console.log(numberStack.pop()); // 2

// Generic class with constraints
interface Comparable<T> {
  compareTo(other: T): number;
}

class SortedList<T extends Comparable<T>> {
  private items: T[] = [];

  add(item: T): void {
    let index = 0;
    while (index < this.items.length && this.items[index].compareTo(item) < 0) {
      index++;
    }
    this.items.splice(index, 0, item);
  }

  get(index: number): T | undefined {
    return this.items[index];
  }

  indexOf(item: T): number {
    return this.items.findIndex(existing => existing.compareTo(item) === 0);
  }

  toArray(): T[] {
    return [...this.items];
  }
}

// Generic class with static methods
class Utils<T> {
  static create<U>(): Utils<U> {
    return new Utils<U>();
  }

  static isArray<U>(value: unknown): value is U[] {
    return Array.isArray(value);
  }

  private items: T[] = [];

  add(item: T): Utils<T> {
    this.items.push(item);
    return this;
  }

  filter(predicate: (item: T) => boolean): Utils<T> {
    const filtered = new Utils<T>();
    filtered.items = this.items.filter(predicate);
    return filtered;
  }

  map<U>(fn: (item: T) => U): Utils<U> {
    const mapped = new Utils<U>();
    mapped.items = this.items.map(fn);
    return mapped;
  }
}

// Generic abstract class
abstract class Repository<T, ID> {
  abstract findById(id: ID): Promise<T | null>;
  abstract save(entity: T): Promise<T>;
  abstract delete(id: ID): Promise<void>;

  async findByIds(ids: ID[]): Promise<T[]> {
    const results = await Promise.all(
      ids.map(id => this.findById(id))
    );
    return results.filter((item): item is T => item !== null);
  }

  async saveAll(entities: T[]): Promise<T[]> {
    return Promise.all(entities.map(entity => this.save(entity)));
  }
}

class UserRepository extends Repository<User, string> {
  async findById(id: string): Promise<User | null> {
    // Implementation
    return null;
  }

  async save(user: User): Promise<User> {
    // Implementation
    return user;
  }

  async delete(id: string): Promise<void> {
    // Implementation
  }

  // User-specific methods
  async findByEmail(email: string): Promise<User | null> {
    // Implementation
    return null;
  }
}
```

---

### 4. Type Constraints and Bounds

```typescript
// Basic constraint with extends
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): T {
  console.log(`Length: ${arg.length}`);
  return arg;
}

logLength("hello");        // ✅ string has length
logLength([1, 2, 3]);      // ✅ array has length
logLength({ length: 10 }); // ✅ object with length property
// logLength(42);          // ❌ number doesn't have length

// Keyof constraint
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "John", age: 30, city: "New York" };
const name = getProperty(person, "name");  // string
const age = getProperty(person, "age");    // number
// const invalid = getProperty(person, "invalid"); // ❌ Error

// Multiple constraints
interface Named {
  name: string;
}

interface Aged {
  age: number;
}

function greetPerson<T extends Named & Aged>(person: T): string {
  return `Hello ${person.name}, you are ${person.age} years old.`;
}

// Conditional constraints
function processValue<T extends string | number>(
  value: T
): T extends string ? string : number {
  if (typeof value === 'string') {
    return value.toUpperCase() as any;
  }
  return (value as number) * 2 as any;
}

// Recursive constraints
interface TreeNode<T> {
  value: T;
  children?: TreeNode<T>[];
}

function traverseTree<T extends TreeNode<any>>(
  node: T,
  callback: (value: T['value']) => void
): void {
  callback(node.value);
  if (node.children) {
    node.children.forEach(child => traverseTree(child, callback));
  }
}

// Generic constraint with function types
interface Mappable<T> {
  map<U>(fn: (value: T) => U): Mappable<U>;
}

function chainMap<T extends Mappable<any>, U>(
  mappable: T,
  fn: (value: T extends Mappable<infer V> ? V : never) => U
): Mappable<U> {
  return mappable.map(fn);
}

// Constraint using conditional types
type NonNullable<T> = T extends null | undefined ? never : T;

function assertNonNull<T>(value: T): NonNullable<T> {
  if (value === null || value === undefined) {
    throw new Error('Value is null or undefined');
  }
  return value as NonNullable<T>;
}

// Complex constraint example
interface Serializable {
  serialize(): string;
}

interface Deserializable<T> {
  deserialize(data: string): T;
}

function saveToStorage<T extends Serializable>(
  key: string,
  value: T
): void {
  localStorage.setItem(key, value.serialize());
}

function loadFromStorage<
  T extends Serializable,
  D extends Deserializable<T>
>(key: string, deserializer: D): T | null {
  const data = localStorage.getItem(key);
  if (!data) return null;
  return deserializer.deserialize(data);
}
```

---

### 5. Default Generic Parameters

```typescript
// Basic default parameter
interface ApiResponse<T = any> {
  data: T;
  status: number;
  message: string;
}

// Can be used with or without type parameter
const response1: ApiResponse = { data: "anything", status: 200, message: "OK" };
const response2: ApiResponse<User> = { data: user, status: 200, message: "OK" };

// Multiple defaults
interface PaginatedResponse<T = any, M = { total: number }> {
  data: T[];
  meta: M;
}

// Default based on other parameters
interface EventEmitter<T extends Record<string, any> = Record<string, any>> {
  emit<K extends keyof T>(event: K, ...args: T[K] extends any[] ? T[K] : [T[K]]): void;
  on<K extends keyof T>(event: K, handler: (...args: T[K] extends any[] ? T[K] : [T[K]]) => void): void;
}

// Conditional defaults
type AsyncWrapper<T, E = T extends Promise<any> ? T : Promise<T>> = E;

// Default generic in functions
function createArray<T = string>(length: number, value?: T): T[] {
  if (value === undefined) {
    return Array(length).fill("") as T[];
  }
  return Array(length).fill(value);
}

const stringArray = createArray(5);           // string[]
const numberArray = createArray<number>(3, 0); // number[]

// Default constraints
interface Repository<
  T extends { id: any } = { id: string },
  ID = T['id']
> {
  findById(id: ID): Promise<T | null>;
  save(entity: Omit<T, 'id'>): Promise<T>;
}

// Complex default example
type Transform<
  T,
  U = T extends any[] ? T[0] : T,
  R = U extends object ? keyof U : never
> = R;

type UserKeys = Transform<User>; // keyof User
type ArrayItemKeys = Transform<User[]>; // keyof User

// Default with mapped types
type PartialBy<T, K extends keyof T = keyof T> = {
  [P in Exclude<keyof T, K>]: T[P];
} & {
  [P in K]?: T[P];
};

// Usage: make some properties optional
type PartialUser = PartialBy<User, 'email' | 'age'>; // name required, email and age optional
```

---

### 6. Advanced Generic Patterns

```typescript
// Higher-kinded types simulation
interface Functor<F> {
  map<A, B>(fa: F, f: (a: A) => B): F;
}

interface ArrayFunctor extends Functor<any[]> {
  map<A, B>(fa: A[], f: (a: A) => B): B[];
}

// Generic builder pattern
class QueryBuilder<T, S = {}> {
  private query: any = {};

  select<K extends keyof T>(...fields: K[]): QueryBuilder<T, Pick<T, K>> {
    this.query.select = fields;
    return this as any;
  }

  where<K extends keyof T>(field: K, value: T[K]): QueryBuilder<T, S> {
    this.query.where = { ...this.query.where, [field]: value };
    return this;
  }

  orderBy<K extends keyof T>(field: K, direction: 'ASC' | 'DESC' = 'ASC'): QueryBuilder<T, S> {
    this.query.orderBy = { field, direction };
    return this;
  }

  build(): { select: (keyof S)[], where: Partial<T>, orderBy: any } {
    return this.query;
  }
}

// Type-level programming
type Head<T extends readonly any[]> = T extends readonly [infer H, ...any[]] ? H : never;
type Tail<T extends readonly any[]> = T extends readonly [any, ...infer Rest] ? Rest : [];
type Length<T extends readonly any[]> = T['length'];

type FirstElement = Head<[1, 2, 3]>; // 1
type RestElements = Tail<[1, 2, 3]>; // [2, 3]
type ArrayLength = Length<[1, 2, 3]>; // 3

// Recursive generic types
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends (infer U)[]
    ? readonly DeepReadonly<U>[]
    : T[P] extends object
    ? DeepReadonly<T[P]>
    : T[P];
};

type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends (infer U)[]
    ? DeepPartial<U>[]
    : T[P] extends object
    ? DeepPartial<T[P]>
    : T[P];
};

// Generic validation system
interface Validator<T> {
  validate(value: unknown): value is T;
  errors?: string[];
}

class ValidationBuilder<T> {
  private validators: Array<(value: unknown) => value is any> = [];

  required(): ValidationBuilder<NonNullable<T>> {
    this.validators.push((value): value is NonNullable<T> => value != null);
    return this as any;
  }

  string(): ValidationBuilder<T extends string ? T : string> {
    this.validators.push((value): value is string => typeof value === 'string');
    return this as any;
  }

  number(): ValidationBuilder<T extends number ? T : number> {
    this.validators.push((value): value is number => typeof value === 'number');
    return this as any;
  }

  array<U>(): ValidationBuilder<T extends U[] ? T : U[]> {
    this.validators.push((value): value is U[] => Array.isArray(value));
    return this as any;
  }

  custom<U extends T>(validator: (value: T) => value is U): ValidationBuilder<U> {
    this.validators.push(validator);
    return this as any;
  }

  build(): Validator<T> {
    const validators = [...this.validators];
    return {
      validate: (value: unknown): value is T => {
        return validators.every(validator => validator(value));
      }
    };
  }
}

// Usage
const userValidator = new ValidationBuilder<unknown>()
  .required()
  .custom((value): value is { name: string; email: string } => 
    typeof value === 'object' && value !== null &&
    'name' in value && typeof value.name === 'string' &&
    'email' in value && typeof value.email === 'string'
  )
  .build();

// Generic state management
interface Action<T = any> {
  type: string;
  payload?: T;
}

type Reducer<S, A extends Action> = (state: S, action: A) => S;

class Store<S, A extends Action = Action> {
  constructor(
    private reducer: Reducer<S, A>,
    private state: S
  ) {}

  dispatch(action: A): void {
    this.state = this.reducer(this.state, action);
    this.notifySubscribers();
  }

  getState(): S {
    return this.state;
  }

  subscribe(listener: (state: S) => void): () => void {
    // Implementation details
    return () => {};
  }

  private notifySubscribers(): void {
    // Implementation details
  }
}

// Type-safe action creators
type ActionCreator<T extends Action> = T['payload'] extends undefined
  ? () => T
  : (payload: T['payload']) => T;

function createActionCreator<T extends Action>(type: T['type']): ActionCreator<T> {
  return ((payload?: any) => ({ type, payload })) as ActionCreator<T>;
}
```

---

## 💡 Real-World Examples

### Example 1: Generic Repository Pattern with Database Abstraction

```typescript
// Entity base interface
interface Entity {
  id: string;
  createdAt: Date;
  updatedAt: Date;
}

// Query options
interface QueryOptions<T> {
  where?: Partial<T>;
  orderBy?: {
    field: keyof T;
    direction: 'ASC' | 'DESC';
  }[];
  limit?: number;
  offset?: number;
  include?: string[];
}

interface PaginationResult<T> {
  data: T[];
  total: number;
  page: number;
  pageSize: number;
  totalPages: number;
}

// Database adapter interface
interface DatabaseAdapter {
  connect(): Promise<void>;
  disconnect(): Promise<void>;
  query<T>(sql: string, params?: any[]): Promise<T[]>;
  execute(sql: string, params?: any[]): Promise<{ affectedRows: number; insertId?: string }>;
  beginTransaction(): Promise<void>;
  commit(): Promise<void>;
  rollback(): Promise<void>;
}

// Generic repository base class
abstract class BaseRepository<T extends Entity, TCreate = Omit<T, keyof Entity>, TUpdate = Partial<TCreate>> {
  constructor(
    protected db: DatabaseAdapter,
    protected tableName: string
  ) {}

  async findById(id: string): Promise<T | null> {
    const results = await this.db.query<T>(
      `SELECT * FROM ${this.tableName} WHERE id = ?`,
      [id]
    );
    return results[0] || null;
  }

  async findOne(where: Partial<T>): Promise<T | null> {
    const { whereClause, params } = this.buildWhereClause(where);
    const results = await this.db.query<T>(
      `SELECT * FROM ${this.tableName} WHERE ${whereClause}`,
      params
    );
    return results[0] || null;
  }

  async findMany(options: QueryOptions<T> = {}): Promise<T[]> {
    const query = this.buildSelectQuery(options);
    return this.db.query<T>(query.sql, query.params);
  }

  async findWithPagination(
    options: QueryOptions<T> = {},
    page: number = 1,
    pageSize: number = 10
  ): Promise<PaginationResult<T>> {
    const offset = (page - 1) * pageSize;
    const dataQuery = this.buildSelectQuery({ ...options, limit: pageSize, offset });
    const countQuery = this.buildCountQuery(options);

    const [data, countResult] = await Promise.all([
      this.db.query<T>(dataQuery.sql, dataQuery.params),
      this.db.query<{ count: number }>(countQuery.sql, countQuery.params)
    ]);

    const total = countResult[0]?.count || 0;
    const totalPages = Math.ceil(total / pageSize);

    return {
      data,
      total,
      page,
      pageSize,
      totalPages
    };
  }

  async create(data: TCreate): Promise<T> {
    const now = new Date();
    const entityData = {
      ...data,
      id: this.generateId(),
      createdAt: now,
      updatedAt: now
    };

    const { fields, values, params } = this.buildInsertClause(entityData);
    const result = await this.db.execute(
      `INSERT INTO ${this.tableName} (${fields}) VALUES (${values})`,
      params
    );

    return this.findById(entityData.id) as Promise<T>;
  }

  async update(id: string, data: TUpdate): Promise<T | null> {
    const updateData = {
      ...data,
      updatedAt: new Date()
    };

    const { setClause, params } = this.buildUpdateClause(updateData);
    await this.db.execute(
      `UPDATE ${this.tableName} SET ${setClause} WHERE id = ?`,
      [...params, id]
    );

    return this.findById(id);
  }

  async delete(id: string): Promise<boolean> {
    const result = await this.db.execute(
      `DELETE FROM ${this.tableName} WHERE id = ?`,
      [id]
    );
    return result.affectedRows > 0;
  }

  async exists(where: Partial<T>): Promise<boolean> {
    const { whereClause, params } = this.buildWhereClause(where);
    const results = await this.db.query<{ count: number }>(
      `SELECT COUNT(*) as count FROM ${this.tableName} WHERE ${whereClause}`,
      params
    );
    return (results[0]?.count || 0) > 0;
  }

  // Transaction support
  async transaction<R>(callback: (repo: this) => Promise<R>): Promise<R> {
    await this.db.beginTransaction();
    try {
      const result = await callback(this);
      await this.db.commit();
      return result;
    } catch (error) {
      await this.db.rollback();
      throw error;
    }
  }

  // Protected helper methods
  protected generateId(): string {
    return `${Date.now()}-${Math.random().toString(36).substr(2, 9)}`;
  }

  protected buildWhereClause(where: Partial<T>): { whereClause: string; params: any[] } {
    const conditions: string[] = [];
    const params: any[] = [];

    Object.entries(where).forEach(([key, value]) => {
      if (value !== undefined) {
        conditions.push(`${key} = ?`);
        params.push(value);
      }
    });

    return {
      whereClause: conditions.join(' AND '),
      params
    };
  }

  protected buildSelectQuery(options: QueryOptions<T>): { sql: string; params: any[] } {
    let sql = `SELECT * FROM ${this.tableName}`;
    const params: any[] = [];

    if (options.where) {
      const { whereClause, params: whereParams } = this.buildWhereClause(options.where);
      sql += ` WHERE ${whereClause}`;
      params.push(...whereParams);
    }

    if (options.orderBy) {
      const orderBy = options.orderBy
        .map(o => `${String(o.field)} ${o.direction}`)
        .join(', ');
      sql += ` ORDER BY ${orderBy}`;
    }

    if (options.limit) {
      sql += ` LIMIT ${options.limit}`;
    }

    if (options.offset) {
      sql += ` OFFSET ${options.offset}`;
    }

    return { sql, params };
  }

  protected buildCountQuery(options: QueryOptions<T>): { sql: string; params: any[] } {
    let sql = `SELECT COUNT(*) as count FROM ${this.tableName}`;
    const params: any[] = [];

    if (options.where) {
      const { whereClause, params: whereParams } = this.buildWhereClause(options.where);
      sql += ` WHERE ${whereClause}`;
      params.push(...whereParams);
    }

    return { sql, params };
  }

  protected buildInsertClause(data: any): { fields: string; values: string; params: any[] } {
    const keys = Object.keys(data);
    const fields = keys.join(', ');
    const values = keys.map(() => '?').join(', ');
    const params = keys.map(key => data[key]);

    return { fields, values, params };
  }

  protected buildUpdateClause(data: any): { setClause: string; params: any[] } {
    const entries = Object.entries(data).filter(([_, value]) => value !== undefined);
    const setClause = entries.map(([key]) => `${key} = ?`).join(', ');
    const params = entries.map(([_, value]) => value);

    return { setClause, params };
  }
}

// Specific entity implementations
interface User extends Entity {
  email: string;
  name: string;
  role: 'admin' | 'user' | 'guest';
  isActive: boolean;
}

interface CreateUserData {
  email: string;
  name: string;
  role?: 'admin' | 'user' | 'guest';
  isActive?: boolean;
}

interface UpdateUserData {
  email?: string;
  name?: string;
  role?: 'admin' | 'user' | 'guest';
  isActive?: boolean;
}

class UserRepository extends BaseRepository<User, CreateUserData, UpdateUserData> {
  constructor(db: DatabaseAdapter) {
    super(db, 'users');
  }

  async findByEmail(email: string): Promise<User | null> {
    return this.findOne({ email } as Partial<User>);
  }

  async findActiveUsers(): Promise<User[]> {
    return this.findMany({
      where: { isActive: true } as Partial<User>,
      orderBy: [{ field: 'name', direction: 'ASC' }]
    });
  }

  async findByRole(role: User['role']): Promise<User[]> {
    return this.findMany({
      where: { role } as Partial<User>
    });
  }

  async activateUser(id: string): Promise<User | null> {
    return this.update(id, { isActive: true });
  }

  async deactivateUser(id: string): Promise<User | null> {
    return this.update(id, { isActive: false });
  }

  async changeUserRole(id: string, role: User['role']): Promise<User | null> {
    return this.update(id, { role });
  }
}

// Usage example
class UserService {
  constructor(private userRepository: UserRepository) {}

  async registerUser(userData: CreateUserData): Promise<User> {
    // Check if email already exists
    const existingUser = await this.userRepository.findByEmail(userData.email);
    if (existingUser) {
      throw new Error('Email already exists');
    }

    // Create new user
    const user = await this.userRepository.create({
      ...userData,
      role: userData.role || 'user',
      isActive: userData.isActive ?? true
    });

    return user;
  }

  async getUserProfile(userId: string): Promise<User | null> {
    return this.userRepository.findById(userId);
  }

  async updateUserProfile(userId: string, updates: UpdateUserData): Promise<User | null> {
    return this.userRepository.update(userId, updates);
  }

  async deleteUser(userId: string): Promise<boolean> {
    return this.userRepository.delete(userId);
  }

  async getActiveUsers(page: number = 1, pageSize: number = 10): Promise<PaginationResult<User>> {
    return this.userRepository.findWithPagination(
      { where: { isActive: true } as Partial<User> },
      page,
      pageSize
    );
  }

  async promoteToAdmin(userId: string): Promise<User | null> {
    return this.userRepository.changeUserRole(userId, 'admin');
  }
}
```

### Example 2: Generic State Management System

```typescript
// Action and reducer types
interface Action<T = any> {
  type: string;
  payload?: T;
  meta?: any;
  error?: boolean;
}

type Reducer<S, A extends Action = Action> = (state: S, action: A) => S;

// State slice definition
interface StateSlice<T, A extends Action = Action> {
  name: string;
  initialState: T;
  reducers: {
    [K in A['type']]?: Reducer<T, Extract<A, { type: K }>>;
  };
  selectors?: {
    [key: string]: (state: T) => any;
  };
}

// Store configuration
interface StoreConfig<S extends Record<string, any>, A extends Action = Action> {
  slices: {
    [K in keyof S]: StateSlice<S[K], A>;
  };
  middleware?: Array<Middleware<S, A>>;
  devTools?: boolean;
}

// Middleware type
type Middleware<S, A extends Action> = (
  store: Store<S, A>
) => (next: (action: A) => void) => (action: A) => void;

// Generic store implementation
class Store<S extends Record<string, any>, A extends Action = Action> {
  private state: S;
  private listeners: Set<(state: S) => void> = new Set();
  private reducers: { [K in keyof S]: Reducer<S[K], A> };
  private middleware: Array<Middleware<S, A>> = [];

  constructor(config: StoreConfig<S, A>) {
    // Initialize state
    this.state = Object.keys(config.slices).reduce((state, key) => {
      state[key as keyof S] = config.slices[key as keyof S].initialState;
      return state;
    }, {} as S);

    // Build combined reducer
    this.reducers = Object.keys(config.slices).reduce((reducers, key) => {
      const slice = config.slices[key as keyof S];
      reducers[key as keyof S] = (state: S[keyof S], action: A) => {
        const handler = slice.reducers[action.type];
        return handler ? handler(state, action as any) : state;
      };
      return reducers;
    }, {} as { [K in keyof S]: Reducer<S[K], A> });

    this.middleware = config.middleware || [];
  }

  getState(): S {
    return { ...this.state };
  }

  dispatch = (action: A): void => {
    // Apply middleware
    const dispatch = this.middleware.reduceRight(
      (next, middleware) => middleware(this)(next),
      this.internalDispatch
    );

    dispatch(action);
  };

  private internalDispatch = (action: A): void => {
    // Update state using reducers
    const newState = Object.keys(this.reducers).reduce((state, key) => {
      state[key as keyof S] = this.reducers[key as keyof S](
        this.state[key as keyof S],
        action
      );
      return state;
    }, {} as S);

    const hasChanged = Object.keys(newState).some(
      key => newState[key as keyof S] !== this.state[key as keyof S]
    );

    if (hasChanged) {
      this.state = newState;
      this.notifyListeners();
    }
  };

  subscribe(listener: (state: S) => void): () => void {
    this.listeners.add(listener);
    return () => {
      this.listeners.delete(listener);
    };
  }

  private notifyListeners(): void {
    this.listeners.forEach(listener => listener(this.state));
  }

  // Selector methods
  select<R>(selector: (state: S) => R): R {
    return selector(this.state);
  }

  selectSlice<K extends keyof S>(sliceName: K): S[K] {
    return this.state[sliceName];
  }
}

// Helper to create type-safe action creators
type ActionCreator<T extends Action> = T['payload'] extends undefined
  ? () => T
  : (payload: T['payload']) => T;

type ActionCreators<A extends Action> = {
  [K in A['type']]: ActionCreator<Extract<A, { type: K }>>;
};

function createActionCreators<A extends Action>(
  actionTypes: A['type'][]
): ActionCreators<A> {
  return actionTypes.reduce((creators, type) => {
    creators[type] = ((payload?: any) => ({ type, payload })) as any;
    return creators;
  }, {} as ActionCreators<A>);
}

// Slice builder helper
class SliceBuilder<T, A extends Action = Action> {
  private slice: Partial<StateSlice<T, A>> = {};

  name(name: string): SliceBuilder<T, A> {
    this.slice.name = name;
    return this;
  }

  initialState(state: T): SliceBuilder<T, A> {
    this.slice.initialState = state;
    return this;
  }

  addReducer<K extends A['type']>(
    actionType: K,
    reducer: Reducer<T, Extract<A, { type: K }>>
  ): SliceBuilder<T, A> {
    if (!this.slice.reducers) {
      this.slice.reducers = {};
    }
    this.slice.reducers[actionType] = reducer as any;
    return this;
  }

  addSelector<R>(name: string, selector: (state: T) => R): SliceBuilder<T, A> {
    if (!this.slice.selectors) {
      this.slice.selectors = {};
    }
    this.slice.selectors[name] = selector;
    return this;
  }

  build(): StateSlice<T, A> {
    if (!this.slice.name || !this.slice.initialState || !this.slice.reducers) {
      throw new Error('Slice must have name, initialState, and reducers');
    }
    return this.slice as StateSlice<T, A>;
  }
}

// Usage example
interface UserState {
  currentUser: User | null;
  users: User[];
  loading: boolean;
  error: string | null;
}

interface TodoState {
  items: Todo[];
  filter: 'all' | 'active' | 'completed';
  loading: boolean;
}

interface AppState {
  user: UserState;
  todos: TodoState;
}

// Define actions
type UserActions =
  | { type: 'USER_LOGIN_START' }
  | { type: 'USER_LOGIN_SUCCESS'; payload: User }
  | { type: 'USER_LOGIN_ERROR'; payload: string }
  | { type: 'USER_LOGOUT' }
  | { type: 'USERS_LOAD_SUCCESS'; payload: User[] };

type TodoActions =
  | { type: 'TODO_ADD'; payload: { text: string } }
  | { type: 'TODO_TOGGLE'; payload: { id: string } }
  | { type: 'TODO_DELETE'; payload: { id: string } }
  | { type: 'TODOS_SET_FILTER'; payload: { filter: TodoState['filter'] } };

type AppActions = UserActions | TodoActions;

// Create slices
const userSlice = new SliceBuilder<UserState, AppActions>()
  .name('user')
  .initialState({
    currentUser: null,
    users: [],
    loading: false,
    error: null
  })
  .addReducer('USER_LOGIN_START', (state) => ({
    ...state,
    loading: true,
    error: null
  }))
  .addReducer('USER_LOGIN_SUCCESS', (state, action) => ({
    ...state,
    currentUser: action.payload,
    loading: false,
    error: null
  }))
  .addReducer('USER_LOGIN_ERROR', (state, action) => ({
    ...state,
    loading: false,
    error: action.payload
  }))
  .addReducer('USER_LOGOUT', (state) => ({
    ...state,
    currentUser: null
  }))
  .addReducer('USERS_LOAD_SUCCESS', (state, action) => ({
    ...state,
    users: action.payload
  }))
  .addSelector('isAuthenticated', (state) => state.currentUser !== null)
  .addSelector('getUserById', (state) => (id: string) => 
    state.users.find(user => user.id === id)
  )
  .build();

const todoSlice = new SliceBuilder<TodoState, AppActions>()
  .name('todos')
  .initialState({
    items: [],
    filter: 'all',
    loading: false
  })
  .addReducer('TODO_ADD', (state, action) => ({
    ...state,
    items: [...state.items, {
      id: Date.now().toString(),
      text: action.payload.text,
      completed: false,
      createdAt: new Date(),
      updatedAt: new Date()
    } as Todo]
  }))
  .addReducer('TODO_TOGGLE', (state, action) => ({
    ...state,
    items: state.items.map(item =>
      item.id === action.payload.id
        ? { ...item, completed: !item.completed, updatedAt: new Date() }
        : item
    )
  }))
  .addReducer('TODO_DELETE', (state, action) => ({
    ...state,
    items: state.items.filter(item => item.id !== action.payload.id)
  }))
  .addReducer('TODOS_SET_FILTER', (state, action) => ({
    ...state,
    filter: action.payload.filter
  }))
  .addSelector('getVisibleTodos', (state) => {
    switch (state.filter) {
      case 'active': return state.items.filter(todo => !todo.completed);
      case 'completed': return state.items.filter(todo => todo.completed);
      default: return state.items;
    }
  })
  .build();

// Create store
const store = new Store<AppState, AppActions>({
  slices: {
    user: userSlice,
    todos: todoSlice
  }
});

// Create action creators
const actions = createActionCreators<AppActions>([
  'USER_LOGIN_START',
  'USER_LOGIN_SUCCESS',
  'USER_LOGIN_ERROR',
  'USER_LOGOUT',
  'USERS_LOAD_SUCCESS',
  'TODO_ADD',
  'TODO_TOGGLE',
  'TODO_DELETE',
  'TODOS_SET_FILTER'
]);

// Usage
store.dispatch(actions.USER_LOGIN_START());
store.dispatch(actions.USER_LOGIN_SUCCESS({
  id: '1',
  name: 'John Doe',
  email: 'john@example.com',
  status: 'active'
} as User));

store.dispatch(actions.TODO_ADD({ text: 'Learn TypeScript generics' }));

// Subscribe to state changes
const unsubscribe = store.subscribe((state) => {
  console.log('State updated:', state);
});

// Get current state
const currentState = store.getState();
console.log('Current user:', currentState.user.currentUser);
console.log('Todo count:', currentState.todos.items.length);
```

### Example 3: Generic API Client with Type-Safe Endpoints

```typescript
// HTTP method types
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'PATCH' | 'DELETE';

// Request configuration
interface RequestConfig {
  headers?: Record<string, string>;
  timeout?: number;
  retries?: number;
  cache?: boolean;
  credentials?: 'include' | 'same-origin' | 'omit';
}

// Response wrapper
interface ApiResponse<T> {
  data: T;
  status: number;
  statusText: string;
  headers: Record<string, string>;
  config: RequestConfig;
}

// Error response
interface ApiError {
  message: string;
  status: number;
  code?: string;
  details?: any;
}

// Endpoint definition
interface EndpointDefinition {
  method: HttpMethod;
  path: string;
  params?: Record<string, string>;
  query?: Record<string, any>;
  body?: any;
  response: any;
  headers?: Record<string, string>;
}

// Extract path parameters
type ExtractPathParams<T extends string> = 
  T extends `${infer Start}/:${infer Param}/${infer Rest}`
    ? { [K in Param]: string } & ExtractPathParams<`${Start}/${Rest}`>
    : T extends `${infer Start}/:${infer Param}`
    ? { [K in Param]: string }
    : {};

// Request type builder
type BuildRequestType<E extends EndpointDefinition> = {
  params: E['path'] extends string ? ExtractPathParams<E['path']> : never;
  query: E['query'] extends undefined ? {} : E['query'];
  body: E['method'] extends 'GET' | 'DELETE' ? never : E['body'];
  headers?: Record<string, string>;
  config?: RequestConfig;
};

// Generic API client
class ApiClient<TEndpoints extends Record<string, EndpointDefinition> = {}> {
  private baseURL: string;
  private defaultHeaders: Record<string, string> = {};
  private defaultConfig: RequestConfig = {};
  private interceptors: {
    request: Array<(config: any) => any>;
    response: Array<(response: any) => any>;
  } = { request: [], response: [] };

  constructor(baseURL: string, config?: RequestConfig) {
    this.baseURL = baseURL;
    this.defaultConfig = config || {};
  }

  // Configure default headers
  setDefaultHeader(name: string, value: string): void {
    this.defaultHeaders[name] = value;
  }

  removeDefaultHeader(name: string): void {
    delete this.defaultHeaders[name];
  }

  // Add interceptors
  addRequestInterceptor(interceptor: (config: any) => any): number {
    return this.interceptors.request.push(interceptor) - 1;
  }

  addResponseInterceptor(interceptor: (response: any) => any): number {
    return this.interceptors.response.push(interceptor) - 1;
  }

  // Generic request method
  async request<K extends keyof TEndpoints>(
    endpoint: K,
    params: BuildRequestType<TEndpoints[K]>
  ): Promise<ApiResponse<TEndpoints[K]['response']>> {
    const endpointDef = this.getEndpointDefinition(endpoint);
    const url = this.buildUrl(endpointDef.path, params.params);
    
    // Build request configuration
    let config: RequestInit = {
      method: endpointDef.method,
      headers: {
        'Content-Type': 'application/json',
        ...this.defaultHeaders,
        ...endpointDef.headers,
        ...params.headers
      },
      ...this.defaultConfig,
      ...params.config
    };

    // Add query parameters for GET requests
    const urlWithQuery = endpointDef.method === 'GET' && params.query
      ? `${url}?${new URLSearchParams(params.query).toString()}`
      : url;

    // Add body for non-GET requests
    if (endpointDef.method !== 'GET' && endpointDef.method !== 'DELETE' && params.body) {
      config.body = JSON.stringify(params.body);
    }

    // Apply request interceptors
    config = this.interceptors.request.reduce(
      (acc, interceptor) => interceptor(acc), 
      config
    );

    try {
      const response = await fetch(`${this.baseURL}${urlWithQuery}`, config);
      
      if (!response.ok) {
        throw new ApiError({
          message: `HTTP ${response.status}: ${response.statusText}`,
          status: response.status,
          details: await response.text()
        });
      }

      const data = await response.json();
      const apiResponse: ApiResponse<TEndpoints[K]['response']> = {
        data,
        status: response.status,
        statusText: response.statusText,
        headers: Object.fromEntries(response.headers.entries()),
        config: params.config || {}
      };

      // Apply response interceptors
      return this.interceptors.response.reduce(
        (acc, interceptor) => interceptor(acc),
        apiResponse
      );
    } catch (error) {
      if (error instanceof ApiError) {
        throw error;
      }
      throw new ApiError({
        message: error instanceof Error ? error.message : 'Unknown error',
        status: 0
      });
    }
  }

  // Convenience methods
  async get<K extends keyof TEndpoints>(
    endpoint: K,
    params: Omit<BuildRequestType<TEndpoints[K]>, 'body'>
  ): Promise<ApiResponse<TEndpoints[K]['response']>> {
    return this.request(endpoint, params as any);
  }

  async post<K extends keyof TEndpoints>(
    endpoint: K,
    params: BuildRequestType<TEndpoints[K]>
  ): Promise<ApiResponse<TEndpoints[K]['response']>> {
    return this.request(endpoint, params);
  }

  async put<K extends keyof TEndpoints>(
    endpoint: K,
    params: BuildRequestType<TEndpoints[K]>
  ): Promise<ApiResponse<TEndpoints[K]['response']>> {
    return this.request(endpoint, params);
  }

  async patch<K extends keyof TEndpoints>(
    endpoint: K,
    params: BuildRequestType<TEndpoints[K]>
  ): Promise<ApiResponse<TEndpoints[K]['response']>> {
    return this.request(endpoint, params);
  }

  async delete<K extends keyof TEndpoints>(
    endpoint: K,
    params: Omit<BuildRequestType<TEndpoints[K]>, 'body'>
  ): Promise<ApiResponse<TEndpoints[K]['response']>> {
    return this.request(endpoint, params as any);
  }

  // Helper methods
  private buildUrl(path: string, params?: Record<string, string>): string {
    if (!params) return path;
    
    return Object.entries(params).reduce(
      (url, [key, value]) => url.replace(`:${key}`, encodeURIComponent(value)),
      path
    );
  }

  private getEndpointDefinition(endpoint: keyof TEndpoints): TEndpoints[typeof endpoint] {
    // This would be implemented based on how endpoints are defined
    // For this example, we'll assume it's stored somewhere
    return {} as TEndpoints[typeof endpoint];
  }
}

// API endpoint definitions
interface MyApiEndpoints {
  getUsers: {
    method: 'GET';
    path: '/users';
    query: { page?: number; limit?: number; search?: string };
    response: PaginatedResponse<User>;
  };
  
  getUser: {
    method: 'GET';
    path: '/users/:id';
    params: { id: string };
    response: User;
  };
  
  createUser: {
    method: 'POST';
    path: '/users';
    body: Omit<User, 'id' | 'createdAt' | 'updatedAt'>;
    response: User;
  };
  
  updateUser: {
    method: 'PUT';
    path: '/users/:id';
    params: { id: string };
    body: Partial<Omit<User, 'id' | 'createdAt' | 'updatedAt'>>;
    response: User;
  };
  
  deleteUser: {
    method: 'DELETE';
    path: '/users/:id';
    params: { id: string };
    response: { success: boolean };
  };
  
  getUserPosts: {
    method: 'GET';
    path: '/users/:userId/posts';
    params: { userId: string };
    query: { published?: boolean };
    response: Post[];
  };
}

// Usage
const apiClient = new ApiClient<MyApiEndpoints>('https://api.example.com');

// Type-safe API calls
const usersResponse = await apiClient.get('getUsers', {
  params: {},
  query: { page: 1, limit: 10, search: 'john' }
});

const userResponse = await apiClient.get('getUser', {
  params: { id: '123' }
});

const newUser = await apiClient.post('createUser', {
  params: {},
  body: {
    name: 'John Doe',
    email: 'john@example.com',
    status: 'active'
  }
});

const updatedUser = await apiClient.put('updateUser', {
  params: { id: '123' },
  body: { name: 'Jane Doe' }
});

// Type-safe error handling
try {
  await apiClient.delete('deleteUser', {
    params: { id: '123' }
  });
} catch (error) {
  if (error instanceof ApiError) {
    console.error(`API Error: ${error.message} (${error.status})`);
  }
}

// Service layer with API client
class UserService {
  constructor(private apiClient: ApiClient<MyApiEndpoints>) {}

  async getAllUsers(options: { page?: number; limit?: number; search?: string } = {}) {
    const response = await this.apiClient.get('getUsers', {
      params: {},
      query: options
    });
    return response.data;
  }

  async getUserById(id: string): Promise<User> {
    const response = await this.apiClient.get('getUser', {
      params: { id }
    });
    return response.data;
  }

  async createUser(userData: Omit<User, 'id' | 'createdAt' | 'updatedAt'>): Promise<User> {
    const response = await this.apiClient.post('createUser', {
      params: {},
      body: userData
    });
    return response.data;
  }

  async updateUser(id: string, updates: Partial<Omit<User, 'id' | 'createdAt' | 'updatedAt'>>): Promise<User> {
    const response = await this.apiClient.put('updateUser', {
      params: { id },
      body: updates
    });
    return response.data;
  }

  async deleteUser(id: string): Promise<boolean> {
    const response = await this.apiClient.delete('deleteUser', {
      params: { id }
    });
    return response.data.success;
  }
}

// Additional types
interface PaginatedResponse<T> {
  data: T[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

interface Post extends Entity {
  title: string;
  content: string;
  published: boolean;
  userId: string;
}

interface Todo extends Entity {
  text: string;
  completed: boolean;
}
```

---

## 🎤 Interview Questions

### Q1: What are generics and why are they useful in TypeScript?

**Answer:**

**Generics** allow you to write reusable code that works with multiple types while preserving type safety:

```typescript
// Without generics - code duplication
function identityString(arg: string): string { return arg; }
function identityNumber(arg: number): number { return arg; }

// With generics - reusable
function identity<T>(arg: T): T { return arg; }

const str = identity("hello");  // string
const num = identity(42);       // number

// Benefits:
// 1. Type safety: errors caught at compile time
// 2. Code reuse: one implementation for multiple types  
// 3. Better IntelliSense: IDE knows exact types
// 4. Self-documenting: types show intent clearly
```

---

### Q2: How do generic constraints work with `extends`?

**Answer:**

Generic constraints limit what types can be used with `extends`:

```typescript
// Constraint ensures T has length property
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(item: T): T {
  console.log(item.length); // Safe to access length
  return item;
}

logLength("hello");     // ✅ string has length
logLength([1, 2, 3]);   // ✅ array has length  
// logLength(42);       // ❌ number doesn't have length

// Multiple constraints
interface Named { name: string; }
interface Aged { age: number; }

function greet<T extends Named & Aged>(person: T): string {
  return `Hello ${person.name}, age ${person.age}`;
}

// Keyof constraints for safe property access
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]; // Type-safe property access
}
```

---

### Q3: What's the difference between generic functions and generic classes?

**Answer:**

**Generic functions** parameterize individual function calls, while **generic classes** parameterize entire class instances:

```typescript
// Generic function - type parameter per call
function identity<T>(value: T): T { return value; }
const str = identity<string>("hello");
const num = identity<number>(42);

// Generic class - type parameter per instance
class Container<T> {
  constructor(private value: T) {}
  
  getValue(): T { return this.value; }
  setValue(value: T): void { this.value = value; }
}

const stringContainer = new Container<string>("hello");
const numberContainer = new Container<number>(42);

// stringContainer can only hold strings
// numberContainer can only hold numbers
```

---

### Q4: How do conditional types work with generics?

**Answer:**

Conditional types choose between types based on type relationships:

```typescript
// Basic conditional type
type IsArray<T> = T extends any[] ? true : false;

type StringIsArray = IsArray<string>;   // false
type ArrayIsArray = IsArray<number[]>;  // true

// Conditional with infer
type ArrayElement<T> = T extends (infer U)[] ? U : never;

type StringElement = ArrayElement<string[]>; // string
type NumberElement = ArrayElement<number[]>; // number

// Distributive conditional types
type ToArray<T> = T extends any ? T[] : never;
type UnionArrays = ToArray<string | number>; // string[] | number[]

// Real-world example: API response type
type ApiResponse<T> = T extends any[]
  ? { data: T; total: number }      // Array response has total
  : { data: T; timestamp: Date };   // Single item has timestamp

type UserListResponse = ApiResponse<User[]>;  // { data: User[]; total: number }
type UserResponse = ApiResponse<User>;        // { data: User; timestamp: Date }
```

---

### Q5: What are mapped types and how do they work with generics?

**Answer:**

Mapped types transform properties of existing types:

```typescript
// Basic mapped type
type Partial<T> = {
  [P in keyof T]?: T[P];  // Make all properties optional
};

type Required<T> = {
  [P in keyof T]-?: T[P]; // Make all properties required (remove ?)
};

// Key remapping with template literals
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

interface User {
  name: string;
  age: number;
}

type UserGetters = Getters<User>;
// { getName: () => string; getAge: () => number; }

// Conditional property mapping
type NonNullable<T> = {
  [P in keyof T]: T[P] extends null | undefined ? never : T[P];
};

// Filter properties by type
type StringKeys<T> = {
  [K in keyof T]: T[K] extends string ? K : never;
}[keyof T];

type UserStringKeys = StringKeys<User>; // "name"
```

---

### Q6: How do you handle generic variance and type relationships?

**Answer:**

**Variance** describes how generic type relationships change:

```typescript
// Covariance - preserved direction
interface Container<out T> {  // Covariant in T
  get(): T;
}

// Container<Dog> is assignable to Container<Animal> (if Dog extends Animal)

// Contravariance - reversed direction  
interface Processor<in T> {   // Contravariant in T
  process(item: T): void;
}

// Processor<Animal> is assignable to Processor<Dog>

// Invariance - no relationship (default in TypeScript)
interface Storage<T> {
  get(): T;
  set(item: T): void;
}

// Storage<Dog> is NOT assignable to Storage<Animal>

// Real-world example with function types
type EventHandler<T> = (event: T) => void;

interface MouseEvent { x: number; y: number; }
interface ClickEvent extends MouseEvent { button: number; }

// Function parameters are contravariant
const mouseHandler: EventHandler<MouseEvent> = (e) => console.log(e.x, e.y);
const clickHandler: EventHandler<ClickEvent> = mouseHandler; // ✅ OK

// Function return types are covariant  
type EventProducer<T> = () => T;
const clickProducer: EventProducer<ClickEvent> = () => ({ x: 0, y: 0, button: 1 });
const mouseProducer: EventProducer<MouseEvent> = clickProducer; // ✅ OK
```

---

## 📚 Summary

**Key Takeaways:**
- Generics enable type-safe, reusable code that works with multiple types
- Type constraints with `extends` limit generic parameters to specific shapes
- Generic functions parameterize individual calls; generic classes parameterize instances
- Default generic parameters provide fallback types when not explicitly specified
- Conditional types enable type-level programming and computed types
- Mapped types transform existing types by iterating over properties
- Variance rules govern how generic type relationships behave
- Proper generic design improves code reusability, type safety, and maintainability

---

**Next Topic:** [25. Advanced Types](./25-advanced-types.md)