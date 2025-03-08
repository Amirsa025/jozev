 # سناریوهای استفاده از متدهای استاتیک Promise برای یک سنیور فرانت‌اند دولوپر

به عنوان یک سنیور فرانت‌اند دولوپر، در سناریوهای متعددی از متدهای استاتیک Promise استفاده می‌کنم:

## Promise.all()
- **دریافت همزمان داده‌ها برای داشبوردها**: وقتی داشبوردی با چندین بخش مختلف دارم که هر کدام به API جداگانه‌ای نیاز دارند.
- **بارگذاری همزمان منابع**: وقتی نیاز به بارگذاری همزمان تصاویر، فایل‌های CSS یا اسکریپت‌ها دارم.
- **عملیات‌های وابسته به هم**: وقتی به داده‌های چندین درخواست API نیاز دارم تا بتوانم نتیجه نهایی را محاسبه کنم.

```javascript
// مثال: لود همزمان اطلاعات مورد نیاز داشبورد
Promise.all([
  api.getUserProfile(),
  api.getRecentOrders(),
  api.getNotifications(),
  api.getAnalytics()
]).then(([profile, orders, notifications, analytics]) => {
  renderDashboard(profile, orders, notifications, analytics);
}).catch(error => {
  showErrorNotification('خطا در بارگذاری داشبورد');
});
```

## Promise.allSettled()
- **دریافت داده‌های غیر ضروری**: وقتی که می‌خواهم همه درخواست‌ها را انجام دهم، حتی اگر برخی از آنها با خطا مواجه شوند.
- **اسکرپ کردن داده‌ها از منابع مختلف**: وقتی داده‌ها را از منابع مختلف جمع‌آوری می‌کنم و اشکال در یک منبع نباید کل عملیات را متوقف کند.
- **گزارش‌گیری از وضعیت درخواست‌ها**: نمایش وضعیت موفقیت یا شکست هر عملیات به کاربر.

```javascript
// مثال: بارگذاری بخش‌های مختلف صفحه به صورت همزمان
Promise.allSettled([
  loadMainContent(),
  loadSidebar(),
  loadComments(),
  loadRecommendations()
]).then(results => {
  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      renderSection(index, result.value);
    } else {
      renderErrorState(index, 'بخشی از محتوا در دسترس نیست');
      logError(result.reason);
    }
  });
});
```

## Promise.race()
- **پیاده‌سازی timeout**: تنظیم زمان‌بندی برای درخواست‌های API که ممکن است خیلی طول بکشند.
- **کنترل UX بارگذاری**: نمایش اسکلتون لودر اگر درخواست API بیش از مدت زمان مشخصی طول بکشد.
- **تشخیص سریع بودن منابع**: انتخاب سریع‌ترین منبع داده از بین چندین گزینه.

```javascript
// مثال: نمایش لودر اگر درخواست بیش از 500ms طول بکشد
let showLoaderTimeout;
const dataPromise = fetchData();
const timerPromise = new Promise(resolve => {
  showLoaderTimeout = setTimeout(() => {
    showLoader();
    resolve();
  }, 500);
});

Promise.race([dataPromise, timerPromise])
  .then(result => {
    if (result) { // یعنی dataPromise زودتر برگشته
      clearTimeout(showLoaderTimeout);
      hideLoader();
      renderData(result);
    }
  });
```

## Promise.any()
- **درخواست از CDN‌های مختلف**: استفاده از اولین CDN که پاسخ موفق می‌دهد.
- **فالبک API**: ارسال درخواست به چندین نسخه API و استفاده از اولین پاسخ موفق.
- **سرویس‌های ژئولوکیشن**: استفاده از اولین سرویس موقعیت‌یابی که پاسخ می‌دهد.

```javascript
// مثال: بارگذاری فونت از CDN‌های مختلف
const cdnUrls = [
  'https://cdn1.example.com/font.woff2',
  'https://cdn2.example.com/font.woff2',
  'https://cdn3.example.com/font.woff2'
];

const fontLoaders = cdnUrls.map(url => 
  fetch(url)
    .then(response => {
      if (!response.ok) throw new Error('Font not available');
      return response.blob();
    })
    .then(fontBlob => {
      const fontUrl = URL.createObjectURL(fontBlob);
      loadFontIntoDocument(fontUrl);
      return fontUrl;
    })
);

Promise.any(fontLoaders)
  .then(fontUrl => console.log('Font loaded from:', fontUrl))
  .catch(error => {
    console.error('All font CDNs failed:', error);
    showFallbackFont();
  });
```

## Promise.resolve() و Promise.reject()
- **ایجاد ارتباط با کدهای قدیمی**: تبدیل callback‌ها یا مقادیر ساده به Promise برای یکپارچگی با کدهای async/await.
- **پیاده‌سازی کش**: برگرداندن مقادیر کش‌شده به صورت Promise برای سازگاری با بقیه کد.
- **تست‌نویسی**: مکنویسی (mock) سرویس‌های API برای تست‌نویسی راحت‌تر.

```javascript
// مثال: سرویس داده با قابلیت کش
class ProductService {
  constructor() {
    this.cache = new Map();
    this.cacheExpiry = new Map();
  }
  
  getProduct(id) {
    const now = Date.now();
    
    // بررسی اینکه آیا داده در کش وجود دارد و منقضی نشده
    if (this.cache.has(id) && this.cacheExpiry.get(id) > now) {
      console.log('Using cached product data');
      return Promise.resolve(this.cache.get(id));
    }
    
    // در غیر این صورت از API درخواست کن
    return api.fetchProduct(id)
      .then(product => {
        // ذخیره در کش برای ۵ دقیقه
        this.cache.set(id, product);
        this.cacheExpiry.set(id, now + 5 * 60 * 1000);
        return product;
      })
      .catch(error => {
        // اگر قبلاً در کش داریم، از داده قدیمی استفاده کن حتی اگر منقضی شده
        if (this.cache.has(id)) {
          console.warn('API request failed, using stale data');
          return this.cache.get(id);
        }
        return Promise.reject(error);
      });
  }
}
```

## سناریوهای ترکیبی در اپلیکیشن‌های پیچیده

در یک اپلیکیشن بزرگ، اغلب این متدها را با یکدیگر ترکیب می‌کنم:

```javascript
// مثال: بارگذاری صفحه اصلی فروشگاه با منطق پیچیده
async function loadStorePage(storeId) {
  // اول اطلاعات ضروری فروشگاه را می‌گیریم
  const storeInfo = await api.getStoreInfo(storeId);
  
  // سپس به صورت موازی چندین درخواست را انجام می‌دهیم
  const [products, reviews] = await Promise.all([
    api.getStoreProducts(storeId),
    api.getStoreReviews(storeId)
  ]);
  
  // برای محصولات مشابه، از چند سرویس مختلف استفاده می‌کنیم و سریع‌ترین را می‌گیریم
  const similarProductsPromise = Promise.any([
    recommendationService.getSimilarProducts(storeInfo.category),
    legacyRecommendationAPI.getRecommendations(storeId),
    aiRecommendationEngine.getPersonalizedSuggestions(userPreferences)
  ]).catch(() => Promise.resolve([])); // اگر همه شکست خوردند، آرایه خالی برگردان
  
  // برای اطلاعات غیر ضروری از allSettled استفاده می‌کنیم
  const secondaryDataPromise = Promise.allSettled([
    api.getStorePromotions(storeId),
    api.getStoreEvents(storeId),
    api.getRelatedStores(storeInfo.category)
  ]);
  
  // از race برای تایم‌اوت استفاده می‌کنیم
  const timeoutPromise = new Promise((_, reject) => 
    setTimeout(() => reject(new Error('Analytics timed out')), 2000)
  );
  
  const analyticsPromise = Promise.race([
    api.trackStoreVisit(storeId, userId),
    timeoutPromise
  ]).catch(error => {
    console.warn('Analytics error:', error);
    return null; // خطای آنالیتیکس را نادیده می‌گیریم
  });
  
  // منتظر بقیه داده‌ها می‌مانیم
  const [similarProducts, secondaryDataResults, _] = await Promise.all([
    similarProductsPromise,
    secondaryDataPromise,
    analyticsPromise // نتیجه را نمی‌خواهیم، فقط منتظر تکمیل هستیم
  ]);
  
  // پردازش نتایج secondaryData
  const secondaryData = {
    promotions: secondaryDataResults[0].status === 'fulfilled' ? secondaryDataResults[0].value : [],
    events: secondaryDataResults[1].status === 'fulfilled' ? secondaryDataResults[1].value : [],
    relatedStores: secondaryDataResults[2].status === 'fulfilled' ? secondaryDataResults[2].value : []
  };
  
  // حالا همه داده‌ها را داریم و می‌توانیم صفحه را رندر کنیم
  renderStorePage(storeInfo, products, reviews, similarProducts, secondaryData);
}
```

این نوع استفاده ترکیبی از متدهای Promise امکان کنترل دقیق جریان داده، مدیریت خطاها و بهینه‌سازی عملکرد را در اپلیکیشن‌های پیچیده فراهم می‌کند.

