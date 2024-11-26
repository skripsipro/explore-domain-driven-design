
# Struktur Proyek Express.js dengan Arsitektur Domain-Driven Design (DDD)

Berikut adalah struktur proyek Express.js yang diorganisir menggunakan arsitektur Domain-Driven Design (DDD). Struktur ini membantu memisahkan logika bisnis dari infrastruktur dan membuat kode lebih mudah untuk dikembangkan dan dipelihara.

## **Struktur Direktori**

```
project/
├── src/
│   ├── domain/
│   │   ├── entities/
│   │   ├── value-objects/
│   │   ├── services/
│   │   ├── repositories/
│   │   └── events/
│   ├── application/
│   │   ├── use-cases/
│   │   └── dtos/
│   ├── infrastructure/
│   │   ├── http/
│   │   │   ├── controllers/
│   │   │   ├── middlewares/
│   │   │   └── routes/
│   │   ├── database/
│   │   │   ├── models/
│   │   │   ├── migrations/
│   │   │   └── seeders/
│   │   ├── config/
│   │   └── logger/
│   └── shared/
│       ├── utils/
│       └── constants/
├── public/
│   └── assets/
│       ├── css/
│       ├── js/
│       └── images/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── .env
├── package.json
├── README.md
└── server.js
```

---

## **Penjelasan Direktori**

### **1. src/**

Folder utama yang berisi semua kode sumber aplikasi.

#### **a. domain/**
Berisi logika bisnis murni tanpa tergantung pada detail implementasi teknis.

- **entities/**: Objek utama dengan identitas unik (misalnya `User`, `Product`, `Order`).
- **value-objects/**: Objek yang tidak memiliki identitas unik (misalnya `Email`, `Address`).
- **services/**: Logika bisnis yang tidak cocok dimasukkan ke dalam entitas atau value objects.
- **repositories/**: Antarmuka untuk operasi penyimpanan dan pengambilan data.
- **events/**: Peristiwa domain yang signifikan (misalnya `UserRegistered`, `OrderPlaced`).

#### **b. application/**
Lapisan ini mengatur bagaimana aplikasi menggunakan domain untuk menyelesaikan tugas tertentu.

- **use-cases/**: Implementasi kasus penggunaan aplikasi (misalnya `RegisterUser`, `PlaceOrder`).
- **dtos/**: Data Transfer Objects untuk komunikasi antar lapisan (misalnya `UserDTO`, `OrderDTO`).

#### **c. infrastructure/**
Berisi detail implementasi teknis seperti framework, database, dan komunikasi eksternal.

- **http/**:
  - **controllers/**: Mengelola permintaan HTTP.
  - **middlewares/**: Fungsi middleware Express.js.
  - **routes/**: Definisi rute aplikasi.
- **database/**:
  - **models/**: Definisi model database.
  - **migrations/**: Skrip migrasi database.
  - **seeders/**: Data awal untuk pengembangan.
- **config/**: Konfigurasi aplikasi.
- **logger/**: Konfigurasi logging.

#### **d. shared/**
Berisi kode yang dapat digunakan kembali di seluruh aplikasi, seperti utilitas dan konstanta.

---

## **Contoh Implementasi**

### **1. Domain Layer**

#### **Entitas (entities/User.js)**
```javascript
class User {
  constructor({ id, name, email, password }) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.password = password;
  }

  changeEmail(newEmail) {
    this.email = newEmail;
  }
}

module.exports = User;
```

#### **Repository (repositories/UserRepository.js)**
```javascript
class UserRepository {
  async save(user) {
    throw new Error('Metode save() harus diimplementasikan');
  }

  async findByEmail(email) {
    throw new Error('Metode findByEmail() harus diimplementasikan');
  }
}

module.exports = UserRepository;
```

---

### **2. Application Layer**

#### **Kasus Penggunaan (use-cases/RegisterUser.js)**
```javascript
const User = require('../../domain/entities/User');

class RegisterUser {
  constructor({ userRepository, emailService }) {
    this.userRepository = userRepository;
    this.emailService = emailService;
  }

  async execute(userData) {
    const user = new User(userData);
    await this.userRepository.save(user);
    await this.emailService.sendWelcomeEmail(user.email);
    return user;
  }
}

module.exports = RegisterUser;
```

---

### **3. Infrastructure Layer**

#### **Implementasi Repository (database/repositories/UserRepositoryImpl.js)**
```javascript
const UserRepository = require('../../../domain/repositories/UserRepository');
const UserModel = require('../models/UserModel');

class UserRepositoryImpl extends UserRepository {
  async save(user) {
    const userRecord = new UserModel(user);
    await userRecord.save();
  }

  async findByEmail(email) {
    return UserModel.findOne({ email });
  }
}

module.exports = UserRepositoryImpl;
```

#### **Controller (http/controllers/UserController.js)**
```javascript
const RegisterUser = require('../../application/use-cases/RegisterUser');
const UserRepositoryImpl = require('../database/repositories/UserRepositoryImpl');
const EmailService = require('../services/EmailService');

const userRepository = new UserRepositoryImpl();
const emailService = new EmailService();

class UserController {
  async register(req, res, next) {
    try {
      const registerUser = new RegisterUser({ userRepository, emailService });
      const user = await registerUser.execute(req.body);
      res.status(201).json({ message: 'User registered', user });
    } catch (error) {
      next(error);
    }
  }
}

module.exports = new UserController();
```

#### **Route (http/routes/userRoutes.js)**
```javascript
const express = require('express');
const router = express.Router();
const UserController = require('../controllers/UserController');

router.post('/register', UserController.register);

module.exports = router;
```

#### **Server.js**
```javascript
const express = require('express');
const app = express();
const userRoutes = require('./src/infrastructure/http/routes/userRoutes');

app.use(express.json());
app.use('/users', userRoutes);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server berjalan di port ${PORT}`);
});

```

---

## **Langkah-Langkah Implementasi**

1. Definisikan **Entitas** dan **Value Objects** di dalam `domain/`.
2. Buat **Interface Repositories** di `domain/repositories/`.
3. Implementasikan **Use Cases** di `application/use-cases/`.
4. Implementasikan **Repositories** di `infrastructure/database/repositories/`.
5. Buat **Controllers dan Routes** di `infrastructure/http/`.
6. Konfigurasikan server Express.js di `server.js`.

---

## **Keuntungan Menggunakan DDD**

- **Pemisahan yang Jelas** antara logika bisnis dan infrastruktur.
- **Skalabilitas** yang lebih baik karena kode lebih modular.
- **Kemudahan Pengujian** karena logika domain dapat diuji secara terpisah.
- **Pemahaman yang Lebih Baik** tentang domain bisnis melalui bahasa seragam.

---

Jika Anda membutuhkan bantuan lebih lanjut, beri tahu saya! 😊
