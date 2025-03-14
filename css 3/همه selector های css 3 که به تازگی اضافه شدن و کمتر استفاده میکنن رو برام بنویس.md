سلکتورهای CSS3 ابزارهای قدرتمندی هستند که امکان انتخاب دقیق‌تر و استایل‌دهی به عناصر HTML را فراهم می‌کنند. در اینجا تعدادی از سلکتورهای CSS3 که ممکن است کمتر استفاده شده باشند، آورده شده است[1][3]:

**1. سلکتورهای ساختاری (Structural pseudo-classes):**
   *   `:first-of-type`: اولین فرزند از یک نوع خاص را در داخل یک عنصر والد انتخاب می‌کند[1].
   *   `:last-of-type`: آخرین فرزند از یک نوع خاص را در داخل یک عنصر والد انتخاب می‌کند[1].
   *   `:only-child`: عنصری را انتخاب می‌کند که تنها فرزند والد خود باشد[1].
   *   `:only-of-type`: عنصری را انتخاب می‌کند که تنها فرزند از نوع خود در داخل والدش باشد.
   *   `:nth-child(n)`: عنصری را انتخاب می‌کند که nامین فرزند والد خود باشد[1].
   *   `:nth-last-child(n)`:  عنصری را انتخاب می‌کند که nامین فرزند از آخر والد خود باشد[1].
   *   `:nth-of-type(n)`: عنصری را انتخاب می‌کند که nامین فرزند از نوع خود در داخل والدش باشد.
   *   `:nth-last-of-type(n)`: عنصری را انتخاب می‌کند که nامین فرزند از نوع خود از آخر در داخل والدش باشد.

**2. سلکتورهای ویژگی (Attribute selectors):**
   *   `[attribute^=value]`: عناصری را انتخاب می‌کند که مقدار ویژگی آن‌ها با مقدار معینی شروع می‌شود[2].
   *   `[attribute$=value]`: عناصری را انتخاب می‌کند که مقدار ویژگی آن‌ها با مقدار معینی تمام می‌شود[2].
   *   `[attribute*=value]`: عناصری را انتخاب می‌کند که مقدار ویژگی آن‌ها شامل مقدار معینی باشد.

**3. سلکتورهای ترکیبی (Combinators):**
   *   `element + element`:  عنصر همجوار (Adjacent Sibling) را انتخاب می‌کند. این سلکتور فقط عنصرهای همجوار عنصر انتخاب شده را انتخاب کرده و استایل دهی می‌کند[2].
   *   `element ~ element`:  عناصر خواهر و برادر عمومی (General Sibling) را انتخاب می‌کند.  این سلکتور نیز همانند سلکتور بالا عمل می‌کند با این تفاوت که دیگر فقط عنصر مجاور انتخاب نمی‌شود، بلکه تمامی عنصرهایی که پس از عنصر انتخاب شده وجود دارند را انتخاب کرده و استایل دهی می‌کند[2].

**4. سلکتور نفی (Negation pseudo-class):**
   *   `:not(selector)`:  عناصری را انتخاب می‌کند که با سلکتور مشخص شده مطابقت نداشته باشند[1].

**مثال‌ها:**

*   انتخاب اولین تگ `<a>` در یک `<div>`:

```css
div a:first-of-type {
  color: red;
}
```

*   انتخاب تمام عناصر `<div>` که کلاس آن‌ها با "test" شروع می‌شود:

```css
div[class^="test"] {
  background: brown;
  color: white;
}
```

*   انتخاب تمام تگ‌هایی که کلاس `green` ندارند:

```css
:not(.green) {
    border: 1px solid red;
}
```

این سلکتورها به شما کمک می‌کنند تا استایل‌های دقیق‌تر و منعطف‌تری ایجاد کنید. استفاده از این سلکتورها می‌تواند به بهبود خوانایی و نگهداری کد CSS شما کمک کند[1][3].

Citations:
[1] https://frontcast.ir/css-selectors/
[2] https://programstore.ir/%D8%B3%D9%84%DA%A9%D8%AA%D9%88%D8%B1%D9%87%D8%A7-%D9%88-%D8%A7%D9%86%D9%88%D8%A7%D8%B9-%D8%A2%D9%86-%D8%AF%D8%B1-css/
[3] https://www.atriya.com/Blog/ArticleDetails/%D8%A2%D9%85%D9%88%D8%B2%D8%B4-%D8%B3%D9%84%DA%A9%D8%AA%D9%88%D8%B1%D9%87%D8%A7-%D8%AF%D8%B1-css
[4] https://sabzlearn.ir/blog/css-selectors/
[5] https://free-learn.ir/%D8%A7%D9%86%D8%AA%D8%AE%D8%A7%D8%A8%DA%AF%D8%B1%D9%87%D8%A7-%DB%8C%D8%A7-selectors-%D8%AF%D8%B1-css/
[6] https://megademy.ir/2764/02/
[7] https://roocket.ir/articles/how-to-write-css-advanced-selectors-like-a-pro
[8] https://frontcast.ir/how-to-use-css-selectors/