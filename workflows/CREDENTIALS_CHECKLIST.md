# VAPT Workflow System — Credentials Checklist
> Dokumen ini berisi **semua** kredensial, token, ID, dan konfigurasi yang harus dipersiapkan sebelum sistem dapat dijalankan.
> Isi kolom **Nilai** setelah setiap item berhasil didapatkan.

---

## RINGKASAN KOMPONEN

| # | Komponen | Platform | Jumlah Item |
|---|----------|----------|-------------|
| 1 | Telegram Bots | Telegram / @BotFather | 3 bot token + 4 chat ID |
| 2 | Notion Integration | Notion | 1 API key + 8 database ID + 3 template page ID |
| 3 | Anthropic / Claude AI | Anthropic Console | 1 API key |
| 4 | Redis | ~~Self-hosted / Cloud~~ | **TIDAK DIPERLUKAN** (diganti Notion DB-8) |
| 5 | n8n Credentials | n8n Settings | 5 credential entries (tanpa Redis) |
| 6 | n8n Variables | n8n Settings | 27 variable entries |

---

## BAGIAN 1 — TELEGRAM

### 1.1 Bot Tokens (dari @BotFather)

Cara mendapatkan:
1. Buka Telegram → cari **@BotFather**
2. Ketik `/newbot`
3. Ikuti instruksi: masukkan nama display dan username bot
4. BotFather akan memberikan **HTTP API Token**

| Bot | Nama Bot (Display) | Username Bot | Token | Status |
|-----|-------------------|--------------|-------|--------|
| **Bot A** — PM Assistant | `VAPT PM Assistant` | `@vapt_pm_bot` (contoh) | `___________:___________` | ☐ |
| **Bot B** — Sales & Admin | `VAPT Sales Bot` | `@vapt_sales_bot` (contoh) | `___________:___________` | ☐ |
| **Bot C** — Pentester Group | `VAPT Pentester Bot` | `@vapt_pentester_bot` (contoh) | `___________:___________` | ☐ |

> **Format Token:** `1234567890:ABCdefGHIjklMNOpqrSTUvwxYZ123456789`

---

### 1.2 Telegram User ID — Project Manager

Digunakan untuk autentikasi Bot A (hanya PM yang boleh menggunakan bot ini).

| Item | Nilai | Cara Mendapatkan |
|------|-------|-----------------|
| **PM Telegram User ID** | `___________` | Chat dengan [@userinfobot](https://t.me/userinfobot) → lihat field `Id` |
| **PM Telegram Chat ID** | `___________` | Sama dengan User ID untuk private chat. Kirim pesan ke Bot A → cek `getUpdates` |

> **Catatan:** Untuk private chat, `user_id` = `chat_id`. Gunakan nilai yang sama untuk keduanya.

---

### 1.3 Telegram Group Chat IDs

Digunakan untuk Bot B (Sales Group) dan Bot C (Pentester Group).

| Group | Nama Group | Chat ID | Status |
|-------|------------|---------|--------|
| **Sales & Admin Group** | (nama group Telegram Sales) | `___________` | ☐ |
| **Pentester Group** | (nama group Telegram Pentester) | `___________` | ☐ |

**Cara mendapatkan Group Chat ID:**

**Metode 1 — via getUpdates:**
```
1. Tambahkan Bot B ke Sales group, dan Bot C ke Pentester group
2. Kirim sembarang pesan di group tersebut
3. Buka browser → akses URL:
   https://api.telegram.org/bot{TOKEN}/getUpdates
4. Cari field: "chat": { "id": -XXXXXXXXX }
   (Group chat ID selalu negatif, dimulai dengan -)
```

**Metode 2 — via @RawDataBot:**
```
1. Tambahkan @RawDataBot ke group
2. Ketik sembarang pesan
3. Bot akan membalas dengan raw data termasuk chat.id
4. Hapus @RawDataBot setelah mendapatkan ID
```

---

### 1.4 Konfigurasi Bot via @BotFather

Lakukan pengaturan berikut setelah bot dibuat:

| Bot | Pengaturan | Perintah BotFather |
|-----|------------|-------------------|
| **Bot A** | Nonaktifkan join group (private only) | `/setjoingroups` → Disable |
| **Bot A** | Set deskripsi | `/setdescription` → "VAPT PM Personal Assistant" |
| **Bot B** | Izinkan join group | `/setjoingroups` → Enable |
| **Bot B** | Set deskripsi | `/setdescription` → "VAPT Sales & Admin Bot" |
| **Bot C** | Izinkan join group | `/setjoingroups` → Enable |
| **Bot C** | Set deskripsi | `/setdescription` → "VAPT Pentester Group Bot" |
| **Semua Bot** | Set commands | `/setcommands` → daftar perintah (lihat bawah) |

**Command list untuk Bot A** (`/setcommands` → pilih Bot A):
```
generate_sow - Generate Statement of Work
generate_roe - Generate Rules of Engagement
generate_proposal - Generate Proposal Proyek
new_project - Input Proyek Baru
update_phase - Update Fase Proyek
summary - Dashboard Ringkasan Proyek
help - Tampilkan bantuan
```

**Command list untuk Bot B & C:**
```
summary - Ringkasan status proyek
```

**Command list tambahan Bot C:**
```
update_activity - Log aktivitas harian
add_vuln - Tambah temuan vulnerability
```

---

## BAGIAN 2 — NOTION

### 2.1 Notion Integration API Key

| Item | Nilai | Status |
|------|-------|--------|
| **Integration Name** | `VAPT n8n Integration` | ☐ dibuat |
| **Internal Integration Token** | `secret_____________________` | ☐ dicatat |

**Cara membuat:**
```
1. Buka notion.so → Settings & Members → Connections → Develop or manage integrations
   ATAU buka langsung: https://www.notion.so/my-integrations
2. Klik "+ New integration"
3. Isi:
   - Name: VAPT n8n Integration
   - Logo: (opsional)
   - Associated workspace: pilih workspace yang digunakan
4. Capabilities yang harus diaktifkan:
   ✅ Read content
   ✅ Update content
   ✅ Insert content
5. Klik Submit → catat "Internal Integration Token"
```

---

### 2.2 Notion Database IDs (7 Database)

**Cara mendapatkan Database ID:**
```
Buka database di Notion → klik Share → Copy link
URL format: https://www.notion.so/{workspace}/{DATABASE_ID}?v={view_id}
Database ID = string 32 karakter setelah nama workspace
Contoh: https://notion.so/myworkspace/a1b2c3d4e5f6...7890?v=...
                                       ^^^^^^^^^^^^^^^^ ini Database ID
```

| # | Database | n8n Variable | Database ID | Diinvite ke Integration | Status |
|---|----------|-------------|-------------|------------------------|--------|
| DB-1 | Project List | `NOTION_DB1_PROJECT_LIST` | `________________________________` | ☐ | ☐ |
| DB-2 | Pentester List | `NOTION_DB2_PENTESTER_LIST` | `________________________________` | ☐ | ☐ |
| DB-3 | Pentester Task List | `NOTION_DB3_TASK_LIST` | `________________________________` | ☐ | ☐ |
| DB-4 | Daily Activity Log | `NOTION_DB4_DAILY_LOG` | `________________________________` | ☐ | ☐ |
| DB-5 | Vulnerability List | `NOTION_DB5_VULN_LIST` | `________________________________` | ☐ | ☐ |
| DB-6 | Weekly Summary Report | `NOTION_DB6_WEEKLY_SUMMARY` | `________________________________` | ☐ | ☐ |
| DB-7 | Error Log | `NOTION_DB7_ERROR_LOG` | `________________________________` | ☐ | ☐ |
| DB-8 | **Session Store** *(pengganti Redis)* | `NOTION_DB8_SESSION_STORE` | `________________________________` | ☐ | ☐ |

**DB-8 Schema (Session Store) — buat database Notion baru dengan properti:**

| Properti | Tipe | Keterangan |
|----------|------|------------|
| `session_key` | **Title** | ID sesi (chatId atau userId Telegram) |
| `session_data` | **Text** (Rich Text) | JSON string state percakapan |

**Cara invite integration ke database:**
```
1. Buka database di Notion
2. Klik ••• (tiga titik) di pojok kanan atas
3. Pilih "Connections" → "Add connections"
4. Cari "VAPT n8n Integration" → klik Confirm
5. Ulangi untuk semua 7 database
```

---

### 2.3 Data Awal yang Harus Diinput ke Notion

Sebelum sistem dapat berjalan, data berikut harus sudah ada di Notion:

**DB-2 (Pentester List) — Wajib diisi sebelum Bot C bisa digunakan:**

| Field | Contoh Nilai | Keterangan |
|-------|-------------|------------|
| `pentester_id` | `PENT-001` | ID unik pentester |
| `name` | `Ahmad Fatah` | Nama lengkap |
| `telegram_user_id` | `123456789` | User ID Telegram (ambil via @userinfobot) |
| `current_status` | `Available` | Status awal |

> Setiap anggota tim pentester harus sudah terdaftar di DB-2 dengan `telegram_user_id` yang benar agar bisa menggunakan `/update_activity` dan `/add_vuln`.

---

## BAGIAN 3 — ANTHROPIC / CLAUDE AI

### 3.1 Anthropic API Key

| Item | Nilai | Status |
|------|-------|--------|
| **API Key** | `sk-ant-___________________________` | ☐ |
| **Model yang digunakan** | `claude-sonnet-4-20250514` | ✅ sudah dikonfigurasi |

**Cara mendapatkan:**
```
1. Buka https://console.anthropic.com
2. Login / daftar akun
3. Pergi ke API Keys → Create Key
4. Beri nama: "VAPT n8n"
5. Catat API key (hanya tampil sekali)
```

**Rate Limits yang perlu diketahui:**
| Model | RPM (Request/menit) | TPM (Token/menit) |
|-------|--------------------|--------------------|
| claude-sonnet-4-20250514 | 1.000 | 80.000 |

---

## BAGIAN 4 — REDIS (TIDAK DIPERLUKAN)

> **Versi ini tidak menggunakan Redis.** Session state dikelola oleh sub-workflow `VAPT_Session_Manager` menggunakan Notion DB-8 sebagai backend. Tidak perlu instalasi atau konfigurasi Redis.
>
> **Keuntungan tanpa Redis:**
> - Tidak perlu server Redis tambahan
> - Session data tersimpan permanen di Notion (dapat diaudit)
> - Lebih mudah di-debug (buka DB-8 di Notion untuk melihat state)
>
> **Konsekuensi:**
> - SET/DEL session bersifat async (fire-and-forget) — data ditulis ke Notion setelah respons dikirim
> - Latensi sedikit lebih tinggi vs Redis (Notion API ~200–500ms)
> - Tidak ada TTL otomatis — session lama tidak terhapus otomatis (bersihkan manual jika diperlukan)

---

## BAGIAN 5 — SETUP N8N CREDENTIALS

Daftarkan semua credential di: **n8n → Settings → Credentials → Add Credential**

### 5.1 Daftar Credentials yang Harus Dibuat

| # | Nama Credential (harus sama persis) | Tipe di n8n | Field yang diisi | Status |
|---|-------------------------------------|-------------|-----------------|--------|
| 1 | `Telegram Bot A - PM Assistant` | Telegram API | Bot Token (dari 1.1) | ☐ |
| 2 | `Telegram Bot B - Sales Admin` | Telegram API | Bot Token (dari 1.1) | ☐ |
| 3 | `Telegram Bot C - Pentester Group` | Telegram API | Bot Token (dari 1.1) | ☐ |
| 4 | `Notion API` | Notion API | API Key (dari 2.1) | ☐ |
| 5 | `Anthropic API` | Anthropic | API Key (dari 3.1) | ☐ |

> **Catatan:** Credential `Redis VAPT` **tidak lagi diperlukan** di versi ini.

> **Penting:** Nama credential harus **sama persis** seperti di tabel, termasuk huruf kapital dan spasi, karena nama ini direferensikan langsung di semua workflow JSON.

---

## BAGIAN 6 — SETUP N8N VARIABLES

Daftarkan semua variable di: **n8n → Settings → Variables → Add Variable**

### 6.1 Telegram IDs

| Variable Name | Nilai | Sumber | Status |
|--------------|-------|--------|--------|
| `PM_TELEGRAM_USER_ID` | `___________` | Bagian 1.2 | ☐ |
| `PM_TELEGRAM_CHAT_ID` | `___________` | Bagian 1.2 | ☐ |
| `SALES_GROUP_CHAT_ID` | `-___________` | Bagian 1.3 | ☐ |
| `PENTESTER_GROUP_CHAT_ID` | `-___________` | Bagian 1.3 | ☐ |

### 6.2 Notion Database IDs

| Variable Name | Nilai | Sumber | Status |
|--------------|-------|--------|--------|
| `NOTION_DB1_PROJECT_LIST` | `________________________________` | Bagian 2.2 | ☐ |
| `NOTION_DB2_PENTESTER_LIST` | `________________________________` | Bagian 2.2 | ☐ |
| `NOTION_DB3_TASK_LIST` | `________________________________` | Bagian 2.2 | ☐ |
| `NOTION_DB4_DAILY_LOG` | `________________________________` | Bagian 2.2 | ☐ |
| `NOTION_DB5_VULN_LIST` | `________________________________` | Bagian 2.2 | ☐ |
| `NOTION_DB6_WEEKLY_SUMMARY` | `________________________________` | Bagian 2.2 | ☐ |
| `NOTION_DB7_ERROR_LOG` | `________________________________` | Bagian 2.2 | ☐ |
| `NOTION_DB8_SESSION_STORE` | `________________________________` | Bagian 2.2 (DB-8) | ☐ |

### 6.2b Notion Template Page IDs

Template dokumen disimpan sebagai halaman Notion berisi rich text. Salin konten dari file DOCX ke dalam halaman Notion, lalu ambil Page ID-nya (32 karakter dari URL halaman).

| Variable Name | Nilai | Keterangan | Status |
|--------------|-------|------------|--------|
| `NOTION_PAGE_SOW_TEMPLATE` | `________________________________` | Halaman Notion berisi template SOW | ☐ |
| `NOTION_PAGE_ROE_TEMPLATE` | `________________________________` | Halaman Notion berisi template ROE | ☐ |
| `NOTION_PAGE_PROPOSAL_TEMPLATE` | `________________________________` | Halaman Notion berisi template Proposal | ☐ |

**Cara membuat halaman template:**
1. Buka Notion → buat halaman baru di workspace (bukan di database)
2. Beri judul: `Template SOW`, `Template ROE`, atau `Template Proposal`
3. Salin/ketik isi template DOCX ke halaman tersebut (gunakan heading, bullet list, dll)
4. Invite Notion Integration ke halaman tersebut (Share → pilih integration)
5. Ambil Page ID dari URL: `notion.so/.../{PAGE_ID}` (32 karakter setelah nama halaman)

### 6.3 Environment

| Variable Name | Nilai | Status |
|--------------|-------|--------|
| `ENVIRONMENT` | `production` | ☐ |

### 6.4 Workflow IDs (diisi SETELAH semua workflow di-import)

Dapatkan Workflow ID setelah import: buka workflow → lihat URL → `/workflow/{ID}`

| Variable Name | Nilai | Workflow File | Status |
|--------------|-------|--------------|--------|
| `VAPT_ERROR_HANDLER_ID` | `___________` | VAPT_Error_Handler.json | ☐ |
| `WF_ID_SESSION_MANAGER` | `___________` | **VAPT_Session_Manager.json** *(import pertama!)* | ☐ |
| `WF_ID_SHARED_SUMMARY` | `___________` | VAPT_Shared_Summary_Generator.json | ☐ |
| `WF_ID_A1_GENERATE_DOCUMENTS` | `___________` | VAPT_A1_Generate_Documents.json | ☐ |
| `WF_ID_A2_GENERATE_PROPOSAL` | `___________` | VAPT_A2_Generate_Proposal.json | ☐ |
| `WF_ID_A3_NEW_PROJECT` | `___________` | VAPT_A3_New_Project.json | ☐ |
| `WF_ID_A4_UPDATE_PHASE` | `___________` | VAPT_A4_Update_Phase.json | ☐ |
| `WF_ID_A5_PM_SUMMARY` | `___________` | VAPT_A5_PM_Summary.json | ☐ |
| `WF_ID_C3_DAILY_ACTIVITY` | `___________` | VAPT_C3_Daily_Activity.json | ☐ |
| `WF_ID_C4_VULN_UPDATE` | `___________` | VAPT_C4_Vulnerability_Update.json | ☐ |

---

## BAGIAN 7 — URUTAN SETUP (Step-by-Step)

Ikuti urutan ini agar tidak ada langkah yang terlewat:

```
FASE 1 — Persiapkan semua akun & token eksternal
  ☐ 1. Buat 3 Telegram Bot via @BotFather → catat 3 token
  ☐ 2. Dapatkan Telegram User ID milik PM → via @userinfobot
  ☐ 3. Buat Notion Integration → catat API key
  ☐ 4. Buat 8 Notion Database → catat semua ID (termasuk DB-8 Session Store)
  ☐ 5. Invite Integration ke semua 8 database
  ☐ 6. Buat Anthropic API key → catat
  (Redis tidak diperlukan di versi ini)

FASE 2 — Input data awal ke Notion
  ☐ 7. Input data PM ke DB-2 (Pentester List) dengan telegram_user_id PM
  ☐ 8. Input data semua pentester ke DB-2 dengan telegram_user_id masing-masing
  ☐ 9. DB-8 (Session Store) dibiarkan kosong — terisi otomatis saat digunakan

FASE 3 — Setup n8n
  ☐ 10. Buat 5 Credentials di n8n (Bagian 5.1) — tanpa Redis
  ☐ 11. Buat Variables Telegram IDs (Bagian 6.1)
  ☐ 12. Buat Variables Notion DB IDs (Bagian 6.2) — termasuk NOTION_DB8_SESSION_STORE
  ☐ 13. Set ENVIRONMENT = production (Bagian 6.3)

FASE 4 — Import & konfigurasi Workflows
  ☐ 14. Import VAPT_Error_Handler.json → catat Workflow ID
  ☐ 15. Import VAPT_Session_Manager.json → catat Workflow ID (PENTING: import sebelum yang lain!)
  ☐ 16. Import VAPT_Shared_Summary_Generator.json → catat Workflow ID
  ☐ 17. Import VAPT_A1_Generate_Documents.json → catat Workflow ID
  ☐ 18. Import VAPT_A2_Generate_Proposal.json → catat Workflow ID
  ☐ 19. Import VAPT_A3_New_Project.json → catat Workflow ID
  ☐ 20. Import VAPT_A4_Update_Phase.json → catat Workflow ID
  ☐ 21. Import VAPT_A5_PM_Summary.json → catat Workflow ID
  ☐ 22. Import VAPT_C3_Daily_Activity.json → catat Workflow ID
  ☐ 23. Import VAPT_C4_Vulnerability_Update.json → catat Workflow ID
  ☐ 24. Import VAPT_BOT_A_Dispatcher.json
  ☐ 25. Import VAPT_BOT_B_Dispatcher.json
  ☐ 26. Import VAPT_BOT_C_Dispatcher.json
  ☐ 27. Import VAPT_A6_PM_Reminder.json
  ☐ 28. Import VAPT_B2_Sales_AutoUpdate.json
  ☐ 29. Import VAPT_C2_Pentester_AutoUpdate.json
  ☐ 30. Import VAPT_C5_Weekly_Plan.json
  ☐ 31. Import VAPT_C6_Weekly_Recap.json
  ☐ 32. Isi semua Workflow IDs di n8n Variables (Bagian 6.4) — termasuk WF_ID_SESSION_MANAGER
  ☐ 33. Update errorWorkflow ID di semua workflow ke ID Error Handler

FASE 5 — Aktivasi & Test
  ☐ 34. Aktifkan VAPT_Error_Handler
  ☐ 35. Aktifkan VAPT_Session_Manager (wajib aktif sebelum bot digunakan!)
  ☐ 36. Aktifkan VAPT_Shared_Summary_Generator
  ☐ 37. Aktifkan semua Sub-Workflows (A1–A5, C3, C4)
  ☐ 38. Aktifkan Bot Dispatchers (A, B, C)
  ☐ 39. Aktifkan Scheduled Workflows (A6, B2, C2, C5, C6)
  ☐ 40. Test Bot A: kirim /start → /summary
  ☐ 41. Test Bot C: kirim /update_activity → cek DB-8 di Notion (session harus tertulis)
  ☐ 42. Test manual trigger A6 → cek reminder dikirim
```

---

## BAGIAN 8 — REFERENSI CEPAT

### Semua Credential yang Diperlukan (Ringkasan)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TELEGRAM TOKENS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Bot A Token    : ___________:___________
Bot B Token    : ___________:___________
Bot C Token    : ___________:___________

TELEGRAM IDs
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PM User ID     : ___________
PM Chat ID     : ___________
Sales Group ID : -___________
Pentester ID   : -___________

NOTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
API Key        : secret_____________________
DB-1 (Projects): ________________________________
DB-2 (Pentsrs) : ________________________________
DB-3 (Tasks)   : ________________________________
DB-4 (Logs)    : ________________________________
DB-5 (Vulns)   : ________________________________
DB-6 (Weekly)  : ________________________________
DB-7 (Errors)  : ________________________________
DB-8 (Sessions): ________________________________

ANTHROPIC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
API Key        : sk-ant-___________________________

REDIS: TIDAK DIPERLUKAN (diganti Notion DB-8)

WORKFLOW IDs (diisi setelah import)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Error Handler  : ___________
Session Manager: ___________
Shared Summary : ___________
A1 Documents   : ___________
A2 Proposal    : ___________
A3 New Project : ___________
A4 Update Phase: ___________
A5 PM Summary  : ___________
C3 Activity    : ___________
C4 Vuln Update : ___________
```

---

> **KEAMANAN:**
> - Jangan simpan file ini di repository publik (git, GitHub, dsb.)
> - Simpan token dan API key di password manager (Bitwarden, 1Password, dsb.)
> - Rotasi API key secara berkala (minimal 6 bulan sekali)
> - Jika ada credential yang bocor, segera regenerate dari platform masing-masing

---

*VAPT Workflow System v1.0 | Dibuat dengan Claude Code*
