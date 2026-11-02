# TypeScript

TypeScript là ngôn ngữ mã nguồn mở phát triển bởi Microsoft, là một siêu tập cú pháp nghiêm ngặt (strict syntactical superset) của JavaScript, bổ sung hệ thống kiểu tĩnh (static typing) và kiểm tra lỗi ngay trong giai đoạn biên dịch (compile-time).

Mã TypeScript được biên dịch (transpile) thành JavaScript thuần để thực thi trên trình duyệt hoặc Node.js. Hầu hết các định nghĩa kiểu sẽ bị loại bỏ hoàn toàn (type erasure) sau khi biên dịch, do đó dữ liệu runtime từ bên ngoài (như API) vẫn cần được kiểm tra hoặc xác thực khi cần thiết.

---

## 1. Basic Types — các kiểu dữ liệu cơ bản

TypeScript hỗ trợ toàn bộ các kiểu dữ liệu nguyên thủy của JavaScript cùng với các kiểu đặc biệt riêng.

### Kiểu nguyên thủy (Primitive Types)

```ts
let isDone: boolean = false;
let total: number = 100;
let userName: string = "Alice";
let n: null = null;
let u: undefined = undefined;
let big: bigint = 100n;
let sym: symbol = Symbol("key");
```

### Kiểu đặc biệt: `any`, `unknown`, `void`, `never`

| Kiểu | Ý nghĩa | Hành vi an toàn |
| ---- | ------- | --------------- |
| `any` | Tắt hoàn toàn kiểm tra kiểu của TypeScript cho biến này. | Không an toàn, nên hạn chế tối đa. |
| `unknown` | Biểu thị kiểu chưa biết; tương tự `any` nhưng **buộc phải kiểm tra kiểu** (type check/narrowing) trước khi thao tác. | Thay thế an toàn cho `any`. |
| `void` | Thường dùng cho kiểu trả về của hàm không có giá trị hữu ích. | Cho phép trả về `undefined`. |
| `never` | Biểu thị giá trị không bao giờ xảy ra (hàm luôn quăng lỗi hoặc lặp vô tận). | Dùng trong kiểm tra kiệt cùng (Exhaustiveness checking). |

```ts
// any vs unknown
let valueAny: any = "Hello";
valueAny.toUpperCase(); // Hợp lệ nhưng tiềm ẩn nguy cơ lỗi runtime nếu đổi giá trị

let valueUnknown: unknown = "Hello";
// valueUnknown.toUpperCase(); // Lỗi: Object is of type 'unknown'
if (typeof valueUnknown === "string") {
  console.log(valueUnknown.toUpperCase()); // Hợp lệ sau khi thu hẹp kiểu
}

// void vs never
function logMessage(msg: string): void {
  console.log(msg);
}

function throwFatalError(message: string): never {
  throw new Error(message);
}
```

### Arrays và Tuples

- **Array (`Type[]` hoặc `Array<Type>`)**: Danh sách độ dài động chứa các phần tử có cùng một hoặc nhiều kiểu.
- **Tuple (`[Type1, Type2, ...]`)**: Mảng có số lượng phần tử cố định và mỗi vị trí có một kiểu dữ liệu xác định.

```ts
// Array
const numbers: number[] = [1, 2, 3];
numbers.push(4);      // Hợp lệ
// numbers.push("5"); // Lỗi: string không thể gán vào number[]

// Tuple
let userTuple: [number, string, boolean];
userTuple = [1, "Alice", true]; // Hợp lệ
// userTuple = ["1", "Alice", true]; // Lỗi kiểu tại vị trí 0

// Tuple với thuộc tính tùy chọn hoặc rest
let optionalTuple: [string, number?];
optionalTuple = ["Bob"]; // Hợp lệ
```

---

## 2. Type Inference & Type Annotations — suy luận và chú thích kiểu

### Type Annotations (Chú thích kiểu tường minh)

Khai báo trực tiếp kiểu bằng cú pháp `: Type`. Thường dùng cho tham số hàm, thuộc tính object hoặc khi biến chưa gán giá trị ngay.

### Type Inference (Suy luận kiểu tự động)

TypeScript có khả năng tự động suy luận kiểu dữ liệu dựa vào giá trị khởi tạo ban đầu hoặc giá trị trả về của hàm mà không cần ghi thủ công.

```ts
let city = "Hà Nội";     // Tự suy luận là string
let population = 8000000; // Tự suy luận là number
// city = 100;           // Lỗi: Type 'number' is not assignable to type 'string'

// Suy luận kiểu trả về của hàm
function addNumbers(a: number, b: number) {
  return a + b; // Tự suy luận kiểu trả về là number
}

// Suy luận theo ngữ cảnh (Contextual Typing)
const names = ["Alice", "Bob", "Charlie"];
names.forEach((name) => {
  console.log(name.toUpperCase()); // 'name' tự suy luận là string
});
```

> **Quy tắc:** Nên để TypeScript tự suy luận kiểu cho các biến cục bộ đơn giản. Ghi chú thích rõ ràng cho tham số hàm, API return types và các cấu trúc dữ liệu phức tạp.

---

## 3. Literal Types & Union / Intersection Types

### Literal Types

Literal Type giới hạn biến chỉ được nhận một hoặc một tập các giá trị cụ thể, thay vì nhận mọi giá trị của kiểu rộng hơn.

```ts
type Direction = "north" | "south" | "east" | "west";
let moveDir: Direction = "north";
// moveDir = "up"; // Lỗi: Type '"up"' is not assignable to type 'Direction'

type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
let roll: DiceRoll = 4;
```

### Union Types (`|`)

Cho phép một giá trị có thể thuộc một trong nhiều kiểu được chỉ định.

```ts
function printId(id: number | string) {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id.toFixed(0));
  }
}
printId(101);
printId("usr_101");
```

### Intersection Types (`&`)

Kết hợp nhiều kiểu lại với nhau; đối tượng phải thỏa mãn tất cả các thuộc tính của các kiểu thành phần.

```ts
type Person = {
  name: string;
};

type Employee = {
  employeeId: number;
};

type StaffMember = Person & Employee;

const staff: StaffMember = {
  name: "Dũng",
  employeeId: 9901
};
```

### Template Literal Types

Kết hợp các kiểu literal thành chuỗi định dạng bằng cú pháp backtick:

```ts
type EventName = "click" | "hover";
type EventHandler = `on${Capitalize<EventName>}`; // "onClick" | "onHover"

type HttpMethod = "GET" | "POST";
type Endpoint = "/users" | "/posts";
type ApiRoute = `${HttpMethod} ${Endpoint}`; // "GET /users" | "GET /posts" | "POST /users" | "POST /posts"
```

---

## 4. Objects & Index Signatures

### Cấu trúc Object

```ts
type User = {
  readonly id: number;   // Không thể thay đổi sau khi khởi tạo
  name: string;
  age?: number;          // Thuộc tính tùy chọn (Optional)
};

const user: User = {
  id: 1,
  name: "Ngọc"
};
// user.id = 2; // Lỗi: Cannot assign to 'id' because it is a read-only property
```

### Index Signatures — thuộc tính với key động

Dùng khi không biết trước danh sách tên thuộc tính nhưng biết quy luật kiểu của key và value.

```ts
interface StringDictionary {
  [key: string]: string;
}

const colors: StringDictionary = {
  primary: "#007bff",
  secondary: "#6c757d",
  danger: "#dc3545"
};

// Kết hợp thuộc tính cố định và Index Signature
interface UserDatabase {
  totalUsers: number;
  [username: string]: number | string; // Các thuộc tính khác phải tương thích kiểu này
}
```

---

## 5. Type Aliases và Interfaces

Cả `type` và `interface` đều được dùng để định nghĩa cấu trúc dữ liệu.

### Type Alias (`type`)

Định danh tên cho bất kỳ kiểu dữ liệu nào (Primitive, Union, Tuple, Object, Function, ...).

```ts
type ID = string | number;
type Point = { x: number; y: number };
type Callback = (data: string) => void;
```

### Interface (`interface`)

Chuyên dùng để mô tả hình dạng (shape) của đối tượng hoặc class.

```ts
interface Rectangle {
  width: number;
  height: number;
}

// Mở rộng bằng extends
interface ColoredRectangle extends Rectangle {
  color: string;
}

const rect: ColoredRectangle = {
  width: 10,
  height: 20,
  color: "blue"
};
```

### Declaration Merging (Chỉ có ở `interface`)

Nhiều khai báo `interface` cùng tên trong cùng scope sẽ tự động được gộp lại với nhau (hữu ích khi mở rộng thư viện bên thứ 3).

```ts
interface Product {
  name: string;
  price: number;
}

interface Product {
  description: string;
}

// Lúc này Product có cả 3 thuộc tính: name, price, description
const p: Product = {
  name: "Bàn phím",
  price: 500000,
  description: "Bàn phím cơ"
};
```

### Bảng so sánh `type` vs `interface`

| Tiêu chí | `interface` | `type` |
| -------- | ----------- | ------ |
| **Mục đích chính** | Mô tả cấu trúc của Object, Class | Đặt bí danh cho mọi loại kiểu dữ liệu |
| **Kiểu nguyên thủy / Union / Tuple** | Không hỗ trợ trực tiếp | Hỗ trợ đầy đủ (`type ID = string \| number`) |
| **Kế thừa / Mở rộng** | Dùng từ khóa `extends` | Dùng Intersection `&` |
| **Declaration Merging** | Có (tự động gộp khi khai báo trùng tên) | Không thể trùng tên (`Duplicate identifier`) |
| **Thực thi trong Class** | Dùng với `implements` | Dùng được với `implements` nếu là object type |

> **Khuyến nghị:** Dùng `interface` cho cấu trúc dữ liệu đối tượng, model trong kiến trúc hướng đối tượng; dùng `type` cho Union, Tuple, Function signatures và các phép biến đổi kiểu phức tạp.

---

## 6. Enums

`enum` cho phép định nghĩa một tập hợp các hằng số được đặt tên.

### Numeric Enum

Mặc định tự động đánh số tăng dần từ 0, hoặc có thể gán giá trị tùy chỉnh.

```ts
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right  // 3
}

enum HttpStatus {
  OK = 200,
  BadRequest = 400,
  Unauthorized = 401,
  NotFound = 404
}
```

### String Enum

Gán rõ ràng giá trị chuỗi cho từng thành viên, giúp log và debug dễ đọc hơn.

```ts
enum Role {
  Admin = "ADMIN",
  User = "USER",
  Guest = "GUEST"
}

const currentRole: Role = Role.Admin;
```

### So sánh `enum` với Union String Literals

| Tiêu chí | `enum` | Union String Literals (`"A" \| "B"`) |
| -------- | ------ | ----------------------------------- |
| **Mã JavaScript sau biên dịch** | Tạo ra một Object thật sự trong runtime | Hoàn toàn biến mất (0 byte overhead) |
| **Tự động gợi ý IDE** | Tốt (`Role.Admin`) | Rất tốt (autocomplete chuỗi trực tiếp) |
| **Tính linh hoạt** | Cần import Enum để dùng | Không cần import, truyền thẳng chuỗi |

> **Thực tiễn hiện đại:** Trong các dự án TypeScript mới, cộng đồng ưu tiên sử dụng **Union String Literals** hoặc object với `as const` thay vì `enum` truyền thống để giảm kích thước bundle.

---

## 7. Functions — hàm

### Định nghĩa kiểu tham số và kiểu trả về

```ts
function multiply(x: number, y: number): number {
  return x * y;
}

// Arrow function với kiểu
const divide = (x: number, y: number): number => x / y;
```

### Tham số tùy chọn, mặc định và Rest parameters

```ts
// Tham số tùy chọn (c?) và giá trị mặc định (base = 10)
function calculate(amount: number, discount?: number, base: number = 0): number {
  return base + amount - (discount ?? 0);
}

// Rest parameters
function sumAll(...numbers: number[]): number {
  return numbers.reduce((acc, n) => acc + n, 0);
}
```

### Function Type Alias

```ts
type MathOperation = (a: number, b: number) => number;

const add: MathOperation = (a, b) => a + b;
const subtract: MathOperation = (a, b) => a - b;
```

### Function Overloading (Nạp chồng hàm)

Cho phép một hàm có nhiều chữ ký gọi khác nhau với kiểu tham số và kiểu trả về khác nhau, nhưng chỉ có một hàm thực thi (implementation) duy nhất.

```ts
// Các signatures khai báo
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;

// Implementation signature (chứa toàn bộ logic)
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp - 1, d);
  } else {
    return new Date(mOrTimestamp);
  }
}

const d1 = makeDate(12345678);
const d2 = makeDate(5, 12, 2026);
// const d3 = makeDate(5, 12); // Lỗi: Không có overload nhận 2 tham số
```

---

## 8. Type Narrowing & Type Guards — thu hẹp kiểu

Type Guard là các phép kiểm tra logic lúc runtime giúp trình biên dịch TypeScript thu hẹp kiểu của một biến sang kiểu cụ thể hơn trong một khối lệnh.

### Các cơ chế Type Guard tiêu chuẩn

| Cơ chế | Cú pháp | Áp dụng cho |
| ------ | ------- | ----------- |
| `typeof` | `typeof val === "string"` | Các kiểu nguyên thủy (`string`, `number`, `boolean`, `symbol`, `bigint`, `undefined`) |
| `instanceof` | `err instanceof Error` | Các Class, Constructor hoặc đối tượng dựng sẵn |
| `in` | `"swim" in animal` | Kiểm tra sự tồn tại của thuộc tính trong đối tượng |
| Truthiness | `if (value) { ... }` | Lọc bỏ `null`, `undefined`, `""`, `0`, `false` |
| Equality | `if (x === y)` | So sánh bằng để thu hẹp union |

```ts
// 1. typeof
function formatValue(value: string | number) {
  if (typeof value === "string") {
    return value.trim();
  }
  return value.toFixed(2);
}

// 2. instanceof
function handleError(error: unknown) {
  if (error instanceof Error) {
    console.error(error.message);
  } else {
    console.error("Lỗi không xác định:", String(error));
  }
}

// 3. in
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(creature: Fish | Bird) {
  if ("swim" in creature) {
    creature.swim();
  } else {
    creature.fly();
  }
}
```

### Discriminated Unions (Union phân biệt)

Tạo một thuộc tính nhãn chung (thường là `kind`, `type`, hoặc `status`) có kiểu literal để TypeScript tự động phân biệt nhánh.

```ts
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; sideLength: number }
  | { kind: "rectangle"; width: number; height: number };

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    case "rectangle":
      return shape.width * shape.height;
    default:
      // Exhaustiveness check: nếu thêm shape mới mà quên case, dòng này sẽ báo lỗi lúc compile
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```

### User-defined Type Guard với từ khóa `is`

Định nghĩa hàm kiểm tra kiểu tùy chỉnh với kiểu trả về là `paramName is TargetType`.

```ts
interface AdminUser {
  name: string;
  permissions: string[];
}

function isAdmin(user: unknown): user is AdminUser {
  return (
    typeof user === "object" &&
    user !== null &&
    "permissions" in user &&
    Array.isArray((user as any).permissions)
  );
}

function processUser(user: unknown) {
  if (isAdmin(user)) {
    console.log("Quyền hạn:", user.permissions.join(", ")); // TypeScript nhận biết user là AdminUser
  }
}
```

### Assertion Functions với từ khóa `asserts`

Báo cho TypeScript biết nếu hàm không quăng lỗi thì biến đầu vào chắc chắn thuộc kiểu chỉ định.

```ts
function assertIsNonNullable<T>(val: T): asserts val is NonNullable<T> {
  if (val === null || val === undefined) {
    throw new Error(`Giá trị mong đợi không được là null hoặc undefined`);
  }
}

let maybeString: string | null = "Antigravity";
assertIsNonNullable(maybeString);
console.log(maybeString.toUpperCase()); // Hợp lệ, maybeString đã được khẳng định là string
```

---

## 9. Type Assertions & Special Operators

### Type Assertion (`as`)

Báo cho trình biên dịch biết lập trình viên nắm rõ kiểu dữ liệu hơn TypeScript. Không làm thay đổi dữ liệu runtime.

```ts
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
```

### `as const` (Const Assertions)

Khóa toàn bộ kiểu của literal object/array thành dạng `readonly` và giữ nguyên kiểu literal chính xác nhất.

```ts
const config = {
  endpoint: "https://api.domain.com",
  timeout: 5000,
  methods: ["GET", "POST"]
} as const;

// Kiểu của config:
// {
//   readonly endpoint: "https://api.domain.com";
//   readonly timeout: 5000;
//   readonly methods: readonly ["GET", "POST"];
// }
// config.timeout = 10000; // Lỗi: Cannot assign to 'timeout' because it is a read-only property
```

### Toán tử `satisfies` (TypeScript 4.9+)

Kiểm tra một giá trị có khớp với một kiểu nhất định hay không **mà không làm mất đi kiểu suy luận cụ thể ban đầu** của biến.

```ts
type Palette = Record<string, string | [number, number, number]>;

const palette = {
  red: "#ff0000",
  green: [0, 255, 0]
} satisfies Palette;

// Vừa đảm bảo đúng cấu trúc Palette, vừa giữ nguyên kiểu chuỗi cho 'red' và tuple cho 'green':
palette.red.toUpperCase();     // Hợp lệ! (Nếu dùng : Palette thì red có kiểu string | number[] và dòng này lỗi)
palette.green.map(n => n * 2); // Hợp lệ!
```

### Toán tử `keyof` và `typeof` trong Type Context

- `typeof val`: Lấy kiểu dữ liệu từ một biến hoặc đối tượng JavaScript có sẵn.
- `keyof Type`: Lấy union tập hợp tất cả các key hợp lệ của một kiểu Object.

```ts
const point = { x: 10, y: 20 };
type PointType = typeof point; // { x: number; y: number }

type PointKeys = keyof PointType; // "x" | "y"
```

### Indexed Access Types (`T[K]`)

Truy cập kiểu của một thuộc tính cụ thể bên trong kiểu khác:

```ts
interface Car {
  make: string;
  model: string;
  year: number;
}

type CarYear = Car["year"]; // number
```

---

## 10. Generics — lập trình tổng quát

Generics cho phép tạo ra các hàm, interface, class có thể tái sử dụng với nhiều kiểu dữ liệu khác nhau mà vẫn đảm bảo an toàn kiểu (Type Safety).

### Generic Functions

```ts
function getFirstElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

const n = getFirstElement([1, 2, 3]);       // T tự suy luận là number, n có kiểu number | undefined
const s = getFirstElement(["a", "b", "c"]); // T tự suy luận là string
```

### Generic Interfaces & Types

```ts
interface ApiResponse<DataPayload> {
  statusCode: number;
  message: string;
  data: DataPayload;
}

interface UserProfile {
  id: number;
  email: string;
}

const response: ApiResponse<UserProfile> = {
  statusCode: 200,
  message: "Success",
  data: { id: 10, email: "user@example.com" }
};
```

### Generic Constraints (`extends`)

Giới hạn kiểu `T` phải đáp ứng một cấu trúc nhất định.

```ts
interface HasLength {
  length: number;
}

function logLength<T extends HasLength>(item: T): void {
  console.log("Độ dài:", item.length);
}

logLength("Hello");     // Hợp lệ (chuỗi có thuộc tính length)
logLength([1, 2, 3]);   // Hợp lệ (mảng có thuộc tính length)
// logLength(123);      // Lỗi: number không có thuộc tính length
```

### Kết hợp `keyof` và Generics

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "Thành", age: 30 };
const personName = getProperty(person, "name"); // string
const personAge = getProperty(person, "age");   // number
// getProperty(person, "gender"); // Lỗi: Argument of type '"gender"' is not assignable to "name" | "age"
```

---

## 11. Built-in Utility Types — các kiểu tiện ích có sẵn

TypeScript cung cấp sẵn bộ Utility Types hữu ích để chuyển đổi và thao tác trên các kiểu dữ liệu có sẵn.

| Utility Type | Cú pháp | Tác dụng |
| ------------ | ------- | -------- |
| `Partial<T>` | `Partial<T>` | Biến tất cả thuộc tính của `T` thành **tùy chọn (`?`)**. |
| `Required<T>` | `Required<T>` | Biến tất cả thuộc tính của `T` thành **bắt buộc** (bỏ `?`). |
| `Readonly<T>` | `Readonly<T>` | Đặt tất cả thuộc tính của `T` thành **chỉ đọc (`readonly`)**. |
| `Record<K, T>` | `Record<string, number>` | Tạo object type có key thuộc `K` và value thuộc `T`. |
| `Pick<T, K>` | `Pick<T, "id" \| "name">` | Trích xuất chỉ các thuộc tính trong danh sách `K` từ `T`. |
| `Omit<T, K>` | `Omit<T, "password">` | Loại bỏ các thuộc tính trong danh sách `K` ra khỏi `T`. |
| `Exclude<T, U>` | `Exclude<"a" \| "b", "a">` | Loại bỏ khỏi union `T` những kiểu có thể gán cho `U`. |
| `Extract<T, U>` | `Extract<"a" \| "b", "a" \| "c">` | Trích xuất từ union `T` những kiểu có trong `U`. |
| `NonNullable<T>` | `NonNullable<string \| null>` | Loại bỏ `null` và `undefined` khỏi kiểu `T`. |
| `ReturnType<T>` | `ReturnType<typeof myFunc>` | Lấy kiểu dữ liệu trả về của một hàm. |
| `Parameters<T>` | `Parameters<typeof myFunc>` | Lấy tuple các kiểu tham số của một hàm. |
| `Awaited<T>` | `Awaited<Promise<string>>` | Mở gói Promise hoặc đệ quy Unwrap Promise để lấy kiểu giá trị bên trong (`string`). |

### Ví dụ sử dụng Utility Types

```ts
interface Article {
  id: number;
  title: string;
  content: string;
  published: boolean;
}

// 1. Partial: cập nhật một phần thuộc tính
function updateArticle(id: number, fields: Partial<Article>) {
  // fields có thể chỉ gồm { title: "Tiêu đề mới" }
}

// 2. Pick & Omit
type ArticleSummary = Pick<Article, "id" | "title">;
type ArticleCreateInput = Omit<Article, "id">;

// 3. Record
const articlesById: Record<number, Article> = {
  101: { id: 101, title: "TS Guide", content: "...", published: true }
};

// 4. ReturnType & Awaited
async function fetchPost() {
  return { id: 1, title: "Post 1" };
}
type AsyncPostResult = Awaited<ReturnType<typeof fetchPost>>; // { id: number, title: string }
```

---

## 12. Classes & OOP trong TypeScript

TypeScript bổ sung bộ kiểm soát truy cập (Access Modifiers) và tính năng hướng đối tượng mạnh mẽ cho cú pháp Class của JavaScript.

### Access Modifiers

- `public` (mặc định): Có thể truy cập ở bất cứ đâu.
- `protected`: Chỉ có thể truy cập bên trong class hiện tại và các class con kế thừa nó.
- `private`: Chỉ có thể truy cập bên trong chính class định nghĩa nó.
- `readonly`: Thuộc tính chỉ được gán trong phần khai báo hoặc trong `constructor`.

### Parameter Properties (Cú pháp khai báo nhanh)

Khai báo Access Modifier ngay trong tham số của `constructor` sẽ tự động tạo và gán giá trị cho thuộc tính tương ứng:

```ts
class BankAccount {
  // Cú pháp ngắn gọn: tự tạo thuộc tính balance (private) và accountNumber (readonly public)
  constructor(
    public readonly accountNumber: string,
    private balance: number
  ) {}

  public deposit(amount: number): void {
    if (amount > 0) {
      this.balance += amount;
    }
  }

  public getBalance(): number {
    return this.balance;
  }
}

const account = new BankAccount("VN123456", 1000);
account.deposit(500);
console.log(account.getBalance()); // 1500
// console.log(account.balance);    // Lỗi: Property 'balance' is private
```

### Abstract Classes & Interface Implementation

```ts
interface Identifiable {
  getId(): string;
}

abstract class BaseRepository<T extends Identifiable> {
  abstract save(entity: T): Promise<void>;

  public logEntity(entity: T): void {
    console.log(`[Repository] Entity ID: ${entity.getId()}`);
  }
}

class UserRepository extends BaseRepository<{ getId(): string; name: string }> {
  async save(user: { getId(): string; name: string }): Promise<void> {
    console.log(`Lưu người dùng: ${user.name}`);
  }
}
```

---

## 13. Advanced Types — các kiểu nâng cao

### Conditional Types (Kiểu có điều kiện)

Cú pháp: `T extends U ? X : Y`

```ts
type IsString<T> = T extends string ? "yes" : "no";

type A = IsString<string>; // "yes"
type B = IsString<number>; // "no"

// Phân giải kiểu của mảng
type ElementType<T> = T extends (infer U)[] ? U : T;
type NumberArrElement = ElementType<number[]>; // number
type SingleString = ElementType<string>;        // string
```

### Mapped Types (Ánh xạ kiểu)

Tạo một kiểu mới bằng cách duyệt qua các thuộc tính của một kiểu có sẵn:

```ts
type FeatureFlags = {
  darkMode: () => void;
  betaAccess: () => void;
};

// Biến tất cả phương thức thành giá trị boolean
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean;
};

type FeatureOptions = OptionsFlags<FeatureFlags>;
// Kết quả:
// {
//   darkMode: boolean;
//   betaAccess: boolean;
// }
```

---

## 14. Asynchronous TypeScript — lập trình bất đồng bộ

### Kiểu dữ liệu của `Promise`

Hàm bất đồng bộ (`async`) luôn trả về một `Promise<T>`:

```ts
interface Post {
  id: number;
  title: string;
  views: number;
}

async function getPostById(id: number): Promise<Post> {
  const response = await fetch(`https://api.example.com/posts/${id}`);
  if (!response.ok) {
    throw new Error(`HTTP Error ${response.status}`);
  }
  const post: Post = await response.json();
  return post;
}
```

### Xử lý lỗi an toàn trong khối `catch`

Trong TypeScript (với cờ `useUnknownInCatchVariables: true`, mặc định bật trong `strict`), biến `error` trong khối `catch` có kiểu `unknown`. Cần kiểm tra kiểu trước khi đọc thuộc tính:

```ts
async function run() {
  try {
    const post = await getPostById(42);
    console.log("Tiêu đề:", post.title);
  } catch (error: unknown) {
    if (error instanceof Error) {
      console.error("Lỗi:", error.message);
    } else {
      console.error("Lỗi ngoại lệ không xác định:", error);
    }
  }
}
```

---

## 15. Thiết lập dự án Node.js với TypeScript & `tsconfig.json`

### Các bước khởi tạo dự án

```bash
# 1. Tạo thư mục và khởi tạo package.json
mkdir ts-backend-app
cd ts-backend-app
npm init -y

# 2. Cài đặt TypeScript và type definitions cho Node.js
npm install -D typescript @types/node ts-node

# 3. Tạo file cấu hình tsconfig.json
npx tsc --init
```

### Cấu hình `tsconfig.json` chuẩn sản xuất

```json
{
  "compilerOptions": {
    /* Mục tiêu & Module */
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "rootDir": "./src",
    "outDir": "./dist",

    /* Kiểm tra kiểu nghiêm ngặt (Strict Type-Checking) */
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,

    /* Tương thích Module & Thư viện */
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Bảng giải thích các tùy chọn quan trọng trong `tsconfig.json`

| Tùy chọn | Mục đích |
| -------- | -------- |
| `target` | Phiên bản ECMAScript đầu ra của JavaScript sau khi biên dịch (`ES6`, `ES2020`, `ES2022`, ...). |
| `module` | Hệ thống module đầu ra (`commonjs`, `ESNext`, `NodeNext`). |
| `moduleResolution` | Chiến lược tìm kiếm và giải quyết đường dẫn import (`node`, `NodeNext`, `bundler`). |
| `rootDir` | Thư mục chứa mã nguồn TypeScript gốc (thường là `src`). |
| `outDir` | Thư mục chứa mã JavaScript sau khi biên dịch (thường là `dist` hoặc `build`). |
| `strict` | Bật toàn bộ các tùy chọn kiểm tra kiểu nghiêm ngặt nhất của TypeScript. |
| `esModuleInterop` | Cho phép import các module CommonJS bằng cú pháp ES Module chuẩn (`import fs from 'fs'`). |
| `skipLibCheck` | Bỏ qua kiểm tra kiểu các file `.d.ts` trong thư viện bên thứ 3, giúp tăng tốc độ biên dịch. |
| `sourceMap` | Tạo file `.map` để ánh xạ mã JavaScript đã build ngược về file TypeScript khi debug. |

### Cấu trúc thư mục chuẩn dự án

```text
ts-backend-app/
├── src/
│   ├── index.ts
│   ├── controllers/
│   ├── services/
│   ├── models/
│   └── types/
├── dist/             # JavaScript sau khi chạy 'npx tsc'
├── node_modules/
├── package.json
└── tsconfig.json
```

### Thao tác biên dịch và thực thi

- Biên dịch TypeScript thành JavaScript:
  ```bash
  npx tsc
  ```
- Chạy trực tiếp file TypeScript trong lúc phát triển mà không cần build thủ công:
  ```bash
  npx ts-node src/index.ts
  ```
