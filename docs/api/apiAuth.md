# 🔐 API Contract: Authentication (Auth)

Dokumen ini berisi spesifikasi dan kontrak API untuk fitur autentikasi 
Tim Front-End (FE) dan Back-End (BE) wajib merujuk pada format data di bawah ini untuk proses integrasi.

**Base URL API:** `http://localhost:8000` *(Ubah sesuai URL server saat deployment)*

---

## 1. Login User

Endpoint untuk melakukan autentikasi reguler menggunakan email dan kata sandi.

* **URL:** `/api/auth/login`
* **Method:** `POST`
* **Content-Type:** `application/json`

### Request Body
```json
{
  "email": "streamer@neon.com",
  "password": "kata_sandi_rahasia_anda"
}
Responses
✅ 200 OK (Login Sukses)

JSON
{
  "status": "success",
  "message": "Login berhasil. Selamat datang kembali!",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "1",
      "email": "streamer@neon.com",
      "displayName": "NeonGhost",
      "role": "streamer"
    }
  }
}
❌ 401 Unauthorized (Kredensial Salah)

JSON
{
  "status": "error",
  "message": "Alamat email atau kata sandi Anda salah.",
  "error_code": "AUTH_CREDENTIALS_INVALID"
}
❌ 429 Too Many Requests (Brute-Force Detected)

JSON
{
  "status": "error",
  "message": "Upaya login mencurigakan dideteksi. Akun Anda telah dibatasi sementara.",
  "error_code": "AUTH_BRUTE_FORCE_DETECTED",
  "retry_after_seconds": 3600
}
2. Register Akun Streamer
Endpoint untuk pendaftaran akun streamer baru.

URL: /api/auth/register

Method: POST

Content-Type: application/json

Request Body
JSON
{
  "displayName": "NeonGhost",
  "email": "streamer@neon.protocol",
  "password": "password_minimal_8_karakter",
  "confirmPassword": "password_minimal_8_karakter"
}
Responses
✅ 201 Created (Registrasi Sukses)

JSON
{
  "status": "success",
  "message": "Registrasi berhasil.",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "2",
      "email": "streamer@neon.protocol",
      "displayName": "NeonGhost",
      "role": "streamer"
    }
  }
}
❌ 409 Conflict (Email Sudah Digunakan)

JSON
{
  "status": "error",
  "message": "Email ini sudah digunakan oleh pengguna lain.",
  "error_code": "AUTH_EMAIL_TAKEN"
}
3. Login/Register dengan Google SSO
Endpoint untuk menangani autentikasi melalui Google Single Sign-On.

URL: /api/auth/sso/google

Method: POST

Content-Type: application/json

Request Body
JSON
{
  "provider_token": "id_token_from_google_here..."
}
Responses
✅ 200 OK (SSO Sukses)

JSON
{
  "status": "success",
  "message": "Autentikasi Google SSO berhasil.",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "3",
      "email": "google_user@gmail.com",
      "displayName": "Google User",
      "role": "streamer"
    },
    "new_account": false
  }
}
4. Lupa Password (Request Reset)
Endpoint untuk meminta pengiriman link reset kata sandi ke email.

URL: /api/auth/password/forgot

Method: POST

Content-Type: application/json

Request Body
JSON
{
  "email": "streamer@neon.com"
}
Responses
✅ 200 OK (Link Terkirim)

JSON
{
  "status": "success",
  "message": "Tautan reset kata sandi telah dikirim ke alamat email Anda.",
  "data": {
    "next_step": "Cek kotak masuk atau folder spam email Anda."
  }
}
❌ 404 Not Found (Email Tidak Terdaftar)

JSON
{
  "status": "error",
  "message": "Alamat email tidak terdaftar dalam sistem kami.",
  "error_code": "PASS_EMAIL_NOT_FOUND"
}
5. Reset Password (Recovery)
Endpoint untuk menyimpan kata sandi baru setelah user mengklik link dari email.

URL: /api/auth/password/reset

Method: POST

Content-Type: application/json

Request Body
JSON
{
  "token": "token_recovery_from_email_here...",
  "newPassword": "password_baru_anda_8_karakter",
  "confirmNewPassword": "password_baru_anda_8_karakter"
}
Responses
✅ 200 OK (Password Berhasil Diubah)

JSON
{
  "status": "success",
  "message": "Kata sandi Anda telah berhasil diperbarui. Silakan login kembali."
}
❌ 400 Bad Request (Token Tidak Valid / Kadaluarsa)

JSON
{
  "status": "error",
  "message": "Tautan reset kata sandi Anda tidak valid atau telah kadaluarsa.",
  "error_code": "PASS_TOKEN_INVALID"
}