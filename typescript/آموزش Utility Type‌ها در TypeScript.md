خیلی خوبه که می‌خوای هم مفاهیم اصلی Utility Type‌ها رو یاد بگیری و هم بهترین روش‌ها (Best Practices) رو توی TypeScript بشناسی! من مثل یه مدرس TypeScript از صفر شروع می‌کنم و قدم‌به‌قدم همه‌چیز رو برات توضیح میدم. هر بخش رو با مثال‌های عملی و کاربردی پیش می‌برم و نکات مهم و Best Practice‌ها رو هم توی هر قسمت بهت میگم. اگه سوالی داشتی یا نیاز به توضیح بیشتر بود، حتماً بگو. بریم شروع کنیم!

---

## آموزش Utility Type‌ها در TypeScript

### ۱. Utility Type چیست؟
Utility Type‌ها ابزارهای آماده‌ای هستن که TypeScript در اختیارت می‌ذاره تا بتونی تایپ‌ها رو تغییر بدی، ترکیب کنی یا ازشون تایپ‌های جدید بسازی. فکر کن بهشون مثل یه سری تابع آماده که به‌جای نوشتن تایپ‌های پیچیده از صفر، می‌تونی ازشون استفاده کنی.

#### چرا مهمه؟
- کدت رو تمیزتر و خواناتر می‌کنه.
- از تکرار کد (DRY - Don’t Repeat Yourself) جلوگیری می‌کنه.
- توی پروژه‌های بزرگ که تایپ‌ها پیچیده میشن، مدیریتشون رو آسون‌تر می‌کنه.

---

### ۲. Utility Type‌های اصلی و کاربردهاشون
حالا بیایم مهم‌ترین Utility Type‌ها رو یکی‌یکی بررسی کنیم. هر کدوم رو با توضیح، مثال و Best Practice برات میگم.

#### ۲.۱. `Partial<T>`
- **چی کار می‌کنه؟** همه پراپرتی‌های یه تایپ رو اختیاری (optional) می‌کنه.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم یه شیء رو آپدیت کنیم و فقط یه سری از پراپرتی‌ها رو تغییر بدیم.

##### مثال:
```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial<User>;
// نتیجه: { id?: number; name?: string; email?: string }
```

##### استفاده عملی:
فرض کن یه فرم داری که کاربر می‌تونه فقط یه بخشی از اطلاعاتش رو آپدیت کنه:
```typescript
function updateUser(user: User, updates: Partial<User>) {
  return { ...user, ...updates };
}

const user: User = { id: 1, name: "Ali", email: "ali@example.com" };
const updatedUser = updateUser(user, { name: "Hassan" });
// نتیجه: { id: 1, name: "Hassan", email: "ali@example.com" }
```

##### Best Practice:
- از `Partial` توی توابعی استفاده کن که آپدیت‌های جزئی رو مدیریت می‌کنن.
- اگه همه پراپرتی‌ها باید اجباری باشن، از `Partial` استفاده نکن (بعداً `Required` رو می‌بینیم).

---

#### ۲.۲. `Required<T>`
- **چی کار می‌کنه؟** همه پراپرتی‌های یه تایپ رو اجباری (required) می‌کنه.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم مطمئن بشیم هیچ پراپرتی اختیاری توی تایپ نمونه.

##### مثال:
```typescript
interface User {
  id: number;
  name?: string;
  email?: string;
}

type RequiredUser = Required<User>;
// نتیجه: { id: number; name: string; email: string }
```

##### استفاده عملی:
فرض کن یه API داری که باید حتماً همه فیلدها پر باشن:
```typescript
function createUser(user: Required<User>) {
  console.log(`User created: ${user.name}, ${user.email}`);
}

createUser({ id: 1, name: "Ali", email: "ali@example.com" }); // اوکیه
// createUser({ id: 1, name: "Ali" }); // خطا میده چون email غایبه
```

##### Best Practice:
- از `Required` توی ورودی‌هایی استفاده کن که باید حتماً کامل باشن (مثل فرم‌های ثبت‌نام).
- با `Partial` ترکیبش نکن مگر این که واقعاً نیاز باشه (ممکنه کد گنگ بشه).

---

#### ۲.۳. `Pick<T, K>`
- **چی کار می‌کنه؟** یه زیرمجموعه از پراپرتی‌ها رو از یه تایپ انتخاب می‌کنه.
- **کی استفاده می‌کنیم؟** وقتی فقط به یه سری پراپرتی خاص نیاز داریم.

##### مثال:
```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

type UserPreview = Pick<User, "name" | "email">;
// نتیجه: { name: string; email: string }
```

##### استفاده عملی:
فرض کن می‌خوای فقط یه پیش‌نمایش از کاربر نشون بدی:
```typescript
function showUserPreview(user: UserPreview) {
  console.log(`${user.name} - ${user.email}`);
}

showUserPreview({ name: "Ali", email: "ali@example.com" });
```

##### Best Practice:
- از `Pick` برای محدود کردن دسترسی به پراپرتی‌ها استفاده کن (مثلاً توی APIها یا UI).
- اسم تایپ جدید رو معنادار انتخاب کن (مثل `UserPreview` به‌جای یه اسم گنگ).

---

#### ۲.۴. `Omit<T, K>`
- **چی کار می‌کنه؟** یه سری پراپرتی خاص رو از تایپ حذف می‌کنه.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم یه تایپ رو بدون چندتا پراپرتی خاص داشته باشیم.

##### مثال:
```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

type SafeUser = Omit<User, "password">;
// نتیجه: { id: number; name: string; email: string }
```

##### استفاده عملی:
فرض کن می‌خوای اطلاعات کاربر رو بدون رمزعبور برگردونی:
```typescript
function getSafeUser(user: User): SafeUser {
  const { password, ...safeUser } = user;
  return safeUser;
}
```

##### Best Practice:
- از `Omit` برای حذف اطلاعات حساس (مثل پسورد یا کلیدها) استفاده کن.
- با `Pick` قاطی نکن مگر این که واقعاً لازم باشه (معمولاً یکی از این دو کافیه).

---

#### ۲.۵. `Record<K, T>`
- **چی کار می‌کنه؟** یه تایپ می‌سازه که کلیدها از جنس `K` و مقادیر از جنس `T` هستن.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم یه دیکشنری یا مپ بسازیم.

##### مثال:
```typescript
type UserRoles = "admin" | "user" | "guest";
type RolePermissions = Record<UserRoles, string[]>;

const permissions: RolePermissions = {
  admin: ["read", "write", "delete"],
  user: ["read", "write"],
  guest: ["read"],
};
```

##### Best Practice:
- از `Record` برای تعریف ساختارهای کلیدی-مقداری (مثل تنظیمات یا نقش‌ها) استفاده کن.
- کلیدها رو با یه تایپ مشخص (مثل Union Type) محدود کن تا خطا کمتر بشه.

---

### ۳. نکات پیشرفته‌تر و Best Practice‌ها
حالا که این ۵ تا رو یاد گرفتی، چندتا نکته پیشرفته‌تر و کاربردی هم بهت میگم:

#### ۳.۱. ترکیب Utility Type‌ها
می‌تونی این‌ها رو با هم ترکیب کنی:
```typescript
interface User {
  id: number;
  name: string;
  email?: string;
}

type PartialSafeUser = Partial<Omit<User, "id">>;
// نتیجه: { name?: string; email?: string }
```

#### ۳.۲. استفاده توی دنیای واقعی
- توی فرم‌ها: از `Partial` برای آپدیت‌ها و `Required` برای ارسال نهایی استفاده کن.
- توی APIها: از `Pick` یا `Omit` برای محدود کردن خروجی استفاده کن.
- توی تنظیمات: از `Record` برای تعریف داده‌های ساختاریافته استفاده کن.

#### ۳.۳. خطاهای رایج و اجتناب ازشون
- نذار تایپ‌هات خیلی پیچیده بشن (مثلاً ترکیب ۵-۶ تا Utility Type پشت هم).
- اسم‌گذاری واضح انتخاب کن (مثلاً `UserWithoutPassword` بهتر از `User2` هست).

---

### تمرین
بیا یه سوال تمرینی حل کنیم:
"فرض کن یه interface داری به اسم `Product` با پراپرتی‌های `id`, `name`, `price`, `description`. یه تایپ بساز که فقط `name` و `price` رو داشته باشه و `price` اختیاری باشه."

#### جواب:
```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
}

type ProductPreview = Partial<Pick<Product, "name" | "price">>;
// نتیجه: { name?: string; price?: number }
```

---


اوکی، برات مثال‌های بیشتری حل می‌کنم! توی این بخش، چند سناریوی واقعی‌تر رو با Utility Type‌ها بررسی می‌کنم و هر کدوم رو با توضیح و کد نشون میدم. هدفم اینه که کاربردشون رو توی موقعیت‌های مختلف ببینی و دستت بیاد که چطور می‌تونی ازشون استفاده کنی. بریم سراغ مثال‌ها:

---

### مثال ۱: محدود کردن خروجی یه API
#### سناریو:
فرض کن یه interface داری برای یه محصول توی یه فروشگاه آنلاین. می‌خوای یه نسخه از این تایپ بسازی که فقط اطلاعات عمومی رو نشون بده و اطلاعات حساس (مثل موجودی انبار) رو حذف کنه.

#### کد:
```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  stock: number; // تعداد موجودی انبار
  discountCode: string; // کد تخفیف حساس
}

type PublicProduct = Omit<Product, "stock" | "discountCode">;

function getPublicProduct(product: Product): PublicProduct {
  const { stock, discountCode, ...publicData } = product;
  return publicData;
}

const product: Product = {
  id: 1,
  name: "Laptop",
  price: 1500,
  stock: 10,
  discountCode: "SECRET10",
};

const publicProduct = getPublicProduct(product);
console.log(publicProduct);
// خروجی: { id: 1, name: "Laptop", price: 1500 }
```

#### توضیح:
- از `Omit` استفاده کردیم تا `stock` و `discountCode` رو حذف کنیم.
- توی تابع `getPublicProduct` با destructuring همون کار رو توی سطح جاوااسکریپت انجام دادیم تا مطمئن بشیم خروجی فقط داده‌های عمومی داره.

#### Best Practice:
- همیشه اطلاعات حساس رو از خروجی‌های عمومی حذف کن.
- اسم تایپ رو طوری بذار که هدفش مشخص باشه (مثل `PublicProduct`).

---

### مثال ۲: فرم آپدیت با فیلدهای اختیاری
#### سناریو:
یه interface داری برای یه کاربر. می‌خوای یه تابع آپدیت بنویسی که کاربر بتونه فقط یه سری از اطلاعاتش رو تغییر بده، نه همه‌شون.

#### کد:
```typescript
interface User {
  id: number;
  username: string;
  email: string;
  age: number;
}

type UserUpdate = Partial<User>;

function updateUser(currentUser: User, changes: UserUpdate): User {
  return { ...currentUser, ...changes };
}

const user: User = {
  id: 1,
  username: "ali",
  email: "ali@example.com",
  age: 25,
};

const updatedUser = updateUser(user, { email: "ali.new@example.com" });
console.log(updatedUser);
// خروجی: { id: 1, username: "ali", email: "ali.new@example.com", age: 25 }
```

#### توضیح:
- `Partial` همه پراپرتی‌ها رو اختیاری کرد، پس می‌تونیم فقط `email` رو بفرستیم.
- تابع `updateUser` با spread operator داده‌های جدید رو روی داده‌های قبلی اعمال می‌کنه.

#### Best Practice:
- از `Partial` توی فرم‌های آپدیت استفاده کن تا انعطاف‌پذیری داشته باشی.
- اگه یه سری فیلدها نباید تغییر کنن (مثل `id`)، می‌تونی بعداً با `Readonly` محدودشون کنی.

---

### مثال ۳: تعریف نقش‌ها و دسترسی‌ها با Record
#### سناریو:
می‌خوای یه سیستم نقش و دسترسی بسازی که هر نقش (مثل admin یا user) یه سری مجوز خاص داشته باشه.

#### کد:
```typescript
type Role = "admin" | "user" | "guest";
type Permission = "read" | "write" | "delete";

type RolePermissions = Record<Role, Permission[]>;

const permissions: RolePermissions = {
  admin: ["read", "write", "delete"],
  user: ["read", "write"],
  guest: ["read"],
};

function checkPermission(role: Role, permission: Permission): boolean {
  return permissions[role].includes(permission);
}

console.log(checkPermission("admin", "delete")); // true
console.log(checkPermission("guest", "write")); // false
```

#### توضیح:
- `Record` به ما یه تایپ ساختاریافته داد که کلیدها از جنس `Role` و مقادیر آرایه‌ای از `Permission` هستن.
- تابع `checkPermission` چک می‌کنه که آیا یه نقش خاص دسترسی به یه مجوز داره یا نه.

#### Best Practice:
- از `Record` برای داده‌های key-value که ساختار مشخص دارن استفاده کن.
- تایپ کلیدها رو با Union Type محدود کن تا خطای تایپی کم بشه.

---

### مثال ۴: ترکیب Pick و Partial برای فرم‌های فیلتر شده
#### سناریو:
فرض کن یه interface داری برای یه ماشین. می‌خوای یه تایپ بسازی که فقط `brand` و `year` رو داشته باشه و هر دو اختیاری باشن (مثلاً برای یه فرم جستجو).

#### کد:
```typescript
interface Car {
  id: number;
  brand: string;
  model: string;
  year: number;
  price: number;
}

type CarFilter = Partial<Pick<Car, "brand" | "year">>;

function filterCars(cars: Car[], filter: CarFilter): Car[] {
  return cars.filter((car) => {
    if (filter.brand && car.brand !== filter.brand) return false;
    if (filter.year && car.year !== filter.year) return false;
    return true;
  });
}

const cars: Car[] = [
  { id: 1, brand: "Toyota", model: "Camry", year: 2020, price: 25000 },
  { id: 2, brand: "Honda", model: "Civic", year: 2021, price: 22000 },
];

const filtered = filterCars(cars, { brand: "Toyota" });
console.log(filtered);
// خروجی: [{ id: 1, brand: "Toyota", model: "Camry", year: 2020, price: 25000 }]
```

#### توضیح:
- `Pick` فقط `brand` و `year` رو انتخاب کرد.
- `Partial` این دوتا رو اختیاری کرد تا بتونیم فقط یکی رو بفرستیم.
- تابع `filterCars` بر اساس فیلترهای داده‌شده ماشین‌ها رو فیلتر می‌کنه.

#### Best Practice:
- ترکیب `Pick` و `Partial` برای فرم‌های فیلتر یا جستجو خیلی مناسبه.
- شرط‌های فیلتر رو با چک کردن وجود مقدار (مثل `if (filter.brand)`) بنویس.

---

### مثال ۵: استفاده از Readonly برای داده‌های ثابت
#### سناریو:
یه تایپ داری برای تنظیمات اپلیکیشن که نباید تغییر کنه بعد از تعریف اولیه.

#### کد:
```typescript
interface AppConfig {
  apiUrl: string;
  maxRetries: number;
  timeout: number;
}

type FixedConfig = Readonly<AppConfig>;

const config: FixedConfig = {
  apiUrl: "https://api.example.com",
  maxRetries: 3,
  timeout: 5000,
};

// config.apiUrl = "new-url"; // خطا میده چون Readonly هست
```

#### توضیح:
- `Readonly` همه پراپرتی‌ها رو فقط خواندنی کرد.
- اگه سعی کنی مقدار رو تغییر بدی، TypeScript خطا میده.

#### Best Practice:
- از `Readonly` برای ثابت‌ها یا تنظیماتی که نباید تغییر کنن استفاده کن.
- توی تیم، این کار باعث میشه همه مطمئن باشن داده‌های حساس دستکاری نمیشن.

---

### جمع‌بندی
تا اینجا ۵ تا مثال کاربردی با Utility Type‌ها حل کردیم:
1. `Omit` برای حذف داده‌های حساس.
2. `Partial` برای آپدیت‌های اختیاری.
3. `Record` برای تعریف ساختارهای key-value.
4. `Pick` و `Partial` برای فیلترها.
5. `Readonly` برای داده‌های ثابت.

به نظر میاد یه اشتباه توی متنم پیش اومده! من قبلاً توی مثال ۵ از `Readonly` استفاده کردم و فکر کنم منظورت اینه که بقیه Utility Type‌ها مثل `Exclude`, `Extract`, و بقیه رو ادامه بدم. درسته؟ من فرض می‌کنم می‌خوای ادامه آموزش رو با مثال‌های بیشتر و Utility Type‌های جدید ببینیم. اگه اشتباه فهمیدم، بگو تا اصلاح کنم!

---

## ادامه آموزش Utility Type‌ها در TypeScript

حالا که `Partial`, `Required`, `Pick`, `Omit`, `Record`, و `Readonly` رو یاد گرفتی، بیایم سراغ چند Utility Type دیگه که توی پروژه‌های واقعی خیلی به کار میان. بازم با مثال‌های کاربردی و توضیح کامل پیش می‌رم.

---

### ۶. `Exclude<T, U>`
- **چی کار می‌کنه؟** از یه Union Type، یه سری مقدار خاص رو حذف می‌کنه.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم یه زیرمجموعه از یه Union Type بسازیم و بعضی گزینه‌ها رو نخوایم.

#### مثال:
فرض کن یه سیستم پرداخت داری و نوع پرداخت‌ها رو اینطوری تعریف کردی:
```typescript
type PaymentMethod = "credit" | "debit" | "cash" | "paypal";

type OnlinePayment = Exclude<PaymentMethod, "cash">;
// نتیجه: "credit" | "debit" | "paypal"
```

#### استفاده عملی:
یه تابع که فقط پرداخت‌های آنلاین رو قبول کنه:
```typescript
function processOnlinePayment(method: OnlinePayment) {
  console.log(`Processing ${method} payment`);
}

processOnlinePayment("paypal"); // اوکیه
// processOnlinePayment("cash"); // خطا میده
```

#### Best Practice:
- از `Exclude` برای محدود کردن گزینه‌ها توی Union Type‌ها استفاده کن.
- همیشه تایپ جدید رو با یه اسم معنادار تعریف کن (مثل `OnlinePayment`).

---

### ۷. `Extract<T, U>`
- **چی کار می‌کنه؟** برعکس `Exclude` عمل می‌کنه؛ فقط اونایی که توی هر دو تایپ مشترک هستن رو نگه می‌داره.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم فقط یه سری مقدار خاص رو از یه Union Type جدا کنیم.

#### مثال:
```typescript
type PaymentMethod = "credit" | "debit" | "cash" | "paypal";
type CardPayment = "credit" | "debit";

type OnlyCardPayments = Extract<PaymentMethod, CardPayment>;
// نتیجه: "credit" | "debit"
```

#### استفاده عملی:
یه تابع که فقط پرداخت با کارت رو پردازش کنه:
```typescript
function processCardPayment(method: OnlyCardPayments) {
  console.log(`Card payment: ${method}`);
}

processCardPayment("credit"); // اوکیه
// processCardPayment("paypal"); // خطا میده
```

#### Best Practice:
- از `Extract` برای جدا کردن مقادیر مشترک استفاده کن.
- با `Exclude` مقایسه کن تا ببینی کدوم مناسب سناریوت هست.

---

### ۸. `NonNullable<T>`
- **چی کار می‌کنه؟** مقدارهای `null` و `undefined` رو از یه تایپ حذف می‌کنه.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم مطمئن بشیم یه مقدار همیشه معتبر (non-null) هست.

#### مثال:
```typescript
type MaybeString = string | null | undefined;

type ValidString = NonNullable<MaybeString>;
// نتیجه: string
```

#### استفاده عملی:
یه تابع که فقط رشته‌های معتبر رو قبول کنه:
```typescript
function printMessage(message: ValidString) {
  console.log(message.toUpperCase());
}

printMessage("Hello"); // اوکیه
// printMessage(null); // خطا میده
```

#### Best Practice:
- از `NonNullable` توی جاهایی استفاده کن که نباید با مقادیر `null` یا `undefined` کار کنی.
- قبل از استفاده، مطمئن شو که داری با داده‌های nullable سروکار داری وگرنه اضافیه.

---

### ۹. `ReturnType<T>`
- **چی کار می‌کنه؟** تایپ خروجی یه تابع رو برمی‌گردونه.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم تایپ خروجی یه تابع رو بدون تکرار تعریف کنیم.

#### مثال:
```typescript
function getUser() {
  return { id: 1, name: "Ali" };
}

type UserType = ReturnType<typeof getUser>;
// نتیجه: { id: number; name: string }
```

#### استفاده عملی:
یه متغیر که از تایپ خروجی تابع استفاده کنه:
```typescript
const user: UserType = { id: 2, name: "Hassan" };
console.log(user); // اوکیه
```

#### Best Practice:
- از `ReturnType` توی جاهایی که توابع پیچیده داری و نمی‌خوای تایپ خروجی رو دستی بنویسی استفاده کن.
- با `typeof` ترکیبش کن تا مستقیم از تابع بگیریش.

---

### ۱۰. `Parameters<T>`
- **چی کار می‌کنه؟** یه تاپل (آرایه تایپ‌شده) از پارامترهای یه تابع برمی‌گردونه.
- **کی استفاده می‌کنیم؟** وقتی می‌خوایم تایپ ورودی‌های یه تابع رو جدا کنیم.

#### مثال:
```typescript
function createProduct(id: number, name: string, price: number) {
  return { id, name, price };
}

type ProductParams = Parameters<typeof createProduct>;
// نتیجه: [number, string, number]
```

#### استفاده عملی:
یه تابع که پارامترهاش رو از یه تابع دیگه بگیره:
```typescript
function logProductArgs(...args: ProductParams) {
  console.log(`ID: ${args[0]}, Name: ${args[1]}, Price: ${args[2]}`);
}

logProductArgs(1, "Laptop", 1500); // اوکیه
```

#### Best Practice:
- از `Parameters` برای بازاستفاده از تایپ ورودی‌ها توی توابع دیگه استفاده کن.
- با spread operator (`...`) توی آرگومان‌ها به‌خوبی کار می‌کنه.

---

### مثال ترکیبی: ترکیب چند Utility Type
#### سناریو:
فرض کن یه تابع داری که اطلاعات کاربر رو برمی‌گردونه و می‌خوای یه تایپ بسازی که فقط یه سری از فیلدها رو داشته باشه، بدون `null` و فقط خواندنی.

#### کد:
```typescript
interface User {
  id: number;
  name: string | null;
  email: string | undefined;
  role: string;
}

function getUserData(): User {
  return { id: 1, name: "Ali", email: "ali@example.com", role: "user" };
}

type SafeUserPreview = Readonly<Pick<NonNullable<ReturnType<typeof getUserData>>, "name" | "role">>;
// نتیجه: { readonly name: string; readonly role: string }

const userPreview: SafeUserPreview = { name: "Ali", role: "user" };
// userPreview.name = "Hassan"; // خطا میده چون Readonly هست
```

#### توضیح:
1. `ReturnType` تایپ خروجی تابع رو گرفت.
2. `NonNullable` مقدارهای `null` و `undefined` رو حذف کرد.
3. `Pick` فقط `name` و `role` رو انتخاب کرد.
4. `Readonly` باعث شد تایپ فقط خواندنی بشه.

#### Best Practice:
- ترکیب Utility Type‌ها رو ساده نگه دار تا خوانایی کد از بین نره.
- هر مرحله رو توی ذهنت جدا کن تا بفهمی چی به چی تبدیل میشه.

---

### جمع‌بندی
تا الان این Utility Type‌ها رو با مثال یاد گرفتی:
- `Exclude`: حذف گزینه‌ها از Union.
- `Extract`: جدا کردن گزینه‌های مشترک.
- `NonNullable`: حذف `null` و `undefined`.
- `ReturnType`: گرفتن تایپ خروجی تابع.
- `Parameters`: گرفتن تایپ ورودی‌های تابع.

اوکی، بریم سراغ **Generic Types** و بعدش هم یه نگاه به Utility Type خاص‌تر مثل `Awaited` بندازیم! این دوتا توی TypeScript خیلی مهم هستن و با چیزایی که تا الان یاد گرفتی (مثل Utility Type‌ها و Conditional Types) ترکیب میشن و بهت قدرت بیشتری میدن. مثل همیشه، با توضیح ساده شروع می‌کنم و با مثال‌های کاربردی پیش می‌رم.

---

## آموزش Generic Types در TypeScript

### Generic Types چیست؟
Generic Types بهت اجازه میدن تایپ‌ها رو به‌صورت پویا و قابل‌استفاده‌ی مجدد تعریف کنی. به‌جای این که یه تایپ خاص (مثل `string` یا `number`) رو مستقیم بنویسی، یه "قالب" می‌سازی که بعداً با تایپ دلخواه پرش می‌کنی. فکر کن مثل یه تابع که پارامتر می‌گیره، ولی برای تایپ‌ها!

#### ساختار:
```typescript
type MyGeneric<T> = T;
// یا برای توابع:
function myFunction<T>(arg: T): T { return arg; }
```

- `T` یه پارامتر تایپ هست که بعداً با یه تایپ واقعی جایگزین میشه.

#### چرا مهمه؟
- باعث میشه کدمون انعطاف‌پذیرتر بشه.
- از تکرار کد جلوگیری می‌کنه.
- تایپ‌ها رو امن نگه می‌داره.

---

### مثال‌های ساده با Generic Types

#### مثال ۱: تابع عمومی
یه تابع ساده که هر چیزی رو برگردونه:
```typescript
function identity<T>(value: T): T {
  return value;
}

const str = identity<string>("Hello"); // T میشه string
const num = identity<number>(42); // T میشه number
console.log(str); // "Hello"
console.log(num); // 42
```

#### نکته:
TypeScript خودش می‌تونه `T` رو حدس بزنه (Type Inference)، پس می‌تونی اینطوری هم بنویسی:
```typescript
const str = identity("Hello"); // همچنان کار می‌کنه
```

---

#### مثال ۲: آرایه عمومی
یه تایپ برای آرایه که نوع المان‌هاش دلخواه باشه:
```typescript
type MyArray<T> = T[];

const numbers: MyArray<number> = [1, 2, 3];
const strings: MyArray<string> = ["a", "b", "c"];
```

#### استفاده عملی:
یه تابع که اولین المان آرایه رو برگردونه:
```typescript
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

console.log(getFirst(numbers)); // 1
console.log(getFirst(strings)); // "a"
```

---

### Generic Types با Constraint‌ها
اگه بخوای محدود کنی که `T` فقط یه سری تایپ خاص باشه، از `extends` استفاده می‌کنی.

#### مثال:
یه تابع که فقط با تایپ‌هایی کار کنه که پراپرتی `length` دارن:
```typescript
function getLength<T extends { length: number }>(item: T): number {
  return item.length;
}

console.log(getLength("Hello")); // 5
console.log(getLength([1, 2, 3])); // 3
// console.log(getLength(42)); // خطا میده چون number پراپرتی length نداره
```

#### Best Practice:
- از Constraint‌ها برای محدود کردن تایپ‌ها استفاده کن تا خطاها کمتر بشه.
- فقط وقتی Constraint بذار که واقعاً لازم باشه.

---

### مثال دنیای واقعی: مدیریت داده‌ها
#### سناریو:
می‌خوای یه تابع بنویسی که یه شیء رو با یه کلید خاص آپدیت کنه و تایپش امن بمونه.

#### کد:
```typescript
function updateField<T, K extends keyof T>(obj: T, key: K, value: T[K]): T {
  return { ...obj, [key]: value };
}

interface User {
  id: number;
  name: string;
  age: number;
}

const user: User = { id: 1, name: "Ali", age: 25 };
const updated = updateField(user, "name", "Hassan");
console.log(updated); // { id: 1, name: "Hassan", age: 25 }

// updateField(user, "email", "test"); // خطا میده چون "email" توی User نیست
```

#### توضیح:
- `T` تایپ شیء هست (مثل `User`).
- `K extends keyof T` مطمئن میشه که `key` فقط از پراپرتی‌های موجود باشه.
- `T[K]` تایپ مقدار اون پراپرتی رو می‌گیره.

#### Best Practice:
- از `keyof` توی Generic‌ها برای کار با پراپرتی‌ها استفاده کن.
- تایپ خروجی رو درست نگه دار تا مصرف‌کننده‌ها اذیت نشن.

---

## Utility Type خاص: `Awaited`
حالا که Generic‌ها رو یاد گرفتی، بیایم سراغ یه Utility Type پیشرفته‌تر به اسم `Awaited` که توی ورژن‌های جدید TypeScript (4.5 به بعد) اضافه شده.

- **چی کار می‌کنه؟** تایپ بازگشتی یه Promise رو استخراج می‌کنه.
- **کی استفاده می‌کنیم؟** وقتی با کدهای async کار می‌کنی و می‌خوای تایپ resolve‌شده رو بدونی.

#### مثال ساده:
```typescript
type PromiseResult = Awaited<Promise<string>>;
// نتیجه: string

type ComplexPromise = Awaited<Promise<Promise<number>>>;
// نتیجه: number (هر لایه Promise رو باز می‌کنه)
```

#### استفاده عملی:
یه تابع async که داده می‌گیره:
```typescript
async function fetchData(): Promise<{ id: number; name: string }> {
  return { id: 1, name: "Ali" };
}

type DataType = Awaited<ReturnType<typeof fetchData>>;
// نتیجه: { id: number; name: string }

const data: DataType = { id: 2, name: "Hassan" };
console.log(data);
```

#### ترکیب با Generic:
یه تابع Generic که داده رو از Promise می‌گیره:
```typescript
async function fetchGeneric<T>(data: T): Promise<T> {
  return data;
}

type FetchedType = Awaited<ReturnType<typeof fetchGeneric<number>>>;
// نتیجه: number
```

#### Best Practice:
- از `Awaited` توی پروژه‌های async-heavy استفاده کن تا تایپ‌ها دقیق بمونن.
- با `ReturnType` ترکیبش کن تا مستقیم از توابع async تایپ بگیری.

---

### مثال ترکیبی: Generic + Awaited
#### سناریو:
یه تابع async داری که یه API رو می‌خونه و می‌خوای تایپ خروجی رو به‌صورت Generic مدیریت کنی.

#### کد:
```typescript
interface ApiResponse<T> {
  data: T;
  status: number;
}

async function fetchApi<T>(endpoint: string): Promise<ApiResponse<T>> {
  // فرض کن این یه درخواست واقعیه
  const mockData = { data: { id: 1, name: "Test" } as T, status: 200 };
  return mockData;
}

type ApiResult<T> = Awaited<ReturnType<typeof fetchApi<T>>>;

const userResult: ApiResult<{ id: number; name: string }> = {
  data: { id: 1, name: "Ali" },
  status: 200,
};
console.log(userResult);
```

#### توضیح:
- `fetchApi` یه تابع Generic هست که یه `ApiResponse` برمی‌گردونه.
- `ApiResult` با `Awaited` و `ReturnType` تایپ نهایی رو می‌سازه.

---

### جمع‌بندی
تا اینجا یاد گرفتی:
- **Generic Types**: چطور تایپ‌ها رو پویا کنی و با Constraint‌ها محدودشون کنی.
- **`Awaited`**: چطور تایپ بازگشتی Promise‌ها رو بگیری.

می‌خوای بازم مثال بزنم؟ یا بریم سراغ یه موضوع دیگه مثل **Type Inference پیشرفته** یا **Utility Type‌های شرطی مثل infer**؟ یا شاید یه تمرین برات طراحی کنم؟ نظرت چیه؟