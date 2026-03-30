# API Endpoints Documentation

Base URL: `https://api.domainanda.com/api/v1`

---

## 1. Donation Flow & AI Moderation (Public)

Alur ini digunakan oleh halaman donasi publik (Donatur). Titik kritis AI berada di endpoint `/validate`.

### A. Validasi Pesan (AI Core)
Digunakan untuk mengecek teks donasi ke *engine* AI sebelum donatur bisa melanjutkan ke pembayaran.

* **URL:** `/donate/{slug_url}/validate`
* **Method:** `POST`
* **Request Body:**
    ```json
    {
      "message": "malam bang, info pola slot gacor dong",
      "amount": 50000
    }
    ```
* **Success Response (Pesan Aman):**
    * **Code:** `200 OK`
    * **Content:**
        ```json
        {
          "status": "success",
          "data": {
            "is_safe": true,
            "message": "Pesan valid dan aman."
          }
        }
        ```
* **Error Response (Pesan Diblokir AI):**
    * **Code:** `400 Bad Request`
    * **Content:**
        ```json
        {
          "status": "error",
          "error": {
            "code": "CONTENT_BLOCKED",
            "message": "Pesan ditolak karena terindikasi spam atau promosi ilegal.",
            "ai_reason": "Terdeteksi keyword judol: slot, gacor"
          }
        }
        ```

### B. Buat Transaksi (Checkout)
Dipanggil hanya jika endpoint `/validate` mengembalikan status aman (`is_safe: true`).

* **URL:** `/donate/{slug_url}/checkout`
* **Method:** `POST`
* **Request Body:**
    ```json
    {
      "donator_name": "Hamba Allah",
      "donator_email": "anon@mail.com",
      "amount": 50000,
      "message": "Semangat terus bang streamnya!",
      "payment_method": "QRIS"
    }
    ```
* **Success Response:**
    * **Code:** `201 Created`
    * **Content:**
        ```json
        {
          "status": "success",
          "data": {
            "transaction_id": "550e8400-e29b-41d4-a716-446655440000",
            "payment_type": "QRIS",
            "qr_string": "00020101021126670016COM.GOJEK.WWW011893600914...",
            "checkout_url": "[https://app.sandbox.midtrans.com/snap/v2/vtweb/](https://app.sandbox.midtrans.com/snap/v2/vtweb/)..."
          }
        }
        ```

---

## 2. Webhooks & WebSockets (System)

Digunakan untuk komunikasi *real-time* antara sistem pembayaran, *server* Anda, dan layar OBS Streamer.

### A. Payment Gateway Webhook
Menerima *update* status transaksi dari pihak ketiga (contoh: Midtrans/Xendit).

* **URL:** `/webhook/payment`
* **Method:** `POST`
* **Request Body:** *(Format standar dari Payment Gateway)*
    ```json
    {
      "order_id": "550e8400-e29b-41d4-a716-446655440000",
      "transaction_status": "settlement",
      "gross_amount": "50000.00"
    }
    ```
* **Action:** Jika `transaction_status` == `settlement` (sukses), server akan mengubah status di DB dan memancarkan (emit) data ke WebSocket.
* **Success Response:** `200 OK`

### B. OBS Overlay WebSocket
Koneksi yang dibuka oleh *Browser Source* OBS untuk mendengarkan *event* donasi baru secara *real-time*.

* **URL:** `ws://api.domainanda.com/ws/overlay/{overlay_token}`
* **Protocol:** WebSocket (`ws://` atau `wss://`)
* **Event Emitted (Saat Donasi Sukses):**
    ```json
    {
      "event": "new_donation",
      "data": {
        "donator_name": "Hamba Allah",
        "amount": 50000,
        "message": "Semangat terus bang streamnya!"
      }
    }
    ```

---

## 3. Dashboard & Account (Protected)

Membutuhkan *header* otorisasi: `Authorization: Bearer <JWT_TOKEN>`

| Method | Endpoint | Deskripsi |
| :--- | :--- | :--- |
| **POST** | `/auth/register` | Pendaftaran akun streamer baru. |
| **POST** | `/auth/login` | Login streamer, mengembalikan token JWT. |
| **GET** | `/streamer/dashboard` | Mengambil metrik utama (saldo, jumlah donasi, donasi terakhir). |
| **GET** | `/streamer/moderation-logs` | Menampilkan riwayat pesan yang berhasil diblokir AI. |
| **GET** | `/streamer/overlay` | Mengambil data pengaturan visual Overlay OBS (warna, ukuran font). |
| **PUT** | `/streamer/overlay` | Memperbarui pengaturan visual overlay (warna teks, *font family*, tata letak). |
| **POST** | `/streamer/withdrawals` | Mengajukan pencairan saldo (mengurangi `users.balance`). |