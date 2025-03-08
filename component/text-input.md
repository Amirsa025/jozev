
من برات کامپوننت `TextInput` رو با استفاده از `cva` (Class Variance Authority) پیاده‌سازی می‌کنم تا استایل‌ها رو به صورت داینامیک و مرتب‌تر مدیریت کنیم. `cva` یه ابزار عالی برای تعریف کلاس‌های Tailwind به صورت شرطی و متغیره که باعث می‌شه کد تمیزتر و قابل‌نگهداری‌تر بشه. بعدش هم یه مثال ازش توی فرم می‌زنم.

---

### **نصب و آماده‌سازی**
اول باید `cva` رو نصب کنی:
```bash
npm install class-variance-authority
```

و TailwindCSS باید توی پروژه‌ات تنظیم شده باشه.

---

### **پیاده‌سازی TextInput با cva**
اینجا از `cva` برای مدیریت استایل‌های ورودی استفاده می‌کنم و بعدش توی یه فرم نمونه نشونش می‌دم.

#### **کد TextInput:**

```tsx
import React from "react";
import { cva, type VariantProps } from "class-variance-authority";
import { UseFormRegister, FieldValues, RegisterOptions, FieldError } from "react-hook-form";

// تعریف استایل‌ها با cva
const inputVariants = cva(
  "block w-full p-2 border rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500", // استایل پایه
  {
    variants: {
      error: {
        true: "border-red-500",
        false: "border-gray-300",
      },
      disabled: {
        true: "bg-gray-100 cursor-not-allowed",
        false: "",
      },
    },
    defaultVariants: {
      error: false,
      disabled: false,
    },
  }
);

// تایپ پراپ‌ها
interface TextInputProps<T extends FieldValues> extends VariantProps<typeof inputVariants> {
  id: string;
  label: string;
  name: keyof T;
  register: UseFormRegister<T>;
  validation?: RegisterOptions<T, string>;
  error?: FieldError;
  type?: "text" | "email" | "password";
  placeholder?: string;
  disabled?: boolean;
}

const TextInput = <T extends FieldValues>({
  id,
  label,
  name,
  register,
  validation,
  error,
  type = "text",
  placeholder,
  disabled = false,
}: TextInputProps<T>) => {
  return (
    <div className="space-y-1">
      {/* لیبل */}
      <label htmlFor={id} className="block text-sm font-medium text-gray-700">
        {label}
      </label>

      {/* ورودی */}
      <input
        id={id}
        type={type}
        {...register(name as any, validation)}
        placeholder={placeholder}
        disabled={disabled}
        className={inputVariants({ error: !!error, disabled })}
      />

      {/* خطا */}
      {error && <p className="text-red-500 text-sm">{error.message}</p>}
    </div>
  );
};

export default TextInput;
```

---

### **توضیحات cva:**
- **`inputVariants`**: تابع `cva` یه کلاس پایه می‌گیره و بعد با استفاده از `variants` حالت‌های مختلف رو تعریف می‌کنه.
  - `error`: وقتی خطا باشه، مرز قرمز می‌شه (`border-red-500`)، وگرنه خاکستریه (`border-gray-300`).
  - `disabled`: وقتی غیرفعاله، پس‌زمینه خاکستری و کرسر غیرفعال می‌شه.
  - `defaultVariants`: مقادیر پیش‌فرض برای متغیرها.
- **ادغام با کامپوننت**: توی `className` از `inputVariants` استفاده می‌کنیم و بر اساس `error` و `disabled` کلاس‌ها رو داینامیک می‌کنیم.

---

### **مثال استفاده در فرم**
حالا بیایم از این کامپوننت توی یه فرم استفاده کنیم.

#### **کد فرم:**

```tsx
import React from "react";
import { useForm, SubmitHandler } from "react-hook-form";
import TextInput from "./TextInput"; // فرض می‌کنیم توی یه فایل دیگه‌ست

// تایپ داده‌های فرم
interface FormData {
  name: string;
  email: string;
  password: string;
}

const MyForm: React.FC = () => {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<FormData>({
    defaultValues: {
      name: "",
      email: "",
      password: "",
    },
  });

  const onSubmit: SubmitHandler<FormData> = (data) => {
    console.log("Form Data:", data);
  };

  return (
    <div className="max-w-md mx-auto mt-10 p-6 bg-white rounded-lg shadow-lg">
      <h2 className="text-2xl font-bold text-center mb-6">فرم ثبت‌نام</h2>
      <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
        {/* فیلد نام */}
        <TextInput
          id="name"
          label="نام"
          name="name"
          register={register}
          validation={{ required: "نام اجباری است", minLength: { value: 3, message: "حداقل ۳ کاراکتر" } }}
          error={errors.name}
          placeholder="نام خود را وارد کنید"
        />

        {/* فیلد ایمیل */}
        <TextInput
          id="email"
          label="ایمیل"
          name="email"
          register={register}
          validation={{
            required: "ایمیل اجباری است",
            pattern: { value: /^\S+@\S+$/i, message: "فرمت ایمیل اشتباه است" },
          }}
          error={errors.email}
          type="email"
          placeholder="ایمیل خود را وارد کنید"
        />

        {/* فیلد پسورد */}
        <TextInput
          id="password"
          label="رمز عبور"
          name="password"
          register={register}
          validation={{ required: "رمز عبور اجباری است", minLength: { value: 6, message: "حداقل ۶ کاراکتر" } }}
          error={errors.password}
          type="password"
          placeholder="رمز عبور خود را وارد کنید"
          disabled={isSubmitting} // غیرفعال کردن موقع ارسال
        />

        {/* دکمه سابمیت */}
        <button
          type="submit"
          disabled={isSubmitting}
          className="w-full py-2 px-4 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 disabled:bg-gray-400 transition-colors"
        >
          {isSubmitting ? "در حال ارسال..." : "ارسال"}
        </button>
      </form>
    </div>
  );
};

export default MyForm;
```

---

### **مزایای استفاده از cva:**
1. **مدیریت بهتر استایل‌ها**: کلاس‌ها به صورت متمرکز و شرطی تعریف می‌شن و دیگه نیازی به نوشتن شرط‌های طولانی توی `className` نیست.
2. **خوانایی بیشتر**: کد استایل‌ها تمیزتر و قابل‌فهم‌تره.
3. **انعطاف‌پذیری**: می‌تونی به راحتی متغیرهای جدید (مثل اندازه، رنگ و غیره) به `inputVariants` اضافه کنی.

---

### **نتیجه**
این پیاده‌سازی یه `TextInput` کاملاً تایپ‌سیف، داینامیک و شیک بهت می‌ده که با Tailwind و `react-hook-form` کار می‌کنه. اگر می‌خوای متغیرهای بیشتری به `cva` اضافه کنم (مثل اندازه یا رنگ‌های مختلف) یا تغییر دیگه‌ای بدی، بگو تا برات انجام بدم!


==========================================================
برای استفاده از `zod` به‌عنوان ابزار اعتبارسنجی با `react-hook-form`، باید `zod` رو نصب کنی و از `@hookform/resolvers/zod` استفاده کنی تا اعتبارسنجی‌های `zod` رو به فرم متصل کنه. من برات کامپوننت `TextInput` و یه فرم نمونه رو با `zod` بازنویسی می‌کنم و از `cva` هم مثل قبل برای استایل‌ها استفاده می‌کنم.

---

### **نصب پکیج‌ها**
اول باید پکیج‌های موردنیاز رو نصب کنی:
```bash
npm install zod @hookform/resolvers class-variance-authority
```

---

### **پیاده‌سازی TextInput با zod**
اینجا از `zod` برای تعریف اسکیمای اعتبارسنجی استفاده می‌کنم و بعدش توی `react-hook-form` ادغامش می‌کنم.

#### **کد TextInput:**

```tsx
import React from "react";
import { cva, type VariantProps } from "class-variance-authority";
import { UseFormRegister, FieldValues, FieldError } from "react-hook-form";

// تعریف استایل‌ها با cva
const inputVariants = cva(
  "block w-full p-2 border rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500",
  {
    variants: {
      error: {
        true: "border-red-500",
        false: "border-gray-300",
      },
      disabled: {
        true: "bg-gray-100 cursor-not-allowed",
        false: "",
      },
    },
    defaultVariants: {
      error: false,
      disabled: false,
    },
  }
);

// تایپ پراپ‌ها
interface TextInputProps<T extends FieldValues> extends VariantProps<typeof inputVariants> {
  id: string;
  label: string;
  name: keyof T;
  register: UseFormRegister<T>;
  error?: FieldError;
  type?: "text" | "email" | "password";
  placeholder?: string;
  disabled?: boolean;
}

const TextInput = <T extends FieldValues>({
  id,
  label,
  name,
  register,
  error,
  type = "text",
  placeholder,
  disabled = false,
}: TextInputProps<T>) => {
  return (
    <div className="space-y-1">
      <label htmlFor={id} className="block text-sm font-medium text-gray-700">
        {label}
      </label>
      <input
        id={id}
        type={type}
        {...register(name as any)}
        placeholder={placeholder}
        disabled={disabled}
        className={inputVariants({ error: !!error, disabled })}
      />
      {error && <p className="text-red-500 text-sm">{error.message}</p>}
    </div>
  );
};

export default TextInput;
```

---

### **فرم نمونه با zod**
حالا یه فرم می‌نویسم که از `zod` برای اعتبارسنجی استفاده می‌کنه.

#### **کد فرم:**

```tsx
import React from "react";
import { useForm, SubmitHandler } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import * as z from "zod";
import TextInput from "./TextInput";

// تعریف اسکیما با zod
const formSchema = z.object({
  name: z.string().min(3, "نام باید حداقل ۳ کاراکتر باشد").nonempty("نام اجباری است"),
  email: z.string().email("فرمت ایمیل اشتباه است").nonempty("ایمیل اجباری است"),
  password: z.string().min(6, "رمز عبور باید حداقل ۶ کاراکتر باشد").nonempty("رمز عبور اجباری است"),
});

// استخراج تایپ از اسکیما برای استفاده در فرم
type FormData = z.infer<typeof formSchema>;

const MyForm: React.FC = () => {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<FormData>({
    resolver: zodResolver(formSchema), // اتصال zod به react-hook-form
    defaultValues: {
      name: "",
      email: "",
      password: "",
    },
  });

  const onSubmit: SubmitHandler<FormData> = (data) => {
    console.log("Form Data:", data);
  };

  return (
    <div className="max-w-md mx-auto mt-10 p-6 bg-white rounded-lg shadow-lg">
      <h2 className="text-2xl font-bold text-center mb-6">فرم ثبت‌نام</h2>
      <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
        {/* فیلد نام */}
        <TextInput
          id="name"
          label="نام"
          name="name"
          register={register}
          error={errors.name}
          placeholder="نام خود را وارد کنید"
        />

        {/* فیلد ایمیل */}
        <TextInput
          id="email"
          label="ایمیل"
          name="email"
          register={register}
          error={errors.email}
          type="email"
          placeholder="ایمیل خود را وارد کنید"
        />

        {/* فیلد پسورد */}
        <TextInput
          id="password"
          label="رمز عبور"
          name="password"
          register={register}
          error={errors.password}
          type="password"
          placeholder="رمز عبور خود را وارد کنید"
          disabled={isSubmitting}
        />

        {/* دکمه سابمیت */}
        <button
          type="submit"
          disabled={isSubmitting}
          className="w-full py-2 px-4 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 disabled:bg-gray-400 transition-colors"
        >
          {isSubmitting ? "در حال ارسال..." : "ارسال"}
        </button>
      </form>
    </div>
  );
};

export default MyForm;
```

---

### **توضیحات:**
1. **اسکیما با `zod`**:
   - `formSchema`: یه شیء با `zod` تعریف کردم که قوانین اعتبارسنجی رو مشخص می‌کنه:
     - `name`: باید حداقل ۳ کاراکتر باشه و خالی نباشه.
     - `email`: باید فرمت ایمیل درست باشه و خالی نباشه.
     - `password`: باید حداقل ۶ کاراکتر باشه و خالی نباشه.
   - با `z.infer` تایپ داده‌ها رو از اسکیما استخراج کردم.

2. **اتصال به `react-hook-form`**:
   - از `zodResolver` برای اتصال اسکیمای `zod` به فرم استفاده کردم. این کار جایگزین تنظیمات دستی `validation` توی `register` می‌شه.

3. **تغییر در `TextInput`**:
   - چون اعتبارسنجی حالا با `zod` مدیریت می‌شه، دیگه نیازی به پراپ `validation` توی `TextInput` نیست و فقط `register` رو پاس می‌دیم.

4. **استایل با `cva`**:
   - مثل قبل از `cva` برای مدیریت استایل‌های داینامیک استفاده کردم.

---

### **مزایای استفاده از zod:**
- **متمرکز بودن اعتبارسنجی**: همه قوانین توی یه اسکیما تعریف می‌شن و کد تکراری کمتری داری.
- **تایپ‌سیف**: تایپ‌ها به صورت خودکار از اسکیما استخراج می‌شن.
- **انعطاف‌پذیری**: می‌تونی اعتبارسنجی‌های پیچیده‌تر (مثل شرط‌های ترکیبی یا وابسته) رو با `zod` پیاده‌سازی کنی.

---

### **نتیجه**
این کد یه فرم کامل با `zod` برای اعتبارسنجی، `react-hook-form` برای مدیریت فرم و `cva` برای استایل‌های داینامیک بهت می‌ده. اگر می‌خوای اعتبارسنجی پیچیده‌تری اضافه کنم یا چیزی رو تغییر بدم، بگو تا برات درستش کنم!