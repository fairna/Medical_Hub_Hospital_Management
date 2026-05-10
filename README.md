# Final Project for Database Management
<div align="center">

# 🏥 MedHub

### Web-Based Doctor Appointment & Reservation System

*Sistem reservasi dokter online dengan validasi slot otomatis dan manajemen pembayaran*

![PHP](https://img.shields.io/badge/PHP-8.0-777BB4?style=for-the-badge&logo=php&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![Bootstrap](https://img.shields.io/badge/Bootstrap-5.3-7952B3?style=for-the-badge&logo=bootstrap&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)

[Features](#-features) • [Database Design](#-database-design-highlights) • [Tech Stack](#-tech-stack) • [Installation](#-installation) • [Demo](#-demo)

</div>

---

## 📖 Overview

**MedHub** adalah aplikasi web untuk reservasi dokter yang memungkinkan pasien mencari dokter berdasarkan spesialisasi, melihat jadwal praktik, membuat janji temu, dan melakukan pembayaran — semuanya dalam satu platform.

Project ini dibangun dengan fokus pada **database design yang optimal**, memanfaatkan **stored procedures, triggers, functions, dan views** MySQL untuk memastikan integritas data dan business logic yang konsisten di sisi database.

---

## 🌟 Features

- 🔐 **User Authentication** — Register & login dengan validasi email
- 👨‍⚕️ **Doctor Search** — Filter dokter berdasarkan spesialisasi
- 📅 **Smart Scheduling** — Sistem booking otomatis cek ketersediaan slot 30 menit
- ⚡ **Real-Time Validation** — Trigger database mencegah double-booking & validasi jam praktik
- 💳 **Payment Management** — 5 metode pembayaran (Transfer, Kartu Kredit, Shopeepay, Gopay, Asuransi)
- 🧾 **Auto-Calculate Cost** — Function SQL otomatis hitung biaya + 10% biaya admin
- 📊 **Appointment History** — Riwayat reservasi pasien dengan view database
- 👤 **Profile Management** — Update profil & hapus akun

---

## 🎯 Database Design Highlights

> Semua business logic diimplementasikan ke database layer untuk konsistensi dan performa.

### 📐 Schema Overview

```
Users ──┐
        ├──> Pasien ──┐
                      ├──> Reservasi ──> payment_conf ──> Pembayaran
        Dokter ───────┘        ▲
           │                   │
           └─> Dokter_Jadwal_Dokter ──> Jadwal_Dokter
```

### ⚙️ Stored Procedures (5)

Database ini menggunakan Procedures untuk encapsulate query kompleks agar re-usable:

| Procedure | Fungsi |
|-----------|--------|
| `GetDoctorsBySpecialization` | Ambil dokter + jadwal berdasarkan spesialisasi (JOIN 3 tabel) |
| `GetAllDoctors` | List semua dokter dengan jadwalnya |
| `GetSchedulesByDoctorName` | Cari jadwal praktik dokter by name |
| `GetSchedulesByDoctor` | Cari jadwal praktik by doctor ID |
| `GetDoctorIdByName` | Lookup ID dokter dari nama (output parameter) |

### 🔧 Functions (2)

Business logic sistem diimplementasikan dalam Functions database

| Function | Return | Use Case |
|----------|--------|----------|
| `DoctorCountBySpecialization` | `INT` | Hitung jumlah dokter per spesialisasi |
| `calculate_total_cost` | `DECIMAL(10,2)` | Hitung total biaya = harga dokter × 1.10 (admin fee 10%) |

### ⚡ Triggers (3) — Business Rules di Database Layer

Triggers dibuat untuk mencegah bug atau error pada logika bisnis sistem reservasu

#### 1️⃣ `check_slot_availability` (BEFORE INSERT)
Mencegah double-booking — jika ada appointment lain dalam rentang ±30 menit pada dokter yang sama, transaksi di-block dengan error:
> *"Jadwal sudah terisi, Pilih jadwal lain!"*

#### 2️⃣ `validate_reservation_time` (BEFORE INSERT)
Memastikan reservasi hanya dibuat dalam jam praktik dokter:
> *"Waktu yang dipilih di luar jam praktik dokter!"*

#### 3️⃣ `update_status_pembayaran` (AFTER INSERT)
Auto-update `Status_pembayaran = TRUE` di tabel Reservasi ketika data masuk ke `payment_conf` — memastikan payment status selalu sinkron tanpa perlu logic manual di PHP.

### 👁️ Views (1)

| View | Fungsi |
|------|--------|
| `View_Appointment_History` | Denormalized view yang JOIN 6 tabel untuk halaman history pasien — UI tinggal `SELECT *` tanpa JOIN manual |

### 🛡️ Why This Matters

Pendekatan **database-first** ini diimplementasikan agar:

- ✅ **Single source of truth** — business rules tidak tersebar di banyak file PHP
- ✅ **Atomic transactions** — trigger memastikan validasi terjadi dalam transaksi yang sama dengan INSERT
- ✅ **Performance** — query kompleks dieksekusi dekat dengan data, bukan round-trip ke aplikasi
- ✅ **Reusability** — procedure bisa dipanggil dari mana saja (PHP, CLI, BI tools)

---

## 🛠️ Tech Stack

### Backend
- **PHP 8.0+** — Server-side scripting dengan `mysqli` prepared statements
- **MySQL 8.0** — Database dengan stored procedures, triggers, functions, views

### Frontend
- **Bootstrap 5.3** — Responsive UI framework
- **JavaScript (Vanilla)** — Dynamic dropdown loading via XMLHttpRequest
- **HTML5 + CSS3** — Semantic markup & custom styling

### Libraries & Tools
- Font Awesome, Bootstrap Icons, BoxIcons, RemixIcon — Icon sets
- Swiper.js — Touch slider untuk appointment history
- GLightbox — Lightbox gallery
- AOS (Animate on Scroll) — Scroll animations
- Template base: [BootstrapMade MedHub](https://bootstrapmade.com/) (customized)

### Architecture
- **Server-side rendering** dengan PHP
- **Session-based authentication**
- **AJAX** untuk dropdown dependent (specialization → doctor → schedule)
- **Prepared statements** untuk SQL injection prevention

---

## 🚀 Installation

### Prerequisites
- PHP 8.0 atau lebih baru
- MySQL 8.0 / MariaDB 10.4+
- Web server (Apache/Nginx) — disarankan pakai **XAMPP** atau **Laragon**

### Steps

#### 1. Clone Repository
```bash
git clone https://github.com/USERNAME/medhub.git
cd medhub
```

#### 2. Setup Database
- Buka **phpMyAdmin** → buat database baru bernama `mbd`
- Import file `mbd.sql` ke database tersebut
- Pastikan stored procedures, triggers, functions, dan view ter-create dengan benar

#### 3. Configure Database Connection
Buka `connection.php` dan sesuaikan kredensial:
```php
$hostname = "localhost";
$username = "root";
$password = "";
$database = "mbd";
```

#### 4. Deploy ke Web Server
- Copy folder project ke `htdocs/` (XAMPP) atau `www/` (Laragon)
- Start Apache & MySQL
- Akses di browser: `http://localhost/medhub`

#### 5. Login dengan Akun Demo
Database sudah berisi seed data dengan akun siap pakai:

| Email | Password |
|-------|----------|
| `gibran@example.com` | `Pass` |
| `nayla@example.com` | `nay2023` |
| `alicewonderland@example.com` | `alice123` |

---

## 🎬 Demo

### 📸 Screenshots

> *Screenshots akan ditambahkan di sini*

<details>
<summary><b>📋 Daftar halaman yang akan ditampilkan</b></summary>

- 🏠 Landing Page
- 🔐 Login & Register
- 👨‍⚕️ Doctor Search Page
- 📝 Patient Data Form
- 📅 Appointment Booking
- 💳 Payment Page
- 🧾 Appointment History (Ticket-style)
- 👤 Profile Management

</details>

### 🎥 Video Demo

> *Video demo akan ditambahkan di sini (link YouTube unlisted)*

---

## 🧪 Testing the Database Logic

Setelah install, coba scenarios berikut untuk lihat trigger & business logic in action:

### Test 1: Double-Booking Prevention
```sql
-- Insert pertama berhasil
INSERT INTO Reservasi (waktu_reservasi, hari_reservasi, jadwal_reservasi, 
                       keluhan_reservasi, Dokter_ID_dokter, Pasien_ID_pasien, Status_pembayaran)
VALUES (CURRENT_TIMESTAMP, 'Monday', '2026-06-01 10:00:00', 'Test 1', 1, 1, FALSE);

-- Insert kedua di slot yang sama → DI-BLOCK oleh trigger ✋
INSERT INTO Reservasi (waktu_reservasi, hari_reservasi, jadwal_reservasi, 
                       keluhan_reservasi, Dokter_ID_dokter, Pasien_ID_pasien, Status_pembayaran)
VALUES (CURRENT_TIMESTAMP, 'Monday', '2026-06-01 10:15:00', 'Test 2', 1, 2, FALSE);
-- Error: Jadwal sudah terisi, Pilih jadwal lain!
```

### Test 2: Outside Working Hours
```sql
-- Coba booking jam 23:00 (di luar jam praktik) → DI-BLOCK ✋
INSERT INTO Reservasi (waktu_reservasi, hari_reservasi, jadwal_reservasi, 
                       keluhan_reservasi, Dokter_ID_dokter, Pasien_ID_pasien, Status_pembayaran)
VALUES (CURRENT_TIMESTAMP, 'Monday', '2026-06-01 23:00:00', 'Test 3', 1, 1, FALSE);
-- Error: Waktu yang dipilih di luar jam praktik dokter!
```

### Test 3: Auto-Calculate Total Cost
```sql
SELECT calculate_total_cost(500000); 
-- Output: 550000.00 (harga × 1.10)
```

### Test 4: Doctor Count by Specialization
```sql
SELECT DoctorCountBySpecialization('Mata');
-- Output: 5 (jumlah dokter spesialis Mata)
```

---


</div>
