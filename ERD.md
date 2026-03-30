# System Architecture & API Documentation

Dokumen ini berisi rancangan skema *database* dan struktur API untuk platform AI-Secured Streamer Donation.

---

## 1. Database Schema (Entity-Relationship Diagram)

Bagan di bawah ini dibuat menggunakan Mermaid.js yang didukung secara *native* oleh GitHub.

```mermaid
erDiagram
    USERS ||--o| OVERLAY_SETTINGS : "has (1:1)"
    USERS ||--o{ DONATIONS : "receives (1:N)"
    USERS ||--o{ MODERATION_LOGS : "records (1:N)"
    USERS ||--o{ WITHDRAWALS : "requests (1:N)"

    USERS {
        uuid id PK
        varchar name
        varchar email
        varchar password_hash
        varchar slug_url
        decimal balance
        timestamp created_at
        timestamp updated_at
    }

    OVERLAY_SETTINGS {
        uuid id PK
        uuid user_id FK
        varchar overlay_token
        boolean tts_enabled
        decimal min_tts_amount
        json theme_settings
        timestamp updated_at
    }

    DONATIONS {
        uuid id PK
        uuid user_id FK
        varchar donator_name
        varchar donator_email
        decimal amount
        text message
        varchar payment_method
        enum status "PENDING, SUCCESS, FAILED"
        varchar payment_gateway_ref
        timestamp created_at
    }

    MODERATION_LOGS {
        uuid id PK
        uuid user_id FK
        varchar donator_name
        decimal attempted_amount
        text blocked_message
        varchar ai_reason
        timestamp created_at
    }

    WITHDRAWALS {
        uuid id PK
        uuid user_id FK
        decimal amount
        varchar destination_bank
        varchar account_number
        enum status "PENDING, SUCCESS, FAILED"
        timestamp created_at
    }