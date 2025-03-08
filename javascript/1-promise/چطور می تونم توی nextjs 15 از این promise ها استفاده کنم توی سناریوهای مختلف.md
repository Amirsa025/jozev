 استفاده از FetchWrapper در Next.js 15 می‌تواند مدیریت درخواست‌های HTTP را بسیار ساده‌تر کند. یک FetchWrapper کلاسی است که API درخواست‌های شبکه را کپسوله می‌کند و امکاناتی مانند مدیریت خطا، ریترای (retry) خودکار، و تنظیم هدرها به صورت متمرکز را فراهم می‌کند.

بیایید یک FetchWrapper کامل برای استفاده در Next.js 15 ایجاد کنیم:


```javascript 
// lib/fetchWrapper.js

/**
 * یک Wrapper برای متد fetch که امکانات بیشتری مانند مدیریت خطا، تنظیم هدرها،
 * تلاش مجدد و کش‌کردن را فراهم می‌کند
 */
class FetchWrapper {
  constructor(baseUrl = '') {
    this.baseUrl = baseUrl;
    this.headers = {
      'Content-Type': 'application/json',
    };
  }

  /**
   * تنظیم توکن احراز هویت
   * @param {string} token - توکن احراز هویت
   */
  setAuthToken(token) {
    if (token) {
      this.headers['Authorization'] = `Bearer ${token}`;
    } else {
      delete this.headers['Authorization'];
    }
    return this;
  }

  /**
   * تنظیم سرآیندهای اضافی
   * @param {Object} headers - سرآیندهای اضافی
   */
  setHeaders(headers) {
    this.headers = { ...this.headers, ...headers };
    return this;
  }

  /**
   * افزودن یک سرآیند جدید
   * @param {string} key - کلید سرآیند
   * @param {string} value - مقدار سرآیند
   */
  addHeader(key, value) {
    this.headers[key] = value;
    return this;
  }

  /**
   * اجرای درخواست با امکان تلاش مجدد
   * @param {string} url - آدرس درخواست
   * @param {Object} options - تنظیمات درخواست
   * @param {number} retries - تعداد تلاش مجدد
   * @param {number} retryDelay - تاخیر بین تلاش‌ها (میلی‌ثانیه)
   */
  async fetchWithRetry(url, options, retries = 3, retryDelay = 300) {
    try {
      const response = await fetch(url, options);
      
      if (!response.ok && retries > 0) {
        // تاخیر قبل از تلاش مجدد
        await new Promise(resolve => setTimeout(resolve, retryDelay));
        
        // تلاش مجدد با تعداد کمتر
        return this.fetchWithRetry(
          url, 
          options, 
          retries - 1, 
          retryDelay * 2 // افزایش زمان تاخیر در هر تلاش (exponential backoff)
        );
      }
      
      return response;
    } catch (error) {
      if (retries > 0) {
        // تاخیر قبل از تلاش مجدد
        await new Promise(resolve => setTimeout(resolve, retryDelay));
        
        // تلاش مجدد با تعداد کمتر
        return this.fetchWithRetry(
          url, 
          options, 
          retries - 1, 
          retryDelay * 2
        );
      }
      
      throw error;
    }
  }

  /**
   * ایجاد آدرس کامل
   * @param {string} endpoint - مسیر نسبی
   */
  getFullUrl(endpoint) {
    return `${this.baseUrl}${endpoint}`;
  }

  /**
   * پردازش پاسخ دریافتی
   * @param {Response} response - پاسخ دریافتی از سرور
   */
  async handleResponse(response) {
    const contentType = response.headers.get('content-type');
    
    if (!response.ok) {
      let errorData;
      
      try {
        if (contentType && contentType.includes('application/json')) {
          errorData = await response.json();
        } else {
          errorData = await response.text();
        }
      } catch (e) {
        errorData = 'خطای نامشخص';
      }
      
      const error = new Error(
        typeof errorData === 'object' && errorData.message 
          ? errorData.message 
          : `درخواست با خطای ${response.status} مواجه شد`
      );
      
      error.status = response.status;
      error.data = errorData;
      throw error;
    }
    
    if (contentType && contentType.includes('application/json')) {
      return response.json();
    }
    
    if (contentType && contentType.includes('text/')) {
      return response.text();
    }
    
    return response;
  }

  /**
   * اجرای یک درخواست GET
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} params - پارامترهای درخواست
   * @param {number} retries - تعداد تلاش مجدد در صورت خطا
   */
  async get(endpoint, params = {}, retries = 3) {
    const url = new URL(this.getFullUrl(endpoint));
    
    // افزودن پارامترهای درخواست به URL
    Object.keys(params).forEach(key => {
      if (params[key] !== undefined && params[key] !== null) {
        url.searchParams.append(key, params[key]);
      }
    });
    
    const response = await this.fetchWithRetry(
      url.toString(),
      {
        method: 'GET',
        headers: this.headers,
      },
      retries
    );
    
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست POST
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} data - داده‌های درخواست
   * @param {number} retries - تعداد تلاش مجدد در صورت خطا
   */
  async post(endpoint, data = {}, retries = 3) {
    const response = await this.fetchWithRetry(
      this.getFullUrl(endpoint),
      {
        method: 'POST',
        headers: this.headers,
        body: JSON.stringify(data),
      },
      retries
    );
    
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست PUT
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} data - داده‌های درخواست
   * @param {number} retries - تعداد تلاش مجدد در صورت خطا
   */
  async put(endpoint, data = {}, retries = 3) {
    const response = await this.fetchWithRetry(
      this.getFullUrl(endpoint),
      {
        method: 'PUT',
        headers: this.headers,
        body: JSON.stringify(data),
      },
      retries
    );
    
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست PATCH
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} data - داده‌های درخواست
   * @param {number} retries - تعداد تلاش مجدد در صورت خطا
   */
  async patch(endpoint, data = {}, retries = 3) {
    const response = await this.fetchWithRetry(
      this.getFullUrl(endpoint),
      {
        method: 'PATCH',
        headers: this.headers,
        body: JSON.stringify(data),
      },
      retries
    );
    
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست DELETE
   * @param {string} endpoint - مسیر نسبی
   * @param {number} retries - تعداد تلاش مجدد در صورت خطا
   */
  async delete(endpoint, retries = 3) {
    const response = await this.fetchWithRetry(
      this.getFullUrl(endpoint),
      {
        method: 'DELETE',
        headers: this.headers,
      },
      retries
    );
    
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست با تایم‌اوت
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} options - تنظیمات درخواست
   * @param {number} timeout - زمان تایم‌اوت (میلی‌ثانیه)
   */
  async fetchWithTimeout(endpoint, options, timeout = 5000) {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), timeout);
    
    try {
      const response = await fetch(
        this.getFullUrl(endpoint), 
        {
          ...options,
          signal: controller.signal
        }
      );
      
      clearTimeout(timeoutId);
      return this.handleResponse(response);
    } catch (error) {
      clearTimeout(timeoutId);
      
      if (error.name === 'AbortError') {
        throw new Error(`درخواست بعد از ${timeout} میلی‌ثانیه با تایم‌اوت مواجه شد`);
      }
      
      throw error;
    }
  }
}

// ایجاد یک نمونه با آدرس پایه API
const api = new FetchWrapper(process.env.NEXT_PUBLIC_API_URL || 'https://api.example.com');

export default api;

```


حال اجازه دهید نشان دهم چگونه می‌توانید از این FetchWrapper در سناریوهای مختلف استفاده کنید:

## 1. استفاده در Server Components:

```javascript
// app/users/page.js (Server Component)
import api from '@/lib/fetchWrapper';

export default async function UsersPage() {
  try {
    // درخواست با retry خودکار
    const users = await api.get('/users');
    
    return (
      <div>
        <h1>لیست کاربران</h1>
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      </div>
    );
  } catch (error) {
    return <div>خطا در بارگذاری کاربران: {error.message}</div>;
  }
}
```

## 2. استفاده در Server Actions:

```javascript
// app/actions.js
'use server'

import api from '@/lib/fetchWrapper';
import { cookies } from 'next/headers';

export async function createPost(formData) {
  try {
    // دریافت توکن از کوکی
    const token = cookies().get('auth_token')?.value;
    
    // تنظیم توکن احراز هویت
    api.setAuthToken(token);
    
    // ارسال درخواست
    const result = await api.post('/posts', {
      title: formData.get('title'),
      content: formData.get('content')
    });
    
    return { success: true, data: result };
  } catch (error) {
    return { 
      success: false, 
      error: error.message 
    };
  }
}
```

## 3. استفاده در Client Components:

```javascript
'use client'
// app/profile/edit-form.js
import { useState } from 'react';
import api from '@/lib/fetchWrapper';

export default function EditProfileForm({ userId, initialData }) {
  const [formData, setFormData] = useState(initialData);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    setError(null);
    
    try {
      // خواندن توکن از localStorage در سمت کلاینت
      const token = localStorage.getItem('auth_token');
      
      // ارسال درخواست با تنظیم header
      const result = await api
        .setAuthToken(token)
        .put(`/users/${userId}`, formData);
      
      alert('پروفایل با موفقیت به‌روزرسانی شد');
    } catch (error) {
      setError(error.message);
    } finally {
      setLoading(false);
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      {error && <div className="error">{error}</div>}
      
      <div>
        <label htmlFor="name">نام:</label>
        <input
          type="text"
          id="name"
          name="name"
          value={formData.name}
          onChange={handleChange}
        />
      </div>
      
      <div>
        <label htmlFor="email">ایمیل:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
        />
      </div>
      
      <button type="submit" disabled={loading}>
        {loading ? 'در حال ارسال...' : 'ذخیره تغییرات'}
      </button>
    </form>
  );
}
```

## 4. استفاده با تایم‌اوت (برای API‌های کند):

```javascript
// app/search/page.js
import api from '@/lib/fetchWrapper';

export default async function SearchPage({ searchParams }) {
  const query = searchParams.q || '';
  
  try {
    // درخواست با تایم‌اوت 3 ثانیه
    const searchResults = await api.fetchWithTimeout(
      '/search',
      {
        method: 'GET',
        headers: api.headers,
      },
      3000 // 3 seconds timeout
    );
    
    return (
      <div>
        <h1>نتایج جستجو برای: {query}</h1>
        {/* نمایش نتایج */}
      </div>
    );
  } catch (error) {
    if (error.message.includes('تایم‌اوت')) {
      return <div>جستجو زمان زیادی طول کشید. لطفاً دوباره تلاش کنید.</div>;
    }
    
    return <div>خطا در جستجو: {error.message}</div>;
  }
}
```

## 5. ایجاد یک Hook کاربردی برای استفاده در سمت کلاینت:

```javascript
// hooks/useApi.js
'use client'

import { useState, useEffect, useCallback } from 'react';
import api from '@/lib/fetchWrapper';

export function useApi(endpoint, params = {}, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  const { initialData, deps = [], withAuth = true } = options;
  
  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      
      if (withAuth) {
        const token = localStorage.getItem('auth_token');
        api.setAuthToken(token);
      }
      
      const result = await api.get(endpoint, params);
      setData(result);
      setError(null);
    } catch (err) {
      setError(err.message);
      setData(initialData);
    } finally {
      setLoading(false);
    }
  }, [endpoint, ...deps]);
  
  useEffect(() => {
    fetchData();
  }, [fetchData]);
  
  const refetch = useCallback(() => {
    return fetchData();
  }, [fetchData]);
  
  return { data, loading, error, refetch };
}

// نحوه استفاده:
// const { data, loading, error, refetch } = useApi('/users', { page: 1 });
```

## 6. مدیریت Upload فایل:

```javascript
// اضافه کنید به fetchWrapper.js

/**
 * آپلود فایل
 * @param {string} endpoint - مسیر نسبی
 * @param {FormData} formData - داده‌های فرم شامل فایل
 * @param {Function} onProgress - تابع callback برای پیشرفت آپلود
 */
async uploadFile(endpoint, formData, onProgress = null) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    
    // تنظیم رویداد progress
    if (onProgress) {
      xhr.upload.addEventListener('progress', (event) => {
        if (event.lengthComputable) {
          const percentComplete = (event.loaded / event.total) * 100;
          onProgress(percentComplete);
        }
      });
    }
    
    // تنظیم رویداد load
    xhr.onload = () => {
      if (xhr.status >= 200 && xhr.status < 300) {
        let response;
        try {
          response = JSON.parse(xhr.responseText);
        } catch (e) {
          response = xhr.responseText;
        }
        resolve(response);
      } else {
        reject(new Error(`خطای ${xhr.status}: ${xhr.statusText}`));
      }
    };
    
    // تنظیم رویداد error
    xhr.onerror = () => {
      reject(new Error('خطا در ارتباط با سرور'));
    };
    
    // شروع درخواست
    xhr.open('POST', this.getFullUrl(endpoint));
    
    // اضافه کردن هدرهای احراز هویت
    if (this.headers['Authorization']) {
      xhr.setRequestHeader('Authorization', this.headers['Authorization']);
    }
    
    xhr.send(formData);
  });
}
```

و نحوه استفاده از آن:

```javascript
'use client'
// app/upload/page.js
import { useState } from 'react';
import api from '@/lib/fetchWrapper';

export default function UploadPage() {
  const [file, setFile] = useState(null);
  const [progress, setProgress] = useState(0);
  const [uploading, setUploading] = useState(false);
  
  const handleFileChange = (e) => {
    setFile(e.target.files[0]);
  };
  
  const handleUpload = async () => {
    if (!file) return;
    
    const formData = new FormData();
    formData.append('file', file);
    
    setUploading(true);
    setProgress(0);
    
    try {
      // خواندن توکن از localStorage
      const token = localStorage.getItem('auth_token');
      api.setAuthToken(token);
      
      // آپلود فایل با نمایش پیشرفت
      const result = await api.uploadFile(
        '/upload',
        formData,
        (progressPercent) => {
          setProgress(progressPercent);
        }
      );
      
      alert('فایل با موفقیت آپلود شد');
    } catch (error) {
      alert(`خطا در آپلود: ${error.message}`);
    } finally {
      setUploading(false);
    }
  };
  
  return (
    <div>
      <h1>آپلود فایل</h1>
      
      <input type="file" onChange={handleFileChange} />
      
      <button onClick={handleUpload} disabled={!file || uploading}>
        آپلود
      </button>
      
      {uploading && (
        <div>
          <progress value={progress} max="100" />
          <span>{progress.toFixed(1)}%</span>
        </div>
      )}
    </div>
  );
}
```

## 7. ایجاد یک ساختار کلی برای API ها:

برای پروژه‌های بزرگ‌تر، می‌توانید API‌های خود را به صورت ماژولار سازماندهی کنید:

```javascript
// services/userService.js
import api from '@/lib/fetchWrapper';

export const userService = {
  getProfile: () => api.get('/users/me'),
  updateProfile: (data) => api.put('/users/me', data),
  getUsers: (params) => api.get('/users', params),
  getUserById: (id) => api.get(`/users/${id}`),
  createUser: (data) => api.post('/users', data),
  deleteUser: (id) => api.delete(`/users/${id}`),
};

// services/authService.js
import api from '@/lib/fetchWrapper';

export const authService = {
  login: (credentials) => api.post('/auth/login', credentials),
  register: (userData) => api.post('/auth/register', userData),
  logout: () => api.post('/auth/logout'),
  refreshToken: (token) => api.post('/auth/refresh', { token }),
};

// services/index.js
export * from './userService';
export * from './authService';
// و دیگر سرویس‌ها
```

و استفاده از آن‌ها:

```javascript
// app/profile/page.js
import { userService } from '@/services';

export default async function ProfilePage() {
  try {
    const profile = await userService.getProfile();
    
    return (
      <div>
        <h1>پروفایل</h1>
        <div>نام: {profile.name}</div>
        <div>ایمیل: {profile.email}</div>
      </div>
    );
  } catch (error) {
    return <div>خطا در بارگذاری پروفایل: {error.message}</div>;
  }
}
```

این مثال‌ها نشان می‌دهند چگونه می‌توانید از یک FetchWrapper قدرتمند در سناریوهای مختلف Next.js 15 استفاده کنید. این ساختار به شما کمک می‌کند کد تمیزتر، قابل نگهداری‌تر و یکپارچه‌تر داشته باشید.



==========================================================

 استفاده از FetchWrapper در Server Components در Next.js 15 می‌تواند مدیریت درخواست‌های API را ساده‌تر کند و کد شما را تمیزتر و قابل نگهداری‌تر سازد. بیایید یک FetchWrapper مخصوص Server Components ایجاد کنیم.


```javascript 
// lib/serverFetchWrapper.js

/**
 * FetchWrapper برای استفاده در Server Components
 * این کلاس برای مدیریت درخواست‌های API در سمت سرور طراحی شده است
 */
class ServerFetchWrapper {
  constructor(baseUrl = '') {
    this.baseUrl = baseUrl;
    this.headers = {
      'Content-Type': 'application/json',
    };
    this.cache = 'no-store'; // پیش‌فرض بدون کش
  }

  /**
   * تنظیم سرآیندهای اضافی
   * @param {Object} headers - سرآیندهای اضافی
   */
  setHeaders(headers) {
    this.headers = { ...this.headers, ...headers };
    return this;
  }

  /**
   * تنظیم توکن احراز هویت
   * @param {string} token - توکن احراز هویت
   */
  setAuthToken(token) {
    if (token) {
      this.headers['Authorization'] = `Bearer ${token}`;
    } else {
      delete this.headers['Authorization'];
    }
    return this;
  }

  /**
   * تنظیم استراتژی کش
   * @param {string} cacheStrategy - استراتژی کش ('force-cache', 'no-store', یا مقدار next.revalidate)
   * @param {number} revalidateTime - زمان اعتبارسنجی مجدد به ثانیه (برای next.revalidate)
   */
  setCacheStrategy(cacheStrategy, revalidateTime = null) {
    if (cacheStrategy === 'force-cache' || cacheStrategy === 'no-store') {
      this.cache = cacheStrategy;
      this.revalidate = undefined;
    } else if (typeof revalidateTime === 'number') {
      this.cache = undefined;
      this.revalidate = revalidateTime;
    }
    return this;
  }

  /**
   * ایجاد آدرس کامل
   * @param {string} endpoint - مسیر نسبی
   */
  getFullUrl(endpoint) {
    return `${this.baseUrl}${endpoint}`;
  }

  /**
   * مدیریت خطاهای درخواست
   * @param {Response} response - پاسخ دریافتی
   */
  async handleErrorResponse(response) {
    const contentType = response.headers.get('content-type');
    let errorData;
    
    try {
      if (contentType && contentType.includes('application/json')) {
        errorData = await response.json();
      } else {
        errorData = await response.text();
      }
    } catch (e) {
      errorData = 'خطای نامشخص';
    }
    
    const error = new Error(
      typeof errorData === 'object' && errorData.message 
        ? errorData.message 
        : `درخواست با خطای ${response.status} مواجه شد`
    );
    
    error.status = response.status;
    error.data = errorData;
    throw error;
  }

  /**
   * پردازش پاسخ دریافتی
   * @param {Response} response - پاسخ دریافتی از سرور
   */
  async handleResponse(response) {
    if (!response.ok) {
      await this.handleErrorResponse(response);
    }
    
    const contentType = response.headers.get('content-type');
    
    if (contentType && contentType.includes('application/json')) {
      return response.json();
    }
    
    if (contentType && contentType.includes('text/')) {
      return response.text();
    }
    
    return response;
  }

  /**
   * اجرای یک درخواست GET
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} params - پارامترهای درخواست
   */
  async get(endpoint, params = {}) {
    const url = new URL(this.getFullUrl(endpoint));
    
    // افزودن پارامترهای درخواست به URL
    Object.keys(params).forEach(key => {
      if (params[key] !== undefined && params[key] !== null) {
        url.searchParams.append(key, params[key].toString());
      }
    });
    
    const fetchOptions = {
      method: 'GET',
      headers: this.headers,
    };
    
    // تنظیم استراتژی کش
    if (this.cache) {
      fetchOptions.cache = this.cache;
    }
    if (this.revalidate !== undefined) {
      fetchOptions.next = { revalidate: this.revalidate };
    }
    
    const response = await fetch(url.toString(), fetchOptions);
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست POST
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} data - داده‌های درخواست
   */
  async post(endpoint, data = {}) {
    const fetchOptions = {
      method: 'POST',
      headers: this.headers,
      body: JSON.stringify(data),
    };
    
    // تنظیم استراتژی کش
    if (this.cache) {
      fetchOptions.cache = this.cache;
    }
    if (this.revalidate !== undefined) {
      fetchOptions.next = { revalidate: this.revalidate };
    }
    
    const response = await fetch(this.getFullUrl(endpoint), fetchOptions);
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست PUT
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} data - داده‌های درخواست
   */
  async put(endpoint, data = {}) {
    const fetchOptions = {
      method: 'PUT',
      headers: this.headers,
      body: JSON.stringify(data),
    };
    
    // تنظیم استراتژی کش
    if (this.cache) {
      fetchOptions.cache = this.cache;
    }
    if (this.revalidate !== undefined) {
      fetchOptions.next = { revalidate: this.revalidate };
    }
    
    const response = await fetch(this.getFullUrl(endpoint), fetchOptions);
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست PATCH
   * @param {string} endpoint - مسیر نسبی
   * @param {Object} data - داده‌های درخواست
   */
  async patch(endpoint, data = {}) {
    const fetchOptions = {
      method: 'PATCH',
      headers: this.headers,
      body: JSON.stringify(data),
    };
    
    // تنظیم استراتژی کش
    if (this.cache) {
      fetchOptions.cache = this.cache;
    }
    if (this.revalidate !== undefined) {
      fetchOptions.next = { revalidate: this.revalidate };
    }
    
    const response = await fetch(this.getFullUrl(endpoint), fetchOptions);
    return this.handleResponse(response);
  }

  /**
   * اجرای یک درخواست DELETE
   * @param {string} endpoint - مسیر نسبی
   */
  async delete(endpoint) {
    const fetchOptions = {
      method: 'DELETE',
      headers: this.headers,
    };
    
    // تنظیم استراتژی کش
    if (this.cache) {
      fetchOptions.cache = this.cache;
    }
    if (this.revalidate !== undefined) {
      fetchOptions.next = { revalidate: this.revalidate };
    }
    
    const response = await fetch(this.getFullUrl(endpoint), fetchOptions);
    return this.handleResponse(response);
  }

  /**
   * اجرای چندین درخواست همزمان
   * @param {Array} requests - آرایه‌ای از توابع اجرایی درخواست
   */
  async all(requests) {
    return Promise.all(requests);
  }
}

// ایجاد یک نمونه با آدرس پایه API
const serverApi = new ServerFetchWrapper(process.env.API_URL || 'https://api.example.com');

export default serverApi;

```


حالا اجازه دهید چند مثال کاربردی از نحوه استفاده از این FetchWrapper در Server Components ارائه دهم:


```javascript 
// app/users/page.js
import serverApi from '@/lib/serverFetchWrapper';
import { cookies } from 'next/headers';

export default async function UsersPage() {
  try {
    // دریافت توکن از کوکی‌ها
    const cookieStore = cookies();
    const token = cookieStore.get('auth_token')?.value;
    
    // استفاده از FetchWrapper با کش فعال برای 60 ثانیه
    const users = await serverApi
      .setAuthToken(token)
      .setCacheStrategy(null, 60) // revalidate every 60 seconds
      .get('/users', { limit: 10 });
    
    return (
      <div>
        <h1>لیست کاربران</h1>
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      </div>
    );
  } catch (error) {
    return <div>خطا در بارگذاری کاربران: {error.message}</div>;
  }
}

// app/dashboard/page.js
import serverApi from '@/lib/serverFetchWrapper';
import { cookies } from 'next/headers';

export default async function DashboardPage() {
  try {
    // دریافت توکن از کوکی‌ها
    const cookieStore = cookies();
    const token = cookieStore.get('auth_token')?.value;
    
    // تنظیم توکن احراز هویت
    serverApi.setAuthToken(token);
    
    // دریافت همزمان چندین منبع داده با استفاده از Promise.all
    const [userProfile, posts, notifications] = await serverApi.all([
      serverApi.get('/users/me'),
      serverApi.get('/posts', { limit: 5 }),
      serverApi.get('/notifications', { unread: true })
    ]);
    
    return (
      <div>
        <h1>داشبورد {userProfile.name}</h1>
        
        <section>
          <h2>آخرین پست‌ها</h2>
          <ul>
            {posts.map(post => (
              <li key={post.id}>{post.title}</li>
            ))}
          </ul>
        </section>
        
        <section>
          <h2>اعلان‌های جدید ({notifications.length})</h2>
          <ul>
            {notifications.map(notification => (
              <li key={notification.id}>{notification.message}</li>
            ))}
          </ul>
        </section>
      </div>
    );
  } catch (error) {
    return <div>خطا در بارگذاری داشبورد: {error.message}</div>;
  }
}

// app/products/[id]/page.js
import serverApi from '@/lib/serverFetchWrapper';
import { notFound } from 'next/navigation';

export async function generateStaticParams() {
  // دریافت تمام محصولات برای ساخت صفحات استاتیک
  const products = await serverApi.get('/products', { limit: 100 });
  
  return products.map(product => ({
    id: product.id.toString()
  }));
}

export default async function ProductPage({ params }) {
  try {
    // دریافت اطلاعات محصول با کش دائمی
    const product = await serverApi
      .setCacheStrategy('force-cache')
      .get(`/products/${params.id}`);
    
    // دریافت اطلاعات موجودی بدون کش
    const inventory = await serverApi
      .setCacheStrategy('no-store')
      .get(`/inventory/${params.id}`);
    
    return (
      <div>
        <h1>{product.name}</h1>
        <p>{product.description}</p>
        <p>قیمت: {product.price.toLocaleString()} تومان</p>
        <p>موجودی: {inventory.quantity > 0 ? `${inventory.quantity} عدد` : 'ناموجود'}</p>
      </div>
    );
  } catch (error) {
    // اگر محصول پیدا نشد، صفحه 404 نمایش داده می‌شود
    if (error.status === 404) {
      notFound();
    }
    
    return <div>خطا در بارگذاری محصول: {error.message}</div>;
  }
}

// app/api/search/route.js
import serverApi from '@/lib/serverFetchWrapper';
import { NextResponse } from 'next/server';

export async function GET(request) {
  try {
    const { searchParams } = new URL(request.url);
    const query = searchParams.get('q') || '';
    
    // استفاده از FetchWrapper برای دریافت نتایج جستجو
    const searchResults = await serverApi
      .setCacheStrategy('no-store')
      .get('/search', { query });
    
    return NextResponse.json(searchResults);
  } catch (error) {
    return NextResponse.json(
      { error: error.message }, 
      { status: error.status || 500 }
    );
  }
}

// lib/actions.js
'use server'

import serverApi from '@/lib/serverFetchWrapper';
import { cookies } from 'next/headers';
import { redirect } from 'next/navigation';

/**
 * Server Action برای ثبت نام کاربر
 */
export async function registerUser(formData) {
  try {
    const userData = {
      name: formData.get('name'),
      email: formData.get('email'),
      password: formData.get('password')
    };
    
    const result = await serverApi.post('/auth/register', userData);
    
    // ذخیره توکن در کوکی
    cookies().set('auth_token', result.token, { 
      httpOnly: true,
      secure: process.env.NODE_ENV === 'production',
      maxAge: 60 * 60 * 24 * 7, // یک هفته
      path: '/'
    });
    
    return { success: true };
  } catch (error) {
    return { 
      success: false, 
      error: error.message
    };
  }
}

/**
 * Server Action برای خروج کاربر
 */
export async function logoutUser() {
  try {
    const token = cookies().get('auth_token')?.value;
    
    if (token) {
      // درخواست به API برای باطل کردن توکن
      await serverApi
        .setAuthToken(token)
        .post('/auth/logout');
      
      // حذف توکن از کوکی
      cookies().delete('auth_token');
    }
    
    redirect('/login');
  } catch (error) {
    // حتی در صورت خطا، کوکی را حذف می‌کنیم
    cookies().delete('auth_token');
    redirect('/login');
  }
}

```


اکنون بیایید یک مثال پیشرفته‌تر از مدیریت داده در Server Components با استفاده از FetchWrapper ارائه دهیم:


```javascript 
// lib/api/apiServices.js
import serverApi from '@/lib/serverFetchWrapper';

// سرویس مدیریت محصولات
export const productService = {
  /**
   * دریافت لیست محصولات
   * @param {Object} params - پارامترهای فیلتر
   * @param {number} cacheTime - زمان کش به ثانیه (undefined = بدون کش)
   */
  getProducts: async (params = {}, cacheTime = 300) => {
    return serverApi
      .setCacheStrategy(cacheTime ? null : 'no-store', cacheTime)
      .get('/products', params);
  },
  
  /**
   * دریافت اطلاعات یک محصول
   * @param {string|number} id - شناسه محصول
   * @param {number} cacheTime - زمان کش به ثانیه (undefined = بدون کش)
   */
  getProductById: async (id, cacheTime = 300) => {
    return serverApi
      .setCacheStrategy(cacheTime ? null : 'no-store', cacheTime)
      .get(`/products/${id}`);
  },
  
  /**
   * جستجوی محصولات
   * @param {string} query - عبارت جستجو
   */
  searchProducts: async (query) => {
    return serverApi
      .setCacheStrategy('no-store')
      .get('/products/search', { query });
  }
};

// سرویس مدیریت کاربران
export const userService = {
  /**
   * دریافت پروفایل کاربر
   * @param {string} token - توکن احراز هویت
   */
  getProfile: async (token) => {
    return serverApi
      .setAuthToken(token)
      .setCacheStrategy('no-store')
      .get('/users/me');
  },
  
  /**
   * دریافت سفارش‌های کاربر
   * @param {string} token - توکن احراز هویت
   * @param {Object} params - پارامترهای فیلتر
   */
  getOrders: async (token, params = {}) => {
    return serverApi
      .setAuthToken(token)
      .setCacheStrategy('no-store')
      .get('/users/me/orders', params);
  }
};

// سرویس احراز هویت
export const authService = {
  /**
   * ورود کاربر
   * @param {Object} credentials - اطلاعات ورود
   */
  login: async (credentials) => {
    return serverApi
      .setCacheStrategy('no-store')
      .post('/auth/login', credentials);
  },
  
  /**
   * ثبت نام کاربر
   * @param {Object} userData - اطلاعات کاربر
   */
  register: async (userData) => {
    return serverApi
      .setCacheStrategy('no-store')
      .post('/auth/register', userData);
  },
  
  /**
   * بررسی اعتبار توکن
   * @param {string} token - توکن احراز هویت
   */
  validateToken: async (token) => {
    return serverApi
      .setAuthToken(token)
      .setCacheStrategy('no-store')
      .get('/auth/validate');
  }
};

// app/shop/page.js
import { productService } from '@/lib/api/apiServices';
import { Suspense } from 'react';

// کامپوننت جستجو
const SearchResults = async ({ query }) => {
  if (!query) return null;
  
  const products = await productService.searchProducts(query);
  
  if (products.length === 0) {
    return <p>محصولی یافت نشد.</p>;
  }
  
  return (
    <div>
      <h2>نتایج جستجو برای "{query}"</h2>
      <ul>
        {products.map(product => (
          <li key={product.id}>
            <a href={`/shop/${product.id}`}>{product.name}</a>
          </li>
        ))}
      </ul>
    </div>
  );
};

// کامپوننت لیست محصولات
const ProductList = async ({ category }) => {
  const params = category ? { category } : {};
  const products = await productService.getProducts(params, 60); // کش برای 60 ثانیه
  
  return (
    <div>
      <h2>محصولات {category ? category : ''}</h2>
      <div className="grid grid-cols-3 gap-4">
        {products.map(product => (
          <div key={product.id} className="border p-4 rounded">
            <h3>{product.name}</h3>
            <p>{product.price.toLocaleString()} تومان</p>
            <a href={`/shop/${product.id}`} className="btn">مشاهده</a>
          </div>
        ))}
      </div>
    </div>
  );
};

// صفحه اصلی فروشگاه
export default async function ShopPage({ searchParams }) {
  const query = searchParams.q || '';
  const category = searchParams.category || '';
  
  return (
    <div>
      <h1>فروشگاه</h1>
      
      {/* بخش جستجو با Suspense */}
      <Suspense fallback={<p>در حال جستجو...</p>}>
        {query && <SearchResults query={query} />}
      </Suspense>
      
      {/* نمایش محصولات با Suspense */}
      <Suspense fallback={<p>در حال بارگذاری محصولات...</p>}>
        <ProductList category={category} />
      </Suspense>
    </div>
  );
}

// app/account/page.js
import { userService } from '@/lib/api/apiServices';
import { cookies } from 'next/headers';
import { redirect } from 'next/navigation';

// کامپوننت پروفایل کاربر
const UserProfile = async () => {
  const cookieStore = cookies();
  const token = cookieStore.get('auth_token')?.value;
  
  if (!token) {
    redirect('/login');
  }
  
  try {
    const profile = await userService.getProfile(token);
    
    return (
      <div>
        <h1>پروفایل {profile.name}</h1>
        <p>ایمیل: {profile.email}</p>
        <p>تاریخ عضویت: {new Date(profile.createdAt).toLocaleDateString('fa-IR')}</p>
      </div>
    );
  } catch (error) {
    // اگر توکن منقضی شده باشد، کاربر را به صفحه لاگین هدایت می‌کنیم
    if (error.status === 401) {
      cookies().delete('auth_token');
      redirect('/login');
    }
    
    return <div>خطا در بارگذاری پروفایل: {error.message}</div>;
  }
};

// کامپوننت سفارش‌های کاربر
const UserOrders = async () => {
  const cookieStore = cookies();
  const token = cookieStore.get('auth_token')?.value;
  
  if (!token) {
    return null;
  }
  
  try {
    const orders = await userService.getOrders(token, { limit: 5 });
    
    return (
      <div>
        <h2>آخرین سفارش‌ها</h2>
        {orders.length === 0 ? (
          <p>هنوز سفارشی ثبت نکرده‌اید.</p>
        ) : (
          <ul>
            {orders.map(order => (
              <li key={order.id}>
                <p>شماره سفارش: {order.id}</p>
                <p>تاریخ: {new Date(order.createdAt).toLocaleDateString('fa-IR')}</p>
                <p>مبلغ: {order.totalAmount.toLocaleString()} تومان</p>
                <p>وضعیت: {order.status}</p>
              </li>
            ))}
          </ul>
        )}
      </div>
    );
  } catch (error) {
    return <div>خطا در بارگذاری سفارش‌ها: {error.message}</div>;
  }
};

// صفحه حساب کاربری
export default async function AccountPage() {
  return (
    <div>
      <Suspense fallback={<p>در حال بارگذاری پروفایل...</p>}>
        <UserProfile />
      </Suspense>
      
      <Suspense fallback={<p>در حال بارگذاری سفارش‌ها...</p>}>
        <UserOrders />
      </Suspense>
    </div>
  );
}

// middleware.js
import { NextResponse } from 'next/server';
import { authService } from '@/lib/api/apiServices';

export async function middleware(request) {
  // مسیرهایی که نیاز به احراز هویت دارند
  const protectedPaths = ['/account', '/dashboard', '/orders'];
  
  // بررسی آیا مسیر فعلی نیاز به احراز هویت دارد
  const isProtectedPath = protectedPaths.some(path => 
    request.nextUrl.pathname.startsWith(path)
  );
  
  if (isProtectedPath) {
    // دریافت توکن از کوکی
    const token = request.cookies.get('auth_token')?.value;
    
    // اگر توکن وجود نداشت، هدایت به صفحه ورود
    if (!token) {
      const loginUrl = new URL('/login', request.url);
      loginUrl.searchParams.set('redirect', request.nextUrl.pathname);
      return NextResponse.redirect(loginUrl);
    }
    
    try {
      // بررسی اعتبار توکن
      await authService.validateToken(token);
      
      // اگر توکن معتبر باشد، اجازه دسترسی به صفحه را می‌دهیم
      return NextResponse.next();
    } catch (error) {
      // اگر توکن منقضی شده باشد، هدایت به صفحه ورود
      const response = NextResponse.redirect(new URL('/login', request.url));
      
      // حذف کوکی توکن
      response.cookies.delete('auth_token');
      
      return response;
    }
  }
  
  // برای سایر مسیرها، اجازه دسترسی می‌دهیم
  return NextResponse.next();
}

export const config = {
  matcher: [
    '/account/:path*',
    '/dashboard/:path*',
    '/orders/:path*',
  ]
};

```


روش‌های پیشرفته‌تر برای ارتقای FetchWrapper در Server Components:


```javascript 
// lib/serverFetchWrapper.js (اضافه کردن قابلیت‌های پیشرفته)

/**
 * رسیدگی به خطاهای شبکه با سرور فرعی (fallback)
 * @param {string} endpoint - مسیر نسبی
 * @param {Object} options - تنظیمات درخواست
 * @param {Array} fallbackUrls - آرایه‌ای از URLهای جایگزین
 */
async fetchWithFallback(endpoint, options, fallbackUrls = []) {
  // آرایه‌ای از تمام URLهای ممکن، شامل URL اصلی و جایگزین‌ها
  const urls = [this.baseUrl, ...fallbackUrls];
  
  let lastError;
  
  // تلاش برای هر URL
  for (const baseUrl of urls) {
    try {
      const url = `${baseUrl}${endpoint}`;
      const response = await fetch(url, options);
      return await this.handleResponse(response);
    } catch (error) {
      lastError = error;
      // ادامه حلقه و تلاش با URL بعدی
    }
  }
  
  // اگر تمام URLها با خطا مواجه شدند، آخرین خطا را برمی‌گردانیم
  throw lastError;
}

/**
 * کش داده‌ها در حافظه (Memory Cache) برای درخواست‌های تکراری در یک رندر
 * این متد را به کلاس FetchWrapper اضافه کنید
 */
constructor(baseUrl = '') {
  this.baseUrl = baseUrl;
  this.headers = {
    'Content-Type': 'application/json',
  };
  this.cache = 'no-store';
  this.memoryCache = new Map(); // کش حافظه برای درخواست‌های تکراری
  this.memoryCacheTTL = 5000; // طول عمر کش (5 ثانیه)
}

/**
 * تنظیم طول عمر کش حافظه
 * @param {number} ttl - طول عمر به میلی‌ثانیه
 
```
