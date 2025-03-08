    بیایم سوالهات رو یکی‌یکی با مثال‌های عملی و به زبون ساده جواب بدیم. فرض می‌کنیم با TypeScript و React کار می‌کنی و می‌خوای تایپ‌ها رو بهینه و کاربردی تعریف کنی.

---

### 1. چطور تایپ‌ها رو توی یه پروژه ری‌اکت بهینه تعریف کنیم؟
برای بهینه‌سازی تایپ‌ها توی پروژه React، بهتره یه ساختار منظم داشته باشی و تایپ‌ها رو توی فایل‌های جداگونه (مثل `types.ts`) نگه داری. این کار باعث می‌شه کدت تمیزتر باشه و دوباره‌کاری کم بشه.

**مثال عملی:**
```typescript
// src/types/index.ts
export interface User {
  id: number;
  name: string;
  email?: string; // اختیاری
}

// src/components/UserProfile.tsx
import { User } from '../types';

type UserProfileProps = {
  user: User;
};

const UserProfile: React.FC<UserProfileProps> = ({ user }) => {
  return (
    <div>
      <h1>{user.name}</h1>
      {user.email && <p>{user.email}</p>}
    </div>
  );
};

export default UserProfile;
```
- **نکته:** از `interface` برای تایپ‌های ثابت و از `type` برای تایپ‌های پیچیده‌تر (مثل یونیون‌ها) استفاده کن.
- **بهینه‌سازی:** تایپ‌های مشترک رو توی یه فایل مرکزی بذار و همه جا واردشون کن.

---

### 2. چطور تایپ یه فرم با چندین فیلد رو مدیریت کنیم؟
برای فرم‌ها، می‌تونی از یه interface برای تعریف ساختار داده‌ها استفاده کنی و با کتابخونه‌هایی مثل `react-hook-form` تایپ‌ها رو بهینه‌تر مدیریت کنی.

**مثال عملی:**
```typescript
// src/types/formTypes.ts
export interface FormData {
  username: string;
  email: string;
  age: number | null;
}

// src/components/MyForm.tsx
import { useForm } from 'react-hook-form';
import { FormData } from '../types/formTypes';

const MyForm = () => {
  const { register, handleSubmit } = useForm<FormData>({
    defaultValues: { username: '', email: '', age: null },
  });

  const onSubmit = (data: FormData) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('username')} placeholder="Username" />
      <input {...register('email')} placeholder="Email" />
      <input type="number" {...register('age')} placeholder="Age" />
      <button type="submit">Submit</button>
    </form>
  );
};

export default MyForm;
```
- **نکته:** با `react-hook-form` تایپ‌ها مستقیم از `FormData` میاد و نیازی به تعریف جداگونه برای هر فیلد نیست.

---

### 3. چطور تایپ داده‌های پویا (مثل JSON از سرور) رو تعریف کنیم؟
داده‌های پویا معمولاً ساختار مشخصی دارن، ولی گاهی کاملاً قابل پیش‌بینی نیستن. می‌تونی از `interface` با فیلدهای اختیاری یا ابزارهایی مثل `zod` برای اعتبارسنجی و تایپ‌سازی استفاده کنی.

**مثال عملی:**
```typescript
// src/types/apiTypes.ts
export interface ServerResponse {
  id: number;
  title: string;
  metadata?: Record<string, any>; // برای داده‌های پویا
}

// src/components/DataFetcher.tsx
import { useEffect, useState } from 'react';
import { ServerResponse } from '../types/apiTypes';

const DataFetcher = () => {
  const [data, setData] = useState<ServerResponse | null>(null);

  useEffect(() => {
    fetch('https://api.example.com/data')
      .then((res) => res.json())
      .then((result: ServerResponse) => setData(result));
  }, []);

  return <div>{data?.title || 'Loading...'}</div>;
};

export default DataFetcher;
```
- **نکته:** اگه داده‌ها خیلی پویا هستن، از `any` استفاده نکن؛ به جاش `unknown` بذار و با نوع‌سنجی دستی چک کن.

---

### 4. چطور تایپ‌ها رو توی تست‌نویسی (مثل Jest) به کار ببریم؟
توی تست‌نویسی، تایپ‌ها بهت کمک می‌کنن مطمئن شی که ورودی‌ها و خروجی‌ها همون چیزی هستن که انتظار داری.

**مثال عملی:**
```typescript
// src/utils/sum.ts
export const sum = (a: number, b: number): number => a + b;

// src/utils/sum.test.ts
import { sum } from './sum';

describe('sum function', () => {
  it('should add two numbers correctly', () => {
    const result: number = sum(2, 3);
    expect(result).toBe(5);
  });

  it('should handle negative numbers', () => {
    const result: number = sum(-1, 5);
    expect(result).toBe(4);
  });
});
```
- **نکته:** اگه از `ts-jest` استفاده می‌کنی، تایپ‌ها به صورت خودکار با کدت هماهنگ می‌شن. فقط مطمئن شو که ورودی‌ها و خروجی‌ها رو درست تایپ کردی.

---

### 5. چطور تایپ یه Context در ری‌اکت رو تعریف کنیم؟
برای Context، باید تایپ مقدار پیش‌فرض و مقدار واقعی رو مشخص کنی. بهتره یه تایپ جدا برای Context بسازی.

**مثال عملی:**
```typescript
// src/context/ThemeContext.tsx
import { createContext, useContext, useState, ReactNode } from 'react';

// تایپ برای مقدار Context
interface ThemeContextType {
  theme: string;
  toggleTheme: () => void;
}

// مقدار پیش‌فرض
const defaultValue: ThemeContextType = {
  theme: 'light',
  toggleTheme: () => {},
};

const ThemeContext = createContext<ThemeContextType>(defaultValue);

export const ThemeProvider = ({ children }: { children: ReactNode }) => {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme((prev) => (prev === 'light' ? 'dark' : 'light'));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// هوک برای استفاده از Context
export const useTheme = () => useContext(ThemeContext);

// src/components/ThemeSwitcher.tsx
import { useTheme } from '../context/ThemeContext';

const ThemeSwitcher = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <div>
      <p>Current Theme: {theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
};

export default ThemeSwitcher;
```
- **نکته:** همیشه یه مقدار پیش‌فرض بده که با تایپ Context سازگار باشه تا خطای runtime نگیری.

---

### جمع‌بندی
- **فایل جدا برای تایپ‌ها:** کدت رو تمیز و قابل‌استفاده مجدد نگه می‌داره.
- **فرم‌ها:** با ابزارهایی مثل `react-hook-form` تایپ‌ها رو ساده‌تر کن.
- **داده‌های پویا:** از فیلدهای اختیاری یا ابزارهای اعتبارسنجی استفاده کن.
- **تست‌ها:** تایپ‌ها رو برای ورودی و خروجی‌ها مشخص کن.
- **Context:** یه تایپ کامل بساز و مقدار پیش‌فرض رو هماهنگ کن.

اگه سوالی دیگه یا نیاز به توضیح بیشتر داری، بگو!