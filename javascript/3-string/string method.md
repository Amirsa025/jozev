خوشحالم که خوشت اومد! بله، قطعاً متدهای مهم دیگه‌ای برای String در جاوااسکریپت وجود دارن که توی لیستت نبودن، اما خیلی کاربردی‌ان و توی برنامه‌نویسی روزمره زیاد استفاده می‌شن. من چندتا از مهم‌هاشون رو که فکر می‌کنم باید بشناسی، با مثال و نکات بهترین روش (best practices) برات مرور می‌کنم:

---

### 1. `toLowerCase()` و `toUpperCase()`
- **توضیح:** رشته رو به حروف کوچک یا بزرگ تبدیل می‌کنه.
- **مثال:**
  ```javascript
  const str = "Hello World";
  console.log(str.toLowerCase()); // "hello world"
  console.log(str.toUpperCase()); // "HELLO WORLD"
  ```
- **Best Practice:**
  - برای مقایسه رشته‌ها بدون حساسیت به حروف (case-insensitive) خیلی کاربردیه:
    ```javascript
    if (str.toLowerCase().includes("hello")) { ... }
    ```
  - قبل از ذخیره یا پردازش داده‌های متنی که قراره استاندارد باشن (مثل ایمیل) ازش استفاده کن.

---

### 2. `trim()`, `trimStart()`, `trimEnd()`
- **توضیح:** فاصله‌های خالی (whitespace) رو از ابتدا و انتهای رشته (یا فقط ابتدا/انتها) حذف می‌کنه.
- **مثال:**
  ```javascript
  const str = "  Hello World  ";
  console.log(str.trim());      // "Hello World"
  console.log(str.trimStart()); // "Hello World  "
  console.log(str.trimEnd());   // "  Hello World"
  ```
- **Best Practice:**
  - همیشه ورودی‌های کاربر (مثل فرم‌ها) رو با `trim` تمیز کن تا از خطاهای ناخواسته جلوگیری بشه.
  - از `trimStart` و `trimEnd` وقتی استفاده کن که فقط یه طرف رشته رو بخوای تمیز کنی.

---

### 3. `replace(searchValue, newValue)` و `replaceAll(searchValue, newValue)`
- **توضیح:** یه زیررشته (یا الگو) رو با مقدار جدید جایگزین می‌کنه. `replace` فقط اولین مورد رو عوض می‌کنه، `replaceAll` همه موارد رو.
- **مثال:**
  ```javascript
  const str = "I like cats, cats are great!";
  console.log(str.replace("cats", "dogs"));     // "I like dogs, cats are great!"
  console.log(str.replaceAll("cats", "dogs"));  // "I like dogs, dogs are great!"
  console.log(str.replace(/cats/g, "dogs"));    // با regex مثل replaceAll عمل می‌کنه
  ```
- **Best Practice:**
  - برای جایگزینی ساده و تک‌موردی از `replace` استفاده کن.
  - برای جایگزینی همه موارد، `replaceAll` خواناتر از regex با flag `g` هست (مخصوصاً از ES2021 به بعد).
  - اگه با regex کار می‌کنی، مراقب کاراکترهای خاص (مثل `.` یا `*`) باشن که نیاز به escape دارن.

---

### 4. `split(separator)`
- **توضیح:** رشته رو با استفاده از جداکننده مشخص‌شده به آرایه‌ای از زیررشته‌ها تقسیم می‌کنه.
- **مثال:**
  ```javascript
  const str = "apple,banana,orange";
  console.log(str.split(","));     // ["apple", "banana", "orange"]
  console.log(str.split(""));      // ["a", "p", "p", "l", "e", ...] (هر کاراکتر)
  console.log(str.split(",", 2));  // ["apple", "banana"] (محدود به 2 مورد)
  ```
- **Best Practice:**
  - برای پارس کردن داده‌های متنی (مثل CSV) عالیه.
  - اگه می‌خوای تعداد زیررشته‌ها رو محدود کنی، از پارامتر دوم (limit) استفاده کن.
  - بعد از `split` معمولاً با متدهای آرایه مثل `map` یا `forEach` کار می‌کنی.

---

### 5. `indexOf(substring)` و `lastIndexOf(substring)`
- **توضیح:** شاخص اولین یا آخرین رخداد یه زیررشته رو برمی‌گردونه. اگه پیدا نشه، -1 برمی‌گردونه.
- **مثال:**
  ```javascript
  const str = "Hello World Hello";
  console.log(str.indexOf("Hello"));    // 0 (اولین رخداد)
  console.log(str.lastIndexOf("Hello")); // 12 (آخرین رخداد)
  console.log(str.indexOf("xyz"));      // -1 (پیدا نشد)
  ```
- **Best Practice:**
  - برای پیدا کردن موقعیت یه زیررشته خوبه، ولی اگه فقط می‌خوای وجودش رو چک کنی، `includes` خواناتر و مستقیم‌تره.
  - حساس به حروف هست، پس قبلش ممکنه نیاز به `toLowerCase` داشته باشی.

---

### 6. `substring(start, end)`
- **توضیح:** بخشی از رشته رو از شاخص شروع تا پایان (اختیاری) برمی‌گردونه. پایان شامل نمی‌شه.
- **مثال:**
  ```javascript
  const str = "Hello World";
  console.log(str.substring(0, 5)); // "Hello"
  console.log(str.substring(6));    // "World"
  ```
- **Best Practice:**
  - شبیه `slice` هست، ولی اعداد منفی رو پشتیبانی نمی‌کنه و اگه `start > end` باشه، جاهاشون رو عوض می‌کنه (برخلاف `slice` که نتیجه خالی می‌ده).
  - معمولاً `slice` ترجیح داده می‌شه چون انعطاف‌پذیرتره، مگر اینکه رفتار خاص `substring` رو بخوای.

---

### 7. `startsWith(substring)` و `endsWith(substring)`
- **توضیح:** چک می‌کنه که آیا رشته با زیررشته مشخص‌شده شروع یا تموم می‌شه (true/false).
- **مثال:**
  ```javascript
  const str = "Hello World";
  console.log(str.startsWith("Hello")); // true
  console.log(str.endsWith("World"));   // true
  console.log(str.startsWith("World")); // false
  ```
- **Best Practice:**
  - برای اعتبارسنجی یا بررسی ساختار رشته (مثل پیشوند/پسوند فایل) خیلی مناسبه.
  - مثل `includes`، جایگزین خواناتری برای `indexOf` در موارد خاصه.

---

### نکات تکمیلی:
- این متدها همگی پرکاربردن و توی اکثر پروژه‌ها به کار میان، مخصوصاً `split`، `replace`، و `trim`.
- اگه با داده‌های متنی زیاد کار می‌کنی (مثل پارس کردن یا تمیز کردن ورودی‌ها)، اینا ابزارهای اصلی‌ات می‌شن.
- متدهای جدیدتر مثل `replaceAll` یا `startsWith` توی مرورگرهای قدیمی‌تر ممکنه پشتیبانی نشن، پس اگه پروژه‌ات به polyfill نیاز داره، چک کن.

اگه بخوای روی یکی از اینا عمیق‌تر بریم یا متد دیگه‌ای رو اضافه کنم (مثل `padStart`، `padEnd`، یا `search`)، بگو! نظرت چیه؟