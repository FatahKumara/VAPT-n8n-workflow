# VAPT Workflow System — Setup Guide
> n8n Version: 1.x+ | Platform: Self-hosted | Model: claude-sonnet-4-20250514

---

## 📁 Daftar File Workflow (17 file)

| File | Tipe | Deskripsi |
|------|------|-----------|
| `VAPT_Error_Handler.json` | Global | Error handler untuk semua workflow |
| `VAPT_Shared_Summary_Generator.json` | Sub-WF | Shared summary generator (dipanggil A5, B1, B2, C1, C2) |
| `VAPT_BOT_A_Dispatcher.json` | Dispatcher | Bot A master handler |
| `VAPT_A1_Generate_Documents.json` | Sub-WF | Generate SOW & ROE |
| `VAPT_A2_Generate_Proposal.json` | Sub-WF | Generate Proposal (conversational) |
| `VAPT_A3_New_Project.json` | Sub-WF | Input proyek baru (12-step form) |
| `VAPT_A4_Update_Phase.json` | Sub-WF | Update fase proyek |
| `VAPT_A5_PM_Summary.json` | Sub-WF | PM dashboard summary |
| `VAPT_A6_PM_Reminder.json` | Cron | Daily reminder 07:30 & 16:30 WIB |
| `VAPT_BOT_B_Dispatcher.json` | Dispatcher | Bot B Sales & Admin handler |
| `VAPT_B2_Sales_AutoUpdate.json` | Cron | Auto-update 08:00 & 17:00 WIB |
| `VAPT_BOT_C_Dispatcher.json` | Dispatcher | Bot C Pentester Group handler |
| `VAPT_C3_Daily_Activity.json` | Sub-WF | Log aktivitas harian pentester |
| `VAPT_C4_Vulnerability_Update.json` | Sub-WF | Input vulnerability baru (7-step form) |
| `VAPT_C2_Pentester_AutoUpdate.json` | Cron | Auto-update 08:00 & 17:00 WIB |
| `VAPT_C5_Weekly_Plan.json` | Cron | Weekly plan setiap Senin 09:00 WIB |
| `VAPT_C6_Weekly_Recap.json` | Cron | Weekly recap setiap Jumat 18:00 WIB |

---

## 🔑 Step 1: Setup Credentials di n8n

Buat credentials berikut di n8n Settings → Credentials:

### 1.1 Telegram Bot A — PM Assistant
- **Type:** Telegram
- **Name:** `Telegram Bot A - PM Assistant`
- **Bot Token:** Token dari @BotFather untuk Bot A
- **Note:** Bot ini HANYA merespons PM (user_id divalidasi via n8n Variable)

### 1.2 Telegram Bot B — Sales Admin
- **Type:** Telegram
- **Name:** `Telegram Bot B - Sales Admin`
- **Bot Token:** Token dari @BotFather untuk Bot B

### 1.3 Telegram Bot C — Pentester Group
- **Type:** Telegram
- **Name:** `Telegram Bot C - Pentester Group`
- **Bot Token:** Token dari @BotFather untuk Bot C

### 1.4 Notion API
- **Type:** Notion API
- **Name:** `Notion API`
- **API Key:** Notion Internal Integration Token dari notion.so/my-integrations
- **Pastikan** integration di-invite ke semua **7 database** (DB-1 s/d DB-7)

### 1.5 Anthropic API
- **Type:** Anthropic
- **Name:** `Anthropic API`
- **API Key:** Key dari console.anthropic.com

### 1.6 Redis
- **Type:** Redis
- **Name:** `Redis VAPT`
- **Host:** localhost (atau IP Redis server)
- **Port:** 6379
- **Password:** (jika ada)

---

## ⚙️ Step 2: Setup n8n Variables

Pergi ke n8n Settings → Variables dan buat semua variable berikut:

```
# Telegram User & Chat IDs
PM_TELEGRAM_USER_ID        = [user_id PM — ambil dari @userinfobot]
PM_TELEGRAM_CHAT_ID        = [chat_id PM dengan Bot A — sama dengan user_id untuk private chat]
SALES_GROUP_CHAT_ID        = [chat_id group Sales & Admin — tambahkan bot, lalu /chatid]
PENTESTER_GROUP_CHAT_ID    = [chat_id group Pentester — tambahkan bot, lalu /chatid]

# Notion Database IDs (ambil dari URL database di Notion)
NOTION_DB1_PROJECT_LIST    = [ID DB-1 Project List]
NOTION_DB2_PENTESTER_LIST  = [ID DB-2 Pentester List]
NOTION_DB3_TASK_LIST       = [ID DB-3 Pentester Task List]
NOTION_DB4_DAILY_LOG       = [ID DB-4 Daily Activity Log]
NOTION_DB5_VULN_LIST       = [ID DB-5 Vulnerability List]
NOTION_DB6_WEEKLY_SUMMARY  = [ID DB-6 Weekly Summary Report]
NOTION_DB7_ERROR_LOG       = [ID DB-7 Error Log]

# Environment
ENVIRONMENT                = production
```

**Cara ambil Notion Database ID:**
Buka database di Notion → lihat URL:
`https://notion.so/workspace/[DATABASE_ID]?v=...`

---

## 📥 Step 3: Import Workflows

**Urutan import yang benar (penting!):**

1. Import `VAPT_Error_Handler.json` → **catat Workflow ID-nya**
2. Import `VAPT_Shared_Summary_Generator.json` → **catat Workflow ID-nya**
3. Import semua Sub-Workflows (A1-A5, C3, C4) → **catat semua Workflow ID**
4. Import Dispatcher workflows (BOT_A, BOT_B, BOT_C)
5. Import Scheduled workflows (A6, B2, C2, C5, C6)

**Cara import:** n8n UI → Workflows → Import from File

---

## 🆔 Step 4: Update Workflow IDs di n8n Variables

Setelah semua workflow di-import, tambahkan variable berikut dengan Workflow ID yang didapat:

```
VAPT_ERROR_HANDLER_ID      = [ID dari VAPT_Error_Handler]
WF_ID_SHARED_SUMMARY       = [ID dari VAPT_Shared_Summary_Generator]
WF_ID_A1_GENERATE_DOCUMENTS = [ID dari VAPT_A1_Generate_Documents]
WF_ID_A2_GENERATE_PROPOSAL  = [ID dari VAPT_A2_Generate_Proposal]
WF_ID_A3_NEW_PROJECT        = [ID dari VAPT_A3_New_Project]
WF_ID_A4_UPDATE_PHASE       = [ID dari VAPT_A4_Update_Phase]
WF_ID_A5_PM_SUMMARY         = [ID dari VAPT_A5_PM_Summary]
WF_ID_C3_DAILY_ACTIVITY     = [ID dari VAPT_C3_Daily_Activity]
WF_ID_C4_VULN_UPDATE        = [ID dari VAPT_C4_Vulnerability_Update]
```

---

## 🗄️ Step 5: Setup Notion Databases

> **Seluruh penyimpanan data menggunakan Notion** — tidak ada database eksternal lain.
> Redis hanya digunakan untuk temporary session state (conversational flow), bukan penyimpanan data permanen.

### Langkah Setup:
1. Buat **7 database** di Notion dengan schema sesuai spesifikasi di bawah
2. Buat **Notion Internal Integration** di notion.so/my-integrations
3. Invite integration ke setiap database (Settings → Connections → Add connections)

### DB-1: Project List (minimal properties)
| Property | Type | Values |
|----------|------|--------|
| project_id | Title | - |
| project_name | Text | - |
| client_name | Text | - |
| object_list | Multi-select | Web App, Mobile App, API, Network, Cloud, dll |
| methodology | Select | Black Box, Grey Box, White Box |
| current_phase | Select | Pre-Kickoff, Kickoff, Initial Test, Initial Report, Presentation, Patching, Retest, Retest Report |
| date_pre_kickoff | Date | - |
| date_kickoff | Date | - |
| date_initial_test | Date | - |
| date_initial_report | Date | - |
| date_presentation | Date | - |
| date_patching | Date | - |
| date_retest | Date | - |
| date_retest_report | Date | - |
| pm_notes | Text | - |
| last_updated | Date | - |
| assigned_pentesters | Relation → DB-2 | - |

### DB-2: Pentester List
| Property | Type |
|----------|------|
| pentester_id | Title |
| name | Text |
| telegram_user_id | Text |
| assigned_projects | Relation → DB-1 |
| current_status | Select: Available, On Project, On Leave |

### DB-3: Task List
| Property | Type |
|----------|------|
| task_id | Title |
| project_id | Relation → DB-1 |
| pentester_id | Relation → DB-2 |
| task_name | Text |
| task_phase | Select |
| task_status | Select: Not Started, In Progress, Blocked, Done |
| blocking_reason | Text |
| due_date | Date |
| completion_date | Date |

### DB-4: Daily Activity Log
| Property | Type |
|----------|------|
| log_id | Title |
| project_id | Relation → DB-1 |
| pentester_id | Relation → DB-2 |
| log_date | Date |
| activity_description | Text |
| findings_summary | Text |
| hours_spent | Number |

### DB-5: Vulnerability List
| Property | Type |
|----------|------|
| vuln_id | Title |
| project_id | Relation → DB-1 |
| reported_by | Relation → DB-2 |
| vulnerability_name | Text |
| cvss_score | Number |
| severity | Select: Critical, High, Medium, Low, Informational |
| affected_target | Text |
| description | Text |
| recommendation | Text |
| status | Select: Open, In Remediation, Patched, Accepted Risk, Retest Passed, Retest Failed |
| discovery_date | Date |
| retest_date | Date |

### DB-6: Weekly Summary Report
| Property | Type |
|----------|------|
| summary_id | Title |
| week_number | Number |
| summary_type | Select: Weekly Plan, Weekly Recap |
| summary_content | Text |
| generated_date | Date |
| projects_covered | Relation → DB-1 |

### DB-7: Error Log ⚠️
Database ini digunakan oleh `VAPT_Error_Handler` untuk mencatat semua error workflow.

| Property | Type | Values |
|----------|------|--------|
| log_id | Title | Format: ERR-{timestamp} |
| workflow_name | Text | Nama workflow yang error |
| workflow_id | Text | ID workflow di n8n |
| execution_id | Text | ID execution yang gagal |
| last_node | Text | Node terakhir yang dieksekusi |
| error_message | Text | Pesan error lengkap |
| error_type | Select | Error, TypeError, NetworkError, dll |
| environment | Select | production, staging, development |
| error_timestamp | Date | Tanggal error terjadi |

> Setelah membuat DB-7, catat ID-nya dan set ke n8n Variable `NOTION_DB7_ERROR_LOG`.

---

## 📦 Step 6: Setup Redis (State Machine)

Redis digunakan untuk conversational state management pada workflows:
- A2 (Generate Proposal) — session key: `vapt:session:{chatId}`
- A3 (New Project) — session key: `vapt:session:{chatId}`
- A4 (Update Phase) — session key: `vapt:session:{chatId}`
- C3 (Daily Activity) — session key: `vapt:session:{userId}`
- C4 (Vulnerability Update) — session key: `vapt:session:{userId}`

**TTL:** 3600 detik (1 jam) per session

> **Catatan:** Redis hanya digunakan untuk menyimpan **session state sementara** (TTL 1 jam) selama percakapan multi-langkah berlangsung. Data ini bukan data bisnis — semua data permanen tersimpan di Notion. Jika session expired sebelum user selesai, user cukup mengetik ulang command untuk memulai dari awal.

### Setup dengan Docker:
```bash
docker run -d --name redis-vapt -p 6379:6379 redis:7-alpine
```

---

## 🤖 Step 7: Setup Telegram Bots

### Cara membuat 3 bot via @BotFather:
```
1. /newbot → Masukkan nama → Masukkan username
2. Catat token API
3. Ulangi untuk Bot B dan C
```

### Konfigurasi Bot:
- Bot A: Disable group permissions (private only)
- Bot B: Add ke Sales & Admin group sebagai administrator  
- Bot C: Add ke Pentester group sebagai administrator

### Cara mendapatkan Chat IDs:
1. Tambahkan bot ke group
2. Kirim pesan /chatid@namabot atau cek via:
   `https://api.telegram.org/bot{TOKEN}/getUpdates`

---

## ✅ Step 8: Aktivasi Workflows

Aktifkan dalam urutan ini:
1. `VAPT_Error_Handler` ← Aktifkan pertama
2. `VAPT_Shared_Summary_Generator`
3. Semua Sub-Workflows (A1-A5, C3, C4)
4. `VAPT_BOT_A_Dispatcher` (aktifkan setelah semua sub-WF A siap)
5. `VAPT_BOT_B_Dispatcher`
6. `VAPT_BOT_C_Dispatcher` (aktifkan setelah C3, C4 siap)
7. Semua Scheduled workflows (A6, B2, C2, C5, C6)

---

## 🧪 Testing Guide

### Test Bot A:
```
1. Kirim /start → Cek help message tampil
2. Kirim /generate_sow → Pilih proyek → Verifikasi dokumen digenerate
3. Kirim /new_project → Ikuti form 12 langkah → Cek record di Notion DB-1
4. Kirim /summary → Verifikasi AI summary dikirim
5. Kirim /update_phase → Pilih proyek → Pilih fase → Masukkan notes → Cek Notion diupdate
```

### Test Bot C:
```
1. Pastikan user ID sudah ada di Notion DB-2
2. /update_activity → Pilih proyek → Isi aktivitas → Cek DB-4
3. /add_vuln → Ikuti 7-step form → Cek DB-5
4. /summary → Verifikasi summary dikirim ke group
```

### Test Scheduled:
```
Manual trigger dari n8n UI:
- A6: "Execute" → Verifikasi pesan reminder dikirim ke PM
- B2: "Execute" → Verifikasi auto-update dikirim ke Sales group
- C5: "Execute" → Verifikasi weekly plan dikirim + tersimpan di DB-6
- C6: "Execute" → Verifikasi weekly recap dikirim + tersimpan di DB-6
```

---

## ❗ Troubleshooting

### Session tidak clear setelah submit
- Cek Redis koneksi di n8n credentials
- Manual clear: `redis-cli DEL vapt:session:*`

### Notion API error 429 (Rate Limited)
- Notion limit: 3 req/detik
- Workflow sudah memiliki retry logic (3x, wait 2000ms)
- Jika masih error: tambahkan Wait node sebelum Notion calls

### Telegram "chat not found"
- Pastikan bot sudah pernah di-start oleh user
- Untuk group: pastikan bot adalah anggota aktif group

### Claude API error
- Cek API key validity di console.anthropic.com
- Cek rate limits: 1000 RPM untuk claude-sonnet-4-20250514
- Workflow fallback: jika Claude gagal, kirim plain text message

### Sub-workflow tidak terpanggil
- Pastikan Workflow ID di n8n Variables sudah diupdate setelah import
- Cek workflow sudah dalam status "Active"

---

## 📐 Architecture Overview

```
TELEGRAM MESSAGES
       │
   ┌───┴───┐
   │ Bot A │ → Dispatcher A → A1/A2/A3/A4/A5 (Sub-WF) → Notion + Claude → Telegram
   │ Bot B │ → Dispatcher B → Shared Summary Generator → Telegram
   │ Bot C │ → Dispatcher C → C3/C4 (Sub-WF) + Shared Summary → Notion + Claude → Telegram
   └───────┘

SCHEDULED JOBS (WIB = UTC+7)
   A6: Daily 07:30 + 16:30 → Notion DB-1 → PM Reminder → Bot A
   B2: Daily 08:00 + 17:00 → Summary Generator → Bot B → Sales Group
   C2: Daily 08:00 + 17:00 → Summary Generator → Bot C → Pentester Group
   C5: Every Monday 09:00 → DB-1+DB-3 → Claude → DB-6 + Bot C
   C6: Every Friday 18:00 → DB-4+DB-5+DB-3 → Claude → DB-6 + Bot C

STATE MACHINE (Redis TTL=3600s)
   Key: vapt:session:{chatId|userId}
   Value: { command, step, data }
   Used by: A2, A3, A4, C3, C4

ERROR HANDLING
   All workflows → errorWorkflow: VAPT_Error_Handler
   VAPT_Error_Handler → Alert PM via Bot A + Log to Notion DB-7

DATA STORAGE (100% Notion)
   DB-1: Project List          DB-5: Vulnerability List
   DB-2: Pentester List        DB-6: Weekly Summary Report
   DB-3: Task List             DB-7: Error Log
   DB-4: Daily Activity Log
```

---

## 🔐 Security Checklist

- [ ] PM_TELEGRAM_USER_ID diset ke user_id PM yang benar
- [ ] SALES_GROUP_CHAT_ID diset ke chat_id group yang benar
- [ ] PENTESTER_GROUP_CHAT_ID diset ke chat_id group yang benar
- [ ] Semua pentesters sudah diregister di Notion DB-2 dengan telegram_user_id yang benar
- [ ] NOTION_DB7_ERROR_LOG diset ke ID DB-7 Error Log
- [ ] Notion integration di-invite ke semua 7 database (DB-1 s/d DB-7)
- [ ] Notion integration hanya memiliki akses ke database yang dibutuhkan
- [ ] Redis tidak exposed ke public internet
- [ ] n8n instance menggunakan HTTPS
- [ ] API keys disimpan di n8n Credentials, bukan hardcoded

---

*Generated by Claude Code | VAPT Workflow System v1.0*
*Kompatibel dengan n8n 1.x+*
