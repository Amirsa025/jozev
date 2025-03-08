# راهنمای جامع React 19: API‌ها، هوک‌ها و سوالات مصاحبه

React 19 تغییرات مهمی در معماری و API‌های این کتابخانه محبوب ایجاد کرده است. در این راهنما، مهم‌ترین API‌ها و هوک‌های معرفی شده در React 19 را بررسی می‌کنیم، همراه با مثال‌های کاربردی و سوالاتی که معمولاً در مصاحبه‌های شغلی پرسیده می‌شوند.

## فهرست مطالب

1. [هوک‌های جدید در React 19](#هوک‌های-جدید-در-react-19)
2. [API‌های جدید در React 19](#api‌های-جدید-در-react-19)
3. [تغییرات در رندرینگ](#تغییرات-در-رندرینگ)
4. [سوالات رایج مصاحبه](#سوالات-رایج-مصاحبه)

## هوک‌های جدید در React 19

### 1. `use`

هوک `use` یکی از مهمترین اضافه‌های React 19 است که به شما امکان می‌دهد داده‌های Promises، context و سایر منابع را مستقیماً در کامپوننت‌ها "استفاده" کنید.

#### مثال ساده:

```jsx
import { use } from 'react';

function UserProfile({ userPromise }) {
  // use می‌تواند یک Promise را منتظر بماند و نتیجه آن را برگرداند
  const user = use(userPromise);
  
  return <h1>سلام {user.name}!</h1>;
}
```

#### کاربردها:

- خواندن داده از یک Promise بدون نیاز به useEffect
- استفاده از Context در صورت شرطی
- ساده‌سازی کد در مقایسه با ترکیب چندین هوک

#### سوالات مصاحبه:

**سوال: تفاوت بین `use` و `await` چیست؟**

پاسخ: `use` یک هوک React است که می‌تواند در زمان رندر اجرا شود، در حالی که `await` یک عملگر جاوااسکریپت است که فقط در توابع async قابل استفاده است. `use` می‌تواند در هر جایی از کامپوننت استفاده شود و با سیستم Suspense ریکت یکپارچه است.

**سوال: آیا می‌توان از `use` در شرط‌ها استفاده کرد؟**

پاسخ: بله، برخلاف سایر هوک‌ها، `use` را می‌توان در شرط‌ها، حلقه‌ها و حتی توابع تودرتو استفاده کرد. این یکی از مزایای اصلی `use` نسبت به هوک‌های قبلی است.

### 2. `useFormStatus` و `useFormState`

این هوک‌ها برای مدیریت بهتر فرم‌ها در React طراحی شده‌اند.

#### مثال `useFormStatus`:

```jsx
import { useFormStatus } from 'react-dom';

function SubmitButton() {
  const { pending, data, method, action } = useFormStatus();
  
  return (
    <button disabled={pending} type="submit">
      {pending ? 'در حال ارسال...' : 'ارسال'}
    </button>
  );
}

function ContactForm() {
  return (
    <form action="/api/contact">
      <input name="email" type="email" />
      <SubmitButton />
    </form>
  );
}
```

#### مثال `useFormState`:

```jsx
import { useFormState } from 'react-dom';

// عملکرد سمت سرور یا کلاینت
async function submitAction(prevState, formData) {
  const email = formData.get('email');
  
  if (!email.includes('@')) {
    return { error: 'ایمیل نامعتبر است' };
  }
  
  await saveToDatabase(formData);
  return { success: true };
}

function ContactForm() {
  const [state, formAction] = useFormState(submitAction, { success: false });
  
  return (
    <form action={formAction}>
      {state.error && <p className="error">{state.error}</p>}
      {state.success && <p className="success">فرم با موفقیت ارسال شد!</p>}
      
      <input name="email" type="email" />
      <button type="submit">ارسال</button>
    </form>
  );
}
```

#### سوالات مصاحبه:

**سوال: چه زمانی از `useFormStatus` استفاده می‌کنیم و چه زمانی از `useFormState`؟**

پاسخ: از `useFormStatus` برای دسترسی به وضعیت فعلی فرم (مانند در حال ارسال بودن) استفاده می‌کنیم، در حالی که از `useFormState` برای مدیریت داده‌های فرم و پاسخ‌های عملیات فرم استفاده می‌کنیم. اولی بیشتر برای UI است و دومی برای مدیریت داده.

**سوال: آیا می‌توان این هوک‌ها را با فریم‌ورک‌های فرم مانند Formik یا React Hook Form ترکیب کرد؟**

پاسخ: بله، اما باید با دقت این کار را انجام دهید. این هوک‌ها بیشتر برای کار با API جدید action در فرم‌های React طراحی شده‌اند. اگر از کتابخانه‌های دیگر استفاده می‌کنید، ممکن است نیاز به تطبیق رویکردهای مختلف داشته باشید.

### 3. `useOptimistic`

هوک `useOptimistic` به شما امکان می‌دهد تغییرات خوش‌بینانه (Optimistic Updates) را در UI نمایش دهید، قبل از اینکه پاسخ واقعی از سرور دریافت شود.

#### مثال:

```jsx
import { useOptimistic } from 'react';

function MessageList() {
  const [messages, setMessages] = useState([]);
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage) => [...state, { ...newMessage, sending: true }]
  );

  async function sendMessage(text) {
    const newMessage = { id: Date.now(), text };
    
    // بلافاصله به UI اضافه می‌شود
    addOptimisticMessage(newMessage);
    
    // ارسال واقعی به سرور
    await fetch('/api/messages', {
      method: 'POST',
      body: JSON.stringify(newMessage)
    });
    
    // بعد از موفقیت، state واقعی را به‌روز می‌کنیم
    setMessages([...messages, newMessage]);
  }

  return (
    <div>
      <ul>
        {optimisticMessages.map(msg => (
          <li key={msg.id} style={{ opacity: msg.sending ? 0.5 : 1 }}>
            {msg.text} {msg.sending && '(در حال ارسال...)'}
          </li>
        ))}
      </ul>
      <button onClick={() => sendMessage('پیام جدید')}>ارسال پیام</button>
    </div>
  );
}
```

#### سوالات مصاحبه:

**سوال: چه زمانی باید از `useOptimistic` استفاده کنیم؟**

پاسخ: زمانی که می‌خواهید تجربه کاربری سریع‌تری ارائه دهید، به خصوص در عملیات‌هایی که نیاز به ارتباط با سرور دارند. این هوک به شما امکان می‌دهد قبل از تأیید سرور، تغییرات را در UI نمایش دهید، و سپس در صورت موفقیت، state واقعی را به‌روز کنید.

**سوال: چگونه خطاها را در `useOptimistic` مدیریت می‌کنیم؟**

پاسخ: باید یک مکانیزم برگشت (rollback) پیاده‌سازی کنید. این معمولاً با استفاده از try/catch در عملیات‌های async انجام می‌شود:

```jsx
async function sendMessage(text) {
  const newMessage = { id: Date.now(), text };
  addOptimisticMessage(newMessage);
  
  try {
    await fetch('/api/messages', { method: 'POST', body: JSON.stringify(newMessage) });
    setMessages([...messages, newMessage]);
  } catch (error) {
    // حذف پیام خوش‌بینانه در صورت خطا
    setMessages(messages); // برگشت به state قبلی
    showError('خطا در ارسال پیام');
  }
}
```

### 4. `useDeferredValue`

هوک `useDeferredValue` اگرچه در نسخه‌های قبلی معرفی شده بود، اما در React 19 بهبودهایی یافته است.

#### مثال:

```jsx
import { useState, useDeferredValue } from 'react';

function SearchResults({ query }) {
  const deferredQuery = useDeferredValue(query);
  const results = searchAPI(deferredQuery);
  
  return (
    <ul>
      {results.map(result => (
        <li key={result.id}>{result.title}</li>
      ))}
    </ul>
  );
}

function SearchBox() {
  const [query, setQuery] = useState('');
  
  return (
    <div>
      <input
        value={query}
        onChange={e => setQuery(e.target.value)}
        placeholder="جستجو..."
      />
      <SearchResults query={query} />
    </div>
  );
}
```

#### سوالات مصاحبه:

**سوال: تفاوت بین `useDeferredValue` و `useTransition` چیست؟**

پاسخ: `useDeferredValue` برای به تعویق انداختن به‌روزرسانی یک مقدار استفاده می‌شود، در حالی که `useTransition` برای اولویت‌بندی به‌روزرسانی‌های state استفاده می‌شود. `useDeferredValue` را معمولاً برای مقادیری استفاده می‌کنیم که از بالا به پایین منتقل می‌شوند، در حالی که `useTransition` برای کنترل زمان‌بندی به‌روزرسانی‌های state محلی استفاده می‌شود.

**سوال: آیا `useDeferredValue` باعث رندر اضافی می‌شود؟**

پاسخ: بله، استفاده از `useDeferredValue` می‌تواند منجر به رندرهای اضافی شود. ابتدا کامپوننت با مقدار قدیمی رندر می‌شود و سپس با مقدار جدید به‌روز می‌شود. برای بهینه‌سازی باید از `memo` یا `useMemo` استفاده کنید تا از رندرهای غیرضروری جلوگیری شود.

## API‌های جدید در React 19

### 1. `cache` و کش‌گذاری داده‌ها

React 19 یک API رسمی برای کش‌گذاری داده‌ها معرفی کرده است که به بهینه‌سازی درخواست‌های داده کمک می‌کند.

#### مثال:

```jsx
import { cache } from 'react';

// این تابع کش می‌شود
const fetchUser = cache(async (userId) => {
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
});

function UserProfile({ userId }) {
  // چندین بار صدا زدن این تابع فقط یک بار به API درخواست می‌فرستد
  const user = use(fetchUser(userId));
  
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}

function App() {
  return (
    <>
      <UserProfile userId="123" />
      {/* همان کاربر بدون درخواست جدید */}
      <UserProfile userId="123" />
    </>
  );
}
```

#### سوالات مصاحبه:

**سوال: چگونه می‌توان کش را در `cache` API پاک کرد؟**

پاسخ: در حال حاضر، React مستقیماً روشی برای پاک کردن کش ارائه نمی‌دهد. بهترین راه‌حل استفاده از کلیدهای منحصر به فرد برای هر درخواست است یا می‌توانید یک شناسه نسخه به عنوان پارامتر به تابع اضافه کنید که هنگام نیاز به بی‌اعتبارسازی کش تغییر می‌کند.

```jsx
const fetchUser = cache(async (userId, version = 1) => {
  const response = await fetch(`/api/users/${userId}?v=${version}`);
  return response.json();
});

// استفاده:
const user = use(fetchUser(userId, userVersion));

// برای بی‌اعتبار کردن کش:
setUserVersion(prev => prev + 1);
```

**سوال: آیا `cache` فقط با `use` کار می‌کند؟**

پاسخ: نه، `cache` فقط یک تابع را کش می‌کند و می‌تواند با هر مکانیزم دیگری از جمله `useEffect`، `useSWR` یا `React Query` استفاده شود. با این حال، استفاده از `cache` با `use` بهترین تجربه را برای کش‌گذاری داده‌ها در React 19 فراهم می‌کند.

### 2. کامپوننت‌های سرور (Server Components)

کامپوننت‌های سرور قبلاً معرفی شده بودند، اما در React 19 بهبودهای قابل توجهی یافته‌اند.

#### مثال:

```jsx
// ServerComponent.js - اجرا فقط در سرور
export default async function ServerComponent() {
  // این کد فقط در سرور اجرا می‌شود
  const data = await fetch('https://api.example.com/data').then(r => r.json());
  
  return (
    <div>
      <h1>داده‌های سرور</h1>
      <ul>
        {data.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}

// در کامپوننت دیگر
import ServerComponent from './ServerComponent';

function App() {
  return (
    <div>
      <h1>برنامه من</h1>
      <ServerComponent />
    </div>
  );
}
```

#### سوالات مصاحبه:

**سوال: تفاوت بین کامپوننت‌های سرور و کلاینت چیست؟**

پاسخ: کامپوننت‌های سرور فقط در سرور اجرا می‌شوند و نتیجه HTML آنها به کلاینت ارسال می‌شود. آنها:
- می‌توانند از `async/await` استفاده کنند
- می‌توانند مستقیماً به پایگاه‌داده یا فایل‌سیستم دسترسی داشته باشند
- نمی‌توانند از هوک‌ها یا رویدادها استفاده کنند
- نمی‌توانند از state یا رویدادهای مرورگر استفاده کنند

کامپوننت‌های کلاینت در مرورگر اجرا می‌شوند و می‌توانند از تمام ویژگی‌های React شامل hooks، events و state استفاده کنند.

**سوال: چگونه می‌توان یک کامپوننت کلاینت را در کامپوننت سرور استفاده کرد؟**

پاسخ: با استفاده از دستور "use client" در بالای فایل کامپوننت کلاینت:

```jsx
'use client';

import { useState } from 'react';

export default function ClientComponent() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(count + 1)}>
      شمارش: {count}
    </button>
  );
}

// در کامپوننت سرور
import ClientComponent from './ClientComponent';

export default function ServerComponent() {
  const data = await fetchData();
  
  return (
    <div>
      <h1>داده‌های سرور: {data.title}</h1>
      <ClientComponent />
    </div>
  );
}
```

### 3. `Document` و `Asset` برای مدیریت منابع

React 19 API‌های جدیدی برای مدیریت بهتر منابع و assets معرفی کرده است.

#### مثال:

```jsx
import { Document, Asset } from 'react';

function MyApp() {
  return (
    <Document
      title="برنامه من"
      description="یک برنامه React 19"
      language="fa"
    >
      <Asset href="/styles/main.css" rel="stylesheet" />
      <Asset src="/scripts/analytics.js" async />
      
      <header>
        <h1>به برنامه من خوش آمدید</h1>
      </header>
      <main>
        <p>این یک برنامه React 19 است</p>
      </main>
    </Document>
  );
}
```

#### سوالات مصاحبه:

**سوال: تفاوت بین استفاده از `Document` و تنظیم مستقیم تگ‌های head چیست؟**

پاسخ: `Document` به React اجازه می‌دهد بهینه‌سازی‌های بیشتری انجام دهد و از تداخل‌های احتمالی جلوگیری کند. همچنین در SSR (رندر سمت سرور) بهتر کار می‌کند و می‌تواند متا‌تگ‌ها را به طور پویا مدیریت کند. برخلاف کتابخانه‌هایی مانند React Helmet، `Document` یک API رسمی است که با موتور رندرینگ React یکپارچه است.

**سوال: آیا می‌توان از `Document` در کامپوننت‌های تودرتو استفاده کرد؟**

پاسخ: بله، React به طور هوشمندانه اطلاعات متعدد `Document` را ترکیب می‌کند. اما بهترین روش استفاده از `Document` در سطح بالای برنامه یا در صفحات اصلی است.

## تغییرات در رندرینگ

### 1. واکنش‌های خودکار (Automatic Reactions)

React 19 مفهوم واکنش‌های خودکار را معرفی کرده است که به نوعی رویکرد واکنشی مشابه کتابخانه‌هایی مانند MobX را به React می‌آورد.

#### مثال:

```jsx
import { createReaction, useReaction } from 'react';

// ایجاد یک reaction
export const counterReaction = createReaction({
  initialValue: 0,
  onChange: (newValue) => {
    console.log('مقدار شمارنده تغییر کرد:', newValue);
  }
});

function CounterDisplay() {
  // خواندن مقدار واکنشی
  const count = useReaction(counterReaction);
  
  return <div>شمارنده: {count}</div>;
}

function CounterButtons() {
  return (
    <div>
      <button onClick={() => counterReaction.set(prev => prev + 1)}>افزایش</button>
      <button onClick={() => counterReaction.set(prev => prev - 1)}>کاهش</button>
    </div>
  );
}

function App() {
  return (
    <div>
      <CounterDisplay />
      <CounterButtons />
    </div>
  );
}
```

#### سوالات مصاحبه:

**سوال: تفاوت بین `createReaction` و `useState` چیست؟**

پاسخ: `createReaction` یک مکانیزم state مدیریت‌شده خارج از کامپوننت‌ها ایجاد می‌کند که می‌تواند به طور خودکار کامپوننت‌هایی که از آن استفاده می‌کنند را به‌روز کند. برخلاف `useState` که محدود به یک کامپوننت است، reactions می‌توانند بین چندین کامپوننت به اشتراک گذاشته شوند و حتی خارج از درخت کامپوننت استفاده شوند.

**سوال: آیا reactions جایگزین Redux یا سایر کتابخانه‌های مدیریت state هستند؟**

پاسخ: آنها می‌توانند در بسیاری از موارد جایگزین شوند، اما برای برنامه‌های پیچیده‌تر، Redux هنوز مزایای خود را دارد. reactions برای مدیریت state در مقیاس کوچک تا متوسط مناسب هستند و مخصوصاً برای state‌هایی که نیاز دارید در چندین کامپوننت به آنها دسترسی داشته باشید.

### 2. Suspense برای Data Fetching

Suspense در نسخه‌های قبلی وجود داشت، اما در React 19 بهبودهای قابل توجهی یافته است، به خصوص در ترکیب با هوک `use`.

#### مثال:

```jsx
import { Suspense, use } from 'react';

// تابع دریافت داده‌ها  
function fetchData(id) {
  return fetch(`/api/data/${id}`).then(r => r.json());
}

function DataComponent({ id }) {
  // استفاده از use برای دریافت داده‌ها با Suspense
  const data = use(fetchData(id));
  
  return <div>{data.title}</div>;
}

function App() {
  return (
    <div>
      <h1>برنامه داده</h1>
      <Suspense fallback={<div>در حال بارگذاری...</div>}>
        <DataComponent id="123" />
      </Suspense>
    </div>
  );
}
```

#### سوالات مصاحبه:

**سوال: چگونه می‌توان خطاها را در Suspense مدیریت کرد؟**

پاسخ: با استفاده از کامپوننت `ErrorBoundary` می‌توان خطاهای رخ داده در Suspense را مدیریت کرد:

```jsx
import { ErrorBoundary } from 'react-error-boundary';

function App() {
  return (
    <ErrorBoundary
      fallback={<div>خطایی رخ داد!</div>}
      onError={(error) => console.error(error)}
    >
      <Suspense fallback={<div>در حال بارگذاری...</div>}>
        <DataComponent id="123" />
      </Suspense>
    </ErrorBoundary>
  );
}
```

**سوال: آیا می‌توان چندین Suspense تودرتو داشت؟**

پاسخ: بله، می‌توانید Suspense‌های تودرتو ایجاد کنید. هر Suspense نزدیک‌ترین کامپوننت در حال بارگذاری را مدیریت می‌کند. این به شما امکان می‌دهد بخش‌های مختلف UI را به صورت جداگانه مدیریت کنید:

```jsx
<Suspense fallback={<FullPageLoader />}>
  <Header />
  <Suspense fallback={<ContentSkeleton />}>
    <MainContent />
  </Suspense>
  <Suspense fallback={<SidebarSkeleton />}>
    <Sidebar />
  </Suspense>
</Suspense>
```

## سوالات رایج مصاحبه

### 1. سوالات عمومی درباره React 19

**سوال: مهم‌ترین تغییرات در React 19 نسبت به نسخه‌های قبلی چیست؟**

پاسخ: مهم‌ترین تغییرات عبارتند از:
1. معرفی هوک `use` برای دریافت داده‌های Promise
2. بهبود کامپوننت‌های سرور و یکپارچه‌سازی بهتر با کامپوننت‌های کلاینت
3. API جدید `cache` برای کش‌گذاری داده‌ها
4. سیستم مدیریت فرم‌های بهبودیافته با `useFormState` و `useFormStatus`
5. بهینه‌سازی‌های قابل توجه در موتور رندرینگ

**سوال: آیا React 19 با کدهای نوشته شده برای نسخه‌های قبلی سازگار است؟**

پاسخ: React 19 تلاش کرده تا حداکثر سازگاری را حفظ کند، اما برخی تغییرات شکستنده (breaking changes) وجود دارد. مهمترین تغییرات شامل:
1. تغییر رفتار `useEffect` و زمان‌بندی آن
2. تغییر در نحوه رندرینگ لیست‌ها و کلیدها
3. تغییرات در API های درونی که برنامه‌های وابسته به جزئیات پیاده‌سازی را تحت تأثیر قرار می‌دهد

توصیه می‌شود قبل از به‌روزرسانی به React 19، مستندات رسمی را مطالعه کنید و آزمون‌ها را اجرا کنید.

### 2. مقایسه با دیگر کتابخانه‌ها

**سوال: با توجه به قابلیت‌های جدید React 19، آیا هنوز نیازی به استفاده از Redux یا React Query هست؟**

پاسخ: بله، اگرچه React 19 قابلیت‌های مدیریت state و دریافت داده را بهبود بخشیده، اما کتابخانه‌هایی مانند Redux و React Query هنوز مزایای خود را دارند:

- **Redux**: همچنان برای مدیریت state پیچیده، قابلیت ردیابی تغییرات، middleware‌ها و اکوسیستم گسترده مفید است.
- **React Query**: ویژگی‌های پیشرفته‌ای مانند بی‌اعتبارسازی هوشمند کش، پیش‌بارگذاری، و مدیریت پیچیده‌تر خطاها را ارائه می‌ده