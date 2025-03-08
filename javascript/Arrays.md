# سوالات مصاحبه JavaScript درباره آرایه‌ها

به عنوان مدرس JavaScript با ۱۰ سال سابقه تدریس، ۵۰ سوال رایج مصاحبه درباره آرایه‌ها را همراه با پاسخ‌های کاربردی برای شما آماده کرده‌ام.

## سوالات پایه

### ۱. آرایه در JavaScript چیست؟
**پاسخ:** آرایه در JavaScript یک ساختار داده است که می‌تواند مجموعه‌ای از مقادیر مختلف را در خود ذخیره کند. این مقادیر می‌توانند از انواع مختلف باشند (اعداد، رشته‌ها، اشیاء، توابع و حتی آرایه‌های دیگر). آرایه‌ها با براکت `[]` تعریف می‌شوند و عناصر آن با کاما از هم جدا می‌شوند.

```
let fruits = ['سیب', 'موز', 'پرتقال'];
```

### ۲. روش‌های مختلف ایجاد آرایه در JavaScript را نام ببرید.
**پاسخ:** چند روش برای ایجاد آرایه وجود دارد:
- استفاده از براکت: `let arr = [1, 2, 3];`
- استفاده از سازنده آرایه: `let arr = new Array(1, 2, 3);`
- ایجاد آرایه خالی: `let arr = [];`
- استفاده از `Array.of()`: `let arr = Array.of(1, 2, 3);`
- استفاده از `Array.from()`: `let arr = Array.from("123");`

### ۳. تفاوت بین `new Array()` و `[]` چیست؟
**پاسخ:** هر دو روش آرایه ایجاد می‌کنند، اما تفاوت مهمی دارند:
- وقتی از `new Array(n)` با یک عدد استفاده می‌کنید، آرایه‌ای با طول `n` و عناصر خالی ایجاد می‌شود.
- اما `[n]` آرایه‌ای با یک عنصر که مقدار آن `n` است ایجاد می‌کند.

```
let arr1 = new Array(3); // [empty × 3]
let arr2 = [3]; // [3]
```

### ۴. چگونه به عناصر یک آرایه دسترسی پیدا می‌کنیم؟
**پاسخ:** با استفاده از براکت و اندیس عنصر مورد نظر. اندیس‌ها از صفر شروع می‌شوند:

```
let fruits = ['سیب', 'موز', 'پرتقال'];
console.log(fruits[0]); // سیب
console.log(fruits[1]); // موز
console.log(fruits[2]); // پرتقال
```

### ۵. چگونه طول یک آرایه را بدست می‌آوریم؟
**پاسخ:** با استفاده از خاصیت `length`:

```
let fruits = ['سیب', 'موز', 'پرتقال'];
console.log(fruits.length); // 3
```

## متدهای آرایه

### ۶. تفاوت بین متدهای `push()` و `unshift()` چیست؟
**پاسخ:** 
- `push()`: عناصر را به انتهای آرایه اضافه می‌کند و طول جدید آرایه را برمی‌گرداند.
- `unshift()`: عناصر را به ابتدای آرایه اضافه می‌کند و طول جدید آرایه را برمی‌گرداند.

```
let arr = [2, 3];
arr.push(4); // [2, 3, 4]
arr.unshift(1); // [1, 2, 3, 4]
```

### ۷. تفاوت بین متدهای `pop()` و `shift()` چیست؟
**پاسخ:**
- `pop()`: آخرین عنصر آرایه را حذف می‌کند و آن را برمی‌گرداند.
- `shift()`: اولین عنصر آرایه را حذف می‌کند و آن را برمی‌گرداند.

```
let arr = [1, 2, 3, 4];
let last = arr.pop(); // last = 4, arr = [1, 2, 3]
let first = arr.shift(); // first = 1, arr = [2, 3]
```

### ۸. متد `splice()` چگونه کار می‌کند؟
**پاسخ:** متد `splice()` برای افزودن، حذف یا جایگزینی عناصر آرایه استفاده می‌شود:

```
let arr = [1, 2, 3, 4, 5];

// حذف 2 عنصر از اندیس 1
arr.splice(1, 2); // arr = [1, 4, 5]

// افزودن عناصر در اندیس 1
arr.splice(1, 0, 2, 3); // arr = [1, 2, 3, 4, 5]

// جایگزینی 2 عنصر از اندیس 1
arr.splice(1, 2, 'a', 'b'); // arr = [1, 'a', 'b', 4, 5]
```

### ۹. متد `slice()` چگونه کار می‌کند و تفاوت آن با `splice()` چیست؟
**پاسخ:** متد `slice()` بخشی از آرایه را بدون تغییر آرایه اصلی برمی‌گرداند:

```
let arr = [1, 2, 3, 4, 5];
let newArr = arr.slice(1, 4); // newArr = [2, 3, 4], arr بدون تغییر می‌ماند
```

تفاوت‌های اصلی با `splice()`:
- `slice()` آرایه اصلی را تغییر نمی‌دهد، اما `splice()` آرایه اصلی را تغییر می‌دهد.
- `slice()` دو پارامتر می‌گیرد (شروع و پایان)، اما `splice()` حداقل دو پارامتر می‌گیرد (شروع، تعداد حذف، و عناصر جدید).

### ۱۰. متد `concat()` چه کاری انجام می‌دهد؟
**پاسخ:** متد `concat()` آرایه‌ها را با هم ترکیب می‌کند و آرایه جدیدی برمی‌گرداند، بدون اینکه آرایه‌های اصلی را تغییر دهد:

```
let arr1 = [1, 2];
let arr2 = [3, 4];
let arr3 = arr1.concat(arr2); // arr3 = [1, 2, 3, 4]
```

### ۱۱. متد `join()` چه کاری انجام می‌دهد؟
**پاسخ:** متد `join()` تمام عناصر آرایه را به یک رشته تبدیل می‌کند و با جداکننده مشخص شده آنها را به هم متصل می‌کند:

```
let arr = ['سلام', 'دنیا'];
console.log(arr.join(' ')); // "سلام دنیا"
console.log(arr.join('-')); // "سلام-دنیا"
console.log(arr.join('')); // "سلامدنیا"
```

### ۱۲. متد `indexOf()` و `lastIndexOf()` چه کاری انجام می‌دهند؟
**پاسخ:**
- `indexOf()`: اولین اندیس عنصر مورد نظر را برمی‌گرداند، اگر پیدا نشود -1 برمی‌گرداند.
- `lastIndexOf()`: آخرین اندیس عنصر مورد نظر را برمی‌گرداند، اگر پیدا نشود -1 برمی‌گرداند.

```
let arr = [1, 2, 3, 2, 1];
console.log(arr.indexOf(2)); // 1
console.log(arr.lastIndexOf(2)); // 3
console.log(arr.indexOf(4)); // -1
```

### ۱۳. متد `includes()` چه کاری انجام می‌دهد؟
**پاسخ:** بررسی می‌کند آیا آرایه شامل عنصر خاصی است یا خیر و مقدار بولین برمی‌گرداند:

```
let arr = [1, 2, 3];
console.log(arr.includes(2)); // true
console.log(arr.includes(4)); // false
```

### ۱۴. متد `reverse()` چه کاری انجام می‌دهد؟
**پاسخ:** ترتیب عناصر آرایه را معکوس می‌کند و آرایه اصلی را تغییر می‌دهد:

```
let arr = [1, 2, 3];
arr.reverse(); // arr = [3, 2, 1]
```

### ۱۵. متد `sort()` چگونه کار می‌کند؟
**پاسخ:** عناصر آرایه را مرتب می‌کند و آرایه اصلی را تغییر می‌دهد. به طور پیش‌فرض، عناصر را به عنوان رشته در نظر می‌گیرد و بر اساس کد UTF-16 مرتب می‌کند:

```
let fruits = ['پرتقال', 'سیب', 'موز'];
fruits.sort(); // ['پرتقال', 'سیب', 'موز'] (بر اساس حروف الفبا)

let numbers = [10, 5, 40, 25];
numbers.sort(); // [10, 25, 40, 5] (به عنوان رشته مرتب می‌شوند)

// برای مرتب‌سازی عددی صحیح:
numbers.sort((a, b) => a - b); // [5, 10, 25, 40]
```

## متدهای پیمایش آرایه

### ۱۶. متد `forEach()` چگونه کار می‌کند؟
**پاسخ:** تابعی را برای هر عنصر آرایه اجرا می‌کند:

```
let arr = [1, 2, 3];
arr.forEach((item, index) => {
    console.log(`عنصر ${item} در اندیس ${index} قرار دارد`);
});
```

### ۱۷. متد `map()` چگونه کار می‌کند؟
**پاسخ:** آرایه جدیدی ایجاد می‌کند که نتیجه فراخوانی تابع ارائه شده بر روی هر عنصر آرایه اصلی است:

```
let arr = [1, 2, 3];
let doubled = arr.map(item => item * 2); // [2, 4, 6]
```

### ۱۸. متد `filter()` چگونه کار می‌کند؟
**پاسخ:** آرایه جدیدی ایجاد می‌کند که شامل عناصری است که شرط تابع ارائه شده را برآورده می‌کنند:

```
let arr = [1, 2, 3, 4, 5];
let evens = arr.filter(item => item % 2 === 0); // [2, 4]
```

### ۱۹. متد `reduce()` چگونه کار می‌کند؟
**پاسخ:** آرایه را به یک مقدار واحد کاهش می‌دهد با اجرای تابع روی هر عنصر و انباشت نتیجه:

```
let arr = [1, 2, 3, 4];
let sum = arr.reduce((accumulator, current) => accumulator + current, 0); // 10
```

### ۲۰. متد `find()` چگونه کار می‌کند؟
**پاسخ:** اولین عنصری را که شرط تابع ارائه شده را برآورده می‌کند برمی‌گرداند:

```
let arr = [5, 12, 8, 130, 44];
let found = arr.find(element => element > 10); // 12
```

### ۲۱. تفاوت بین `find()` و `filter()` چیست؟
**پاسخ:**
- `find()`: اولین عنصری که شرط را برآورده می‌کند برمی‌گرداند.
- `filter()`: تمام عناصری که شرط را برآورده می‌کنند در یک آرایه جدید برمی‌گرداند.

```
let arr = [5, 12, 8, 130, 44];
let found = arr.find(element => element > 10); // 12
let filtered = arr.filter(element => element > 10); // [12, 130, 44]
```

### ۲۲. متد `findIndex()` چگونه کار می‌کند؟
**پاسخ:** اندیس اولین عنصری را که شرط تابع ارائه شده را برآورده می‌کند برمی‌گرداند:

```
let arr = [5, 12, 8, 130, 44];
let foundIndex = arr.findIndex(element => element > 10); // 1
```

### ۲۳. متد `every()` چگونه کار می‌کند؟
**پاسخ:** بررسی می‌کند آیا تمام عناصر آرایه شرط تابع ارائه شده را برآورده می‌کنند یا خیر:

```
let arr = [1, 30, 39, 29, 10, 13];
let allBelow40 = arr.every(element => element < 40); // true
let allEven = arr.every(element => element % 2 === 0); // false
```

### ۲۴. متد `some()` چگونه کار می‌کند؟
**پاسخ:** بررسی می‌کند آیا حداقل یک عنصر آرایه شرط تابع ارائه شده را برآورده می‌کند یا خیر:

```
let arr = [1, 2, 3, 4, 5];
let hasEven = arr.some(element => element % 2 === 0); // true
let hasNegative = arr.some(element => element < 0); // false
```

## سوالات پیشرفته

### ۲۵. چگونه می‌توان عناصر تکراری را از یک آرایه حذف کرد؟
**پاسخ:** چند روش وجود دارد:

1. استفاده از `Set`:
```
let arr = [1, 2, 2, 3, 4, 4, 5];
let uniqueArr = [...new Set(arr)]; // [1, 2, 3, 4, 5]
```

1. استفاده از `filter`:
```
let arr = [1, 2, 2, 3, 4, 4, 5];
let uniqueArr = arr.filter((item, index) => arr.indexOf(item) === index); // [1, 2, 3, 4, 5]
```

### ۲۶. چگونه می‌توان آرایه را به صورت تصادفی بهم ریخت (shuffle)؟
**پاسخ:** یک روش معروف، الگوریتم Fisher-Yates است:

```
function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
}

let arr = [1, 2, 3, 4, 5];
shuffleArray(arr); // آرایه به صورت تصادفی بهم ریخته می‌شود
```

### ۲۷. چگونه می‌توان بزرگترین یا کوچکترین عدد در یک آرایه را پیدا کرد؟
**پاسخ:** چند روش وجود دارد:

1. استفاده از `Math.max()` و `Math.min()`:
```
let arr = [5, 1, 3, 8, 2];
let max = Math.max(...arr); // 8
let min = Math.min(...arr); // 1
```

1. استفاده از `reduce()`:
```
let arr = [5, 1, 3, 8, 2];
let max = arr.reduce((a, b) => Math.max(a, b), -Infinity); // 8
let min = arr.reduce((a, b) => Math.min(a, b), Infinity); // 1
```

### ۲۸. چگونه می‌توان آرایه را به چند بخش مساوی تقسیم کرد؟
**پاسخ:** می‌توان از `slice()` استفاده کرد:

```
function chunkArray(array, size) {
    const result = [];
    for (let i = 0; i < array.length; i += size) {
        result.push(array.slice(i, i + size));
    }
    return result;
}

let arr = [1, 2, 3, 4, 5, 6, 7, 8];
console.log(chunkArray(arr, 3)); // [[1, 2, 3], [4, 5, 6], [7, 8]]
```

### ۲۹. چگونه می‌توان دو آرایه را مقایسه کرد که آیا دقیقاً یکسان هستند؟
**پاسخ:** یک روش ساده:

```
function arraysEqual(a, b) {
    if (a.length !== b.length) return false;
    for (let i = 0; i < a.length; i++) {
        if (a[i] !== b[i]) return false;
    }
    return true;
}

console.log(arraysEqual([1, 2, 3], [1, 2, 3])); // true
console.log(arraysEqual([1, 2, 3], [1, 3, 2])); // false
```

### ۳۰. چگونه می‌توان اشتراک دو آرایه را پیدا کرد؟
**پاسخ:** با استفاده از `filter` و `includes`:

```
function intersection(arr1, arr2) {
    return arr1.filter(item => arr2.includes(item));
}

console.log(intersection([1, 2, 3, 4], [3, 4, 5, 6])); // [3, 4]
```

### ۳۱. چگونه می‌توان اجتماع دو آرایه را بدون تکرار عناصر پیدا کرد؟
**پاسخ:** با استفاده از `Set`:

```
function union(arr1, arr2) {
    return [...new Set([...arr1, ...arr2])];
}

console.log(union([1, 2, 3], [3, 4, 5])); // [1, 2, 3, 4, 5]
```

### ۳۲. چگونه می‌توان تفاضل دو آرایه را پیدا کرد؟
**پاسخ:** عناصری از آرایه اول که در آرایه دوم نیستند:

```
function difference(arr1, arr2) {
    return arr1.filter(item => !arr2.includes(item));
}

console.log(difference([1, 2, 3, 4], [3, 4, 5, 6])); // [1, 2]
```

### ۳۳. چگونه می‌توان آرایه را بر اساس یک خاصیت از اشیاء درون آن مرتب کرد؟
**پاسخ:** با استفاده از متد `sort()`:

```
let people = [
    { name: 'علی', age: 30 },
    { name: 'مریم', age: 25 },
    { name: 'رضا', age: 35 }
];

// مرتب‌سازی بر اساس سن
people.sort((a, b) => a.age - b.age);
console.log(people); // [{ name: 'مریم', age: 25 }, { name: 'علی', age: 30 }, { name: 'رضا', age: 35 }]

// مرتب‌سازی بر اساس نام
people.sort((a, b) => a.name.localeCompare(b.name));
console.log(people); // [{ name: 'رضا', age: 35 }, { name: 'علی', age: 30 }, { name: 'مریم', age: 25 }]
```

### ۳۴. چگونه می‌توان آرایه را گروه‌بندی کرد؟
**پاسخ:** با استفاده از `reduce`:

```
let people = [
    { name: 'علی', age: 30 },
    { name: 'مریم', age: 25 },
    { name: 'رضا', age: 30 },
    { name: 'سارا', age: 25 }
];

let groupedByAge = people.reduce((acc, person) => {
    (acc[person.age] = acc[person.age] || []).push(person);
    return acc;
}, {});

console.log(groupedByAge);
/*
{
  25: [{ name: 'مریم', age: 25 }, { name: 'سارا', age: 25 }],
  30: [{ name: 'علی', age: 30 }, { name: 'رضا', age: 30 }]
}
*/
```

### ۳۵. چگونه می‌توان آرایه‌ای از اعداد را جمع زد؟
**پاسخ:** چند روش وجود دارد:

1. استفاده از `reduce()`:
```
let arr = [1, 2, 3, 4, 5];
let sum = arr.reduce((total, num) => total + num, 0); // 15
```

1. استفاده از حلقه:
```
let arr = [1, 2, 3, 4, 5];
let sum = 0;
for (let num of arr) {
    sum += num;
}
console.log(sum); // 15
```

### ۳۶. چگونه می‌توان آرایه‌ای از اعداد را میانگین گرفت؟
**پاسخ:** با استفاده از `reduce()`:

```
let arr = [1, 2, 3, 4, 5];
let average = arr.reduce((total, num) => total + num, 0) / arr.length; // 3
```

### ۳۷. چگونه می‌توان یک آرایه چند بعدی را به یک آرایه یک بعدی تبدیل کرد (flat)؟
**پاسخ:** چند روش وجود دارد:

1. استفاده از متد `flat()`:
```
let arr = [1, [2, 3], [4, [5, 6]]];
let flattened = arr.flat(2); // [1, 2, 3, 4, 5, 6]
```

1. استفاده از `reduce()`:
```
function flattenArray(arr) {
    return arr.reduce((acc, val) => acc.concat(Array.isArray(val) ? flattenArray(val) : val), []);
}

let arr = [1, [2, 3], [4, [5, 6]]];
console.log(flattenArray(arr)); // [1, 2, 3, 4, 5, 6]
```

### ۳۸. چگونه می‌توان یک آرایه را کپی کرد؟
**پاسخ:** چند روش وجود دارد:

1. استفاده از عملگر spread:
```
let original = [1, 2, 3];
let copy = [...original];
```

1. استفاده از `slice()`:
```
let original = [1, 2, 3];
let copy = original.slice();
```

1. استفاده از `Array.from()`:
```
let original = [1, 2, 3];
let copy = Array.from(original);
```

1. استفاده از `concat()`:
```
let original = [1, 2, 3];
let copy = [].concat(original);
```

### ۳۹. چگونه می‌توان یک آرایه را به صورت عمیق کپی کرد (deep copy)؟
**پاسخ:** برای کپی عمیق آرایه‌ای که شامل اشیاء یا آرایه‌های تو در تو است:

```
function deepCopy(arr) {
    return JSON.parse(JSON.stringify(arr));
}

let original = [1, {a: 2}, [3, 4]];
let copy = deepCopy(original);
```

### ۴۰. چگونه می‌توان بررسی کرد که یک متغیر آرایه است یا خیر؟
**پاسخ:** چند روش وجود دارد:

1. استفاده از `Array.isArray()`:
```
console.log(Array.isArray([1, 2, 3])); // true
console.log(Array.isArray({a: 1})); // false
```

1. استفاده از `instanceof`:
```
console.log([1, 2, 3] instanceof Array); // true
```

### ۴۱. چگونه می‌توان یک آرایه را به یک شیء تبدیل کرد؟
**پاسخ:** با استفاده از `reduce()`:

```
let arr = ['a', 'b', 'c'];
let obj = arr.reduce((acc, curr, i) => {
    acc[i] = curr;
    return acc;
}, {});
console.log(obj); // {0: 'a', 1: 'b', 2: 'c'}

// یا با استفاده از Object.assign:
let obj2 = Object.assign({}, arr);
console.log(obj2); // {0: 'a', 1: 'b', 2: 'c'}
```

### ۴۲. چگونه می‌توان یک شیء را به آرایه تبدیل کرد؟
**پاسخ:** چند روش وجود دارد:

1. تبدیل مقادیر به آرایه:
```
let obj = {a: 1, b: 2, c: 3};
let values = Object.values(obj); // [1, 2, 3]
```

1. تبدیل کلیدها به آرایه:
```
let obj = {a: 1, b: 2, c: 3};
let keys = Object.keys(obj); // ['a', 'b', 'c']
```

1. تبدیل جفت‌های کلید-مقدار به آرایه:
```
let obj = {a: 1, b: 2, c: 3};
let entries = Object.entries(obj); // [['a', 1], ['b', 2], ['c', 3]]
```

### ۴۳. چگونه می‌توان یک آرایه را به صورت تصادفی انتخ