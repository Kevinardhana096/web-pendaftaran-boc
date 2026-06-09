# PRD — Sistem Form Pendaftaran Lomba dengan Payment Gateway

## 1. Ringkasan Produk

Sistem ini adalah website form pendaftaran lomba berbasis web custom yang memungkinkan peserta mendaftar secara online, melakukan pembayaran biaya registrasi melalui payment gateway, dan mendapatkan konfirmasi pendaftaran secara otomatis setelah pembayaran berhasil.

Sistem dirancang agar panitia dapat memantau data peserta melalui Google Spreadsheet tanpa perlu membuat dashboard admin kompleks di tahap awal.

---

## 2. Tujuan Produk

Tujuan utama sistem ini adalah:

1. Memudahkan peserta melakukan pendaftaran lomba secara online.
2. Mengotomatisasi proses pembayaran biaya registrasi.
3. Mengurangi pekerjaan manual panitia dalam mengecek bukti transfer.
4. Menyimpan data peserta secara rapi di Google Spreadsheet.
5. Memberikan status pembayaran yang jelas: pending, paid, failed, expired.
6. Mengirimkan konfirmasi pendaftaran setelah pembayaran berhasil.

---

## 3. Target Pengguna

### 3.1 Peserta Lomba

Peserta adalah individu atau tim yang ingin mendaftar lomba melalui website.

Kebutuhan peserta:

* Mengisi data pendaftaran dengan mudah.
* Melihat biaya pendaftaran.
* Mendapatkan link pembayaran.
* Mendapatkan konfirmasi setelah pembayaran berhasil.
* Mendapatkan ID pendaftaran atau nomor peserta.

### 3.2 Panitia

Panitia adalah pengelola lomba yang bertugas memantau data pendaftaran.

Kebutuhan panitia:

* Melihat daftar peserta.
* Melihat status pembayaran peserta.
* Memfilter peserta yang sudah membayar.
* Melakukan verifikasi tambahan jika diperlukan.
* Mengunduh atau mengelola data melalui Google Spreadsheet.

---

## 4. Ruang Lingkup Sistem

### 4.1 Termasuk dalam Scope

Sistem mencakup:

* Landing page/form pendaftaran lomba.
* Validasi input form.
* Penyimpanan data pendaftaran ke Google Spreadsheet.
* Generate `registration_id` dan `order_id`.
* Integrasi payment gateway.
* Redirect peserta ke halaman pembayaran.
* Webhook/callback pembayaran.
* Update status pembayaran otomatis.
* Email konfirmasi setelah pembayaran berhasil.
* Halaman sukses, pending, dan gagal pembayaran.

### 4.2 Tidak Termasuk dalam Scope Tahap Awal

Fitur berikut tidak termasuk dalam tahap awal:

* Login peserta.
* Dashboard admin custom.
* Sistem multi-role.
* Edit data pendaftaran oleh peserta.
* Refund otomatis.
* Sertifikat otomatis.
* WhatsApp gateway otomatis.
* Database PostgreSQL/Supabase.
* Sistem penilaian lomba.

Fitur tersebut dapat dikembangkan pada tahap berikutnya.

---

## 5. Arsitektur Sistem

Arsitektur yang digunakan:

```text
Peserta
↓
Web Custom Form
↓
Google Apps Script / Backend API
↓
Payment Gateway
↓
Webhook Payment Gateway
↓
Google Apps Script / Backend API
↓
Google Spreadsheet
↓
Email Konfirmasi
```

Komponen sistem:

1. Frontend Website
   Digunakan untuk menampilkan form dan halaman status pembayaran.

2. Google Apps Script / Backend API
   Digunakan sebagai penghubung antara frontend, spreadsheet, dan payment gateway.

3. Payment Gateway
   Digunakan untuk membuat transaksi pembayaran dan menerima notifikasi status pembayaran.

4. Google Spreadsheet
   Digunakan sebagai database awal untuk menyimpan data pendaftaran.

5. Email Service
   Digunakan untuk mengirim konfirmasi pendaftaran kepada peserta.

---

## 6. Rekomendasi Teknologi

### 6.1 Frontend

Opsi teknologi:

* HTML, CSS, JavaScript biasa; atau
* React; atau
* Next.js.

Rekomendasi awal:

```text
React / Next.js + deploy ke Vercel
```

### 6.2 Backend

Opsi teknologi:

* Google Apps Script Web App; atau
* Next.js API Route; atau
* Express.js di Render.

Rekomendasi awal untuk versi sederhana:

```text
Google Apps Script Web App
```

Rekomendasi untuk versi lebih profesional:

```text
Next.js API Route + Google Sheets API
```

### 6.3 Database

Database awal:

```text
Google Spreadsheet
```

### 6.4 Payment Gateway

Opsi payment gateway:

* Midtrans Payment Link / Snap
* Xendit Payment Link / Invoice

Rekomendasi awal:

```text
Payment Link / Invoice
```

Alasannya karena lebih sederhana, cepat diterapkan, dan tidak perlu membuat halaman checkout sendiri.

---

## 7. System Flow

### 7.1 High Level System Flow

```text
Peserta
   ↓
Mengisi Form Pendaftaran
   ↓
Frontend Website
   ↓
Backend API
   ↓
Validasi Data
   ↓
Generate Registration ID & Order ID
   ↓
Simpan Data ke Spreadsheet
   ↓
Create Payment Link
   ↓
Payment Gateway
   ↓
Peserta Melakukan Pembayaran
   ↓
Webhook Payment Gateway
   ↓
Backend API
   ↓
Update Status Pembayaran
   ↓
Google Spreadsheet
   ↓
Kirim Email Konfirmasi
   ↓
Peserta Resmi Terdaftar
```

### 7.2 Detail System Flow Pendaftaran

```text
Peserta membuka website
        ↓
Mengisi form pendaftaran
        ↓
Klik tombol Daftar
        ↓
Frontend validasi data
        ↓
Backend menerima data
        ↓
Generate registration_id
Generate order_id
        ↓
Simpan data ke Spreadsheet
(Status: PENDING_PAYMENT)
        ↓
Create payment link
        ↓
Simpan payment URL
        ↓
Redirect ke halaman pembayaran
```

### 7.3 Detail System Flow Pembayaran

```text
Peserta membuka payment link
        ↓
Memilih metode pembayaran
        ↓
Melakukan pembayaran
        ↓
Payment Gateway memproses transaksi
        ↓
Webhook dikirim ke Backend
        ↓
Backend verifikasi signature
        ↓
Cari order_id
        ↓
Update payment_status
        ↓
Update participant_status
        ↓
Kirim email konfirmasi
        ↓
Selesai
```

### 7.4 System Flow Error Handling

```text
Submit Form
      ↓
Validasi Gagal?
      ↓
Ya → Tampilkan Error
Tidak
      ↓
Create Payment Link
      ↓
Gagal?
      ↓
Ya → Simpan Log Error
      ↓
Tampilkan Pesan ke Peserta
Tidak
      ↓
Lanjut ke Pembayaran
```

---

## 8. Business Flow

### 8.1 Business Flow Pendaftaran Peserta

```text
Peserta ingin mengikuti lomba
        ↓
Peserta mengisi formulir
        ↓
Peserta melakukan pembayaran
        ↓
Pembayaran berhasil
        ↓
Peserta resmi terdaftar
        ↓
Peserta menerima email konfirmasi
        ↓
Peserta mengikuti tahapan lomba
```

### 8.2 Business Flow Panitia

```text
Panitia membuka pendaftaran
        ↓
Peserta melakukan registrasi
        ↓
Sistem mencatat data peserta
        ↓
Sistem mencatat pembayaran
        ↓
Panitia memonitor Spreadsheet
        ↓
Panitia melakukan verifikasi data
        ↓
Panitia mengumumkan peserta valid
        ↓
Peserta mengikuti lomba
```

### 8.3 Business Flow Status Peserta

```text
Pendaftaran Dibuat
        ↓
WAITING_PAYMENT
        ↓
Pembayaran Berhasil?
        ↓
Tidak
 ├─ EXPIRED
 ├─ FAILED
 └─ CANCELLED

Ya
        ↓
REGISTERED
        ↓
Verifikasi Panitia
        ↓
VERIFIED
```

### 8.4 Business Flow Pembayaran

```text
Peserta Submit Form
        ↓
Invoice Dibuat
        ↓
Status = PENDING_PAYMENT
        ↓
Peserta Membayar
        ↓
Payment Gateway Mengirim Webhook
        ↓
Status = PAID
        ↓
Email Konfirmasi Dikirim
        ↓
Peserta Aktif
```

### 8.5 Business Rules Flow

```text
Submit Form
      ↓
Data Tersimpan
      ↓
Belum Bayar
      ↓
WAITING_PAYMENT
      ↓
Bayar Berhasil
      ↓
REGISTERED
      ↓
Verifikasi Panitia
      ↓
VERIFIED
      ↓
Mengikuti Lomba
```

---

## 9. Alur Utama Sistem

### 9.1 Alur Pendaftaran Peserta

1. Peserta membuka halaman pendaftaran.
2. Peserta mengisi form pendaftaran.
3. Peserta menekan tombol daftar.
4. Sistem melakukan validasi data.
5. Sistem membuat `registration_id`.
6. Sistem membuat `order_id`.
7. Sistem menyimpan data awal ke Spreadsheet.
8. Sistem membuat payment link melalui payment gateway.
9. Sistem menyimpan payment link ke Spreadsheet.
10. Peserta diarahkan ke halaman pembayaran.

### 9.2 Alur Pembayaran

1. Peserta membuka payment link.
2. Peserta memilih metode pembayaran.
3. Peserta menyelesaikan pembayaran.
4. Payment gateway mengirim webhook ke backend.
5. Backend memverifikasi webhook.
6. Backend mencocokkan `order_id`.
7. Backend memperbarui status pembayaran di Spreadsheet.
8. Jika pembayaran berhasil, status menjadi `PAID`.
9. Sistem mengirim email konfirmasi ke peserta.

### 9.3 Alur Konfirmasi

1. Setelah status pembayaran menjadi `PAID`, sistem membuat atau mengaktifkan nomor peserta.
2. Sistem mengirim email berisi:

   * Nama peserta/tim
   * ID pendaftaran
   * Status pembayaran
   * Kategori lomba
   * Informasi lanjutan lomba
3. Panitia dapat melihat data peserta di Google Spreadsheet.

---

## 10. Field Form Pendaftaran

### 10.1 Data Umum

| Field          | Tipe     | Wajib    | Keterangan                |
| -------------- | -------- | -------- | ------------------------- |
| Nama Tim       | Text     | Ya       | Jika lomba berbasis tim   |
| Nama Ketua     | Text     | Ya       | Nama penanggung jawab tim |
| Email          | Email    | Ya       | Untuk konfirmasi          |
| Nomor WhatsApp | Text     | Ya       | Kontak utama              |
| Instansi       | Text     | Ya       | Sekolah/kampus/organisasi |
| Kategori Lomba | Select   | Ya       | Pilihan kategori lomba    |
| Jumlah Anggota | Number   | Ya       | Jumlah anggota tim        |
| Nama Anggota   | Textarea | Opsional | Bisa disesuaikan          |
| Catatan        | Textarea | Opsional | Catatan tambahan          |

### 10.2 Data Tambahan Opsional

| Field                          | Tipe | Keterangan               |
| ------------------------------ | ---- | ------------------------ |
| Upload Kartu Pelajar/Mahasiswa | File | Disimpan ke Google Drive |
| Upload Surat Delegasi          | File | Disimpan ke Google Drive |
| Asal Daerah                    | Text | Jika lomba nasional      |
| Nama Pembimbing                | Text | Jika perlu               |
| Nomor Pembimbing               | Text | Jika perlu               |

---

## 11. Struktur Google Spreadsheet

Nama sheet utama:

```text
registrations
```

Kolom yang disarankan:

* registration_id
* order_id
* created_at
* nama_tim
* nama_ketua
* email
* whatsapp
* instansi
* kategori_lomba
* jumlah_anggota
* nama_anggota
* amount
* payment_status
* payment_url
* payment_method
* paid_at
* participant_status
* email_status
* notes

---

## 12. Status Sistem

### 12.1 Status Pembayaran

```text
PENDING_PAYMENT
PAID
EXPIRED
FAILED
CANCELLED
REFUNDED
```

### 12.2 Status Peserta

```text
WAITING_PAYMENT
REGISTERED
VERIFIED
REJECTED
```

---

## 13. Halaman Website

### 13.1 Halaman Beranda / Pendaftaran

* Nama lomba
* Deskripsi lomba
* Timeline
* Biaya pendaftaran
* Form pendaftaran

### 13.2 Halaman Pending

* Status pembayaran menunggu
* Payment link
* Registration ID

### 13.3 Halaman Sukses

* Pembayaran berhasil
* Nomor peserta
* Informasi lanjutan

### 13.4 Halaman Gagal

* Pembayaran gagal
* Instruksi bantuan

---

## 14. Validasi Form

### Frontend

* Validasi email
* Validasi nomor WhatsApp
* Validasi field wajib
* Validasi ukuran file

### Backend

* Validasi kategori
* Validasi kuota
* Validasi nominal
* Validasi status pendaftaran

---

## 15. Aturan Bisnis

1. Peserta resmi terdaftar hanya setelah pembayaran berhasil.
2. Status pembayaran hanya berasal dari webhook payment gateway.
3. Data peserta tetap tersimpan walaupun belum membayar.
4. Email konfirmasi hanya dikirim setelah status `PAID`.
5. Panitia melakukan verifikasi lanjutan jika diperlukan.

---

## 16. Payment Gateway Requirement

Sistem harus dapat:

1. Membuat transaksi pembayaran.
2. Membuat payment link.
3. Menerima webhook.
4. Memverifikasi webhook.
5. Mengupdate status pembayaran.
6. Menyimpan metode pembayaran.
7. Menyimpan waktu pembayaran.

---

## 17. Email Konfirmasi

Contoh email:

```text
Halo [Nama Ketua],

Terima kasih telah mendaftar pada [Nama Lomba].

Pendaftaran Anda telah berhasil dikonfirmasi.

ID Pendaftaran: [REG-0001]
Nama Tim: [Nama Tim]
Kategori: [Kategori]
Status Pembayaran: PAID

Silakan simpan email ini sebagai bukti pendaftaran.

Terima kasih.
Panitia [Nama Lomba]
```

---

## 18. Keamanan

1. API key tidak boleh berada di frontend.
2. Webhook wajib diverifikasi.
3. Spreadsheet tidak boleh publik.
4. Validasi dilakukan di frontend dan backend.
5. Status PAID tidak boleh diubah dari frontend.

---

## 19. Error Handling

| Kondisi                  | Respons Sistem     |
| ------------------------ | ------------------ |
| Form tidak lengkap       | Tampilkan validasi |
| Email tidak valid        | Tampilkan error    |
| Payment gateway error    | Simpan log         |
| Spreadsheet gagal update | Simpan log         |
| Webhook tidak valid      | Tolak request      |
| Pembayaran expired       | Update status      |

---

## 20. Logging

Sheet tambahan:

```text
logs
```

Contoh:

```text
2026-06-09 | INFO | CREATE_REGISTRATION | ORD-001 | Registration created
2026-06-09 | INFO | PAYMENT_PAID | ORD-001 | Payment confirmed
2026-06-09 | ERROR | SEND_EMAIL | ORD-001 | Failed to send email
```

---

## 21. Acceptance Criteria

1. Form dapat disubmit.
2. Data tersimpan ke Spreadsheet.
3. Payment link berhasil dibuat.
4. Webhook berhasil diterima.
5. Status pembayaran otomatis berubah.
6. Email konfirmasi terkirim.

---

## 22. MVP Tahap 1

1. Form pendaftaran.
2. Simpan ke Spreadsheet.
3. Generate ID.
4. Payment link.
5. Webhook pembayaran.
6. Update status.
7. Email konfirmasi.

---

## 23. Pengembangan Tahap Berikutnya

1. Dashboard admin.
2. Login panitia.
3. WhatsApp notification.
4. Upload dokumen.
5. QR Check-in.
6. Voucher diskon.
7. Reminder pembayaran.
8. Sertifikat otomatis.

---

## 24. Risiko dan Mitigasi

| Risiko            | Mitigasi             |
| ----------------- | -------------------- |
| Apps Script limit | Migrasi ke Node.js   |
| Webhook gagal     | Retry mechanism      |
| Spreadsheet besar | Migrasi database     |
| Email spam        | Gunakan domain resmi |

---

## 25. Kesimpulan

Sistem pendaftaran lomba ini dirancang untuk mengotomatisasi proses registrasi dan pembayaran peserta dengan biaya implementasi yang rendah serta mudah dikelola oleh panitia.

Stack yang direkomendasikan:

```text
Frontend : Next.js
Backend  : Google Apps Script / Next.js API
Database : Google Spreadsheet
Payment  : Midtrans / Xendit
Email    : Gmail API / Resend
```

Dengan pendekatan ini, peserta dapat mendaftar dan membayar secara mandiri, sementara panitia cukup memonitor seluruh proses melalui Google Spreadsheet tanpa memerlukan dashboard admin khusus pada tahap awal.
