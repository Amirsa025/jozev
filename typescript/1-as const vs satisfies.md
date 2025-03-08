من یک پست تخصصی و کاربردی برای LinkedIn در مورد استفاده از as const و satisfies در TypeScript آماده می‌کنم که برای سنیور فرانت‌اند دولوپرها مفید باشد:

-------------------

🔍 کاربردهای پیشرفته as const و satisfies در TypeScript - راهنمای عملی

به عنوان یک سنیور فرانت‌اند دولوپر، درک عمیق از TypeScript و ویژگی‌های پیشرفته آن ضروری است. امروز می‌خواهم درباره دو ویژگی مهم TypeScript صحبت کنم که در سناریوهای پیچیده به کمک ما می‌آیند.

🎯 as const:
این عملگر زمانی کاربرد دارد که می‌خواهیم:
1. از تغییر ناخواسته مقادیر جلوگیری کنیم
2. Type inference دقیق‌تری داشته باشیم
3. Literal Types ایجاد کنیم

مثال کاربردی:
```typescript
// بدون as const
const config = {
  apiVersion: "v1",
  endpoints: {
    users: "/api/users",
    posts: "/api/posts"
  }
};
// type: { apiVersion: string, endpoints: { users: string, posts: string } }

// با as const
const config = {
  apiVersion: "v1",
  endpoints: {
    users: "/api/users",
    posts: "/api/posts"
  }
} as const;
// type: { readonly apiVersion: "v1", readonly endpoints: { readonly users: "/api/users", readonly posts: "/api/posts" } }
```

🎯 satisfies:
این عملگر در موارد زیر به کار می‌آید:
1. Type checking بدون از دست دادن type inference
2. اطمینان از مطابقت با type بدون explicit type annotation
3. کار با union types

مثال کاربردی:
```typescript
type ThemeColors = "primary" | "secondary" | "accent";

// با satisfies
const theme = {
  primary: "#007bff",
  secondary: "#6c757d",
  accent: "#28a745"
} satisfies Record<ThemeColors, string>;

// همچنان type inference داریم و می‌توانیم به properties دسترسی داشته باشیم
theme.primary // type: string
```

💡 سناریوهای واقعی استفاده:

1️⃣ مدیریت State های پیچیده:
```typescript
const initialState = {
  user: null,
  settings: {
    theme: 'dark',
    notifications: true
  },
  cache: new Map()
} as const;

type AppState = typeof initialState;
```

2️⃣ API Endpoints تایپ‌سیف:
```typescript
const API_ROUTES = {
  auth: {
    login: '/auth/login',
    register: '/auth/register'
  },
  users: {
    getProfile: (id: string) => `/users/${id}`,
    updateProfile: (id: string) => `/users/${id}/update`
  }
} satisfies {
  auth: Record<string, string>,
  users: Record<string, (id: string) => string>
};
```

🔑 نکات کلیدی:
- as const برای immutable data structures
- satisfies برای type validation بدون از دست دادن type inference
- ترکیب هر دو برای type-safe configurations

🚀 مزایای استفاده:
1. Type safety بیشتر
2. Refactoring آسان‌تر
3. Developer experience بهتر
4. کاهش باگ‌های runtime

#TypeScript #Frontend #WebDevelopment #Programming #JavaScript

-------------------

💼 نکته حرفه‌ای: این پست را می‌توانید با یک عکس یا اینفوگرافیک جذاب همراه کنید تا تأثیرگذاری بیشتری داشته باشد.

📌 برای بهترین نتیجه:
- از هشتگ‌های مرتبط استفاده کنید
- پست را در ساعات پربازدید منتشر کنید
- با community تعامل داشته باشید
- به سؤالات و نظرات پاسخ دهید

امیدوارم این پست برای شما مفید باشد و بتواند ارزش حرفه‌ای شما را به خوبی نشان دهد.

با کمال میل! اجازه بدید یک تحلیل عمیق‌تر از تفاوت‌ها و شباهت‌های as const و satisfies ارائه کنم:

🔄 شباهت‌ها:

1. هر دو برای بهبود Type Safety استفاده می‌شوند
2. هر دو در زمان کامپایل کار می‌کنند
3. هر دو برای بهبود Developer Experience طراحی شده‌اند

❗ تفاوت‌های کلیدی:

1️⃣ هدف اصلی:
- `as const`: 
  - تبدیل مقادیر به Literal Types
  - ایجاد Readonly Types
  - جلوگیری از تغییر مقادیر

```typescript
// با as const
const colors = ['red', 'blue', 'green'] as const;
// type: readonly ['red', 'blue', 'green']

// بدون as const
const colors = ['red', 'blue', 'green'];
// type: string[]

// مثال کاربردی
type Colors = typeof colors[number]; 
// با as const: type Colors = 'red' | 'blue' | 'green'
// بدون as const: type Colors = string
```

- `satisfies`:
  - Type Checking بدون از دست دادن Type Inference
  - اطمینان از مطابقت با یک Type خاص
  - حفظ اطلاعات Type در زمان استفاده

```typescript
type ColorConfig = {
  [K in 'primary' | 'secondary']: {
    light: string;
    dark: string;
  };
};

// با satisfies
const theme = {
  primary: {
    light: '#e3f2fd',
    dark: '#1976d2'
  },
  secondary: {
    light: '#f3e5f5',
    dark: '#7b1fa2'
  }
} satisfies ColorConfig;

// TypeScript می‌داند که theme.primary.light یک string است
// و اجازه می‌دهد فقط 'primary' یا 'secondary' را استفاده کنیم

// بدون satisfies، باید explicit type annotation استفاده کنیم:
const theme: ColorConfig = {
  // اینجا type inference را از دست می‌دهیم
}
```

2️⃣ زمان استفاده:

`as const` برای:
- تعریف constant objects و arrays
- Redux actions
- Enum-like structures
- Configuration objects

```typescript
// Redux Actions
const createAction = {
  INCREMENT: 'counter/increment',
  DECREMENT: 'counter/decrement',
  RESET: 'counter/reset'
} as const;

type ActionType = typeof createAction[keyof typeof createAction];
// type: 'counter/increment' | 'counter/decrement' | 'counter/reset'
```

`satisfies` برای:
- Type validation در implementation
- API definitions
- Complex object structures
- Theme configurations

```typescript
type APIEndpoints = {
  get: string;
  post?: string;
  put?: string;
  delete?: string;
};

const userAPI = {
  get: '/api/users',
  post: '/api/users',
  delete: '/api/users'
} satisfies APIEndpoints;

// TypeScript می‌داند که userAPI.put undefined است
// اما type inference برای سایر properties حفظ می‌شود
```

3️⃣ ترکیب هر دو:

گاهی استفاده همزمان از هر دو می‌تواند مفید باشد:

```typescript
type ThemeMode = 'light' | 'dark';
type ColorShade = 'main' | 'light' | 'dark';

const palette = {
  mode: 'light',
  primary: {
    main: '#1976d2',
    light: '#42a5f5',
    dark: '#1565c0'
  },
  secondary: {
    main: '#9c27b0',
    light: '#ba68c8',
    dark: '#7b1fa2'
  }
} as const satisfies Record<'mode', ThemeMode> & Record<'primary' | 'secondary', Record<ColorShade, string>>;

// اینجا هم literal types داریم
// هم type validation
// و هم type inference
```

🎯 Best Practices:

1. از `as const` استفاده کنید وقتی:
   - نیاز به literal types دارید
   - می‌خواهید از تغییر مقادیر جلوگیری کنید
   - با union types کار می‌کنید

2. از `satisfies` استفاده کنید وقتی:
   - نیاز به type validation دارید
   - می‌خواهید type inference را حفظ کنید
   - با complex object structures کار می‌کنید

3. از ترکیب هر دو استفاده کنید وقتی:
   - هم به literal types نیاز دارید
   - هم به type validation
   - با configuration objects پیچیده کار می‌کنید

🚫 اشتباهات رایج:

```typescript
// ❌ اشتباه: استفاده نابجا از as const
const dynamicData = {
  timestamp: Date.now(),
  random: Math.random()
} as const; // این کار منطقی نیست چون مقادیر dynamic هستند

// ✅ درست:
const staticConfig = {
  version: '1.0.0',
  apiKey: 'xyz123'
} as const;

// ❌ اشتباه: استفاده نابجا از satisfies
const simpleArray = ['a', 'b', 'c'] satisfies string[];
// نیازی به satisfies نیست، type inference به تنهایی کافی است

// ✅ درست:
const complexConfig = {
  endpoints: {
    users: '/api/users',
    posts: '/api/posts'
  },
  timeouts: {
    short: 1000,
    long: 5000
  }
} satisfies Record<string, Record<string, string | number>>;
```

استفاده از `as const` و `satisfies` در TypeScript می‌تواند به شما کمک کند تا کد خود را با کیفیت‌تر و ایمن‌تر بنویسید. در ادامه، سناریوهایی را بررسی می‌کنیم که در آن‌ها از این دو ویژگی استفاده می‌شود:

### ۱. **استفاده از `as const` برای تعیین مقادیر ثابت (Literal Types)**

فرض کنید شما یک آبجکت دارید که حاوی تنظیمات یا مقادیر ثابتی است که در طول برنامه تغییر نمی‌کنند. با استفاده از `as const`، می‌توانید این مقادیر را به عنوان `literal types` تعیین کنید، که به TypeScript اجازه می‌دهد دقیق‌تر نوع‌ها را بررسی کند.

```typescript
const colors = {
  primary: '#3498db',
  secondary: '#2ecc71',
  error: '#e74c3c',
} as const;

// حالا TypeScript می‌داند که `primary` دقیقاً '#3498db' است و نه یک string عمومی.
```

در این مثال، `as const` به TypeScript می‌گوید که مقادیر `colors` باید به عنوان `literal types` در نظر گرفته شوند و نه به عنوان `string` عمومی. این باعث می‌شود که TypeScript بتواند خطاهای احتمالی را بهتر تشخیص دهد.

### ۲. **استفاده از `satisfies` برای بررسی تطابق نوع‌ها**

`satisfies` یک ویژگی جدید در TypeScript است که به شما امکان می‌دهد بررسی کنید که یک مقدار با یک نوع خاص مطابقت دارد، بدون اینکه نوع آن مقدار را تغییر دهید. این می‌تواند مفید باشد زمانی که می‌خواهید مطمئن شوید که یک مقدار با یک نوع خاص سازگار است، اما نمی‌خواهید نوع آن مقدار را تغییر دهید.

```typescript
interface Theme {
  primary: string;
  secondary: string;
  error: string;
}

const myTheme = {
  primary: '#3498db',
  secondary: '#2ecc71',
  error: '#e74c3c',
} satisfies Theme;

// حالا TypeScript بررسی می‌کند که `myTheme` با `Theme` سازگار است، اما نوع `myTheme` را تغییر نمی‌دهد.
```

در این مثال، `satisfies` بررسی می‌کند که `myTheme` با اینترفیس `Theme` سازگار است، اما نوع `myTheme` را به `Theme` تغییر نمی‌دهد. این به شما اجازه می‌دهد که از مزایای بررسی نوع‌ها بدون از دست دادن اطلاعات نوع اصلی استفاده کنید.

### ۳. **سناریوی ترکیبی: استفاده از `as const` و `satisfies`**

فرض کنید شما یک آبجکت دارید که حاوی تنظیمات UI است و می‌خواهید مطمئن شوید که این تنظیمات با یک اینترفیس خاص سازگار هستند، اما در عین حال می‌خواهید مقادیر آن‌ها به عنوان `literal types` در نظر گرفته شوند.

```typescript
interface UITheme {
  primary: string;
  secondary: string;
  error: string;
}

const uiSettings = {
  primary: '#3498db',
  secondary: '#2ecc71',
  error: '#e74c3c',
} as const satisfies UITheme;

// حالا TypeScript بررسی می‌کند که `uiSettings` با `UITheme` سازگار است و مقادیر آن به عنوان `literal types` در نظر گرفته می‌شوند.
```

در این مثال، `as const` تضمین می‌کند که مقادیر `uiSettings` به عنوان `literal types` در نظر گرفته شوند، و `satisfies` بررسی می‌کند که `uiSettings` با اینترفیس `UITheme` سازگار است.

### ۴. **استفاده در پست‌های لینک‌دین**

اگر می‌خواهید یک پست لینک‌دین درست کنید و کیفیت و دقت برای شما مهم است، می‌توانید از `as const` و `satisfies` برای اطمینان از اینکه داده‌های شما با ساختار مورد نظر مطابقت دارند استفاده کنید.

```typescript
interface LinkedInPost {
  title: string;
  content: string;
  hashtags: readonly string[];
  author: {
    name: string;
    profileUrl: string;
  };
}

const myPost = {
  title: 'نکات مهم در TypeScript',
  content: 'در این پست به بررسی `as const` و `satisfies` می‌پردازیم.',
  hashtags: ['TypeScript', 'Frontend', 'Programming'] as const,
  author: {
    name: 'John Doe',
    profileUrl: 'https://linkedin.com/in/johndoe',
  },
} satisfies LinkedInPost;

// حالا TypeScript بررسی می‌کند که `myPost` با `LinkedInPost` سازگار است و `hashtags` به عنوان یک آرایه‌ی readonly در نظر گرفته می‌شود.
```

در این مثال، `as const` تضمین می‌کند که `hashtags` به عنوان یک آرایه‌ی `readonly` در نظر گرفته شود، و `satisfies` بررسی می‌کند که `myPost` با اینترفیس `LinkedInPost` سازگار است.

### جمع‌بندی

- **`as const`**: برای تعیین مقادیر ثابت و تبدیل آن‌ها به `literal types` استفاده می‌شود.
- **`satisfies`**: برای بررسی تطابق یک مقدار با یک نوع خاص، بدون تغییر نوع آن مقدار استفاده می‌شود.

با استفاده از این دو ویژگی، می‌توانید کد خود را با کیفیت‌تر و ایمن‌تر بنویسید، به ویژه زمانی که با داده‌های ثابت و ساختارهای پیچیده‌تر سروکار دارید.