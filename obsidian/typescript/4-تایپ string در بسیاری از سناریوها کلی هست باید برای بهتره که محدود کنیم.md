استفاده از تایپ `string` به صورت کلی در TypeScript می‌تواند منجر به فقدان دقت و امنیت در کد شود، زیرا هر رشته‌ای ممکن است قبول شود. برای جلوگیری از این مشکل، می‌توانید تایپ‌های محدود‌تر (Narrow Types) تعریف کنید که تنها مقادیر خاصی را پذیرفته شوند.

### چرا تایپ `string` کلی است؟
وقتی از `string` استفاده می‌کنید، هر رشته‌ای معتبر است، حتی اگر فقط چندین مقدار خاص مورد نیاز باشند. این موضوع می‌تواند منجر به اشتباهات در زمان اجرا شود.

---

## روش‌های محدود کردن تایپ `string`

### 1. **استفاده از Union Types**
برای محدود کردن مقادیر مجاز، می‌توانید از **Union Types** استفاده کنید. این روش به شما اجازه می‌دهد تا فقط چندین مقدار خاص را به عنوان ورودی قبول کنید.

#### مثال:
فرض کنید یک تابع دارید که تنها باید مقادیر `"active"`, `"inactive"`, یا `"pending"` را قبول کند.

```typescript
type Status = "active" | "inactive" | "pending";

function setStatus(status: Status): void {
    console.log(`Status set to: ${status}`);
}

// استفاده صحیح
setStatus("active"); // ✅

// استفاده نادرست
setStatus("unknown"); // ❌ Error: Argument of type '"unknown"' is not assignable to parameter of type 'Status'.
```

---

### 2. **استفاده از Enum**
اگر لیستی از مقادیر ثابت دارید که می‌خواهید محدود کنید، می‌توانید از **Enum** استفاده کنید. Enum‌ها مجموعه‌ای از مقادیر ثابت هستند که به آنها می‌توانید اشاره کنید.

#### مثال:
```typescript
enum Color {
    Red = "red",
    Green = "green",
    Blue = "blue",
}

function setBackgroundColor(color: Color): void {
    console.log(`Background color set to: ${color}`);
}

// استفاده صحیح
setBackgroundColor(Color.Red); // ✅

// استفاده نادرست
setBackgroundColor("yellow"); // ❌ Error: Argument of type '"yellow"' is not assignable to parameter of type 'Color'.
```

---

### 3. **استفاده از Template Literal Types**
اگر می‌خواهید مقادیر خاصی را بر اساس الگوی خاصی محدود کنید، می‌توانید از **Template Literal Types** استفاده کنید. این روش برای مواردی که مقدار باید شامل یک الگوی خاص باشد، مفید است.

#### مثال:
فرض کنید نام‌های متغیرها باید با `"user_"` شروع شوند.

```typescript
type UserName = `user_${string}`;

function validateUserName(name: UserName): void {
    console.log(`Valid username: ${name}`);
}

// استفاده صحیح
validateUserName("user_123"); // ✅

// استفاده نادرست
validateUserName("admin_456"); // ❌ Error: Argument of type '"admin_456"' is not assignable to parameter of type 'UserName'.
```

---

### 4. **استفاده از Regular Expressions با Custom Validators**
اگر بخواهید یک رشته را بر اساس یک الگوی خاص اعتبارسنجی کنید، می‌توانید از توابع اعتبارسنجی استفاده کنید.

#### مثال:
```typescript
function isValidEmail(email: string): email is string {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
}

function processEmail(email: string): void {
    if (!isValidEmail(email)) {
        throw new Error("Invalid email format");
    }
    console.log(`Processing email: ${email}`);
}

// استفاده صحیح
processEmail("example@example.com"); // ✅

// استفاده نادرست
processEmail("invalid-email"); // ❌ Throws an error: Invalid email format
```

---

### 5. **استفاده از Branding Types**
برای جلوگیری از اشتباهات ناشی از استفاده از تایپ‌های کلی، می‌توانید از **Branding Types** استفاده کنید. این روش به شما اجازه می‌دهد تا تایپ‌هایی را تعریف کنید که از لحاظ ساختاری یکسان هستند، اما از لحاظ معنایی متفاوت هستند.

#### مثال:
```typescript
type UserId = string & { __brand: "UserId" };
type ProductId = string & { __brand: "ProductId" };

function getUserById(id: UserId): void {
    console.log(`Fetching user with ID: ${id}`);
}

function getProductById(id: ProductId): void {
    console.log(`Fetching product with ID: ${id}`);
}

// استفاده صحیح
const userId: UserId = "123" as UserId;
getUserById(userId); // ✅

const productId: ProductId = "456" as ProductId;
getProductById(productId); // ✅

// استفاده نادرست
getUserById(productId); // ❌ Error: Argument of type 'ProductId' is not assignable to parameter of type 'UserId'.
```

---

### نتیجه‌گیری:
محدود کردن تایپ `string` به مقادیر خاص یا الگوهای مشخص، کد شما را امن‌تر، دقیق‌تر و قابل حفاظت‌تر می‌کند. روش‌های مختلفی مانند **Union Types**, **Enum**, **Template Literal Types**, و **Branding Types** وجود دارند که می‌توانید بر اساس نیاز خود از آنها استفاده کنید. 

با این روش‌ها، می‌توانید اطمینان حاصل کنید که ورودی‌های نامعتبر به کد شما وارد نشوند و عملکرد برنامه شما بهبود یابد. 😊