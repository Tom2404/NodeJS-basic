# JavaScript

JavaScript là ngôn ngữ lập trình kịch bản động, hướng đối tượng dựa trên prototype, chạy đơn luồng (single-threaded) với cơ chế Event Loop, được sử dụng phổ biến trong phát triển Web (Front-end và Back-end).

---

## 1. Where to write JavaScript — vị trí đặt mã

Trong HTML, JavaScript được đặt bên trong thẻ `<script>` và `</script>`. Có thể đặt script trong `<head>`, `<body>` hoặc cả hai.

### Nhúng trực tiếp (Inline / Internal)

```html
<!DOCTYPE html>
<html>
  <head>
    <script>
      console.log("Script trong head");
    </script>
  </head>
  <body>
    <script>
      console.log("Script cuối body");
    </script>
  </body>
</html>
```

### Sử dụng file JavaScript bên ngoài (External)

```html
<!-- URL đầy đủ (CDN / External domain) -->
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>

<!-- Đường dẫn tuyệt đối tính từ thư mục gốc của website -->
<script src="/js/myScript.js"></script>

<!-- Đường dẫn tương đối -->
<script src="myScript.js"></script>

<!-- Tải bất đồng bộ và trì hoãn thực thi -->
<script src="asyncScript.js" async></script>
<script src="deferScript.js" defer></script>
```

- `async`: Tải script song song với việc phân tích HTML, thực thi ngay khi tải xong (không đảm bảo thứ tự).
- `defer`: Tải song song với HTML nhưng chỉ thực thi sau khi HTML đã phân tích xong (giữ đúng thứ tự file).

---

## 2. Outputs — cách xuất dữ liệu

| Phương thức / Thuộc tính | Tác dụng | Phạm vi khuyến nghị |
| ------------------------ | -------- | ------------------- |
| `innerHTML` | Đọc hoặc ghi toàn bộ nội dung HTML bên trong một phần tử. | Thao tác DOM, cần render thẻ HTML. Cẩn trọng tấn công XSS. |
| `innerText` | Ghi/lấy văn bản hiển thị (nhận biết CSS, bỏ qua text ẩn). | Hiển thị text theo giao diện người dùng. |
| `textContent` | Ghi/lấy toàn bộ nội dung văn bản thô (kể cả thẻ ẩn, nhanh hơn `innerText`). | Khuyến nghị dùng khi chỉ cần cập nhật text an toàn. |
| `document.write()` | Ghi trực tiếp vào HTML document stream. | Không dùng trong ứng dụng thực tế (dễ ghi đè toàn bộ trang). |
| `window.alert()` | Hiển thị hộp thoại modal cảnh báo người dùng. | Debug nhanh, không khuyến nghị trong UI hiện đại. |
| `console.log()` / `console.error()` / `console.warn()` | In dữ liệu, lỗi, cảnh báo ra Developer Console của trình duyệt. | Debug, kiểm tra dữ liệu và trạng thái mã nguồn. |

Ví dụ:

```html
<p id="message"></p>

<script>
  const el = document.getElementById("message");
  el.textContent = "Hello world!";
  console.log("Nội dung đã được gán:", el.textContent);
</script>
```

---

## 3. Syntax & Variables — cú pháp cơ bản và biến

### Literals và Variables

- **Literals** là các giá trị cố định được viết trực tiếp trong mã nguồn (`10`, `"hello"`, `true`, `[1, 2]`, `{ a: 1 }`).
- **Variables** là định danh tham chiếu đến ô nhớ lưu trữ giá trị.

### So sánh `var`, `let`, `const`

| Tiêu chí | `var` | `let` | `const` |
| -------- | ----- | ----- | ------- |
| **Phạm vi (Scope)** | Function scope | Block scope `{}` | Block scope `{}` |
| **Gán lại (Re-assignment)** | Có | Có | Không |
| **Khai báo lại** | Có (dễ gây lỗi vô ý) | Lỗi `SyntaxError` | Lỗi `SyntaxError` |
| **Hoisting** | Có (khởi tạo `undefined`) | Có (nằm trong Temporal Dead Zone - TDZ) | Có (nằm trong Temporal Dead Zone - TDZ) |
| **Thuộc tính `window`** | Tạo thuộc tính trên `window` | Không | Không |

```js
const siteName = "TechBlog"; // Không thể gán lại biến
// siteName = "NewBlog";    // TypeError: Assignment to constant variable.

let count = 0;
count += 1;                  // Hợp lệ

if (true) {
  let blockVar = "Block scope";
  var functionVar = "Function scope";
}
// console.log(blockVar);    // ReferenceError
console.log(functionVar);    // "Function scope"
```

> **Quy tắc:** Mặc định luôn dùng `const`. Chỉ dùng `let` khi chắc chắn cần gán lại giá trị. Không dùng `var` trong mã hiện đại.

### Quy tắc đặt tên biến

- Tên biến có thể chứa chữ cái, chữ số, `_` và `$`.
- Không bắt đầu bằng số.
- Phân biệt hoa thường (`myVar` khác `myvar`).
- Quy ước đặt tên: `camelCase` cho biến và hàm (`userInfo`), `PascalCase` cho Class/Constructor (`UserManager`), `UPPER_SNAKE_CASE` cho hằng số bất biến toàn cục (`API_BASE_URL`).

---

## 4. Data types — kiểu dữ liệu

JavaScript có 7 kiểu dữ liệu nguyên thủy (Primitive) và 1 kiểu dữ liệu phức hợp (Reference / Object).

### Các kiểu nguyên thủy (Primitive)

Dữ liệu nguyên thủy là bất biến (immutable) và được truyền theo giá trị (pass by value).

| Kiểu | Ý nghĩa | Ví dụ |
| ---- | ------- | ----- |
| `string` | Chuỗi ký tự văn bản | `"hello"`, `'world'`, \`id: ${id}\` |
| `number` | Số nguyên và số thực (IEEE 754 float 64-bit) | `42`, `3.14`, `NaN`, `Infinity` |
| `bigint` | Số nguyên độ dài tùy ý vượt giới hạn `2^53 - 1` | `9007199254740991n` |
| `boolean` | Giá trị logic | `true`, `false` |
| `undefined` | Biến đã khai báo nhưng chưa được gán giá trị | `let x;` |
| `null` | Thể hiện sự vắng mặt có chủ đích của giá trị object | `let user = null;` |
| `symbol` | Giá trị định danh độc nhất, không thể trùng lặp | `Symbol("id")` |

### Kiểu tham chiếu (Reference / Object)

Được lưu trữ trên Heap và truyền theo tham chiếu (pass by reference):
- `Object`, `Array`, `Function`, `Date`, `RegExp`, `Map`, `Set`, ...

### Kiểm tra kiểu dữ liệu

```js
typeof "Hello";      // "string"
typeof 123;          // "number"
typeof true;         // "boolean"
typeof undefined;    // "undefined"
typeof 10n;          // "bigint"
typeof Symbol();     // "symbol"
typeof function(){}; // "function"

// Điểm cần chú ý:
typeof null;         // "object" (lỗi lịch sử từ phiên bản đầu tiên của JS)
typeof {};           // "object"
typeof [];           // "object"
```

> Để kiểm tra chính xác mảng: dùng `Array.isArray(val)`.  
> Để kiểm tra null: dùng `val === null`.

### Ép kiểu (Type Coercion & Conversion)

- **Explicit (Tường minh):** `Number("123")`, `String(123)`, `Boolean(0)`.
- **Falsy values trong JS:** `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN`. Mọi giá trị khác đều là **truthy**.

---

## 5. Operators — toán tử

### Toán tử số học & gán

```js
let a = 10;
a += 5;       // a = 15
a %= 4;       // a = 3 (chia lấy dư)
let p = 2 ** 3; // 8 (lũy thừa)
```

### So sánh bằng: `==` vs `===`

- `==` (Loose equality): So sánh sau khi tự động ép kiểu (Implicit Coercion).
- `===` (Strict equality): So sánh cả giá trị lẫn kiểu dữ liệu, không ép kiểu.

```js
5 == "5";   // true  (chuỗi "5" bị ép thành số 5)
5 === "5";  // false (number !== string)
null == undefined;  // true
null === undefined; // false
```

> **Quy tắc:** Luôn ưu tiên `===` và `!==` để tránh lỗi ép kiểu ngầm định.

### Toán tử logic và đoản mạch (Short-circuiting)

- `&&` (AND): Trả về giá trị falsy đầu tiên gặp phải, nếu không có thì trả về giá trị cuối cùng.
- `||` (OR): Trả về giá trị truthy đầu tiên gặp phải, nếu không có thì trả về giá trị cuối cùng.
- `!` (NOT): Nghịch đảo boolean (`!true === false`, `!!val` chuyển thành boolean).

```js
const userRole = currentUser.role || "guest"; // Gán mặc định nếu falsy
isLoggedIn && showDashboard();                // Chạy hàm nếu isLoggedIn là truthy
```

### Nullish Coalescing (`??`) và Optional Chaining (`?.`)

- `??` chỉ dự phòng khi vế trái là `null` hoặc `undefined` (giữ lại `0`, `""`, `false`).
- `?.` dừng và trả về `undefined` nếu thuộc tính trước nó là `null` hoặc `undefined`.

```js
const score = 0;
const finalScore1 = score || 10; // 10 (vì 0 là falsy)
const finalScore2 = score ?? 10; // 0  (vì 0 không phải null/undefined)

const city = user?.address?.city; // Không quăng lỗi nếu user hoặc address là undefined
```

---

## 6. Control Flow & Loops — cấu trúc điều khiển và vòng lặp

### Rẽ nhánh (`if...else`, `switch...case`, toán tử 3 ngôi)

```js
// Toán tử 3 ngôi (Ternary operator)
const access = age >= 18 ? "Cho phép" : "Từ chối";

// switch-case
switch (status) {
  case "active":
    handleActive();
    break;
  case "inactive":
    handleInactive();
    break;
  default:
    handleUnknown();
}
```

### Các vòng lặp

```js
// 1. for truyền thống (biết trước số lần lặp)
for (let i = 0; i < 5; i++) {
  if (i === 2) continue; // Bỏ qua lần lặp
  if (i === 4) break;    // Thoát vòng lặp
}

// 2. while (kiểm tra điều kiện trước)
let count = 3;
while (count > 0) {
  count--;
}

// 3. do...while (chạy ít nhất 1 lần)
let n = 0;
do {
  n++;
} while (n < 1);

// 4. for...of: Duyệt qua các GIÁ TRỊ của Iterable (Array, String, Set, Map)
const colors = ["red", "green", "blue"];
for (const color of colors) {
  console.log(color);
}

// 5. for...in: Duyệt qua các KEY/thuộc tính có thể liệt kê của Object
const student = { name: "Nam", grade: 10 };
for (const key in student) {
  console.log(key, student[key]);
}
```

---

## 7. Strings — chuỗi ký tự

Chuỗi trong JavaScript là **bất biến** (*immutable*). Các phương thức xử lý chuỗi luôn trả về chuỗi mới mà không làm biến đổi chuỗi gốc.

### Bảng phương thức String thông dụng

| Nhóm | Phương thức | Tác dụng |
| ---- | ----------- | -------- |
| **Độ dài & Truy xuất** | `length` | Thuộc tính trả về độ dài tính bằng UTF-16 code units. |
| | `at(index)` | Lấy ký tự theo index; hỗ trợ số âm (`-1` lấy ký tự cuối). |
| | `charAt(index)` | Lấy ký tự tại index; không hỗ trợ số âm. |
| | `charCodeAt(index)` | Lấy mã số UTF-16 code unit tại index. |
| **Trích xuất chuỗi** | `slice(start, end)` | Trích xuất chuỗi từ `start` đến trước `end`. Nhận index âm. |
| | `substring(start, end)` | Trích xuất chuỗi từ `start` đến `end`. Số âm tính là 0. |
| **Tìm kiếm & Kiểm tra** | `includes(search)` | Kiểm tra chuỗi con có xuất hiện hay không (`true`/`false`). |
| | `startsWith(prefix)` | Kiểm tra chuỗi có bắt đầu bằng tiền tố hay không. |
| | `endsWith(suffix)` | Kiểm tra chuỗi có kết thúc bằng hậu tố hay không. |
| | `indexOf(search)` | Vị trí xuất hiện đầu tiên của chuỗi con (`-1` nếu không có). |
| | `lastIndexOf(search)` | Vị trí xuất hiện cuối cùng của chuỗi con. |
| | `search(regex)` | Tìm vị trí khớp đầu tiên với Regular Expression. |
| | `match(regex)` | Trả về mảng kết quả khớp RegExp hoặc `null`. |
| **Biến đổi** | `toUpperCase()` / `toLowerCase()` | Chuyển thành chữ hoa / chữ thường. |
| | `trim()` / `trimStart()` / `trimEnd()` | Loại bỏ khoảng trắng ở 2 đầu / đầu / cuối. |
| | `replace(search, replace)` | Thay thế chuỗi hoặc pattern khớp đầu tiên. |
| | `replaceAll(search, replace)` | Thay thế tất cả chuỗi hoặc pattern khớp. |
| | `split(separator)` | Tách chuỗi thành một mảng các chuỗi con. |
| | `padStart(len, char)` / `padEnd()` | Thêm ký tự bù vào đầu/cuối đến khi đủ độ dài. |
| | `repeat(count)` | Lặp lại chuỗi `count` lần. |

### Ví dụ thao tác chuỗi

```js
const raw = "  Javascript is awesome  ";
const clean = raw.trim(); // "Javascript is awesome"

console.log(clean.slice(0, 10));         // "Javascript"
console.log(clean.includes("awesome"));   // true
console.log(clean.replace("is", "is not")); // "Javascript is not awesome"

const tags = "react,vue,angular".split(","); // ["react", "vue", "angular"]
const paddedId = "42".padStart(6, "0");      // "000042"
```

---

## 8. Arrays — mảng

Mảng trong JavaScript có độ dài động, có thể chứa nhiều kiểu dữ liệu khác nhau và có chỉ mục bắt đầu từ 0.

### Khai báo và khởi tạo

```js
const fruits = ["Apple", "Banana", "Cherry"];
const matrix = [[1, 2], [3, 4]];
const mixed = [1, "two", { active: true }, [4]];
```

### Các phương thức làm thay đổi mảng gốc (Mutating Methods)

| Phương thức | Tác dụng | Giá trị trả về |
| ----------- | -------- | -------------- |
| `push(el1, ...)` | Thêm phần tử vào cuối mảng | Độ dài mới của mảng |
| `pop()` | Xóa và lấy phần tử cuối cùng ra khỏi mảng | Phần tử vừa bị xóa |
| `unshift(el1, ...)` | Thêm phần tử vào đầu mảng | Độ dài mới của mảng |
| `shift()` | Xóa và lấy phần tử đầu tiên ra khỏi mảng | Phần tử vừa bị xóa |
| `splice(start, deleteCount, ...items)` | Xóa, thêm hoặc thay thế phần tử trực tiếp | Mảng chứa các phần tử bị xóa |
| `reverse()` | Đảo ngược thứ tự các phần tử | Mảng gốc sau khi đảo |
| `sort(compareFn)` | Sắp xếp các phần tử tại chỗ (mặc định theo chuỗi ASCII) | Mảng gốc sau khi sắp xếp |
| `fill(value, start, end)` | Điền giá trị vào các phần tử từ start đến end | Mảng gốc |

```js
const nums = [3, 1, 10, 2];
nums.sort((a, b) => a - b); // Sắp xếp tăng dần: [1, 2, 3, 10]

const items = ["a", "b", "c", "d"];
items.splice(1, 2, "x", "y"); // Tại index 1, xóa 2 phần tử, thêm "x", "y"
// items: ["a", "x", "y", "d"]
```

### Các phương thức KHÔNG làm thay đổi mảng gốc (Non-mutating / Iteration)

| Phương thức | Tác dụng | Trả về |
| ----------- | -------- | ------ |
| `map(callback)` | Biến đổi từng phần tử theo hàm biến đổi | Mảng mới cùng độ dài |
| `filter(callback)` | Lọc các phần tử thỏa mãn điều kiện (truthy) | Mảng mới chứa các phần tử thỏa mãn |
| `reduce(callback, initialValue)` | Tích lũy các phần tử thành một giá trị duy nhất | Giá trị tích lũy cuối cùng |
| `find(callback)` | Tìm phần tử đầu tiên thỏa mãn điều kiện | Phần tử tìm thấy hoặc `undefined` |
| `findIndex(callback)` | Tìm chỉ mục của phần tử đầu tiên thỏa mãn | Index tìm thấy hoặc `-1` |
| `some(callback)` | Kiểm tra có ít nhất 1 phần tử thỏa mãn hay không | `true` hoặc `false` |
| `every(callback)` | Kiểm tra tất cả phần tử có thỏa mãn hay không | `true` hoặc `false` |
| `includes(item)` | Kiểm tra phần tử có nằm trong mảng hay không | `true` hoặc `false` |
| `slice(start, end)` | Trích xuất một mảng con | Mảng con mới |
| `concat(arr2)` | Nối hai hoặc nhiều mảng | Mảng mới kết hợp |
| `join(separator)` | Nối các phần tử mảng thành một chuỗi | Chuỗi |
| `flat(depth)` | Làm phẳng mảng lồng nhau theo độ sâu | Mảng mới đã làm phẳng |
| `flatMap(callback)` | Kết hợp `map()` rồi làm phẳng cấp 1 `flat(1)` | Mảng mới |

```js
const numbers = [1, 2, 3, 4, 5];

// 1. map
const doubled = numbers.map(n => n * 2); // [2, 4, 6, 8, 10]

// 2. filter
const evens = numbers.filter(n => n % 2 === 0); // [2, 4]

// 3. reduce: tính tổng
const sum = numbers.reduce((acc, curr) => acc + curr, 0); // 15

// 4. find & some & every
const found = numbers.find(n => n > 3);       // 4
const hasNegative = numbers.some(n => n < 0); // false
const allPositive = numbers.every(n => n > 0);// true
```

---

## 9. Objects — đối tượng

Object là tập hợp các cặp `key: value`, trong đó key là chuỗi (hoặc Symbol) và value có thể là bất kỳ kiểu dữ liệu nào.

### Khởi tạo và truy xuất thuộc tính

```js
const person = {
  name: "Hoàng",
  age: 25,
  greet() {
    return `Xin chào, tôi là ${this.name}`;
  }
};

// Truy xuất
console.log(person.name);       // Dot notation: "Hoàng"
console.log(person["age"]);     // Bracket notation: 25

// Thêm / Cập nhật / Xóa
person.job = "Developer";
delete person.age;
```

### Các phương thức tĩnh hữu ích của `Object`

| Phương thức | Tác dụng |
| ----------- | -------- |
| `Object.keys(obj)` | Trả về mảng các key (thuộc tính) của đối tượng. |
| `Object.values(obj)` | Trả về mảng các giá trị tương ứng của đối tượng. |
| `Object.entries(obj)` | Trả về mảng các cặp `[key, value]`. |
| `Object.assign(target, ...sources)` | Sao chép các thuộc tính từ các đối tượng nguồn vào đối tượng đích. |
| `Object.freeze(obj)` | Đóng băng đối tượng: không thể thêm, xóa, sửa thuộc tính. |
| `Object.seal(obj)` | Niêm phong đối tượng: có thể sửa thuộc tính cũ, không được thêm/xóa. |

```js
const user = { id: 1, name: "Lan", role: "admin" };

console.log(Object.keys(user));    // ["id", "name", "role"]
console.log(Object.values(user));  // [1, "Lan", "admin"]
console.log(Object.entries(user)); // [["id", 1], ["name", "Lan"], ["role", "admin"]]

// Sao chép nông (Shallow Copy)
const copy = Object.assign({}, user);
// Hoặc bằng Spread operator:
const spreadCopy = { ...user };
```

---

## 10. Functions — hàm

### Các cách định nghĩa hàm

```js
// 1. Function Declaration (Được hoisted hoàn toàn)
function add(a, b) {
  return a + b;
}

// 2. Function Expression (Không hoisted giá trị hàm)
const subtract = function(a, b) {
  return a - b;
};

// 3. Arrow Function (ES6)
const multiply = (a, b) => a * b;
```

### So sánh Arrow Function và Regular Function

| Đặc điểm | Regular Function (`function`) | Arrow Function (`() =>`) |
| -------- | ---------------------------- | ------------------------- |
| **Từ khóa `this`** | Động (phụ thuộc vào cách hàm được gọi). | Kế thừa tĩnh từ ngữ cảnh bao quanh (*lexical this*). |
| **Đối tượng `arguments`**| Có sẵn biến `arguments`. | Không có (dùng Rest parameter `...args`). |
| **Làm hàm khởi tạo (`new`)** | Được | Lỗi `TypeError: not a constructor`. |
| **Cú pháp** | Dài hơn, cần từ khóa `return` nếu dùng `{}`. | Gọn hơn, hỗ trợ implicit return khi không có `{}`. |

```js
const timer = {
  seconds: 0,
  start() {
    setInterval(() => {
      this.seconds++; // 'this' trỏ đúng vào object timer nhờ lexical scoping
    }, 1000);
  }
};
```

### Default Parameters và Rest Parameters

```js
// Tham số mặc định
function greet(name = "bạn bè") {
  return `Xin chào ${name}`;
}

// Rest parameter (gom các đối số còn lại thành mảng)
function sumAll(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
console.log(sumAll(1, 2, 3, 4)); // 10
```

---

## 11. Scope, Hoisting & Closures

### Scope (Phạm vi truy cập)

- **Global Scope:** Biến khai báo ngoài mọi hàm/block, truy cập được ở bất kỳ đâu.
- **Function Scope:** Biến khai báo bằng `var`, `let`, `const` bên trong một hàm chỉ truy cập được trong hàm đó.
- **Block Scope:** Biến khai báo bằng `let`, `const` bên trong khối `{}` chỉ có giá trị trong khối đó.

### Hoisting

Hoisting là cơ chế của JavaScript di chuyển các khai báo biến và hàm lên đầu phạm vi chứa chúng trong giai đoạn biên dịch (compile phase).

- `function declaration`: Hoisted cả khai báo lẫn định nghĩa. Có thể gọi hàm trước khi khai báo.
- `var`: Hoisted phần khai báo với giá trị ban đầu là `undefined`.
- `let` và `const`: Hoisted nhưng không được khởi tạo, rơi vào **Temporal Dead Zone (TDZ)** cho đến khi gặp dòng khai báo thực tế.

```js
sayHi(); // Chạy bình thường vì function declaration được hoisted hoàn chỉnh
function sayHi() {
  console.log("Hi!");
}

console.log(x); // undefined
var x = 10;

// console.log(y); // ReferenceError: Cannot access 'y' before initialization (TDZ)
let y = 20;
```

### Closures

Closure là khả năng một hàm bên trong ghi nhớ và truy cập vào các biến thuộc phạm vi của hàm bao ngoài nó (lexical scope), ngay cả khi hàm bao ngoài đã thực thi xong.

```js
function createCounter() {
  let count = 0; // Biến private được bảo vệ bởi closure

  return {
    increment() {
      count++;
      return count;
    },
    decrement() {
      count--;
      return count;
    },
    getCount() {
      return count;
    }
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getCount());  // 2
// count không thể bị can thiệp trực tiếp từ bên ngoài
```

---

## 12. Modern ES6+ Features

### Destructuring (Phân rã mảng và đối tượng)

```js
// Destructuring với Mảng
const rgb = [255, 140, 0];
const [r, g, b] = rgb;

// Bỏ qua phần tử và giá trị mặc định
const [first, , third, fourth = 0] = [10, 20, 30];

// Destructuring với Đối tượng
const user = { id: 101, username: "dev99", email: "dev@example.com" };
const { id, username: accountName, role = "user" } = user;
console.log(accountName); // "dev99" (đổi tên biến)
```

### Spread Syntax (`...`)

Trải các phần tử của một Iterable (Mảng, Object, Chuỗi) ra.

```js
// Nối và sao chép mảng
const arr1 = [1, 2];
const arr2 = [...arr1, 3, 4]; // [1, 2, 3, 4]

// Hợp nhất và sao chép object
const base = { theme: "dark", lang: "vi" };
const settings = { ...base, lang: "en", notifications: true };
```

### Map và Set

- **`Set`**: Tập hợp các giá trị **độc nhất** (không trùng lặp). Thường dùng để loại bỏ trùng lặp trong mảng.
- **`Map`**: Cấu trúc bảng băm lưu trữ key-value, trong đó **key có thể là bất kỳ kiểu dữ liệu nào** (cả object, function).

```js
// Loại bỏ trùng lặp khỏi mảng bằng Set
const duplicateNumbers = [1, 2, 2, 3, 4, 4, 5];
const unique = [...new Set(duplicateNumbers)]; // [1, 2, 3, 4, 5]

// Map
const userMap = new Map();
const objKey = { id: 1 };
userMap.set(objKey, "Thông tin người dùng 1");
console.log(userMap.get(objKey)); // "Thông tin người dùng 1"
```

---

## 13. Asynchronous JavaScript & Event Loop

JavaScript chạy đơn luồng (Single-threaded). Các tác vụ I/O lâu (đọc file, fetch network, timer) được xử lý bất đồng bộ nhờ cơ chế **Event Loop**.

```text
┌────────────────────────────────────────┐
│               Call Stack               │
└──────────────────┬─────────────────────┘
                   │ Đẩy Web API / Node I/O
                   ▼
┌────────────────────────────────────────┐
│         Web APIs / Background          │ (setTimeout, fetch, DOM events)
└──────────────────┬─────────────────────┘
                   │ Đẩy callback vào hàng đợi
                   ▼
┌─────────────────────────────────────────────────────────────────┐
│ Microtask Queue (Promise .then/catch, queueMicrotask)           │ ◄── Ưu tiên cao hơn
├─────────────────────────────────────────────────────────────────┤
│ Macrotask Queue (setTimeout, setInterval, setImmediate, I/O)    │
└─────────────────────────────────────────────────────────────────┘
```

> **Nguyên tắc:** Event Loop sẽ đợi Call Stack trống, sau đó rút hết mọi tác vụ trong **Microtask Queue** trước khi rút tác vụ tiếp theo từ **Macrotask Queue**.

### Promise

`Promise` biểu diễn kết quả tương lai của một tác vụ bất đồng bộ, có 3 trạng thái:
1. `pending`: Đang chờ xử lý.
2. `fulfilled`: Thành công, kèm kết quả (`resolve(value)`).
3. `rejected`: Thất bại, kèm lỗi (`reject(error)`).

```js
function fetchData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url) {
        resolve({ data: "Server response" });
      } else {
        reject(new Error("URL không hợp lệ"));
      }
    }, 1000);
  });
}

fetchData("https://api.example.com")
  .then(res => console.log(res.data))
  .catch(err => console.error(err.message))
  .finally(() => console.log("Hoàn tất yêu cầu"));
```

### Các phương thức tĩnh hữu ích của `Promise`

| Phương thức | Tác dụng |
| ----------- | -------- |
| `Promise.all([p1, p2])` | Chờ tất cả promise thành công; quăng lỗi ngay nếu 1 promise reject. |
| `Promise.allSettled([p1, p2])` | Chờ tất cả hoàn thành (kể cả resolve hay reject), trả về trạng thái chi tiết của từng promise. |
| `Promise.race([p1, p2])` | Trả về kết quả của promise đầu tiên hoàn thành (resolve hoặc reject). |
| `Promise.any([p1, p2])` | Trả về kết quả của promise đầu tiên resolve thành công; chỉ reject khi tất cả đều reject. |

### `async` / `await`

Cú pháp bọc ngoài Promise giúp viết mã bất đồng bộ trông giống như đồng bộ, kết hợp với `try...catch` để bắt lỗi trực quan.

```js
async function loadUserData(userId) {
  try {
    const res = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`);
    if (!res.ok) {
      throw new Error(`HTTP Error: ${res.status}`);
    }
    const data = await res.json();
    console.log("User:", data.name);
    return data;
  } catch (error) {
    console.error("Lỗi tải người dùng:", error.message);
  }
}
```

---

## 14. Error Handling — xử lý ngoại lệ

```js
try {
  // Đoạn mã có thể phát sinh lỗi
  const parsed = JSON.parse("invalid-json");
} catch (error) {
  // Bắt và xử lý lỗi
  console.error("Tên lỗi:", error.name);
  console.error("Thông điệp:", error.message);
} finally {
  // Luôn luôn thực thi dù có lỗi hay không (thường dùng dọn dẹp tài nguyên)
  console.log("Cleanup hoàn tất.");
}

// Ném lỗi chủ động
function validateAge(age) {
  if (age < 0) {
    throw new RangeError("Tuổi không thể là số âm");
  }
  return true;
}
```

---

## 15. DOM Manipulation & Events — thao tác DOM và sự kiện

### Truy vấn phần tử (Selectors)

```js
const btn = document.getElementById("submit-btn");
const activeCard = document.querySelector(".card.active");
const allItems = document.querySelectorAll("ul.list > li"); // NodeList
```

### Chỉnh sửa nội dung, style và class

```js
const box = document.querySelector(".box");

// Thuộc tính văn bản & HTML
box.textContent = "Nội dung an toàn";

// Thuộc tính CSS
box.style.backgroundColor = "#007bff";

// Quản lý Class (ClassList)
box.classList.add("highlight");
box.classList.remove("hidden");
box.classList.toggle("active");
const hasClass = box.classList.contains("highlight"); // true

// Tạo và gắn phần tử mới
const newEl = document.createElement("div");
newEl.className = "item";
newEl.textContent = "Mục mới";
document.body.appendChild(newEl);
```

### Lắng nghe sự kiện (Event Handling)

```js
const button = document.querySelector("#myBtn");

function handleClick(event) {
  console.log("Sự kiện xảy ra trên:", event.target);
  event.preventDefault(); // Ngăn hành vi mặc định (vd: submit form, chuyển link)
  event.stopPropagation(); // Ngăn sự kiện nổi bọt lên phần tử cha
}

button.addEventListener("click", handleClick);
// Gỡ bỏ listener khi không cần thiết
button.removeEventListener("click", handleClick);
```

### Kỹ thuật Event Delegation (Ủy quyền sự kiện)

Thay vì gắn listener cho từng phần tử con, ta gắn 1 listener duy nhất vào phần tử cha để tối ưu bộ nhớ:

```js
const list = document.querySelector("#todo-list");

list.addEventListener("click", (event) => {
  if (event.target.tagName === "LI") {
    console.log("Đã chọn item:", event.target.textContent);
  }
});
```

---

## 16. Modules — mô đun hóa mã nguồn

### ES Modules (Tiêu chuẩn hiện đại của JavaScript)

```js
// utils.js - Named Export & Default Export
export const PI = 3.14159;
export function sum(a, b) {
  return a + b;
}
export default function logger(msg) {
  console.log(`[LOG]: ${msg}`);
}

// main.js - Import
import logger, { PI, sum as addNumbers } from "./utils.js";
logger(addNumbers(PI, 2));
```

### CommonJS (Sử dụng phổ biến trong môi trường Node.js truyền thống)

```js
// math.js
function multiply(x, y) {
  return x * y;
}
module.exports = { multiply };

// app.js
const { multiply } = require("./math");
console.log(multiply(4, 5));
```

---

## 17. Classes & Object-Oriented Programming (OOP)

JavaScript là ngôn ngữ dựa trên prototype (*prototypal inheritance*). Cú pháp `class` (từ ES6) là syntactic sugar giúp mã hướng đối tượng trở nên quen thuộc hơn.

```js
class Animal {
  #secretCode; // Private field (ES2022)

  constructor(name) {
    this.name = name;
    this.#secretCode = Math.random();
  }

  speak() {
    console.log(`${this.name} phát ra âm thanh.`);
  }

  static isAnimal(obj) {
    return obj instanceof Animal;
  }
}

// Kế thừa với extends và super
class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Gọi constructor của class cha
    this.breed = breed;
  }

  // Override phương thức
  speak() {
    console.log(`${this.name} sủa: Gâu gâu!`);
  }
}

const myDog = new Dog("Milu", "Corgi");
myDog.speak(); // "Milu sủa: Gâu gâu!"
console.log(Animal.isAnimal(myDog)); // true
```

---

## 18. Web Storage & Network Requests

### Web Storage API

Lưu trữ dữ liệu dạng key-value (chuỗi) tại trình duyệt của người dùng.

| Tiêu chí | `localStorage` | `sessionStorage` |
| -------- | -------------- | ---------------- |
| **Vòng đời** | Tồn tại vĩnh viễn đến khi chủ động xóa hoặc xóa cache. | Bị xóa ngay khi tab/cửa sổ trình duyệt đóng lại. |
| **Dung lượng** | Thường ~5MB - 10MB tùy trình duyệt. | ~5MB. |
| **Phạm vi** | Cùng Origin (Protocol + Domain + Port). | Cùng tab và cùng Origin. |

```js
// Lưu trữ object phức tạp (cần chuyển sang chuỗi JSON)
const userProfile = { id: 1, name: "Alice" };
localStorage.setItem("user", JSON.stringify(userProfile));

// Đọc dữ liệu
const stored = localStorage.getItem("user");
if (stored) {
  const parsedUser = JSON.parse(stored);
  console.log(parsedUser.name); // "Alice"
}

// Xóa dữ liệu
localStorage.removeItem("user");
localStorage.clear(); // Xóa toàn bộ
```

### Fetch API

Giao thức gửi HTTP Request hiện đại tích hợp sẵn trên trình duyệt và Node.js.

```js
async function sendPostRequest() {
  const url = "https://jsonplaceholder.typicode.com/posts";
  const postData = {
    title: "Học JavaScript",
    body: "Tài liệu hệ thống hóa toàn bộ kiến thức JS",
    userId: 1
  };

  const response = await fetch(url, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(postData)
  });

  const result = await response.json();
  console.log("Kết quả tạo mới:", result);
}
```
