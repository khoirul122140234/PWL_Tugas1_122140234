### Perbedaan Antara Aplikasi Monolitik dan Microservices yang Telah Dimodifikasi

Setelah melakukan perubahan pada proyek **ojek online**, berikut adalah **perbedaan utama** antara versi **monolitik** dan versi **microservices**:


## **1. Struktur Kode**
###  Sebelumnya (Monolitik)
- **Semua fitur berada dalam satu kodebase utama.**
- **Semua API dan layanan berjalan dalam satu server** (misalnya, Laravel atau Java Spring dalam satu proyek).
- **Database tunggal untuk semua layanan** (misalnya tabel pengguna, order, dan pembayaran dalam satu database MySQL).

 **Struktur Lama (Monolitik)**
```
ojek-online-master/
├── DB/
├── IdentityService/
├── OjekOnline/
├── WebApp/
├── WebService/
└── ojekonline.sql
```



### Sekarang (Microservices)
- **Setiap fitur dipecah menjadi layanan terpisah dengan kodebase masing-masing.**
- **Setiap layanan berjalan secara independen** (menggunakan Node.js dengan Express atau tetap mempertahankan Java untuk beberapa layanan).
- **Database dipisah per layanan**, masing-masing memiliki skema dan tabelnya sendiri.

 **Struktur Baru (Microservices)**
```
ojek-online-microservices/
├── api-gateway/  → Mengatur komunikasi antar layanan
├── auth-service/ → Mengelola login & register
├── order-service/ → Mengelola pemesanan & tracking
├── payment-service/ → Mengelola transaksi pembayaran
├── driver-service/ → Mengelola informasi driver
├── database-scripts/ → Berisi file SQL terpisah untuk setiap layanan
```



## **2. Routing API**
###  Sebelumnya (Monolitik)
Semua API ditangani dalam satu aplikasi.

```php
Route::post('/register', 'AuthController@register');
Route::post('/login', 'AuthController@login');
Route::post('/order', 'OrderController@create');
Route::get('/order/{id}', 'OrderController@show');
Route::post('/payment', 'PaymentController@process');
```



###  Sekarang (Microservices)
**Menggunakan API Gateway untuk mengatur komunikasi antar layanan.**
API dipecah menjadi beberapa service:

 **API Gateway**
```js
const express = require('express');
const app = express();
const axios = require('axios');

app.use('/auth', (req, res) => axios(req).then(response => res.send(response.data)));
app.use('/orders', (req, res) => axios(req).then(response => res.send(response.data)));
app.use('/payments', (req, res) => axios(req).then(response => res.send(response.data)));

app.listen(3000, () => console.log('API Gateway running on port 3000'));
```

 **Auth Service**
```js
const express = require('express');
const app = express();
app.use(express.json());

app.post('/register', (req, res) => {
    res.json({ message: 'User registered successfully' });
});

app.listen(4000, () => console.log('Auth Service running on port 4000'));
```

 **Order Service**
```js
const express = require('express');
const app = express();
app.use(express.json());

app.post('/order', (req, res) => {
    res.json({ message: 'Order placed successfully' });
});

app.listen(5000, () => console.log('Order Service running on port 5000'));
```

---

## **3. Database**
###  Sebelumnya (Monolitik)
Semua data disimpan dalam **satu database MySQL**, yang menyebabkan skala aplikasi sulit diperbesar.

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    password VARCHAR(255)
);

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    driver_id INT,
    status VARCHAR(50)
);
```

---

###  Sekarang (Microservices)
**Database dipecah berdasarkan layanan.**  
Setiap layanan memiliki database-nya sendiri.

 **Database untuk Auth Service**
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    password VARCHAR(255)
);
```

 **Database untuk Order Service**
```sql
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    driver_id INT,
    status VARCHAR(50)
);
```

 **Database untuk Payment Service**
```sql
CREATE TABLE payments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    amount DECIMAL(10,2),
    status VARCHAR(50)
);
```



## **4. Deployment & Scalability**
###  Sebelumnya (Monolitik)
- **Aplikasi berjalan dalam satu server besar.**
- **Jika satu layanan crash, seluruh sistem ikut terganggu.**

---

### Sekarang (Microservices)
- **Masing-masing layanan dapat dideploy secara independen.**
- **Menggunakan Docker untuk menjalankan layanan secara terpisah.**
- **Jika satu layanan gagal, layanan lain tetap bisa berjalan.**

 **Contoh Docker Compose untuk Microservices**
```yaml
version: '3'
services:
  api-gateway:
    build: ./api-gateway
    ports:
      - "3000:3000"
  auth-service:
    build: ./auth-service
    ports:
      - "4000:4000"
  order-service:
    build: ./order-service
    ports:
      - "5000:5000"
  payment-service:
    build: ./payment-service
    ports:
      - "6000:6000"
```

---
