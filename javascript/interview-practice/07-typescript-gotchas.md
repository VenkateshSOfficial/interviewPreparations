# Practice: TypeScript-Specific Gotchas

---

### Q1. Find the bug

```ts
interface User {
  name: string;
  age: number;
}

function printUser(user: User) {
  console.log(user.name, user.age);
}

printUser({ name: "Ada" });
```

<details>
<summary>Answer</summary>

Compile error: `Property 'age' is missing in type '{ name: string; }' but required in type 'User'.`

All non-optional properties of an interface must be present when constructing an object of that type. Fix by supplying `age`, or marking it optional: `age?: number`.
</details>

---

### Q2. What's wrong with this generic function?

```ts
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

const items: number[] = [];
const first = getFirst(items);
console.log(first.toFixed(2));
```

<details>
<summary>Answer</summary>

Compiles fine (no TS error) but throws at runtime: `Cannot read properties of undefined (reading 'toFixed')`.

TypeScript's type system doesn't know the array could be empty — `arr[0]` is typed as `T`, not `T | undefined`, even though at runtime it's `undefined` for an empty array. This is a known TS limitation unless `noUncheckedIndexedAccess` is enabled in `tsconfig.json`, which would correctly type `arr[0]` as `T | undefined`.
</details>

---

### Q3. Find the bug — `any` defeats type safety

```ts
function processResponse(response: any) {
  return response.data.items.length;
}

const result = processResponse({ data: null });
```

<details>
<summary>Answer</summary>

Throws at runtime: `Cannot read properties of null (reading 'items')`.

`any` disables all type checking, so TypeScript won't catch that `data` could be `null`. Using a proper type/interface (or `unknown` with explicit narrowing) would have forced a null check at compile time.

```ts
interface ApiResponse {
  data: { items: unknown[] } | null;
}
function processResponse(response: ApiResponse) {
  return response.data?.items.length ?? 0;
}
```
</details>

---

### Q4. What does this print, and what's the TS-specific issue?

```ts
enum Status {
  Active,
  Inactive
}

function getLabel(status: Status): string {
  if (status === Status.Active) return "Active";
  if (status === Status.Inactive) return "Inactive";
  return "Unknown";
}

console.log(Status.Active);        // ?
console.log(getLabel(Status.Active)); // ?
console.log(Status.Active === 0);  // ?
```

<details>
<summary>Answer</summary>

```
0
Active
true
```

Numeric enums compile to plain numbers under the hood, and members auto-increment starting at 0 unless explicitly assigned. This means enum values leak as raw numbers at runtime, and mixing raw numbers with enum members (`Status.Active === 0`) type-checks and works — a common source of confusion for candidates who assume enums are more "type-safe" than they actually are for numeric enums. (String enums avoid this ambiguity.)
</details>

---

### Q5. Find the bug — type assertion hides a real error

```ts
interface Product {
  id: number;
  price: number;
}

const data = JSON.parse('{"id": 1}') as Product;
console.log(data.price.toFixed(2));
```

<details>
<summary>Answer</summary>

Compiles without error (because `as Product` forces TS to trust you), but throws at runtime: `Cannot read properties of undefined (reading 'toFixed')` since the parsed JSON has no `price` field.

`as` type assertions bypass structural checks entirely — they tell the compiler "trust me", but don't validate the shape at runtime. For data coming from `JSON.parse`/APIs, use runtime validation (e.g., Zod, io-ts, or manual checks) instead of blind assertions.
</details>

---

### Q6. Predict the compile-time behavior — structural typing gotcha

```ts
interface Point2D {
  x: number;
  y: number;
}

interface Point3D {
  x: number;
  y: number;
  z: number;
}

function printPoint(point: Point2D) {
  console.log(point.x, point.y);
}

const point3D: Point3D = { x: 1, y: 2, z: 3 };
printPoint(point3D); // ?

const literal = { x: 1, y: 2, z: 3 };
printPoint({ x: 1, y: 2, z: 3 }); // ?
```

<details>
<summary>Answer</summary>

`printPoint(point3D)` compiles fine — TypeScript uses **structural typing**, so any object with at least the required `x`/`y` properties is assignable, extra properties on a variable are allowed.

`printPoint({ x: 1, y: 2, z: 3 })` (an **object literal passed directly**) fails with `Object literal may only specify known properties, and 'z' does not exist in type 'Point2D'.` — this is TypeScript's **excess property check**, which only applies to fresh object literals, not to variables that happen to satisfy the shape. This asymmetry is a classic TS interview gotcha.
</details>

---

### Q7. Find the bug — optional chaining vs strict null checks

```ts
interface Config {
  retries?: number;
}

function getRetries(config: Config): number {
  return config.retries + 1;
}
```

<details>
<summary>Answer</summary>

Compile error (with `strictNullChecks` on): `Object is possibly 'undefined'.`

`retries?: number` means the type is actually `number | undefined`. Adding `+ 1` to a possibly-`undefined` value is unsafe.

**Fix:**
```ts
return (config.retries ?? 0) + 1;
```
</details>
