من مقاله‌ی «Fetching Data» از مستندات Next.js رو خوندم و حالا می‌خوام تمام نکات مهمش رو به زبان خیلی ساده و با مثال‌های کاربردی برای پروژه‌های بزرگ (Large-Scale) توضیح بدم. هدف اینه که حتی اگه تازه‌کار باشی، بتونی راحت بفهمی و توی پروژه‌های واقعی ازش استفاده کنی.

---

### **چرا باید داده‌ها رو درست Fetch کنیم؟**
توی هر اپلیکیشن، گرفتن داده‌ها (مثل اطلاعات کاربرها، پست‌ها یا محصولات) یه بخش اصلیه. Next.js بهت کمک می‌کنه این کار رو به روش‌های مختلف انجام بدی: هم توی سرور (Server) و هم توی کلاینت (Client). حالا بیایم ببینیم چطور می‌تونی این کار رو ساده و بهینه انجام بدی.

---

### **1. گرفتن داده‌ها توی Server Components**
توی Next.js، یه روش خفن اینه که داده‌ها رو مستقیم توی سرور بگیری. این کار باعث می‌شه:

- **سرعت بیشتر بشه**: چون کلاینت منتظر نمی‌مونه و داده‌ها آماده‌ست.
- **امنیت بیشتر بشه**: چون کلیدهای API یا اطلاعات حساس توی کلاینت نشون داده نمی‌شن.

#### **مثال ساده:**
فرض کن یه وب‌سایت فروشگاهی داری و می‌خوای لیست محصولات رو نشون بدی.

```javascript
export default async function ProductsPage() {
  const response = await fetch("https://api.my-shop.com/products");
  const products = await response.json();

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>{product.name} - ${product.price}</li>
      ))}
    </ul>
  );
}
```

- **چطوری کار می‌کنه؟**: اینجا تابع `fetch` رو توی سرور اجرا می‌کنی. Next.js صبر می‌کنه تا داده‌ها بیاد، بعد HTML رو آماده می‌کنه و برای کاربر می‌فرسته.
- **برای پروژه بزرگ**: اگه یه فروشگاه بزرگ داری (مثل دیجی‌کالا)، می‌تونی از این روش برای گرفتن لیست محصولات از دیتابیس یا API استفاده کنی بدون اینکه کاربر منتظر بمونه.

---

### **2. گرفتن داده‌ها با ORM یا دیتابیس**
اگه از دیتابیس (مثل PostgreSQL) یا ORM (مثل Prisma) استفاده می‌کنی، می‌تونی مستقیم توی Server Component داده‌ها رو بگیری.

#### **مثال کاربردی:**
فرض کن یه پنل مدیریت داری و می‌خوای سفارش‌ها رو نشون بدی.

```javascript
import { db } from "@/lib/db"; // دیتابیست رو اینجا وارد می‌کنی

export default async function OrdersPage() {
  const orders = await db.order.findMany(); // گرفتن همه سفارش‌ها

  return (
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>مشتری</th>
          <th>مبلغ</th>
        </tr>
      </thead>
      <tbody>
        {orders.map((order) => (
          <tr key={order.id}>
            <td>{order.id}</td>
            <td>{order.customerName}</td>
            <td>{order.amount}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

- **چرا خوبه؟**: توی پروژه‌های بزرگ که دیتابیس داری (مثل یه سیستم ERP)، این روش بهت اجازه می‌ده مستقیم با دیتابیس کار کنی و نیازی به API اضافی نداشته باشی.

---

### **3. گرفتن داده‌ها توی Client Components**
بعضی وقت‌ها باید داده‌ها رو توی کلاینت بگیری، مثلاً وقتی کاربر یه فیلتر اعمال می‌کنه یا چیزی رو جستجو می‌کنه. اینجا از هوک‌های React یا کتابخونه‌هایی مثل `useSWR` استفاده می‌کنی.

#### **مثال ساده:**
فرض کن توی یه وبلاگ، کاربر می‌خواد پست‌ها رو بر اساس دسته‌بندی فیلتر کنه.

```javascript
"use client"; // این خط می‌گه این کامپوننت توی کلاینت اجرا می‌شه
import useSWR from "swr";

const fetcher = (url) => fetch(url).then((res) => res.json());

export default function BlogPage({ category }) {
  const { data, error, isLoading } = useSWR(
    `https://api.my-blog.com/posts?category=${category}`,
    fetcher
  );

  if (isLoading) return <div>در حال بارگذاری...</div>;
  if (error) return <div>خطا: {error.message}</div>;

  return (
    <ul>
      {data.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

- **برای پروژه بزرگ**: توی یه پلتفرم مثل اینستاگرام، می‌تونی از این روش برای بارگذاری پست‌ها بر اساس فیلترهای کاربر (مثل هشتگ‌ها) استفاده کنی. `useSWR` خودش داده‌ها رو کش (cache) می‌کنه و تجربه کاربری رو بهتر می‌کنه.

---

### **4. استریم کردن داده‌ها (Streaming)**
اگه داده‌ها زیاد باشن یا گرفتن‌شون طول بکشه، می‌تونی صفحه رو تکه‌تکه بارگذاری کنی. این کار با `<Suspense>` و فایل `loading.js` انجام می‌شه.

#### **مثال ساده:**
فرض کن یه داشبورد داری که چند بخش داره (مثل آمار فروش و لیست مشتری‌ها).

```javascript
import { Suspense } from "react";

async function fetchSales() {
  const response = await fetch("https://api.my-shop.com/sales");
  return await response.json();
}

function SalesComponent() {
  const sales = fetchSales(); // اینجا await نمی‌کنیم
  return <div>فروش کل: {sales.total}</div>;
}

export default function DashboardPage() {
  return (
    <div>
      <h1>خوش آمدید به داشبورد</h1>
      <Suspense fallback={<div>در حال بارگذاری فروش...</div>}>
        <SalesComponent />
      </Suspense>
    </div>
  );
}
```

- **فایل loading.js**:
```javascript
export default function Loading() {
  return <div>لطفاً صبر کنید...</div>;
}
```

- **چطوری کار می‌کنه؟**: وقتی کاربر میاد توی صفحه، اول «خوش آمدید» رو می‌بینه و بخش فروش تکه‌تکه بارگذاری می‌شه.
- **برای پروژه بزرگ**: توی یه سیستم CRM (مدیریت ارتباط با مشتری)، می‌تونی آمارها، لیست مشتری‌ها و گزارش‌ها رو جدا جدا بارگذاری کنی تا کاربر منتظر نمونه.

---

### **5. نکات مهم برای پروژه‌های بزرگ**
- **کش کردن (Caching)**: Next.js به صورت پیش‌فرض داده‌های `fetch` رو کش می‌کنه. اگه نمی‌خوای، از `{ cache: 'no-store' }` استفاده کن.
  - مثال: `fetch("https://api.my-shop.com/data", { cache: 'no-store' })`
- **بهینه‌سازی سرعت**: اگه چند تا درخواست داری (مثل محصولات و دسته‌بندی‌ها)، با `Promise.all` همه رو همزمان بگیر:
  ```javascript
  const [products, categories] = await Promise.all([
    fetch("https://api.my-shop.com/products").then((res) => res.json()),
    fetch("https://api.my-shop.com/categories").then((res) => res.json()),
  ]);
  ```
- **مدیریت خطا**: همیشه خطاها رو چک کن:
  ```javascript
  const response = await fetch("https://api.my-shop.com/products");
  if (!response.ok) throw new Error("خطا در گرفتن داده‌ها");
  ```

---

### **جمع‌بندی به زبان ساده**
- **Server Components**: داده‌ها رو توی سرور بگیر، سریع و امنه (مثل لیست محصولات).
- **Client Components**: داده‌ها رو توی کلاینت بگیر، برای چیزای پویا (مثل فیلترها).
- **Streaming**: صفحه رو تکه‌تکه نشون بده، کاربر معطل نشه (مثل داشبورد).
- **بهینه‌سازی**: کش کن، خطاها رو مدیریت کن، و درخواست‌ها رو موازی کن.

من مقاله‌ی «Updating Data» از مستندات Next.js رو خوندم و حالا می‌خوام نکات مهمش رو به زبان خیلی خیلی ساده و با مثال‌های کاربردی برای پروژه‌های بزرگ (Large-Scale) توضیح بدم. هدف اینه که آپدیت کردن داده‌ها رو توی اپلیکیشن‌های Next.js راحت بفهمی و بتونی توی پروژه‌های واقعی ازش استفاده کنی.

---

### **چرا آپدیت کردن داده‌ها مهمه؟**
توی هر اپلیکیشن، کاربرها باید بتونن داده‌ها رو تغییر بدن (مثل اضافه کردن محصول، ویرایش پروفایل یا حذف یه پست). Next.js بهت ابزارهایی می‌ده که این کار رو هم توی سرور و هم توی کلاینت انجام بدی، به شکلی که سریع و بهینه باشه.

---

### **1. آپدیت داده‌ها با Server Actions**
Server Actions یه روش جدید و خفنه که توی Next.js می‌تونی با یه تابع ساده توی سرور داده‌ها رو تغییر بدی. این کار نیازی به API جداگانه نداره و مستقیم توی سرور اجرا می‌شه.

#### **مثال ساده:**
فرض کن یه فرم داری که کاربر می‌تونه باهاش اسم محصول رو تغییر بده.

```javascript
// app/actions.js
"use server"; // این خط می‌گه این کد توی سرور اجرا می‌شه

import { db } from "@/lib/db";

export async function updateProductName(productId, newName) {
  await db.product.update({
    where: { id: productId },
    data: { name: newName },
  });
}
```

```javascript
// app/products/[id]/page.js
import { updateProductName } from "@/app/actions";

export default function ProductPage({ params }) {
  return (
    <form action={async (formData) => {
      "use server"; // اینم توی سرور اجرا می‌شه
      const newName = formData.get("name");
      await updateProductName(params.id, newName);
    }}>
      <input type="text" name="name" placeholder="اسم جدید محصول" />
      <button type="submit">به‌روزرسانی</button>
    </form>
  );
}
```

- **چطوری کار می‌کنه؟**: کاربر اسم جدید رو وارد می‌کنه، فرم به Server Action می‌ره و دیتابیس آپدیت می‌شه.
- **برای پروژه بزرگ**: توی یه فروشگاه آنلاین مثل آمازون، می‌تونی از این روش برای ویرایش سریع مشخصات محصول (مثل قیمت یا توضیحات) استفاده کنی بدون اینکه API جدا بزنی.

---

### **2. آپدیت داده‌ها با API Routes**
اگه بخوای از روش قدیمی‌تر استفاده کنی، می‌تونی API بسازی و از کلاینت درخواست بفرستی.

#### **مثال کاربردی:**
فرض کن توی یه پلتفرم شبکه اجتماعی، کاربر می‌خواد بیوگرافیش رو آپدیت کنه.

```javascript
// app/api/update-bio/route.js
import { db } from "@/lib/db";

export async function POST(request) {
  const { userId, bio } = await request.json();
  await db.user.update({
    where: { id: userId },
    data: { bio },
  });
  return new Response("بیوگرافی آپدیت شد", { status: 200 });
}
```

```javascript
// app/profile/page.js
"use client";

export default function ProfilePage() {
  async function handleSubmit(event) {
    event.preventDefault();
    const bio = event.target.bio.value;
    await fetch("/api/update-bio", {
      method: "POST",
      body: JSON.stringify({ userId: "123", bio }),
      headers: { "Content-Type": "application/json" },
    });
    alert("بیوگرافی آپدیت شد!");
  }

  return (
    <form onSubmit={handleSubmit}>
      <textarea name="bio" placeholder="بیوگرافی جدید" />
      <button type="submit">ذخیره</button>
    </form>
  );
}
```

- **چرا خوبه؟**: توی پروژه‌های بزرگ که سیستم‌های قدیمی داری (مثل یه CMS)، می‌تونی با API‌ها کار کنی و کم‌کم به Server Actions مهاجرت کنی.

---

### **3. بازسازی داده‌ها با revalidatePath**
بعد از آپدیت داده‌ها، باید مطمئن شی که صفحه کاربر هم آپدیت می‌شه. Next.js با `revalidatePath` این کار رو آسون کرده.

#### **مثال ساده:**
فرض کن یه لیست وظایف (To-Do List) داری و وقتی یه وظیفه رو کامل می‌کنی، لیست باید آپدیت بشه.

```javascript
// app/actions.js
"use server";

import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";

export async function completeTask(taskId) {
  await db.task.update({
    where: { id: taskId },
    data: { completed: true },
  });
  revalidatePath("/tasks"); // این خط کش صفحه رو آپدیت می‌کنه
}
```

```javascript
// app/tasks/page.js
import { completeTask } from "@/app/actions";

export default async function TasksPage() {
  const tasks = await db.task.findMany();

  return (
    <ul>
      {tasks.map((task) => (
        <li key={task.id}>
          {task.title}
          {!task.completed && (
            <form action={completeTask}>
              <input type="hidden" name="taskId" value={task.id} />
              <button type="submit">کامل شد</button>
            </form>
          )}
        </li>
      ))}
    </ul>
  );
}
```

- **برای پروژه بزرگ**: توی یه سیستم مدیریت پروژه مثل Trello، وقتی یه کار رو انجام‌شده می‌کنی، این روش باعث می‌شه بقیه اعضای تیم هم سریع تغییرات رو ببینن.

---

### **4. بازسازی داده‌ها با revalidateTag**
اگه از `fetch` استفاده می‌کنی و داده‌ها رو کش کردی، می‌تونی با تگ‌ها مدیریتشون کنی.

#### **مثال کاربردی:**
فرض کن یه داشبورد داری که آمار فروش رو نشون می‌ده.

```javascript
// app/actions.js
"use server";

import { revalidateTag } from "next/cache";

export async function updateSales(newData) {
  // فرض می‌کنیم یه API برای آپدیت دیتا داریم
  await fetch("https://api.my-shop.com/sales", {
    method: "POST",
    body: JSON.stringify(newData),
  });
  revalidateTag("sales"); // کش مربوط به تگ "sales" آپدیت می‌شه
}
```

```javascript
// app/dashboard/page.js
export default async function DashboardPage() {
  const sales = await fetch("https://api.my-shop.com/sales", {
    next: { tags: ["sales"] }, // این تگ به fetch اختصاص داده می‌شه
  }).then((res) => res.json());

  return <div>فروش کل: {sales.total}</div>;
}
```

- **چطوری کار می‌کنه؟**: بعد از آپدیت، فقط داده‌هایی که تگ "sales" دارن دوباره بارگذاری می‌شن.
- **برای پروژه بزرگ**: توی یه سیستم مالی بزرگ، می‌تونی آمارهای مختلف (مثل فروش، سود، هزینه) رو با تگ‌های جداگانه مدیریت کنی.

---

### **5. نکات مهم برای پروژه‌های بزرگ**
- **ترکیب سرور و کلاینت**: از Server Actions برای کارهای اصلی (مثل آپدیت دیتابیس) و از کلاینت برای تجربه کاربری پویا (مثل فرم‌ها) استفاده کن.
- **مدیریت خطا**: همیشه خطاها رو چک کن:
  ```javascript
  "use server";
  export async function updateProduct(formData) {
    try {
      const name = formData.get("name");
      if (!name) throw new Error("اسم نمی‌تونه خالی باشه");
      await db.product.update({ where: { id: 1 }, data: { name } });
    } catch (error) {
      return { error: error.message };
    }
  }
  ```
- **بهینه‌سازی**: اگه چندین صفحه باید آپدیت بشن، از `revalidatePath` با مسیرهای خاص استفاده کن، نه کل سایت.

---

### **جمع‌بندی به زبان ساده**
- **Server Actions**: داده‌ها رو توی سرور آپدیت کن، ساده و سریع (مثل ویرایش محصول).
- **API Routes**: از کلاینت درخواست بفرست، برای سیستم‌های قدیمی (مثل تغییر بیوگرافی).
- **revalidatePath**: صفحه رو بعد از آپدیت بازسازی کن (مثل لیست وظایف).
- **revalidateTag**: داده‌های کش‌شده رو آپدیت کن (مثل آمار فروش).

این روش‌ها توی پروژه‌های بزرگ مثل فروشگاه‌ها، شبکه‌های اجتماعی یا سیستم‌های مدیریت خیلی به‌دردت می‌خورن. اگه سوال دیگه‌ای داری، بگو تا بیشتر توضیح بدم!








این روش‌ها توی پروژه‌های بزرگ مثل فروشگاه‌های آنلاین، سیستم‌های مدیریت یا شبکه‌های اجتماعی خیلی کاربردی‌ان و باعث می‌شن هم سرعت بالا بره، هم کاربر راضی بمونه! اگه سوال دیگه‌ای داری، بگو تا بیشتر توضیح بدم.

با توجه به اطلاعاتی که از دو مقاله‌ی «Fetching Data» و «Updating Data» توی مستندات Next.js برات توضیح دادم، حالا می‌خوام چند تا **Best Practice** (بهترین روش‌ها) رو به زبان ساده و با مثال‌های کاربردی برای پروژه‌های بزرگ (Large-Scale) بهت بگم. این‌ها باعث می‌شن کدهات تمیزتر، سریع‌تر و قابل‌نگهداری‌تر بشن.

---

### **1. استفاده از Server Components برای گرفتن داده‌های اولیه**
**چرا؟** چون گرفتن داده‌ها توی سرور هم سرعت رو بالا می‌بره، هم امنیت رو (کلیدهای API توی کلاینت نشون داده نمی‌شن).

#### **Best Practice:**
همیشه داده‌های اولیه‌ی صفحه (مثل لیست محصولات یا اطلاعات کاربر) رو توی Server Components بگیر و فقط برای تعاملات پویا (مثل فیلتر کردن) به کلاینت برو.

#### **مثال برای پروژه بزرگ:**
فرض کن یه پلتفرم فروش آنلاین داری (مثل دیجی‌کالا). لیست محصولات رو توی سرور بگیر:

```javascript
// app/products/page.js
export default async function ProductsPage() {
  const products = await fetch("https://api.my-shop.com/products", {
    cache: "force-cache", // کش کردن برای سرعت بیشتر
  }).then((res) => res.json());

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>{product.name} - ${product.price}</li>
      ))}
    </ul>
  );
}
```

- **چرا توی پروژه بزرگ خوبه؟**: اگه میلیون‌ها کاربر داری، این روش بار روی کلاینت رو کم می‌کنه و SEO رو هم بهتر می‌کنه.

---

### **2. ترکیب Server Actions و revalidatePath برای آپدیت‌های سریع**
**چرا؟** Server Actions بهت اجازه می‌ده بدون API جداگانه داده‌ها رو آپدیت کنی و `revalidatePath` مطمئن می‌شه کاربر همیشه داده‌های به‌روز رو می‌بینه.

#### **Best Practice:**
برای هر عملیات آپدیت (مثل ویرایش، حذف یا اضافه کردن)، یه Server Action بنویس و بعدش کش مرتبط رو با `revalidatePath` آپدیت کن.

#### **مثال برای پروژه بزرگ:**
فرض کن توی یه سیستم مدیریت انبار، می‌خوای موجودی یه محصول رو آپدیت کنی:

```javascript
// app/actions.js
"use server";
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";

export async function updateStock(productId, newStock) {
  await db.product.update({
    where: { id: productId },
    data: { stock: newStock },
  });
  revalidatePath("/inventory"); // لیست موجودی دوباره بارگذاری می‌شه
}
```

```javascript
// app/inventory/[id]/page.js
import { updateStock } from "@/app/actions";

export default function ProductStock({ params }) {
  return (
    <form action={async (formData) => {
      "use server";
      const stock = formData.get("stock");
      await updateStock(params.id, Number(stock));
    }}>
      <input type="number" name="stock" placeholder="موجودی جدید" />
      <button type="submit">به‌روزرسانی</button>
    </form>
  );
}
```

- **چرا توی پروژه بزرگ خوبه؟**: توی سیستم‌های انبارداری بزرگ، این روش باعث می‌شه همه کارمندها همیشه موجودی به‌روز رو ببینن بدون نیاز به رفرش دستی.

---

### **3. استفاده از Streaming و Suspense برای تجربه کاربری بهتر**
**چرا؟** اگه داده‌ها زیاد باشن یا گرفتن‌شون طول بکشه، با Streaming می‌تونی صفحه رو تکه‌تکه نشون بدی و کاربر منتظر نمونه.

#### **Best Practice:**
هر بخش از صفحه رو جداگانه با `<Suspense>` لود کن و یه فال‌بک (مثل "در حال بارگذاری") بذار.

#### **مثال برای پروژه بزرگ:**
فرض کن یه داشبورد CRM داری که آمار فروش و لیست مشتری‌ها رو نشون می‌ده:

```javascript
// app/dashboard/page.js
import { Suspense } from "react";

async function SalesStats() {
  const sales = await fetch("https://api.my-crm.com/sales").then((res) =>
    res.json()
  );
  return <div>فروش کل: {sales.total}</div>;
}

async function CustomerList() {
  const customers = await fetch("https://api.my-crm.com/customers").then((res) =>
    res.json()
  );
  return (
    <ul>
      {customers.map((c) => (
        <li key={c.id}>{c.name}</li>
      ))}
    </ul>
  );
}

export default function DashboardPage() {
  return (
    <div>
      <h1>داشبورد</h1>
      <Suspense fallback={<div>در حال بارگذاری آمار...</div>}>
        <SalesStats />
      </Suspense>
      <Suspense fallback={<div>در حال بارگذاری مشتری‌ها...</div>}>
        <CustomerList />
      </Suspense>
    </div>
  );
}
```

- **چرا توی پروژه بزرگ خوبه؟**: توی یه CRM با هزاران مشتری، کاربر می‌تونه سریع داشبورد رو ببینه و منتظر لود کامل همه‌چیز نمونه.

---

### **4. مدیریت کش با تگ‌ها برای آپدیت‌های هدفمند**
**چرا؟** توی پروژه‌های بزرگ، نمی‌خوای کل کش سایت رو هر بار آپدیت کنی. با تگ‌ها فقط بخش‌های لازم رو آپدیت می‌کنی.

#### **Best Practice:**
برای هر نوع داده (مثل محصولات، سفارش‌ها، کاربران) یه تگ خاص بذار و فقط همون تگ رو بعد از آپدیت بازسازی کن.

#### **مثال برای پروژه بزرگ:**
فرض کن توی یه پلتفرم مثل اینستاگرام، کاربر یه پست رو لایک می‌کنه:

```javascript
// app/actions.js
"use server";
import { revalidateTag } from "next/cache";

export async function likePost(postId) {
  await fetch("https://api.my-social.com/like", {
    method: "POST",
    body: JSON.stringify({ postId }),
  });
  revalidateTag(`post-${postId}`); // فقط کش اون پست خاص آپدیت می‌شه
}
```

```javascript
// app/posts/[id]/page.js
export default async function PostPage({ params }) {
  const post = await fetch(`https://api.my-social.com/posts/${params.id}`, {
    next: { tags: [`post-${params.id}`] }, // تگ اختصاصی برای این پست
  }).then((res) => res.json());

  return (
    <div>
      <h1>{post.title}</h1>
      <form action={likePost}>
        <input type="hidden" name="postId" value={params.id} />
        <button type="submit">لایک ({post.likes})</button>
      </form>
    </div>
  );
}
```

- **چرا توی پروژه بزرگ خوبه؟**: توی شبکه‌های اجتماعی با میلیون‌ها پست، این روش باعث می‌شه فقط داده‌های مرتبط آپدیت بشن و سرور الکی مشغول نشه.

---

### **5. مدیریت خطاها و تجربه کاربری در کلاینت و سرور**
**چرا؟** توی پروژه‌های بزرگ، خطاها اجتناب‌ناپذیرن. باید هم توی سرور و هم کلاینت درست مدیریتشون کنی تا کاربر سردرگم نشه.

#### **Best Practice:**
توی Server Actions خطاها رو برگردون و توی کلاینت با هوک‌هایی مثل `useSWR` یا پیام‌های ساده به کاربر نشون بده.

#### **مثال برای پروژه بزرگ:**
فرض کن توی یه سیستم بانکی، کاربر می‌خواد تراکنش ثبت کنه:

```javascript
// app/actions.js
"use server";
import { db } from "@/lib/db";

export async function createTransaction(formData) {
  try {
    const amount = Number(formData.get("amount"));
    if (amount <= 0) throw new Error("مبلغ باید مثبت باشه");
    await db.transaction.create({ data: { amount } });
    return { success: true };
  } catch (error) {
    return { error: error.message };
  }
}
```

```javascript
// app/transactions/page.js
"use client";
import { useState } from "react";
import { createTransaction } from "@/app/actions";

export default function TransactionPage() {
  const [message, setMessage] = useState("");

  async function handleSubmit(formData) {
    const result = await createTransaction(formData);
    if (result.error) setMessage(`خطا: ${result.error}`);
    else setMessage("تراکنش با موفقیت ثبت شد");
  }

  return (
    <form action={handleSubmit}>
      <input type="number" name="amount" placeholder="مبلغ" />
      <button type="submit">ثبت تراکنش</button>
      {message && <p>{message}</p>}
    </form>
  );
}
```

- **چرا توی پروژه بزرگ خوبه؟**: توی سیستم‌های مالی که خطا می‌تونه گرون تموم بشه، این روش هم امنیت رو بالا می‌بره، هم کاربر رو راهنمایی می‌کنه.

---

### **جمع‌بندی بهترین روش‌ها**
1. **Server Components**: داده‌های اولیه رو توی سرور بگیر (مثل لیست محصولات).
2. **Server Actions + revalidatePath**: آپدیت‌ها رو سریع و هدفمند انجام بده (مثل موجودی انبار).
3. **Streaming**: صفحه رو تکه‌تکه نشون بده (مثل داشبورد CRM).
4. **تگ‌های کش**: فقط داده‌های لازم رو آپدیت کن (مثل لایک پست).
5. **مدیریت خطا**: خطاها رو توی سرور و کلاینت درست مدیریت کن (مثل تراکنش بانکی).

این روش‌ها توی پروژه‌های بزرگ مثل فروشگاه‌ها، شبکه‌های اجتماعی یا سیستم‌های مدیریت، هم سرعت رو بالا می‌برن، هم کدت رو تمیز و قابل‌مقیاس نگه می‌دارن. اگه بخوای روی یه بخش بیشتر زوم کنیم، بگو!