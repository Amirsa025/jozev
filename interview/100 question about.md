
در زیر یک لیست کامل از **100 سوال سخت** در زمینه **ReactJS 19**، **Next.js 15** و **TypeScript** به همراه پاسخ‌های دقیق و کاربردی ارائه شده است. این سوالات برای یک توسعه‌دهنده سطح میانی تا پیشرفته طراحی شده‌اند و شامل توضیحات و مثال‌های کد هستند. به دلیل محدودیت فضا، پاسخ‌ها مختصر اما جامع‌اند و در صورت نیاز می‌توانید برای هر سوال توضیح بیشتری بخواهید.

---

### سوالات ReactJS 19 (40 سوال)
1. **تفاوت بین state و props چیست؟**  
   **پاسخ:** `state` داده‌های داخلی و تغییرپذیر یک کامپوننت است، در حالی که `props` داده‌های ورودی غیرقابل تغییر از والد هستند.  
   ```jsx
   const Child = ({ text }) => <p>{text}</p>;
   const Parent = () => {
     const [count, setCount] = useState(0);
     return <Child text={count} />;
   };
   ```

2. **چرا Hooks معرفی شدند؟**  
   **پاسخ:** برای حل مشکلات Class Components (مانند پیچیدگی مدیریت state و lifecycle) و امکان استفاده از قابلیت‌ها در Functional Components.  
   ```jsx
   const useCustomHook = () => useState(0);
   ```

3. **تفاوت useState و useReducer چیست؟**  
   **پاسخ:** `useState` برای state ساده و `useReducer` برای state پیچیده با منطق بیشتر مناسب است.  
   ```jsx
   const reducer = (state, action) => (action.type === "increment" ? state + 1 : state);
   const [state, dispatch] = useReducer(reducer, 0);
   ```

4. **چگونه از useEffect استفاده می‌کنید؟**  
   **پاسخ:** برای اجرای کد در زمان mount، update یا unmount.  
   ```jsx
   useEffect(() => {
     console.log("Mounted");
     return () => console.log("Unmounted");
   }, []);
   ```

5. **تفاوت useMemo و useCallback چیست؟**  
   **پاسخ:** `useMemo` مقدار محاسبه‌شده را کش می‌کند، `useCallback` تابع را کش می‌کند.  
   ```jsx
   const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
   const memoizedCallback = useCallback(() => doSomething(a, b), [a, b]);
   ```

6. **React Suspense چیست؟**  
   **پاسخ:** برای مدیریت بارگذاری Lazy و داده‌های غیرهمزمان استفاده می‌شود.  
   ```jsx
   const LazyComponent = lazy(() => import("./Component"));
   <Suspense fallback={<div>Loading...</div>}><LazyComponent /></Suspense>;
   ```

7. **Concurrent Rendering چیست؟**  
   **پاسخ:** رندر موازی برای اولویت‌بندی به‌روزرسانی‌های فوری.  
   ```jsx
   const [isPending, startTransition] = useTransition();
   startTransition(() => setList(newList));
   ```

8. **Transition در React 19 چیست؟**  
   **پاسخ:** به‌روزرسانی‌های غیرفوری را به تعویق می‌اندازد.  
   ```jsx
   startTransition(() => setHeavyState(newValue));
   ```

9. **Controlled vs Uncontrolled Components چیست؟**  
   **پاسخ:** Controlled توسط state مدیریت می‌شود، Uncontrolled توسط DOM.  
   ```jsx
   const [value, setValue] = useState(""); // Controlled
   const ref = useRef(null); // Uncontrolled
   ```

10. **چگونه فرم را با TypeScript هندل می‌کنید؟**  
    **پاسخ:** از نوع `React.FormEvent` و تایپ ورودی‌ها استفاده کنید.  
    ```tsx
    const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
      e.preventDefault();
    };
    ```

11. **Context API چه مشکلی را حل می‌کند؟**  
    **پاسخ:** Prop Drilling را با انتقال داده به تمام سطوح حل می‌کند.  
    ```jsx
    const MyContext = createContext(null);
    ```

12. **React Portals چیست؟**  
    **پاسخ:** رندر محتوا در خارج از DOM اصلی.  
    ```jsx
    return createPortal(<div>Portal</div>, document.body);
    ```

13. **Error Boundaries چیست؟**  
    **پاسخ:** خطاها را در زیرشاخه‌ها مدیریت می‌کند.  
    ```jsx
    class ErrorBoundary extends Component {
      state = { hasError: false };
      static getDerivedStateFromError() { return { hasError: true }; }
      render() { return this.state.hasError ? <h1>Error</h1> : this.props.children; }
    }
    ```

14. **React Fragments چیست؟**  
    **پاسخ:** از رندر نود اضافی جلوگیری می‌کند.  
    ```jsx
    <> <div>1</div> <div>2</div> </>
    ```

15. **تفاوت React.memo و useMemo چیست؟**  
    **پاسخ:** `React.memo` کامپوننت را کش می‌کند، `useMemo` مقدار را.  
    ```jsx
    const MemoComponent = React.memo(() => <div>Hi</div>);
    ```

16. **Custom Hook چیست؟**  
    **پاسخ:** منطق قابل استفاده مجدد در Hook.  
    ```jsx
    const useToggle = () => useState(false);
    ```

17. **Virtual DOM چیست؟**  
    **پاسخ:** کپی DOM برای بهینه‌سازی رندر.  
    **توضیح:** تغییرات ابتدا در Virtual DOM اعمال و سپس با DOM واقعی مقایسه می‌شوند.

18. **Lazy Loading در React چیست؟**  
    **پاسخ:** بارگذاری تاخیری کامپوننت‌ها.  
    ```jsx
    const Lazy = lazy(() => import("./Lazy"));
    ```

19. **Class vs Functional Components چیست؟**  
    **پاسخ:** Class از lifecycle و this استفاده می‌کند، Functional مدرن‌تر است.  
    ```jsx
    function FuncComp() { return <div>Hi</div>; }
    ```

20. **React DevTools چگونه کار می‌کند؟**  
    **پاسخ:** ساختار کامپوننت‌ها و state را نمایش می‌دهد.  
    **توضیح:** افزونه مرورگر برای دیباگ.

21. **Prop Drilling چیست؟**  
    **پاسخ:** انتقال props از چندین لایه. با Context حل می‌شود.  
    ```jsx
    <Context.Provider value={data}><Child /></Context.Provider>
    ```

22. **تایپ کردن props با TypeScript چگونه است؟**  
    **پاسخ:** از interface یا type استفاده کنید.  
    ```tsx
    interface Props { text: string }
    const Comp: React.FC<Props> = ({ text }) => <div>{text}</div>;
    ```

23. **useRef چه کاربردی دارد؟**  
    **پاسخ:** دسترسی به DOM یا نگهداری مقدار پایدار.  
    ```jsx
    const inputRef = useRef(null);
    inputRef.current?.focus();
    ```

24. **Server Components در React 19 چیست؟**  
    **پاسخ:** رندر روی سرور برای کاهش جاوااسکریپت کلاینت.  
    ```jsx
    // در Next.js استفاده می‌شود
    ```

25. **Client-side vs Server-side Rendering چیست؟**  
    **پاسخ:** CSR در مرورگر و SSR روی سرور رندر می‌شود.  
    **توضیح:** SSR برای SEO بهتر است.

26. **چگونه لیست بزرگ را بهینه رندر می‌کنید؟**  
    **پاسخ:** از `React.memo` و کلیدها استفاده کنید.  
    ```jsx
    const Item = React.memo(({ item }) => <div>{item}</div>);
    ```

27. **React Fiber چیست؟**  
    **پاسخ:** معماری جدید برای رندر غیرهمزمان.  
    **توضیح:** رندر را به تکه‌های کوچک تقسیم می‌کند.

28. **SuspenseList چیست؟**  
    **پاسخ:** مدیریت ترتیب بارگذاری چندین Suspense.  
    ```jsx
    <SuspenseList><Suspense>...</Suspense></SuspenseList>
    ```

29. **تفاوت useLayoutEffect و useEffect چیست؟**  
    **پاسخ:** `useLayoutEffect` قبل از رندر DOM اجرا می‌شود.  
    ```jsx
    useLayoutEffect(() => { console.log("layout"); }, []);
    ```

30. **React Query چگونه کار می‌کند؟**  
    **پاسخ:** مدیریت داده‌های سمت سرور با کش و به‌روزرسانی خودکار.  
    ```jsx
    const { data } = useQuery("key", fetchData);
    ```

31. **Strict Mode چیست؟**  
    **پاسخ:** مشکلات احتمالی را شناسایی می‌کند.  
    ```jsx
    <StrictMode><App /></StrictMode>
    ```

32. **تایپ کردن Hook با TypeScript چگونه است؟**  
    **پاسخ:** نوع خروجی را مشخص کنید.  
    ```tsx
    const useTypedHook = (): [string, () => void] => useState("");
    ```

33. **React Keys چه نقشی دارند؟**  
    **پاسخ:** شناسایی تغییرات در لیست‌ها.  
    ```jsx
    items.map(item => <div key={item.id}>{item}</div>)
    ```

34. **بهینه‌سازی رندر در React 19 چگونه است؟**  
    **پاسخ:** از Transition و Concurrent Rendering استفاده کنید.  
    ```jsx
    startTransition(() => setState(newValue));
    ```

35. **useDeferredValue چیست؟**  
    **پاسخ:** تأخیر در رندر مقادیر غیرفوری.  
    ```jsx
    const deferredValue = useDeferredValue(input);
    ```

36. **React Testing Library چیست؟**  
    **پاسخ:** تست رفتار کاربرمحور.  
    ```jsx
    render(<App />); expect(screen.getByText("Hi")).toBeInTheDocument();
    ```

37. **Shallow vs Full Rendering چیست؟**  
    **پاسخ:** Shallow فقط کامپوننت اصلی و Full کل درخت را رندر می‌کند.

38. **Higher-Order Component چیست؟**  
    **پاسخ:** تابع برای افزودن قابلیت به کامپوننت.  
    ```jsx
    const withData = Comp => props => <Comp {...props} data="extra" />;
    ```

39. **Render Props چیست؟**  
    **پاسخ:** انتقال رندر به prop.  
    ```jsx
    <DataProvider render={data => <div>{data}</div>} />
    ```

40. **تایپ کردن Context با TypeScript چگونه است؟**  
    **پاسخ:** از interface استفاده کنید.  
    ```tsx
    interface ContextType { value: string }
    const MyContext = createContext<ContextType>({ value: "" });
    ```

---

### سوالات Next.js 15 (30 سوال)
1. **مزایای Next.js نسبت به CRA چیست؟**  
    **پاسخ:** SSR، SSG و routing داخلی.  
    **توضیح:** CRA فقط CSR دارد.

2. **تفاوت getStaticProps و getServerSideProps چیست؟**  
    **پاسخ:** اولی در بیلد و دومی در هر درخواست اجرا می‌شود.  
    ```tsx
    export const getStaticProps = async () => ({ props: { data: "static" } });
    ```

3. **getStaticPaths چیست؟**  
    **پاسخ:** مسیرهای پویا را برای SSG تولید می‌کند.  
    ```tsx
    export const getStaticPaths = async () => ({ paths: [{ params: { id: "1" } }], fallback: false });
    ```

4. **ISR چیست؟**  
    **پاسخ:** به‌روزرسانی تدریجی صفحات استاتیک.  
    ```tsx
    export const getStaticProps = async () => ({ props: {}, revalidate: 10 });
    ```

5. **Dynamic Routes چگونه کار می‌کند؟**  
    **پاسخ:** با نام فایل مثل `[id].tsx`.  
    ```tsx
    // pages/[id].tsx
    ```

6. **Server Components در Next.js 15 چیست؟**  
    **پاسخ:** رندر روی سرور با کاهش جاوااسکریپت کلاینت.  
    ```tsx
    export default async function Page() { return <h1>{(await fetchData()).title}</h1>; }
    ```

7. **API Routes چیست؟**  
    **پاسخ:** ساخت API در `/api`.  
    ```tsx
    // pages/api/hello.ts
    export default (req, res) => res.json({ message: "Hi" });
    ```

8. **Client-side vs Server-side Fetching چیست؟**  
    **پاسخ:** اولی در مرورگر و دومی در سرور انجام می‌شود.

9. **Image Optimization چگونه کار می‌کند؟**  
    **پاسخ:** بهینه‌سازی خودکار با `next/image`.  
    ```tsx
    import Image from "next/image";
    <Image src="/img.jpg" width={500} height={500} />;
    ```

10. **Link در Next.js چیست؟**  
    **پاسخ:** ناوبری بهینه‌شده با prefetching.  
    ```tsx
    import Link from "next/link";
    <Link href="/about">About</Link>;
    ```

11. **TypeScript در Next.js چگونه است؟**  
    **پاسخ:** به صورت داخلی پشتیبانی می‌شود.  
    ```tsx
    // tsconfig.json خودکار ساخته می‌شود
    ```

12. **Middleware چیست؟**  
    **پاسخ:** کد اجرا شده قبل از درخواست.  
    ```tsx
    // middleware.ts
    export function middleware(req) { return NextResponse.next(); }
    ```

13. **Multi-zone چیست؟**  
    **پاسخ:** ترکیب چندین اپلیکیشن Next.js.

14. **SSG چگونه کار می‌کند؟**  
    **پاسخ:** صفحات در زمان بیلد تولید می‌شوند.  
    ```tsx
    export const getStaticProps = async () => ({ props: { data: "static" } });
    ```

15. **Internationalization (i18n) چیست؟**  
    **پاسخ:** پشتیبانی چندزبانه داخلی.  
    ```tsx
    // next.config.js
    i18n: { locales: ["en", "fa"], defaultLocale: "en" }
    ```

16. **App Router چیست؟**  
    **پاسخ:** سیستم جدید مسیریابی با Server Components.  
    ```tsx
    // app/page.tsx
    ```

17. **React Server Components در Next.js چیست؟**  
    **پاسخ:** رندر سروری با App Router.

18. **SWR چیست؟**  
    **پاسخ:** کش و به‌روزرسانی داده‌ها.  
    ```tsx
    import useSWR from "swr";
    const { data } = useSWR("/api/data", fetcher);
    ```

19. **Automatic Static Optimization چیست؟**  
    **پاسخ:** اگر `getServerSideProps` نباشد، صفحه استاتیک می‌شود.

20. **دیپلوی با Vercel چگونه است؟**  
    **پاسخ:** با `vercel` CLI یا GitHub.

21. **File-based Routing چیست؟**  
    **پاسخ:** مسیرها بر اساس ساختار فایل‌ها.  
    ```tsx
    // pages/about.tsx -> /about
    ```

22. **CSS Modules چگونه است؟**  
    **پاسخ:** استایل محلی با `.module.css`.  
    ```tsx
    import styles from "./style.module.css";
    <div className={styles.container} />;
    ```

23. **Next.js Config چیست؟**  
    **پاسخ:** تنظیمات در `next.config.js`.  
    ```js
    module.exports = { images: { domains: ["example.com"] } };
    ```

24. **تایپ کردن getStaticProps با TypeScript چگونه است؟**  
    **پاسخ:** از `GetStaticProps` استفاده کنید.  
    ```tsx
    export const getStaticProps: GetStaticProps<{ data: string }> = async () => ({ props: { data: "hi" } });
    ```

25. **Streaming چیست؟**  
    **پاسخ:** ارسال تدریجی محتوا به کلاینت.  
    ```tsx
    // با Server Components
    ```

26. **Edge Functions چیست؟**  
    **پاسخ:** کد اجرا شده در لبه شبکه.  
    ```tsx
    // app/api/edge.ts
    export const runtime = "edge";
    ```

27. **Prefetching vs Preloading چیست؟**  
    **پاسخ:** Prefetching برای صفحات و Preloading برای منابع.

28. **تست در Next.js چگونه است؟**  
    **پاسخ:** با Jest و React Testing Library.  
    ```tsx
    test("renders", () => { render(<Page />); });
    ```

29. **Dynamic Imports چیست؟**  
    **پاسخ:** بارگذاری تاخیری ماژول‌ها.  
    ```tsx
    const DynamicComp = dynamic(() => import("./Comp"));
    ```

30. **احراز هویت در Next.js چگونه است؟**  
    **پاسخ:** با Middleware و API Routes.  
    ```tsx
    // middleware.ts
    if (!req.cookies.token) return NextResponse.redirect("/login");
    ```

---

### سوالات TypeScript (30 سوال)
31. **مزایای TypeScript چیست؟**  
    **پاسخ:** تایپ ایمن و خطایابی بهتر.  
    **توضیح:** قبل از اجرا خطاها را نشان می‌دهد.

32. **تفاوت interface و type چیست؟**  
    **پاسخ:** `interface` قابل گسترش و `type` انعطاف‌پذیرتر است.  
    ```tsx
    interface A { x: number }
    type B = { x: number } & { y: string };
    ```

33. **Generics چیست؟**  
    **پاسخ:** تایپ‌های پویا.  
    ```tsx
    function identity<T>(arg: T): T { return arg; }
    ```

34. **Union vs Intersection Types چیست؟**  
    **پاسخ:** `|` یکی و `&` ترکیب.  
    ```tsx
    type A = string | number;
    type B = { x: number } & { y: string };
    ```

35. **تایپ کردن تابع چگونه است؟**  
    **پاسخ:** پارامترها و خروجی را مشخص کنید.  
    ```tsx
    const add = (a: number, b: number): number => a + b;
    ```

36. **never vs void چیست؟**  
    **پاسخ:** `void` خروجی ندارد، `never` هرگز پایان نمی‌یابد.  
    ```tsx
    function fail(): never { throw new Error(); }
    ```

37. **Type Assertion چیست؟**  
    **پاسخ:** تغییر نوع به صورت دستی.  
    ```tsx
    const value = "text" as string;
    ```

38. **Enums چیست؟**  
    **پاسخ:** مجموعه مقادیر ثابت.  
    ```tsx
    enum Color { Red, Green }
    ```

39. **Optional Parameter چگونه است؟**  
    **پاسخ:** با `?`.  
    ```tsx
    function greet(name?: string) {}
    ```

40. **Type Guard چیست؟**  
    **پاسخ:** محدود کردن نوع با شرط.  
    ```tsx
    function isString(value: any): value is string { return typeof value === "string"; }
    ```

41. **Utility Types چیست؟**  
    **پاسخ:** ابزارهایی مثل `Partial`.  
    ```tsx
    type PartialUser = Partial<{ name: string; age: number }>;
    ```

42. **any vs unknown چیست؟**  
    **پاسخ:** `unknown` ایمن‌تر است و نیاز به بررسی دارد.  
    ```tsx
    let val: unknown; if (typeof val === "string") val.toUpperCase();
    ```

43. **تایپ کردن Class چگونه است؟**  
    **پاسخ:** از interface یا مستقیم.  
    ```tsx
    class User { constructor(public name: string) {} }
    ```

44. **Decorators چیست؟**  
    **پاسخ:** متادیتا برای کلاس‌ها.  
    ```tsx
    @log
    class MyClass {}
    ```

45. **Module Augmentation چیست؟**  
    **پاسخ:** گسترش ماژول‌ها.  
    ```tsx
    declare module "lib" { export interface Extra {} }
    ```

46. **Literal Types چیست؟**  
    **پاسخ:** مقادیر خاص.  
    ```tsx
    type Direction = "up" | "down";
    ```

47. **تایپ کردن Array چگونه است؟**  
    **پاسخ:** با `[]` یا `Array<T>`.  
    ```tsx
    const numbers: number[] = [1, 2, 3];
    ```

48. **Conditional Types چیست؟**  
    **پاسخ:** تایپ بر اساس شرط.  
    ```tsx
    type IsString<T> = T extends string ? "yes" : "no";
    ```

49. **Mapped Types چیست؟**  
    **پاسخ:** تبدیل تایپ‌ها.  
    ```tsx
    type Optional<T> = { [K in keyof T]?: T[K] };
    ```

50. **Infer چیست؟**  
    **پاسخ:** استخراج نوع از Generics.  
    ```tsx
    type ReturnType<T> = T extends (...args: any) => infer R ? R : any;
    ```

51. **تایپ کردن Promise چگونه است؟**  
    **پاسخ:** با نوع خروجی.  
    ```tsx
    const fetchData = (): Promise<string> => Promise.resolve("data");
    ```

52. **Namespace چیست؟**  
    **پاسخ:** گروه‌بندی تایپ‌ها.  
    ```tsx
    namespace MySpace { export interface A {} }
    ```

53. **public vs private vs protected چیست؟**  
    **پاسخ:** سطح دسترسی اعضای کلاس.  
    ```tsx
    class A { private x: number; }
    ```

54. **تایپ کردن API Response چگونه است؟**  
    **پاسخ:** با interface.  
    ```tsx
    interface Response { data: string }
    const fetchData = async (): Promise<Response> => (await fetch("url")).json();
    ```

55. **Overloading چگونه است؟**  
    **پاسخ:** تعریف چند امضا برای تابع.  
    ```tsx
    function add(a: number, b: number): number;
    function add(a: string, b: string): string;
    function add(a: any, b: any) { return a + b; }
    ```

56. **دیباگ خطاهای تایپ چگونه است؟**  
    **پاسخ:** با بررسی پیام‌های خطا و استفاده از `--strict`.

57. **strictNullChecks چیست؟**  
    **پاسخ:** بررسی دقیق null و undefined.  
    ```tsx
    let x: string | null = null; // بدون خطا
    ```

58. **تایپ کتابخانه خارجی چگونه است؟**  
    **پاسخ:** با `@types` یا declaration file.  
    ```tsx
    // types.d.ts
    declare module "my-lib" { export function fn(): string; }
    ```

59. **Discriminated Unions چیست؟**  
    **پاسخ:** Union با کلید متمایزکننده.  
    ```tsx
    type A = { kind: "a"; x: number } | { kind: "b"; y: string };
    ```

60. **تایپ کردن Hooks با TypeScript چگونه است؟**  
     **پاسخ:** نوع خروجی و ورودی را مشخص کنید.  
     ```tsx
     const useStringState = () => useState<string>("");
     ```

---

### نکات نهایی
- این سوالات سخت‌ترین مفاهیم را هدف گرفته‌اند و پاسخ‌ها به شکلی طراحی شده‌اند که هم درک تئوری و هم کاربرد عملی را پوشش دهند.
- برای تمرین بیشتر، هر مثال را در یک پروژه واقعی تست کنید.
- اگر سوال خاصی نیاز به توضیح بیشتر داشت، خوشحال می‌شوم عمیق‌تر بحث کنم!

موفق باشید! 🚀