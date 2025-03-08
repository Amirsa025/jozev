3To prevent "noisy" code in TypeScript (code that is cluttered, repetitive, or hard to read), focus on leveraging TypeScript's features to write clean, maintainable, and type-safe code. Below are practical strategies with examples:

---

### 1. **Use Type Inference Where Possible**
Avoid redundant type annotations when TypeScript can infer types automatically.

**Noisy Example:**
```typescript
let age: number = 25; // Redundant type annotation
const name: string = "Alice";
```

**Clean Example:**
```typescript
let age = 25; // TypeScript infers `number`
const name = "Alice"; // TypeScript infers `string`
```

---

### 2. **Use Interfaces/Types for Complex Objects**
Define reusable interfaces/types instead of repeating inline object types.

**Noisy Example:**
```typescript
function printUser(user: { id: number; name: string; age?: number }) {
  console.log(user);
}
```

**Clean Example:**
```typescript
interface User {
  id: number;
  name: string;
  age?: number;
}

function printUser(user: User) {
  console.log(user);
}
```

---

### 3. **Leverage Optional Chaining and Nullish Coalescing**
Simplify checks for `null`/`undefined` with `?.` and `??`.

**Noisy Example:**
```typescript
const street = user.address && user.address.street || "Unknown";
```

**Clean Example:**
```typescript
const street = user.address?.street ?? "Unknown";
```

---

### 4. **Use Destructuring to Reduce Repetition**
Destructure objects/arrays to avoid repetitive property access.

**Noisy Example:**
```typescript
function greetUser(user: User) {
  console.log(`Hello, ${user.name}! Age: ${user.age}`);
}
```

**Clean Example:**
```typescript
function greetUser({ name, age }: User) {
  console.log(`Hello, ${name}! Age: ${age ?? "Unknown"}`);
}
```

---

### 5. **Avoid `any` and Use Explicit Types**
Replace `any` with specific types or `unknown` for type safety.

**Noisy Example:**
```typescript
function parseData(data: any) { // Unsafe
  return JSON.parse(data);
}
```

**Clean Example:**
```typescript
function parseData<T>(data: string): T { // Generic type
  return JSON.parse(data) as T;
}
```

---

### 6. **Use Enums or Const Objects for Magic Values**
Replace magic strings/numbers with enums or constants.

**Noisy Example:**
```typescript
if (status === "active") { // Magic string
  // ...
}
```

**Clean Example:**
```typescript
enum UserStatus {
  Active = "active",
  Inactive = "inactive",
}

if (status === UserStatus.Active) {
  // ...
}
```

---

### 7. **Use Generics for Reusable Logic**
Avoid duplicating code with generics when logic is reusable.

**Noisy Example:**
```typescript
function getFirstString(arr: string[]) {
  return arr[0];
}

function getFirstNumber(arr: number[]) {
  return arr[0];
}
```

**Clean Example:**
```typescript
function getFirst<T>(arr: T[]): T | undefined {
  return arr[0];
}
```

---

### 8. **Modularize Code**
Split code into small, focused functions/files.

**Noisy Example:**
```typescript
// A single large function
function processUser(user: User) {
  // ...20 lines of code...
}
```

**Clean Example:**
```typescript
// Split into smaller functions
function validateUser(user: User) { /* ... */ }
function saveUser(user: User) { /* ... */ }
function processUser(user: User) {
  validateUser(user);
  saveUser(user);
}
```

---

### 9. **Use Type Guards for Type Narrowing**
Avoid type assertions (`as`) and use type guards instead.

**Noisy Example:**
```typescript
function printId(id: string | number) {
  console.log(id.toUpperCase()); // Error: `number` doesn't have `toUpperCase`
}
```

**Clean Example:**
```typescript
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // Type narrowed to `string`
  } else {
    console.log(id);
  }
}
```

---

### 10. **Use Utility Types (e.g., `Partial`, `Pick`, `Omit`)**
Reduce boilerplate with TypeScript's built-in utility types.

**Noisy Example:**
```typescript
interface UserUpdate {
  id?: number;
  name?: string;
  age?: number;
}
```

**Clean Example:**
```typescript
type UserUpdate = Partial<User>;
```

---

### Final Example: Combining Techniques
**Before (Noisy):**
```typescript
function updateUser(oldUser: User, newData: any) {
  if (newData && newData.name && newData.age) {
    oldUser.name = newData.name;
    oldUser.age = newData.age;
  }
}
```

**After (Clean):**
```typescript
interface User {
  id: number;
  name: string;
  age?: number;
}

type UserUpdate = Partial<User>;

function updateUser(oldUser: User, newData: UserUpdate) {
  if (newData?.name && newData.age) {
    return { ...oldUser, ...newData };
  }
  return oldUser;
}
```

---

### Key Takeaways:
1. Let TypeScript infer types where possible.
2. Use interfaces/types to avoid repetition.
3. Simplify null/undefined checks with `?.` and `??`.
4. Destructure objects to reduce verbosity.
5. Avoid `any` and use generics/utility types for flexibility.
6. Split code into modular functions/files.

By following these practices, you’ll write cleaner, more maintainable TypeScript code.