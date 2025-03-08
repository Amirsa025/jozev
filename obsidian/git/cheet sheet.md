
یادگیری تمام کامندهای Git کار بسیار خوبی است! در اینجا فهرستی از مهم‌ترین و پرکاربردترین دستورات Git را دسته‌بندی شده ارائه می‌دهم. اگر روی بخش خاصی تمرکز داری، بگو تا دقیق‌تر توضیح بدهم.

---

## ۱. تنظیمات اولیه

```sh
git config --global user.name "نام شما"
git config --global user.email "ایمیل شما"
git config --list  # نمایش تنظیمات گیت
git config --global core.editor "vim"  # تغییر ویرایشگر پیش‌فرض
```

---

## ۲. شروع یک مخزن (Repository)

```sh
git init  # ایجاد یک مخزن گیت در یک پوشه
git clone <URL>  # کلون کردن یک مخزن از راه دور
git clone --depth=1 <URL>  # کلون کردن فقط آخرین کامیت‌ها
```

---

## ۳. مدیریت فایل‌ها

```sh
git status  # بررسی وضعیت فعلی
git add <file>  # اضافه کردن فایل خاص به استیج
git add .  # اضافه کردن تمام تغییرات به استیج
git rm <file>  # حذف یک فایل و ثبت آن در گیت
git mv <old> <new>  # تغییر نام یا جابجایی فایل‌ها
```

---

## ۴. ثبت تغییرات (Commit)

```sh
git commit -m "پیام کامیت"  # ایجاد یک کامیت جدید
git commit --amend -m "پیام جدید"  # اصلاح آخرین کامیت
```

---

## ۵. بررسی تاریخچه

```sh
git log  # مشاهده لیست کامیت‌ها
git log --oneline  # نمایش کامیت‌ها در یک خط
git log --graph --oneline --all  # نمایش تاریخچه به صورت گرافیکی
```

---

## ۶. شاخه‌ها (Branches)

```sh
git branch  # نمایش شاخه‌های موجود
git branch <branch-name>  # ایجاد شاخه جدید
git checkout <branch-name>  # تغییر شاخه
git switch <branch-name>  # تغییر شاخه (روش جدیدتر)
git checkout -b <branch-name>  # ایجاد و تغییر شاخه هم‌زمان
git switch -c <branch-name>  # معادل دستور بالا
git branch -d <branch-name>  # حذف شاخه
```

---

## ۷. ادغام و ریبیس (Merge & Rebase)

```sh
git merge <branch-name>  # ادغام یک شاخه در شاخه فعلی
git rebase <branch-name>  # ریبیس شاخه فعلی روی شاخه مورد نظر
git cherry-pick <commit-hash>  # اعمال یک کامیت خاص روی شاخه فعلی
```

---

## ۸. ریست و ریورتی (Reset & Revert)

```sh
git reset --soft HEAD~1  # بازگرداندن آخرین کامیت اما نگه داشتن تغییرات در استیج
git reset --mixed HEAD~1  # بازگرداندن آخرین کامیت و حذف از استیج
git reset --hard HEAD~1  # بازگرداندن کامل و حذف تغییرات
git revert <commit-hash>  # برگرداندن یک کامیت بدون تغییر تاریخچه
```

---

## ۹. کار با مخزن راه دور (Remote)

```sh
git remote add origin <URL>  # اضافه کردن ریموت
git remote -v  # نمایش ریموت‌های موجود
git push -u origin main  # ارسال تغییرات به ریموت
git pull origin main  # دریافت آخرین تغییرات از ریموت
git fetch origin  # دریافت آخرین اطلاعات ریموت بدون ادغام
```

---

## ۱۰. استش و تمیزکاری (Stash & Clean)

```sh
git stash  # ذخیره موقت تغییرات بدون کامیت
git stash pop  # بازگرداندن تغییرات از استش و حذف آن
git stash list  # نمایش لیست استش‌ها
git clean -df  # حذف فایل‌های اضافی که تحت کنترل گیت نیستند
```

---

## ۱۱. بررسی و دیباگ

```sh
git diff  # نمایش تفاوت‌های فایل‌ها
git diff --staged  # نمایش تفاوت‌های استیج شده
git bisect start  # شروع فرآیند یافتن کامیت مشکل‌دار
git bisect bad  # اعلام کامیت مشکل‌دار
git bisect good <commit>  # اعلام کامیت سالم
```

---

## ۱۲. سایر دستورات کاربردی

```sh
git tag <tag-name>  # ایجاد یک تگ
git tag -a <tag-name> -m "توضیح"  # ایجاد یک تگ همراه با توضیحات
git blame <file>  # نمایش تغییرات هر خط از فایل و نویسنده آن
git shortlog -sn  # نمایش لیست نویسندگان پروژه
git reflog  # نمایش تمام تغییرات انجام شده در مخزن
```

---

این فهرست تمام دستورات مهم Git را شامل می‌شود. اگر در بخش خاصی نیاز به توضیح بیشتر یا مثال عملی داری، بگو!