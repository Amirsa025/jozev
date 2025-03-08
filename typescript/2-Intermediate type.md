در زبان برنامه‌نویسی **TypeScript**، مفهوم **Intermediate Types** (یا تایپ‌های میانی) به تایپ‌هایی اشاره دارد که در طول پردازش یا تبدیل داده‌ها به وجود می‌آیند و معمولاً فقط برای مراحل خاصی استفاده می‌شوند. این تایپ‌ها می‌توانند در فرآیندهایی مانند تبدیل داده‌ها بین فرمت‌های مختلف، پردازش API، یا آماده‌سازی داده‌ها برای نمایش استفاده شوند.

### چرا Intermediate Types مهم هستند؟
1. **بهبود قابلیت حفاظت از داده‌ها:** با استفاده از Intermediate Types، می‌توانید مطمئن شوید که داده‌ها به صورت صحیح تبدیل می‌شوند و نقض تایپ‌ها را جلوگیری کنید.
2. **کاهش پیچیدگی کد:** Intermediate Types به شما اجازه می‌دهند تا تایپ‌های پیچیده را به تایپ‌های ساده‌تر تقسیم کنید و آنها را به صورت مرحله‌ای مدیریت کنید.
3. **قابلیت تست و اصلاح:** این تایپ‌ها به شما کمک می‌کنند تا بخش‌های مختلف کد را جداگانه تست کنید و مشکلات را سریع‌تر شناسایی کنید.

---

## مثال‌های Intermediate Types

### 1. **در پردازش API**
وقتی از یک API داده‌ها دریافت می‌کنید، ممکن است ساختار داده‌هایی که API ارسال می‌کند، با ساختار داده‌هایی که در برنامه شما مورد استفاده قرار می‌گیرد، متفاوت باشد. در اینجا می‌توانید از یک Intermediate Type استفاده کنید.

#### مثال:
```typescript
// ساختار داده‌های دریافتی از API
interface ApiUserResponse {
    id: number;
    first_name: string;
    last_name: string;
    email: string;
}

// ساختار داده‌هایی که در برنامه ما استفاده می‌شود
interface User {
    userId: number;
    fullName: string;
    contact: string;
}

// Intermediate Type برای پردازش داده‌ها
type IntermediateUser = {
    id: number;
    name: string;
    email: string;
};

// تابع برای تبدیل داده‌های API به Intermediate Type
function convertToIntermediate(user: ApiUserResponse): IntermediateUser {
    return {
        id: user.id,
        name: `${user.first_name} ${user.last_name}`,
        email: user.email,
    };
}

// تابع برای تبدیل Intermediate Type به ساختار نهایی
function convertToFinal(user: IntermediateUser): User {
    return {
        userId: user.id,
        fullName: user.name,
        contact: user.email,
    };
}

// مثال استفاده
const apiData: ApiUserResponse = {
    id: 1,
    first_name: "John",
    last_name: "Doe",
    email: "john.doe@example.com",
};

const intermediateData = convertToIntermediate(apiData);
const finalData = convertToFinal(intermediateData);

console.log(finalData); // { userId: 1, fullName: "John Doe", contact: "john.doe@example.com" }
```

---

### 2. **در پردازش فرم‌ها**
هنگامی که از فرم‌های HTML استفاده می‌کنید، ممکن است داده‌های ورودی کاربر به صورت خام دریافت شود. Intermediate Types می‌توانند کمک کنند تا داده‌ها را پیش از ذخیره‌سازی یا ارسال به سرور، پاکسازی و تبدیل کنید.

#### مثال:
```typescript
// ساختار داده‌های ورودی فرم
interface RawFormData {
    username: string;
    password: string;
    age: string; // مقدار ورودی به صورت رشته است
}

// ساختار داده‌های نهایی
interface CleanedUserData {
    username: string;
    password: string;
    age: number;
}

// Intermediate Type برای پردازش داده‌ها
type IntermediateFormData = {
    username: string;
    password: string;
    age: number | null;
};

// تابع برای تبدیل داده‌های خام به Intermediate Type
function cleanFormData(rawData: RawFormData): IntermediateFormData {
    const age = parseInt(rawData.age, 10);
    return {
        username: rawData.username.trim(),
        password: rawData.password.trim(),
        age: isNaN(age) ? null : age,
    };
}

// تابع برای تبدیل Intermediate Type به ساختار نهایی
function finalizeUserData(data: IntermediateFormData): CleanedUserData | null {
    if (!data.age) {
        console.error("Invalid age");
        return null;
    }
    return {
        username: data.username,
        password: data.password,
        age: data.age,
    };
}

// مثال استفاده
const rawForm: RawFormData = {
    username: "  john_doe  ",
    password: "p@ssw0rd",
    age: "25",
};

const intermediateData = cleanFormData(rawForm);
const finalData = finalizeUserData(intermediateData);

if (finalData) {
    console.log(finalData); // { username: "john_doe", password: "p@ssw0rd", age: 25 }
}
```

---

### 3. **در پردازش آرایه‌ها**
وقتی آرایه‌ای از داده‌ها را پردازش می‌کنید، ممکن است بخواهید قبل از رسیدن به ساختار نهایی، داده‌ها را به یک فرمت میانی تبدیل کنید.

#### مثال:
```typescript
// ساختار داده‌های خام
interface RawItem {
    id: number;
    title: string;
    price: string; // قیمت به صورت رشته است
}

// ساختار داده‌های نهایی
interface FinalItem {
    id: number;
    title: string;
    price: number;
}

// Intermediate Type برای پردازش داده‌ها
type IntermediateItem = {
    id: number;
    title: string;
    price: number | null;
};

// تابع برای تبدیل داده‌های خام به Intermediate Type
function parseItems(items: RawItem[]): IntermediateItem[] {
    return items.map(item => ({
        id: item.id,
        title: item.title.trim(),
        price: parseFloat(item.price),
    }));
}

// تابع برای تبدیل Intermediate Type به ساختار نهایی
function finalizeItems(items: IntermediateItem[]): FinalItem[] {
    return items
        .filter(item => item.price !== null)
        .map(item => ({
            id: item.id,
            title: item.title,
            price: item.price!,
        }));
}

// مثال استفاده
const rawItems: RawItem[] = [
    { id: 1, title: "  Item 1  ", price: "19.99" },
    { id: 2, title: "Item 2", price: "invalid" },
];

const intermediateItems = parseItems(rawItems);
const finalItems = finalizeItems(intermediateItems);

console.log(finalItems); // [{ id: 1, title: "Item 1", price: 19.99 }]
```

---

### نکات مهم:
4. **استفاده از Utility Types:** در Intermediate Types می‌توانید از Utility Types مثل `Partial`, `Omit`, و `Pick` استفاده کنید تا تایپ‌های پیچیده را ساده‌سازی کنید.
5. **اجتناب از Over-Engineering:** Intermediate Types باید فقط برای موارد لازم استفاده شوند. استفاده بیش از حد از آنها می‌تواند منجر به افزایش پیچیدگی کد شود.
6. **نام‌گذاری مناسب:** به Intermediate Types نام‌هایی بدهید که معناشناسی داشته باشند و نقش آنها را روشن کنند.

با استفاده از Intermediate Types، می‌توانید کد خود را خواناتر، قابل حفاظت‌تر و مقیاس‌پذیرتر کنید.