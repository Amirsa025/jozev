


### سوالات مصاحبه‌ای مرتبط با Object در جاوااسکریپت (همراه با پاسخ)

---

#### ۱. **تفاوت بین `Object.assign()` و Spread Operator (`...`) برای کپی کردن اشیاء چیست؟**
- **پاسخ**:  
  هر دو برای کپی سطحی (Shallow Copy) استفاده می‌شوند، اما تفاوت‌ها عبارتند از:
  - **`Object.assign()`**:  
    متدی است که خصوصیات شیء منبع را به شیء مقصد کپی می‌کند و مقدار شیء مقصد را برمی‌گرداند.  
    ```javascript
    const obj1 = { a: 1 };
    const obj2 = Object.assign({}, obj1); // کپی سطحی
    ```
  - **Spread Operator (`...`)**:  
    یک سینتکس است که به صورت مستقیم خصوصیات شیء را گسترش می‌دهد.  
    ```javascript
    const obj1 = { a: 1 };
    const obj2 = { ...obj1 }; // کپی سطحی
    ```
  - **تفاوت کلیدی**:  
    `Object.assign()` می‌تواند چندین شیء را در یکدیگر ادغام کند، در حالی که Spread Operator فقط برای گسترش خصوصیات استفاده می‌شود.

---

#### ۲. **چگونه یک کپی عمیق (Deep Copy) از یک شیء ایجاد کنیم؟**
- **پاسخ**:  
  برای کپی عمیق، باید تمام لایه‌های شیء را کپی کنید. روش‌های رایج عبارتند از:
  - **استفاده از `JSON.parse(JSON.stringify(obj))`**:  
    (فقط برای داده‌های ساده و بدون تابع یا `undefined`)  
    ```javascript
    const original = { a: { b: 2 } };
    const copy = JSON.parse(JSON.stringify(original));
    ```
  - **استفاده از کتابخانه‌هایی مانند Lodash (`_.cloneDeep`)**:  
    ```javascript
    const copy = _.cloneDeep(original);
    ```
  - **پیاده‌سازی دستی با بازگشتی (Recursive)**.

---

#### ۳. **تفاوت بین `let`, `const` و `var` در تعریف خصوصیات شیء چیست؟**
- **پاسخ**:  
  - **`var`**:  
    در Scope تابع یا Global قرار می‌گیرد و می‌تواند Redeclare شود.  
    ```javascript
    var obj = { a: 1 };
    var obj = { b: 2 }; // مجاز است
    ```
  - **`let` و `const`**:  
    در Block Scope قرار می‌گیرند و نمی‌توانند Redeclare شوند.  
    ```javascript
    let obj = { a: 1 };
    // let obj = { b: 2 }; // خطا: Identifier 'obj' has already been declared
    ```
  - **نکته**:  
    برای خصوصیات داخل شیء، `let` و `const` استفاده نمی‌شوند (مقدار خصوصیات شیء همیشه با `=` تعریف می‌شوند).

---

#### ۴. **تفاوت `Object.seal()` و `Object.freeze()` چیست؟**
- **پاسخ**:  
  - **`Object.seal()`**:  
    مانع اضافه یا حذف خصوصیات می‌شود، اما اجازه تغییر مقادیر خصوصیات موجود را می‌دهد.  
    ```javascript
    const obj = { a: 1 };
    Object.seal(obj);
    obj.a = 2; // مجاز
    delete obj.a; // غیرمجاز
    ```
  - **`Object.freeze()`**:  
    هم اضافه/حذف خصوصیات و هم تغییر مقادیر را ممنوع می‌کند.  
    ```javascript
    const obj = { a: 1 };
    Object.freeze(obj);
    obj.a = 2; // غیرمجاز
    ```

---

#### ۵. **تفاوت بین `Map` و `Object` در جاوااسکریپت چیست؟**
- **پاسخ**:  
  | ویژگی                | `Object`                          | `Map`                          |
  |-----------------------|-----------------------------------|--------------------------------|
  | **کلیدها**            | فقط رشته یا Symbol               | هر نوع داده (حتی اشیاء)       |
  | **ترتیب خصوصیات**     | بدون ترتیب ثابت (ES6+)          | ترتیب قابل اتکاء              |
  | **تعداد خصوصیات**      | `Object.keys(obj).length`        | `map.size`                     |
  | **کارایی در حلقه‌ها**  | کمتر برای تعداد زیاد خصوصیت      | بهینه‌تر برای CRUD عملیات     |
  | **متد‌ها**            | ندارد                            | `set`, `get`, `has`, `delete` |

---

#### ۶. **چگونه یک خصوصیت خصوصی (Private) در شیء ایجاد کنیم؟**
- **پاسخ**:  
  در ES6+ می‌توانید از `Symbol` یا `WeakMap` استفاده کنید. در ES2022+ از هش (`#`) استفاده می‌شود:  
  ```javascript
  class Person {
    #privateField = "secret";
    getPrivate() {
      return this.#privateField;
    }
  }
  const person = new Person();
  console.log(person.getPrivate()); // "secret"
  // console.log(person.#privateField); // خطا: Private field
  ```

---

#### ۷. **تفاوت بین `Object.create()` و سینتکس لیترال `{}` چیست؟**
- **پاسخ**:  
  - **`Object.create(proto)`**:  
    یک شیء جدید با پروتوتایپ مشخص ایجاد می‌کند.  
    ```javascript
    const proto = { greet: () => "Hello" };
    const obj = Object.create(proto);
    console.log(obj.greet()); // "Hello"
    ```
  - **لیترال `{}`**:  
    یک شیء جدید با پروتوتایپ پیش‌فرض (`Object.prototype`) ایجاد می‌کند.

---

#### ۸. **چگونه یک شیء را مرتب کنیم؟**
- **پاسخ**:  
  اشیاء ذاتاً بدون ترتیب هستند، اما می‌توانید از `Object.keys()` و `Array.sort()` استفاده کنید:  
  ```javascript
  const obj = { b: 2, a: 1, c: 3 };
  const sortedKeys = Object.keys(obj).sort();
  const sortedObj = {};
  sortedKeys.forEach(key => {
    sortedObj[key] = obj[key];
  });
  console.log(sortedObj); // { a: 1, b: 2, c: 3 }
  ```

---

#### ۹. **تفاوت بین `for...in` و `Object.keys()` چیست؟**
- **پاسخ**:  
  - **`for...in`**:  
    تمام خصوصیات قابل تعداد (Enumerable) شامل خصوصیات وراثتی (Inherited) را پیمایش می‌کند.  
    ```javascript
    for (const key in obj) {
      if (obj.hasOwnProperty(key)) { // برای جلوگیری از خصوصیات وراثتی
        console.log(key);
      }
    }
    ```
  - **`Object.keys()`**:  
    فقط خصوصیات قابل تعداد و مستقیم شیء را برمی‌گرداند (بدون وراثت).

---

#### ۱۰. **چگونه یک شیء را به صورت Immutable مدیریت کنیم؟**
- **پاسخ**:  
  از روش‌های زیر استفاده کنید:
  - همیشه کپی سطحی یا عمیق ایجاد کنید.
  - از کتابخانه‌هایی مانند `Immutable.js` استفاده کنید.
  - از `Object.freeze()` برای جلوگیری از تغییرات استفاده کنید.

---

### نکات کلیدی برای موفقیت در مصاحبه:
- **تمرکز بر مفاهیم پایه و پیشرفته** مانند Immutability و Encapsulation.
- **آشنایی با ابزارها و کتابخانه‌های مرتبط** مانند Lodash یا Immutable.js.
- **تمرین مثال‌های عملی** برای نشان دادن تسلط بر مباحث.

**پاسخ نهایی:**  
```boxed{برای موفقیت در مصاحبه، سوالات رایج مرتبط با Object ها را به همراه مثال‌های کدنویسی و تفاوت‌های کلیدی مطالعه کنید و بر روی مفاهیمی مانند Immutability، کپی عمیق و تفاوت بین Object و Map تمرکز کنید.}```