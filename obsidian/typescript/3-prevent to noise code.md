
در زبان برنامه‌نویسی **TypeScript**، کد نویز (Code Noise) به هر چیزی اشاره دارد که باعث می‌شود کد خوانا و قابل فهم نباشد. این شامل استفاده بیش از حد از تایپ‌ها، پارامترهای غیرضروری، و یا ساختارهای پیچیده است. در ادامه راهکارهایی برای جلوگیری از کد نویز در TypeScript آورده شده است:

---

### 1. **استفاده از Generic Types**
وقتی مجبورید تایپ‌های مشابه را در چندین مکان تعریف کنید، از **Generic Types** استفاده کنید. این روش کمک می‌کند تا کد شما کوتاه‌تر و قابل بازیافت‌تر شود.

#### مثال:
بدون Generic:
```typescript
function getIdAsString(id: number): string {
    return id.toString();
}

function getNameAsString(name: string): string {
    return name;
}
```

با Generic:
```typescript
function convertToString<T>(value: T): string {
    return value.toString();
}
```

---

### 2. **استفاده از Type Aliases یا Interfaces**
برای مدیریت بهتر تایپ‌ها، از `type` یا `interface` استفاده کنید. این روش کمک می‌کند تا تایپ‌های پیچیده را به صورت خوانا و مرتب تعریف کنید.

#### مثال:
بدون Type Alias:
```typescript
const user: { name: string; age: number; isActive: boolean } = {
    name: "Ali",
    age: 25,
    isActive: true,
};
```

با Type Alias:
```typescript
type User = {
    name: string;
    age: number;
    isActive: boolean;
};

const user: User = {
    name: "Ali",
    age: 25,
    isActive: true,
};
```

---

### 3. **از اضافه‌کردن تایپ‌های اجباری خودداری کنید**
وقتی نوع داده به طور واضح مشخص است، لازم نیست آن را دوباره تایپ کنید. TypeScript معمولاً می‌تواند نوع داده را به طور خودکار تشخیص دهد.

#### مثال:
بدون نیاز به تایپ:
```typescript
let count = 0; // TypeScript متوجه می‌شود که نوع count عدد است.
count++;
```

---

### 4. **استفاده از Utility Types**
TypeScript ابزارهایی مثل `Partial`, `Readonly`, `Pick`, و `Omit` را فراهم کرده است که می‌توانید از آنها برای ساده‌سازی تایپ‌ها استفاده کنید.

#### مثال:
```typescript
interface User {
    name: string;
    age: number;
    email: string;
}

// فقط بعضی از ویژگی‌ها را انتخاب کنید
type UserProfile = Pick<User, "name" | "email">;

// یک تایپ فقط-خواندنی تولید کنید
type ReadonlyUser = Readonly<User>;
```

---

### 5. **از Nested Types خودداری کنید**
هنگامی که تایپ‌های پیچیده و تو در تو وجود دارند، کد می‌تواند بسیار پیچیده شود. برای حل این مشکل، از تایپ‌های کوچکتر و جداگانه استفاده کنید.

#### مثال:
بدون ساده‌سازی:
```typescript
type Config = {
    database: {
        host: string;
        port: number;
        username: string;
        password: string;
    };
};
```

با ساده‌سازی:
```typescript
type DatabaseConfig = {
    host: string;
    port: number;
    username: string;
    password: string;
};

type Config = {
    database: DatabaseConfig;
};
```

---

### 6. **استفاده از Default Parameters**
وقتی تابع‌ها پارامترهای اختیاری دارند، از پارامترهای پیش‌فرض استفاده کنید تا کد خواناتر شود.

#### مثال:
بدون پارامتر پیش‌فرض:
```typescript
function greet(name?: string) {
    if (!name) {
        name = "Guest";
    }
    console.log(`Hello, ${name}!`);
}
```

با پارامتر پیش‌فرض:
```typescript
function greet(name: string = "Guest") {
    console.log(`Hello, ${name}!`);
}
```

---

### 7. **کاهش استفاده از Any**
استفاده از `any` می‌تواند منجر به کاهش دقت در تایپ‌ها شود. به جای استفاده از `any`، سعی کنید تایپ‌های دقیق‌تری تعریف کنید.

#### مثال:
بدون تایپ دقیق:
```typescript
function process(data: any) {
    console.log(data);
}
```

با تایپ دقیق:
```typescript
function process(data: string | number) {
    console.log(data);
}
```

---

### 8. **استفاده از JSDoc برای توضیحات**
اگر تایپ‌های پیچیده‌ای دارید که نمی‌توانید آنها را ساده کنید، از JSDoc برای اضافه کردن توضیحات استفاده کنید.

#### مثال:
```typescript
/**
 * این تابع یک آرایه از اعداد را می‌گیرد و مجموع آنها را محاسبه می‌کند.
 * @param numbers - آرایه اعداد
 * @returns مجموع اعداد
 */
function sum(numbers: number[]): number {
    return numbers.reduce((acc, num) => acc + num, 0);
}
```

---

### 9. **استفاده از Linters و Formatters**
ابزارهایی مانند **ESLint** و **Prettier** می‌توانند کمک کنند تا کد شما به صورت یکنواخت و خوانا باشد. این ابزارها می‌توانند از استفاده بیش از حد از تایپ‌ها یا ساختارهای پیچیده جلوگیری کنند.

---

### 10. **مدیریت بزرگی فایل‌ها**
وقتی فایل‌های شما بزرگ می‌شوند، سعی کنید تایپ‌ها و توابع خود را به فایل‌های جداگانه تقسیم کنید. این روش کمک می‌کند تا هر فایل تنها به تایپ‌های مربوط به خود محدود شود.

#### مثال:
```typescript
// types/user.ts
export type User = {
    name: string;
    age: number;
};

// services/userService.ts
import { User } from "../types/user";

function createUser(user: User): void {
    console.log("User created:", user);
}
```

---




========================================================




### 1. **استفاده از Type Annotations به صورت هوشمندانه**
   - TypeScript به شما امکان می‌دهد نوع‌ها را به صورت صریح تعیین کنید، اما گاهی اوقات این کار می‌تواند باعث ایجاد کد نویز شود. اگر TypeScript بتواند نوع را به صورت خودکار استنتاج کند، نیازی به تعیین صریح نوع نیست.
   ```typescript
   // غیرضروری
   let age: number = 25;

   // بهتر
   let age = 25;
   ```

### 2. **استفاده از Interface و Type Aliases**
   - برای تعریف ساختار داده‌ها از `interface` یا `type` استفاده کنید تا کد شما تمیزتر و قابل فهم‌تر شود.
   ```typescript
   interface User {
       id: number;
       name: string;
       email: string;
   }

   const user: User = {
       id: 1,
       name: "John Doe",
       email: "john@example.com"
   };
   ```

### 3. **اجتناب از Any Type**
   - استفاده از `any` باعث از دست رفتن مزایای TypeScript می‌شود. به جای آن سعی کنید از نوع‌های دقیق‌تر استفاده کنید.
   ```typescript
   // بد
   function printValue(value: any) {
       console.log(value);
   }

   // بهتر
   function printValue(value: string | number) {
       console.log(value);
   }
   ```

### 4. **استفاده از توابع کمکی (Utility Functions)**
   - اگر کد تکراری دارید، آن را در یک تابع کمکی قرار دهید تا از تکرار جلوگیری کنید.
   ```typescript
   // تکراری
   const squared = (x: number) => x * x;
   const cubed = (x: number) => x * x * x;

   // بهتر
   const power = (x: number, exponent: number) => Math.pow(x, exponent);
   const squared = (x: number) => power(x, 2);
   const cubed = (x: number) => power(x, 3);
   ```

### 5. **استفاده از Enum یا Union Types**
   - برای مقادیر ثابت از `enum` یا `union types` استفاده کنید تا کد شما خوانا و قابل نگهداری باشد.
   ```typescript
   // بد
   const status = 1; // 1 = Active, 2 = Inactive

   // بهتر
   enum Status {
       Active = 1,
       Inactive = 2
   }

   const status: Status = Status.Active;
   ```

### 6. **استفاده از Optional Chaining و Nullish Coalescing**
   - برای جلوگیری از کد نویز در بررسی‌های `null` یا `undefined`، از Optional Chaining (`?.`) و Nullish Coalescing (`??`) استفاده کنید.
   ```typescript
   // بد
   const name = user && user.profile && user.profile.name;

   // بهتر
   const name = user?.profile?.name ?? "Unknown";
   ```

### 7. **تفکیک مسئولیت‌ها (Separation of Concerns)**
   - کد خود را به بخش‌های کوچک‌تر و مستقل تقسیم کنید. هر تابع یا کلاس باید یک مسئولیت واحد داشته باشد.

### 8. **استفاده از Linting و Formatting Tools**
   - از ابزارهایی مانند ESLint و Prettier برای اعمال قوانین کد نویسی و فرمت‌دهی خودکار استفاده کنید. این ابزارها به شما کمک می‌کنند کد تمیزتر و سازگارتری بنویسید.

### 9. **استفاده از Generics**
   - برای جلوگیری از تکرار کد در توابع یا کلاس‌هایی که با انواع مختلف داده کار می‌کنند، از Generics استفاده کنید.
   ```typescript
   function identity<T>(arg: T): T {
       return arg;
   }

   const output = identity<string>("Hello");
   ```

### 10. **استفاده از Decorators (در صورت نیاز)**
   - اگر از فریم‌ورک‌هایی مانند Angular استفاده می‌کنید، Decorators می‌توانند به شما کمک کنند تا کد را تمیزتر و سازمان‌یافته‌تر کنید.

### 11. **اجتناب از Over-Engineering**
   - سعی کنید کد را ساده نگه دارید و از پیچیدگی‌های غیرضروری اجتناب کنید. گاهی اوقات راه‌حل‌های ساده‌تر بهتر از راه‌حل‌های پیچیده هستند.

### 12. **مستندسازی کد**
   - از کامنت‌ها و مستندات مناسب استفاده کنید، اما از کامنت‌های غیرضروری که فقط کد نویز ایجاد می‌کنند، اجتناب کنید.

با رعایت این روش‌ها، می‌توانید کد TypeScript خود را تمیزتر، خوانا‌تر و قابل نگهداری‌تر کنید.