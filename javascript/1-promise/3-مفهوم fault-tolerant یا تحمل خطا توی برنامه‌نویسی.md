مفهوم **fault-tolerant** یا **تحمل خطا** توی برنامه‌نویسی و سیستم‌های کامپیوتری به این معناست که یه سیستم یا برنامه بتونه در برابر خطاها، خرابی‌ها یا مشکلات غیرمنتظره مقاوم باشه و به کارش ادامه بده، بدون اینکه کامل از کار بیفته. توی زمینه کار با پرامیس‌ها در جاوااسکریپت و Next.js، این یعنی کدت طوری طراحی بشه که اگه یه بخش (مثلاً یه درخواست API) شکست خورد، کل سیستم متوقف نشه و بتونه با داده‌های موجود یا یه راه‌حل جایگزین به کارش ادامه بده.

---

### **چرا تحمل خطا مهمه؟**
توی پروژه‌های واقعی، مخصوصاً پروژه‌های لارج‌اسکیل، همیشه احتمال خطا وجود داره:
- سرور API ممکنه قطع بشه.
- شبکه ممکنه کند یا ناپایدار باشه.
- یه منبع داده ممکنه خراب یا در دسترس نباشه.

اگه سیستم تحمل خطا نداشته باشه، یه خطای کوچک می‌تونه کل برنامه رو از کار بندازه. تحمل خطا کمک می‌کنه تجربه کاربری بهتر بشه و برنامه پایدارتر بمونه.

---

### **مثال ساده از تحمل خطا با Promiseها**
فرض کن داری از سه API مختلف داده می‌گیری. اگه از `Promise.all` استفاده کنی و یکی از درخواست‌ها شکست بخوره، کل عملیات رد می‌شه و چیزی به کاربر نشون داده نمی‌شه. اما با طراحی fault-tolerant، می‌تونی خطاها رو مدیریت کنی و داده‌های موجود رو نمایش بدی.

#### بدون تحمل خطا (با Promise.all):
```javascript
async function getData() {
  const [data1, data2, data3] = await Promise.all([
    fetch('https://api1.com').then(res => res.json()),
    fetch('https://api2.com').then(res => res.json()),
    fetch('https://api3.com').then(res => res.json()), // این سرور خرابه
  ]);
  return { data1, data2, data3 };
}

getData()
  .then(console.log)
  .catch(err => console.error('همه چیز خراب شد:', err));
```

اگه `api3.com` خطا بده، هیچی نمی‌گیری و کاربر چیزی نمی‌بینه.

#### با تحمل خطا (با Promise.allSettled):
```javascript
async function getData() {
  const results = await Promise.allSettled([
    fetch('https://api1.com').then(res => res.json()),
    fetch('https://api2.com').then(res => res.json()),
    fetch('https://api3.com').then(res => res.json()), // این سرور خرابه
  ]);

  const successfulData = results
    .filter(result => result.status === 'fulfilled')
    .map(result => result.value);

  const errors = results
    .filter(result => result.status === 'rejected')
    .map(result => result.reason);

  return { successfulData, errors };
}

getData().then(({ successfulData, errors }) => {
  console.log('داده‌های موفق:', successfulData); // داده‌های api1 و api2
  if (errors.length > 0) {
    console.log('خطاها:', errors); // خطای api3
  }
});
```

#### تفاوت:
- توی حالت اول، یه خطا کل برنامه رو خراب می‌کنه.
- توی حالت دوم (fault-tolerant)، خطاها جدا می‌شن و برنامه با داده‌های موجود کارش رو ادامه می‌ده.

---

### **مثال واقعی در Next.js 15**
فرض کن توی یه صفحه داشبورد، داری اطلاعات "کاربر"، "فروش" و "اعلان‌ها" رو از API می‌گیری. می‌خوای اگه یه بخش خطا داد، بقیه داده‌ها همچنان نمایش داده بشن.

#### کد (Server Component):
```javascript
// app/dashboard/page.js
export default async function DashboardPage() {
  const promises = [
    fetch('https://api.example.com/user').then(res => res.json()),
    fetch('https://api.example.com/sales').then(res => res.json()),
    fetch('https://api.example.com/notifications').then(res => res.json()),
  ];

  const results = await Promise.allSettled(promises);

  const user = results[0].status === 'fulfilled' ? results[0].value : null;
  const sales = results[1].status === 'fulfilled' ? results[1].value : null;
  const notifications = results[2].status === 'fulfilled' ? results[2].value : [];

  return (
    <div>
      {user ? <h1>خوش آمدی، {user.name}</h1> : <p>خطا در بارگذاری کاربر</p>}
      {sales ? <p>فروش امروز: {sales.total}</p> : <p>داده فروش در دسترس نیست</p>}
      <ul>
        {notifications.length > 0 ? (
          notifications.map((note, index) => <li key={index}>{note.message}</li>)
        ) : (
          <li>اعلانی وجود ندارد</li>
        )}
      </ul>
    </div>
  );
}
```

#### اینجا چی داریم؟
- از `Promise.allSettled` استفاده کردیم که همه پرامیس‌ها رو اجرا کنه، چه موفق بشن چه شکست بخورن.
- داده‌های موفق رو نمایش می‌دیم و برای داده‌های ناموفق یه پیام جایگزین می‌ذاریم.
- کاربر همچنان می‌تونه از صفحه استفاده کنه، حتی اگه یه API قطع باشه.

---

### **مثال با Promise.any (تحمل خطا با اولویت موفقیت)**
اگه بخوای اولین پاسخ موفق رو بگیری و بقیه خطاها رو نادیده بگیری:

```javascript
// app/payment/page.js
export default async function PaymentPage() {
  const gateways = [
    fetch('https://gateway1.com/check').then(res => res.json()),
    fetch('https://gateway2.com/check').then(res => res.json()),
    fetch('https://gateway3.com/check').then(res => res.json()),
  ];

  try {
    const firstSuccess = await Promise.any(gateways);
    return (
      <div>
        <h1>پرداخت از طریق: {firstSuccess.name}</h1>
        <a href={firstSuccess.url}>پرداخت کن</a>
      </div>
    );
  } catch (error) {
    return <p>هیچ درگاه پرداختی در دسترس نیست</p>;
  }
}
```

#### اینجا چی داریم؟
- `Promise.any` اولین درگاه موفق رو برمی‌گردونه و خطاهای بقیه رو نادیده می‌گیره.
- اگه همه درگاه‌ها خطا بدن، یه پیام جایگزین نشون می‌دیم.

---

### **ویژگی‌های یه سیستم fault-tolerant**
1. **ادامه کار با خطا:** برنامه متوقف نمی‌شه و با داده‌های موجود کار می‌کنه.
2. **مدیریت خطا:** خطاها جداگانه بررسی و به کاربر اطلاع داده می‌شن.
3. **طراحی انعطاف‌پذیر:** از ابزارهایی مثل `Promise.allSettled` یا `Promise.any` برای سناریوهای مختلف استفاده می‌شه.

---

### **چرا توی Next.js مهمه؟**

Next.js 15 با Server Components
و معماری غیرهمزمانش، به ما اجازه می‌ده درخواست‌ها رو بهینه‌تر مدیریت کنیم. تحمل خطا توی این چارچوب یعنی:
- تجربه کاربری بهتر (صفحه خالی نمی‌بینی).
- پایداری بیشتر (برنامه با خرابی یه سرور از کار نمی‌فته).
- مقیاس‌پذیری (توی پروژه‌های بزرگ، خطاها قابل پیش‌بینی‌تر می‌شن).



