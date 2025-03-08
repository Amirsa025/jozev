 برای استفاده از promise‌ها در Next.js 15 در سناریوهای مختلف، راه‌های متنوعی وجود دارد. Next.js 15 با معماری React Server Components، استفاده از promise‌ها را آسان‌تر کرده است. بیایید به چند سناریوی مهم بپردازیم:

## 1. در سمت سرور (Server Components)

```javascript
// app/page.js (Server Component)
async function getData() {
  const res = await fetch('https://api.example.com/data')
  
  if (!res.ok) {
    throw new Error('Failed to fetch data')
  }
  
  return res.json()
}

export default async function Page() {
  const data = await getData()
  
  return (
    <div>
      <h1>داده‌های دریافتی:</h1>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  )
}
```

در Server Components می‌توانید مستقیماً از await استفاده کنید بدون اینکه نیاز به then() یا catch() داشته باشید.

## 2. در پردازش فرم‌ها (Server Actions)

```javascript
// app/actions.js
'use server'

export async function submitForm(formData) {
  try {
    const name = formData.get('name')
    const email = formData.get('email')
    
    const response = await fetch('https://api.example.com/submit', {
      method: 'POST',
      body: JSON.stringify({ name, email }),
      headers: { 'Content-Type': 'application/json' }
    })
    
    if (!response.ok) {
      return { error: 'مشکلی پیش آمد' }
    }
    
    return { success: true }
  } catch (error) {
    return { error: error.message }
  }
}

// app/form.js (Client Component)
'use client'

import { submitForm } from './actions'
import { useFormState } from 'react-dom'

export default function Form() {
  const [state, formAction] = useFormState(submitForm, {})
  
  return (
    <form action={formAction}>
      <input name="name" placeholder="نام" />
      <input name="email" placeholder="ایمیل" />
      <button type="submit">ارسال</button>
      {state.error && <p>خطا: {state.error}</p>}
      {state.success && <p>با موفقیت ارسال شد!</p>}
    </form>
  )
}
```

## 3. در کامپوننت‌های سمت کلاینت (Client Components)

```javascript
// app/client-page.js
'use client'

import { useState, useEffect } from 'react'

export default function ClientPage() {
  const [data, setData] = useState(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)
  
  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('/api/data')
        if (!response.ok) {
          throw new Error('خطای شبکه')
        }
        const result = await response.json()
        setData(result)
      } catch (err) {
        setError(err.message)
      } finally {
        setLoading(false)
      }
    }
    
    fetchData()
  }, [])
  
  if (loading) return <p>در حال بارگذاری...</p>
  if (error) return <p>خطا: {error}</p>
  
  return (
    <div>
      <h1>داده‌ها:</h1>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  )
}
```

## 4. در API Routes

```javascript
// app/api/data/route.js
export async function GET() {
  try {
    // دریافت داده از دیتابیس یا API خارجی
    const response = await fetch('https://external-api.com/data')
    const data = await response.json()
    
    return Response.json(data)
  } catch (error) {
    return Response.json({ error: error.message }, { status: 500 })
  }
}
```

## 5. متدهای پارالل (همزمان) با Promise.all

```javascript
// app/dashboard/page.js
export default async function Dashboard() {
  // دریافت چند منبع داده به صورت همزمان
  const [userData, postsData, commentsData] = await Promise.all([
    fetch('https://api.example.com/user').then(res => res.json()),
    fetch('https://api.example.com/posts').then(res => res.json()),
    fetch('https://api.example.com/comments').then(res => res.json())
  ])
  
  return (
    <div>
      <UserProfile userData={userData} />
      <PostsList posts={postsData} />
      <CommentSection comments={commentsData} />
    </div>
  )
}
```

## 6. استفاده از Suspense برای لودینگ

```javascript
// app/page.js
import { Suspense } from 'react'
import Posts from './posts'

export default function Page() {
  return (
    <div>
      <h1>صفحه اصلی</h1>
      <Suspense fallback={<p>در حال بارگذاری مطالب...</p>}>
        <Posts />
      </Suspense>
    </div>
  )
}

// app/posts.js
async function getPosts() {
  const res = await fetch('https://api.example.com/posts')
  if (!res.ok) throw new Error('خطا در دریافت پست‌ها')
  return res.json()
}

export default async function Posts() {
  const posts = await getPosts()
  
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```

آیا سناریوی خاصی هست که بیشتر به آن علاقه دارید تا جزئیات بیشتری ارائه دهم؟

==========================================================
توی Next.js 15، استفاده از متدهای استاتیک `Promise` مثل `Promise.all`، `Promise.race`، `Promise.any` و `Promise.resolve` می‌تونه توی سناریوهای مختلف خیلی به مدیریت عملیات غیرهمزمان کمک کنه. چون Next.js 15 بیشتر به سمت معماری غیرهمزمان (asynchronous) حرکت کرده—مثل تبدیل `params` و `searchParams` به پرامیس‌ها—این متدها خیلی کاربردی‌تر شدن. من اینجا چند سناریوی رایج توی پروژه‌های Next.js رو با مثال توضیح می‌دم که چطور ازشون استفاده کنی.

---

### **1. سناریو: دریافت همزمان داده‌ها از چند API (Promise.all)**
#### توضیح:
توی یه صفحه Next.js، مثلاً یه داشبورد، ممکنه نیاز داشته باشی داده‌های مختلفی (مثل اطلاعات کاربر، آمار فروش، و اعلان‌ها) رو از APIهای مختلف بگیری و فقط وقتی همه آماده شدن، صفحه رو رندر کنی.

#### کد (Server Component):
```javascript
// app/dashboard/page.js
export default async function DashboardPage() {
  const fetchUser = fetch('https://api.example.com/user').then(res => res.json());
  const fetchSales = fetch('https://api.example.com/sales').then(res => res.json());
  const fetchNotifications = fetch('https://api.example.com/notifications').then(res => res.json());

  const [user, sales, notifications] = await Promise.all([fetchUser, fetchSales, fetchNotifications]);

  return (
    <div>
      <h1>خوش آمدی، {user.name}</h1>
      <p>فروش امروز: {sales.total}</p>
      <ul>
        {notifications.map((note, index) => (
          <li key={index}>{note.message}</li>
        ))}
      </ul>
    </div>
  );
}
```

#### نکته:
- چون Next.js 15 از Server Components پشتیبانی می‌کنه و این کامپوننت‌ها به‌صورت پیش‌فرض غیرهمزمان هستن، می‌تونی مستقیماً `await` رو توی تابع صفحه استفاده کنی.
- `Promise.all` اینجا تضمین می‌کنه که تا وقتی همه درخواست‌ها کامل نشن، رندرینگ شروع نمی‌شه. اگه یکی خطا بده، با `try/catch` می‌تونی مدیریتش کنی.

---

### **2. سناریو: رقابت بین منابع مختلف (Promise.race)**
#### توضیح:
فرض کن توی یه اپلیکیشن پخش ویدیو، می‌خوای سریع‌ترین سرور رو برای استریم پیدا کنی. اینجا `Promise.race` به کار میاد که اولین پاسخ موفق رو برگردونه.

#### کد (API Route):
```javascript
// app/api/stream/route.js
export async function GET() {
  const server1 = fetch('https://server1.example.com/stream').then(res => res.json());
  const server2 = fetch('https://server2.example.com/stream').then(res => res.json());

  const fastestResponse = await Promise.race([server1, server2]);

  return Response.json({ url: fastestResponse.streamUrl });
}
```

#### نکته:
- توی API Routeهای Next.js 15، می‌تونی از `Promise.race` برای سناریوهای زمان‌حساس استفاده کنی.
- اگه بخوای تایم‌اوت اضافه کنی، یه پرامیس با `setTimeout` بساز که بعد از مدت مشخصی رد بشه:
```javascript
const timeout = new Promise((_, reject) => setTimeout(() => reject('Timeout'), 5000));
const fastestResponse = await Promise.race([server1, server2, timeout]);
```

---

### **3. سناریو: پیدا کردن اولین پاسخ موفق (Promise.any)**
#### توضیح:
توی یه سیستم پرداخت، ممکنه چند درگاه پرداخت داشته باشی و بخوای اولین درگاهی که با موفقیت پاسخ می‌ده رو انتخاب کنی، حتی اگه بقیه خطا بدن.

#### کد (Server Component):
```javascript
// app/payment/page.js
export default async function PaymentPage() {
  const gateway1 = fetch('https://gateway1.com/check').then(res => res.json());
  const gateway2 = fetch('https://gateway2.com/check').then(res => res.json());

  const firstSuccess = await Promise.any([gateway1, gateway2]);

  return (
    <div>
      <h1>پرداخت از طریق: {firstSuccess.name}</h1>
      <a href={firstSuccess.url}>پرداخت کن</a>
    </div>
  );
}
```

#### نکته:
- `Promise.any`
- توی Next.js 15 برای سناریوهای fault-tolerant (تحمل خطا) عالیه.
- اگه همه پرامیس‌ها reject بشن، باید با `try/catch` خطای `AggregateError` رو مدیریت کنی.

---

### **4. سناریو: استفاده از کش یا API (Promise.resolve)**
#### توضیح:
توی یه صفحه محصول، می‌خوای اگه داده توی کش بود فوراً برگردونی، وگرنه از API بگیری. `Promise.resolve` اینجا به کار میاد.

#### کد (Server Component):
```javascript
// app/product/[id]/page.js
import { getCachedProduct } from '@/lib/cache';

export default async function ProductPage({ params }) {
  const { id } = await params; // params در Next.js 15 یه پرامیسه
  const cachedProduct = getCachedProduct(id);

  const productPromise = cachedProduct
    ? Promise.resolve(cachedProduct)
    : fetch(`https://api.example.com/product/${id}`).then(res => res.json());

  const product = await productPromise;

  return (
    <div>
      <h1>{product.name}</h1>
      <p>قیمت: {product.price}</p>
    </div>
  );
}
```

#### نکته:
- چون `params` توی Next.js 15 یه پرامیسه، باید اول `await` بشه.
- `Promise.resolve` بهت کمک می‌کنه یه مقدار ثابت رو به یه پرامیس تبدیل کنی و توی زنجیره غیرهمزمان استفاده کنی.

---

### **5. سناریو: مدیریت خطا و بهینه‌سازی در پروژه‌های بزرگ**
#### توضیح:
توی پروژه‌های لارج‌اسکیل، ممکنه بخوای چندین درخواست رو بفرستی و خطاها رو جداگانه مدیریت کنی، مثلاً برای یه لیست از محصولات.

#### کد (Server Component با Promise.allSettled):
```javascript
// app/products/page.js
export default async function ProductsPage() {
  const productIds = [1, 2, 3, 4];
  const promises = productIds.map(id =>
    fetch(`https://api.example.com/product/${id}`).then(res => res.json())
  );

  const results = await Promise.allSettled(promises);

  const products = results
    .filter(result => result.status === 'fulfilled')
    .map(result => result.value);

  const errors = results
    .filter(result => result.status === 'rejected')
    .map(result => result.reason);

  return (
    <div>
      <h1>محصولات</h1>
      <ul>
        {products.map((product, index) => (
          <li key={index}>{product.name}</li>
        ))}
      </ul>
      {errors.length > 0 && (
        <p>خطاها: {errors.map((err, i) => <span key={i}>{err.message}</span>)}</p>
      )}
    </div>
  );
}
```

#### نکته:
- `Promise.allSettled` توی Next.js 15 برای وقتی که نمی‌خوای یه خطا کل عملیات رو خراب کنه، خیلی مناسبه.
- می‌تونی داده‌های موفق رو جدا کنی و خطاها رو جداگانه به کاربر نشون بدی.

---

### **چطور توی Client Component استفاده کنیم؟**
اگه توی یه Client Component هستی (با `"use client"`)، باید از هوک‌هایی مثل `useEffect` یا `use` (اگه از React 19 استفاده می‌کنی) کمک بگیری:

#### مثال (Client Component):
```javascript
// components/ClientDashboard.js
"use client";
import { useEffect, useState } from 'react';

export default function ClientDashboard() {
  const [data, setData] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      const [user, sales] = await Promise.all([
        fetch('/api/user').then(res => res.json()),
        fetch('/api/sales').then(res => res.json()),
      ]);
      setData({ user, sales });
    };
    fetchData();
  }, []);

  if (!data) return <p>در حال بارگذاری...</p>;

  return (
    <div>
      <h1>کاربر: {data.user.name}</h1>
      <p>فروش: {data.sales.total}</p>
    </div>
  );
}
```

---

### **جمع‌بندی و نکات مهم در Next.js 15**
1. **Server Components:** توی این کامپوننت‌ها مستقیماً از `await` و متدهای `Promise` استفاده کن، چون به‌صورت پیش‌فرض غیرهمزمان هستن.
2. **Client Components:** از هوک‌ها برای مدیریت پرامیس‌ها استفاده کن.
3. **API Routes:** برای منطق سمت سرور، متدهای `Promise` رو توی توابع `GET`، `POST` و ... اعمال کن.
4. **بهینه‌سازی:** برای پروژه‌های بزرگ، از کش (مثل `node-cache`) یا `Promise.allSettled` برای مدیریت خطاها استفاده کن.



