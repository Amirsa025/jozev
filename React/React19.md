
سلام! خوشحالم که می‌خوای هوک‌های جدید React 19 رو مرور کنی. توی این پاسخ، هوک‌های اصلی که توی React 19 معرفی شدن رو با مثال‌های کاربردی توضیح می‌دم تا بتونی توی سناریوهای واقعی ازشون استفاده کنی. هوک‌هایی که بررسی می‌کنیم شامل `useActionState`، `useFormStatus`، `useOptimistic` و `use` هستن. برای هر کدوم یه توضیح ساده و یه سناریوی عملی می‌ذارم که بتونی کاربردش رو توی پروژه‌هات ببینی.

---

### 1. هوک `useActionState`
#### توضیح:
این هوک برای مدیریت وضعیت فرم‌ها و عملیات ناهمگام (asynchronous) به کار می‌ره. به جای اینکه چندین `useState` برای مدیریت داده‌ها، وضعیت در حال انتظار (pending) و خطاها داشته باشی، `useActionState` همه رو توی یه بسته منسجم بهت می‌ده.

#### سناریو:
فرض کن یه فرم ساده برای ثبت نظرات کاربران داری. می‌خوای وقتی کاربر نظرش رو ثبت می‌کنه، وضعیت "در حال ارسال" نشون داده بشه و اگه خطایی پیش اومد، بهش اطلاع بدی.

#### مثال:
```jsx
import { useActionState } from "react";

async function submitComment(prevState, formData) {
  const comment = formData.get("comment");
  try {
    const response = await fetch("/api/comments", {
      method: "POST",
      body: JSON.stringify({ comment }),
    });
    if (!response.ok) throw new Error("ارسال نظر失敗 کرد!");
    return { message: "نظر با موفقیت ثبت شد!", error: null };
  } catch (err) {
    return { message: null, error: err.message };
  }
}

function CommentForm() {
  const [state, submitAction, isPending] = useActionState(submitComment, {
    message: null,
    error: null,
  });

  return (
    <form action={submitAction}>
      <textarea name="comment" placeholder="نظرتو بنویس..." required />
      <button type="submit" disabled={isPending}>
        {isPending ? "در حال ارسال..." : "ارسال نظر"}
      </button>
      {state.message && <p style={{ color: "green" }}>{state.message}</p>}
      {state.error && <p style={{ color: "red" }}>{state.error}</p>}
    </form>
  );
}
```
#### کاربرد:
- وقتی می‌خوای عملیات فرم رو با یه API مدیریت کنی.
- نیاز داری وضعیت‌های مختلف (موفقیت، خطا، در حال انتظار) رو به صورت خودکار مدیریت کنی.

---

### 2. هوک `useFormStatus`
#### توضیح:
این هوک اطلاعاتی درباره وضعیت فرم (مثل اینکه آیا در حال ارساله یا نه) بهت می‌ده. فقط توی کامپوننت‌هایی کار می‌کنه که داخل یه `<form>` هستن و از `react-dom` وارد می‌شه.

#### سناریو:
می‌خوای یه دکمه توی فرم داشته باشی که وقتی فرم در حال ارساله، غیرفعال بشه و پیغام "در حال ارسال" نشون بده.

#### مثال:
```jsx
import { useFormStatus } from "react-dom";

function SubmitButton() {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? "در حال ارسال..." : "ثبت"}
    </button>
  );
}

function Form() {
  async function handleSubmit(formData) {
    await new Promise((resolve) => setTimeout(resolve, 2000)); // شبیه‌سازی API
    console.log("فرم ارسال شد:", formData.get("name"));
  }

  return (
    <form action={handleSubmit}>
      <input type="text" name="name" placeholder="اسمت رو بنویس" />
      <SubmitButton />
    </form>
  );
}
```
#### کاربرد:
- وقتی می‌خوای وضعیت ارسال فرم رو توی یه کامپوننت فرزند (مثل دکمه) مدیریت کنی بدون اینکه نیاز به پاس دادن props باشه.

---

### 3. هوک `useOptimistic`
#### توضیح:
این هوک برای به‌روزرسانی خوش‌بینانه (optimistic updates) استفاده می‌شه. یعنی قبل از اینکه درخواست به سرور کامل بشه، رابط کاربری رو آپدیت می‌کنه و اگه خطایی پیش بیاد، به حالت قبلی برمی‌گرده.

#### سناریو:
فرض کن یه لیست کارها (to-do list) داری و می‌خوای وقتی کاربر یه کار جدید اضافه می‌کنه، فوراً توی لیست نشون داده بشه، حتی اگه درخواست به سرور هنوز کامل نشده باشه.

#### مثال:
```jsx
import { useOptimistic } from "react";

function TodoList() {
  const [todos, setTodos] = React.useState(["خرید نون", "تمرین کدنویسی"]);
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(todos);

  async function addTodo(formData) {
    const newTodo = formData.get("todo");
    addOptimisticTodo([...todos, newTodo]); // آپدیت خوش‌بینانه
    await new Promise((resolve) => setTimeout(resolve, 1000)); // شبیه‌سازی API
    setTodos((prev) => [...prev, newTodo]);
  }

  return (
    <>
      <form action={addTodo}>
        <input type="text" name="todo" placeholder="کار جدید" />
        <button type="submit">اضافه کن</button>
      </form>
      <ul>
        {optimisticTodos.map((todo, index) => (
          <li key={index}>{todo}</li>
        ))}
      </ul>
    </>
  );
}
```
#### کاربرد:
- وقتی می‌خوای تجربه کاربری رو سریع‌تر کنی و منتظر پاسخ سرور نمونی.
- مناسب برای سناریوهایی مثل لایک کردن، اضافه کردن کامنت یا موارد مشابه.

---

### 4. هوک `use`
#### توضیح:
این هوک بهت اجازه می‌ده توی رندر کامپوننت، مقادیر Promise یا Context رو بخونی. برخلاف هوک‌های دیگه، می‌تونی توی شرط‌ها و حلقه‌ها ازش استفاده کنی، ولی فقط توی کامپوننت‌ها یا هوک‌های سفارشی کار می‌کنه.

#### سناریو:
فرض کن می‌خوای داده‌ها رو از یه API بگیری و مستقیم توی رندر استفاده کنی، بدون نیاز به `useEffect`.

#### مثال:
```jsx
import { use } from "react";
import { Suspense } from "react";

function fetchUser(id) {
  return new Promise((resolve) =>
    setTimeout(() => resolve({ id, name: "علی" }), 1000)
  );
}

function UserProfile({ userId }) {
  const user = use(fetchUser(userId)); // گرفتن مستقیم داده از Promise
  return <h1>سلام، {user.name}!</h1>;
}

function App() {
  return (
    <Suspense fallback={<p>در حال بارگذاری...</p>}>
      <UserProfile userId={1} />
    </Suspense>
  );
}
```
#### کاربرد:
- وقتی می‌خوای داده‌ها رو به صورت مستقیم و بدون پیچیدگی‌های `useEffect` بگیری.
- مناسب برای کار با Suspense و فچ کردن داده‌ها توی رندر.

---

### نکات مهم:
1. **ترکیب هوک‌ها:** می‌تونی این هوک‌ها رو با هم ترکیب کنی. مثلاً `useActionState` رو با `useOptimistic` استفاده کن تا هم فرم رو مدیریت کنی و هم آپدیت خوش‌بینانه داشته باشی.
2. **محدودیت‌ها:** `useFormStatus` فقط توی فرزندان `<form>` کار می‌کنه. `use` هم نیاز به Suspense داره اگه Promise استفاده کنی.
3. **به‌روز بودن:** چون React 19 هنوز توی نسخه بتا هست (تا تاریخ امروز، ۴ مارس ۲۰۲۵)، برای پروژه‌های عملیاتی صبر کن تا نسخه پایدار بیاد.

امیدوارم این مثال‌ها بهت کمک کنه که هوک مناسب رو توی سناریوی درست به کار ببری! اگه سوالی داری یا سناریوی خاصی مدنظرت هست، بگو تا بیشتر توضیح بدم.