# Product Requirements Document (PRD)

**Nama Produk:** AI-Secured Streamer Donation Platform  
**Status:** Draft  
**Target Rilis:** MVP (Minimum Viable Product)  

---

## 1. Latar Belakang & Objektif

### 1.1. Problem Statement
Streamer *live-streaming* ingin berinteraksi dengan audiens melalui fitur pesan donasi secara aman, namun terhalang oleh sistem saat ini yang tidak memiliki filter otomatis. Hal ini mengakibatkan donatur bisa dengan bebas mengirimkan pesan promosi judi online (judol) atau konten tidak pantas yang kemudian muncul ke dalam siaran langsung.

### 1.2. Solution
Mengembangkan platform donasi pintar berbasis AI yang secara otomatis memvalidasi dan memblokir pesan promosi judi online serta konten tidak pantas secara *real-time* sebelum ditampilkan (visual) atau dibacakan (Text-to-Speech) di siaran langsung.

### 1.3. Objektif Bisnis & Produk
* Menciptakan lingkungan *live-streaming* yang aman dan bersih dari spam/promosi ilegal.
* Memberikan rasa aman bagi *streamer* untuk mengaktifkan fitur donasi.
* Memastikan proses moderasi berjalan cepat (*low latency*) agar tidak mengganggu *real-time experience* donasi.

---

## 2. User Persona

* **Streamer:** Kreator konten yang melakukan *live-streaming* dan ingin memonetisasi siarannya melalui dukungan finansial dari penonton tanpa risiko teguran pedoman komunitas (banned) akibat konten donasi pihak ketiga.
* **Donatur (Audiens):** Penonton yang ingin memberikan apresiasi finansial dan berinteraksi dengan streamer melalui pesan singkat yang akan muncul di layar.

---

## 3. User Flow 

### 3.1. Alur Donatur (Donatur Flowchart)
1. Donatur melakukan *scan* QR code atau klik *link* donasi.
2. Sistem menampilkan halaman Form Donate.
3. Donatur mengisi: Nama, Email, Pesan, Jumlah Donasi, Media share/GIF, dan Metode Bayar.
4. **[PROSES KRITIKAL]** Sistem memvalidasi input pesan (AI Moderation). Jika pesan ditolak, donatur kembali ke Form Donate dengan notifikasi error. Jika aman, lanjut ke pembayaran.
5. Proses Pembayaran via *Payment Gateway*.
6. Jika pembayaran gagal, donatur kembali ke proses pembayaran. Jika sukses, pesan dan animasi ditampilkan pada *stream* (Overlay).
7. Selesai.

### 3.2. Alur Streamer (Streamer Flowchart)
1. Login/Register akun Streamer.
2. Masuk ke halaman *Dashboard* (Menampilkan: Link/QR Donate, Saldo, dan Riwayat Donasi & Withdraw).
3. Navigasi *Sidebar* memberikan akses ke 4 menu utama:
    * **Profile:** Menampilkan halaman profil -> Ubah Data -> Simpan data.
    * **Withdraw:** Memilih tujuan pencairan dan nominal -> Proses konfirmasi -> Pencairan Berhasil.
    * **Overlay:** Menampilkan URL *Browser Source* untuk OBS, Preview Overlay, dan Pengaturan Teks/Visual Overlay.
    * **Dashboard:** Kembali ke beranda.

---

## 4. Rincian Fitur (Feature Requirements)

### Feature 1: Sistem Validasi Pesan AI (AI Moderation Core)
Fitur utama untuk menyaring pesan donasi secara *real-time* sebelum proses pembayaran dilakukan.

* **User Story 1 (Streamer):** Sebagai Streamer, saya ingin pesan donasi divalidasi oleh AI sebelum masuk ke sistem pembayaran, sehingga siaran *live* saya aman dari konten judol atau kata-kata tidak pantas.
* **User Story 2 (Donatur):** Sebagai Donatur, saya ingin diberi tahu jika pesan saya melanggar aturan sebelum uang saya terpotong, sehingga saya tidak mengalami kerugian finansial sia-sia.
* **Acceptance Criteria:**
  * **AC 1:** Sistem melakukan pengecekan teks via API/Engine AI ketika donatur menekan tombol "Lanjut Pembayaran" di Form Donasi.
  * **AC 2:** Jika AI mendeteksi konten judol/spam/kata kotor, sistem langsung menggagalkan proses dan memunculkan pop-up peringatan: "Pesan ditolak: Mengandung kata yang tidak diizinkan."
  * **AC 3:** Donatur tetap berada di halaman Form Donasi dan tidak diteruskan ke Payment Gateway jika pesan ditolak.
  * **AC 4:** Proses pengecekan oleh AI maksimal memakan waktu 1,5 detik (*latency*).
  * **AC 5:** Jika pesan dinyatakan "Aman" oleh AI, sistem langsung mengarahkan donatur ke halaman instruksi pembayaran.

### Feature 2: Halaman Donasi & Pembayaran Donatur
Halaman antarmuka publik bagi audiens untuk mengirimkan dana dan pesan.

* **User Story:** Sebagai Donatur, saya ingin mengisi form donasi yang mudah digunakan dan memiliki berbagai pilihan metode pembayaran, sehingga saya bisa mendukung streamer favorit saya dengan cepat.
* **Acceptance Criteria:**
  * **AC 1:** Halaman donasi dapat diakses melalui Link URL unik atau *scan* QR Code milik streamer.
  * **AC 2:** Form donasi memiliki kolom wajib: Nama (bisa anonim), Nominal Donasi, Pesan Teks, dan Pilihan Pembayaran (QRIS, E-Wallet).
  * **AC 3:** Sistem menolak input nominal jika di bawah batas minimum yang ditentukan (misal: Rp10.000).
  * **AC 4:** Halaman terintegrasi dengan Payment Gateway untuk memproses transaksi secara aman.
  * **AC 5:** Setelah pembayaran berhasil diverifikasi oleh Payment Gateway, donatur diarahkan ke halaman "Terima Kasih".

### Feature 3: Overlay OBS & Notifikasi Real-Time
Sistem visual dan audio yang terhubung langsung ke *software broadcasting* (OBS/XSplit) milik streamer.

* **User Story:** Sebagai Streamer, saya ingin donasi yang masuk langsung muncul di layar *live streaming* saya beserta animasinya, sehingga saya bisa langsung *shoutout* dan berterima kasih kepada donatur.
* **Acceptance Criteria:**
  * **AC 1:** Sistem menyediakan URL *Browser Source* unik di Dashboard Streamer untuk dipasang ke OBS.
  * **AC 2:** Sistem menerima Webhook "Sukses" dari Payment Gateway dan langsung mengirimkan *trigger* ke URL Overlay tersebut via WebSockets.
  * **AC 3:** Overlay menampilkan animasi GIF/gambar, Nama Donatur, Nominal, dan Teks Pesan di layar OBS.
  * **AC 4:** Sistem membacakan pesan teks menggunakan fitur *Text-to-Speech* (TTS) jika fitur tersebut diaktifkan oleh streamer.
  * **AC 5:** Delay dari pembayaran sukses hingga notifikasi muncul di layar maksimal 3 detik.

### Feature 4: Dashboard Streamer & Manajemen Akun
Pusat kontrol bagi streamer untuk memantau aktivitas donasi dan mengatur profil.

* **User Story 1:** Sebagai Streamer, saya ingin melihat ringkasan pendapatan dan tautan donasi saya di satu halaman utama, sehingga saya bisa mengelolanya dengan efisien.
* **User Story 2:** Sebagai Streamer, saya ingin melihat log pesan yang berhasil diblokir oleh AI, sehingga saya tahu bahwa sistem keamanan berfungsi dengan baik.
* **Acceptance Criteria:**
  * **AC 1:** Dashboard menampilkan total saldo aktif yang bisa ditarik dan Link/QR donasi yang memiliki tombol "Copy".
  * **AC 2:** Dashboard memiliki tabel "Riwayat Donasi Sukses" yang menampilkan Nama, Nominal, Pesan, dan Tanggal.
  * **AC 3:** Dashboard memiliki tab khusus "Moderasi AI" yang menampilkan tabel percobaan donasi gagal (pesan yang terblokir) beserta alasan pemblokirannya (misal: "Terdeteksi keyword: gacor").
  * **AC 4:** Terdapat halaman Profile yang memungkinkan streamer mengubah Nama Display, Email, dan Password.

### Feature 5: Sistem Penarikan Dana (Withdrawal)
Fitur untuk mencairkan saldo donasi ke rekening pribadi streamer.

* **User Story:** Sebagai Streamer, saya ingin mencairkan saldo donasi yang terkumpul ke rekening bank atau dompet digital saya, sehingga saya bisa menikmati hasil dukungan audiens.
* **Acceptance Criteria:**
  * **AC 1:** Halaman Withdraw menampilkan saldo yang tersedia saat ini.
  * **AC 2:** Streamer dapat memilih metode pencairan (Bank Transfer, BCA, Mandiri, GoPay, OVO, dll) dan memasukkan nomor rekening/HP.
  * **AC 3:** Sistem menolak penarikan jika nominal yang diinput melebihi saldo aktif atau di bawah batas minimum pencairan (misal: Rp50.000).
  * **AC 4:** Sistem memotong saldo streamer secara otomatis saat proses *withdraw* diajukan dan mengubah status transaksi menjadi "Pending" atau "Sukses".

---

## 5. Non-Functional Requirements (NFR)

* **Latency AI:** Validasi pesan oleh AI harus terjadi di bawah 1 detik untuk menghindari UX yang buruk bagi donatur.
* **Real-time Response:** Jeda waktu antara pembayaran berhasil dengan munculnya notifikasi di OBS (Overlay) maksimal 2-3 detik.
* **Keamanan & Fraud:** Sistem pembayaran harus aman, dan batas minimum *withdraw* harus diatur untuk mencegah *fraud* pencucian uang kecil-kecilan.
* **Reliabilitas:** Sistem harus memiliki *uptime* 99.9% karena aktivitas *live-streaming* terjadi secara *real-time*.

---

## 6. Metrik Kesuksesan (Success Metrics)

* **AI Moderation Accuracy:** % persentase pesan judol/spam yang berhasil diblokir vs. % *false positives* (pesan aman yang tidak sengaja terblokir).
* **Conversion Rate Donatur:** Persentase donatur yang berhasil menyelesaikan pembayaran setelah masuk ke halaman form.
* **Streamer Acquisition & Retention:** Jumlah streamer baru yang mendaftar dan aktif menggunakan aplikasi setiap minggu (WAU).