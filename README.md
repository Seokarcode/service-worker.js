# service-worker.js
A service-worker.js file for a WordPress site to support PWA (Progressive Web App) features typically includes code for managing caches and handling network requests. Below is a simple service-worker.js example that you can use as a starting point:


برای جایگزینی نام تم و مسیر فایل‌ها با `Seokar`، کد کامل برای `service-worker.js` به صورت زیر خواهد بود:

```javascript
const CACHE_NAME = 'my-site-cache-v3';
const urlsToCache = [
  '/',
  '/wp-content/themes/Seokar/style.css',
  '/wp-content/themes/Seokar/script.js',
  // سایر فایل‌های مورد نیاز
];

self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});

self.addEventListener('activate', event => {
  const cacheWhitelist = [CACHE_NAME];
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cacheName => {
          if (cacheWhitelist.indexOf(cacheName) === -1) {
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(response => {
        if (response) {
          return response;
        }
        return fetch(event.request).then(networkResponse => {
          if (!networkResponse || networkResponse.status !== 200 || networkResponse.type !== 'basic') {
            return networkResponse;
          }
          let responseToCache = networkResponse.clone();
          caches.open(CACHE_NAME)
            .then(cache => {
              cache.put(event.request, responseToCache);
            });
          return networkResponse;
        });
      })
  );
});
```

سپس، برای ثبت سرویس‌ورکر در سایت وردپرسی خود، کد زیر را به فایل `header.php` یا `index.php` تم خود اضافه کنید:

```html
<script>
  if ('serviceWorker' in navigator) {
    window.addEventListener('load', function() {
      navigator.serviceWorker.register('/service-worker.js').then(function(registration) {
        console.log('ServiceWorker registration successful with scope: ', registration.scope);
      }, function(err) {
        console.log('ServiceWorker registration failed: ', err);
      });
    });
  }
</script>
```

این کد باعث می‌شود که سرویس‌ورکر شما به درستی ثبت و فعال شود و فایل‌های مشخص شده را در کش ذخیره کند.
