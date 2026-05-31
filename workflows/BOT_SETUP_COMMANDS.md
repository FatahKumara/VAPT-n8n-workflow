# VAPT Telegram Bot — Setup Commands & Greeting Design

Dokumen ini berisi:
1. **Command list** untuk didaftarkan via @BotFather
2. **Pesan greeting** (`/start`) untuk setiap bot
3. **Panduan penggunaan** per bot

---

## BOT A — PM Personal Assistant

> **Sifat:** Private bot — hanya bisa digunakan oleh 1 user (Project Manager)
> **Validasi:** userId dicek terhadap `PM_TELEGRAM_USER_ID` di n8n Variables

---

### A.1 Command List (daftarkan via @BotFather → /setcommands)

Pilih Bot A saat BotFather bertanya "choose a bot", lalu kirim teks berikut **persis** seperti ini:

```
start - Mulai menggunakan bot
help - Tampilkan daftar perintah
generate_sow - Generate Statement of Work (SOW)
generate_roe - Generate Rules of Engagement (ROE)
generate_proposal - Generate Proposal Proyek VAPT
new_project - Input data proyek baru (form 12 langkah)
update_phase - Update fase proyek yang sedang berjalan
summary - Dashboard ringkasan semua proyek aktif
```

---

### A.2 Greeting Message — /start

> Copy teks HTML berikut sebagai teks respons node `/start` di Bot A Dispatcher.
> Format: HTML (parse_mode: HTML)

```html
👋 Halo, <b>Project Manager</b>!

🤖 <b>VAPT PM Assistant</b> siap membantu mengelola proyek pengujian keamanan Anda.

━━━━━━━━━━━━━━━━━━━━━━
📋 <b>MENU UTAMA</b>
━━━━━━━━━━━━━━━━━━━━━━

<b>📝 Dokumen</b>
/generate_sow — Generate Statement of Work
/generate_roe — Generate Rules of Engagement
/generate_proposal — Generate Proposal Proyek

<b>📁 Manajemen Proyek</b>
/new_project — Input proyek baru (form interaktif)
/update_phase — Update fase proyek yang berjalan

<b>📊 Monitoring</b>
/summary — Dashboard ringkasan semua proyek aktif

━━━━━━━━━━━━━━━━━━━━━━

<i>💡 Ketik /help kapan saja untuk melihat panduan ini kembali.</i>
```

---

### A.3 Help Message — /help

```html
👋 Halo <b>{firstName}</b>!

🤖 <b>VAPT PM Assistant Bot</b>

<b>Perintah yang tersedia:</b>

📝 /generate_sow — Generate Statement of Work
📋 /generate_roe — Generate Rules of Engagement
💼 /generate_proposal — Generate Proposal Proyek
🆕 /new_project — Input Proyek Baru
🔄 /update_phase — Update Fase Proyek
📊 /summary — Dashboard Ringkasan Proyek

<i>Ketik salah satu perintah untuk memulai.</i>
```

---

### A.4 Panduan Penggunaan Bot A

#### `/generate_sow` dan `/generate_roe`
```
1. Ketik /generate_sow atau /generate_roe
2. Bot akan menampilkan daftar proyek aktif (inline keyboard)
3. Pilih proyek yang ingin dibuatkan dokumen
4. Bot memproses dan mengirim dokumen hasil generate via Claude AI
   (waktu proses: 10–30 detik)
5. Dokumen dikirim dalam format teks panjang di chat
```

#### `/generate_proposal`
```
1. Ketik /generate_proposal
2. Bot akan bertanya satu per satu:
   - Nama klien/perusahaan
   - Daftar objek pengujian
   - Metodologi (pilih: Black Box / Grey Box / White Box)
   - Estimasi scope (jumlah IP/URL/endpoint)
3. Setelah semua diisi → bot generate proposal via Claude AI
4. Proposal dikirim ke chat
```

#### `/new_project` — Form 12 Langkah
```
Langkah 1  → ID Proyek         (format: VAPT-YYYY-NNN)
Langkah 2  → Nama Proyek
Langkah 3  → Nama Klien
Langkah 4  → Objek Pengujian   (pisahkan dengan koma)
Langkah 5  → Metodologi        (pilih via inline keyboard)
Langkah 6  → Tanggal Pre-Kickoff
Langkah 7  → Tanggal Kickoff
Langkah 8  → Tanggal Initial Test
Langkah 9  → Tanggal Initial Report
Langkah 10 → Tanggal Presentasi
Langkah 11 → Tanggal Patching
Langkah 12 → Tanggal Retest
             → Tanggal Retest Report

✅ Setelah langkah 12 → data tersimpan otomatis di Notion DB-1
```

#### `/update_phase`
```
1. Ketik /update_phase
2. Bot tampilkan daftar proyek aktif (inline keyboard)
3. Pilih proyek → pilih fase baru dari 8 opsi:
   Pre-Kickoff → Kickoff → Initial Test → Initial Report
   → Presentation → Patching → Retest → Retest Report
4. Pilih status proyek saat ini (inline keyboard):
   🟢 On Progress  |  🔴 Blocking/Delay
   ⚪ Not Started   |  ✅ Done/Finish
5. Masukkan catatan PM (opsional, ketik "-" untuk skip)
6. Notion DB-1 diupdate: current_phase + project_status + pm_notes
```

#### `/summary`
```
1. Ketik /summary
2. Bot mengambil data dari semua database Notion
3. Claude AI menyusun ringkasan dalam format laporan
4. Laporan mencakup:
   - Jumlah proyek aktif per fase
   - Proyek yang mendekati deadline
   - Temuan vulnerability terbaru
   - Aktivitas pentester hari ini
```

---

## BOT B — Sales & Admin Group

> **Sifat:** Group bot — hanya aktif di `SALES_GROUP_CHAT_ID`
> **Validasi:** Chat ID dicek, hanya grup yang terdaftar yang dilayani

---

### B.1 Command List (daftarkan via @BotFather → /setcommands)

Pilih Bot B, lalu kirim:

```
start - Informasi bot
help - Tampilkan perintah yang tersedia
summary - Ringkasan status semua proyek aktif
```

---

### B.2 Greeting Message — /start

```html
👋 Halo, <b>Tim Sales & Admin</b>!

📊 <b>VAPT Sales Bot</b> aktif di grup ini.

━━━━━━━━━━━━━━━━━━━━━━
🔧 <b>PERINTAH TERSEDIA</b>
━━━━━━━━━━━━━━━━━━━━━━

📊 /summary — Ringkasan status semua proyek VAPT saat ini

━━━━━━━━━━━━━━━━━━━━━━

🕐 <b>Update Otomatis:</b>
• Setiap hari <b>08:00 WIB</b> — Morning briefing proyek
• Setiap hari <b>17:00 WIB</b> — Rekap end-of-day

<i>Data diambil langsung dari Notion secara real-time.</i>
```

---

### B.3 Panduan Penggunaan Bot B

#### `/summary` (On-demand)
```
1. Ketik /summary di grup
2. Bot mengambil data dari Notion (semua DB)
3. Claude AI menyusun ringkasan untuk tim sales
4. Laporan mencakup:
   - Status proyek per klien
   - Fase saat ini dan timeline
   - Progress pentesting
   - Update terbaru dari tim lapangan
```

#### Auto-Update Harian (tanpa perintah)
```
• 08:00 WIB — Bot otomatis mengirim briefing pagi:
  - Proyek yang aktif hari ini
  - Jadwal yang perlu diperhatikan
  - Milestone yang akan datang

• 17:00 WIB — Bot otomatis mengirim rekap sore:
  - Progress hari ini
  - Aktivitas yang selesai
  - Yang perlu ditindaklanjuti besok
```

---

## BOT C — Pentester Group

> **Sifat:** Group bot — hanya aktif di `PENTESTER_GROUP_CHAT_ID`
> **Validasi:** Chat ID grup + User ID pentester harus terdaftar di Notion DB-2

---

### C.1 Command List (daftarkan via @BotFather → /setcommands)

Pilih Bot C, lalu kirim:

```
start - Informasi bot dan cara penggunaan
help - Tampilkan perintah yang tersedia
summary - Ringkasan status proyek yang sedang berjalan
update_activity - Log aktivitas harian pengujian
add_vuln - Tambahkan temuan vulnerability baru
```

---

### C.2 Greeting Message — /start

```html
👋 Halo, <b>Tim Pentester</b>!

🔐 <b>VAPT Pentester Bot</b> siap membantu mendokumentasikan hasil pengujian.

━━━━━━━━━━━━━━━━━━━━━━
🔧 <b>PERINTAH TERSEDIA</b>
━━━━━━━━━━━━━━━━━━━━━━

📊 /summary
Lihat status semua proyek aktif + ringkasan temuan

📝 /update_activity
Log aktivitas pengujian harian kamu
(proyek, deskripsi, temuan, jam kerja)

🐛 /add_vuln
Tambah temuan vulnerability baru ke sistem
(nama, target, severity, CVSS, deskripsi, rekomendasi)

━━━━━━━━━━━━━━━━━━━━━━

⚠️ <b>Syarat penggunaan:</b>
Kamu harus sudah terdaftar di sistem (Notion DB-2) dengan Telegram User ID yang benar. Hubungi PM jika belum terdaftar.

🕐 <b>Update Otomatis:</b>
• <b>08:00 WIB</b> — Briefing pagi + task harian
• <b>17:00 WIB</b> — Rekap aktivitas hari ini
```

---

### C.3 Panduan Penggunaan Bot C

#### `/update_activity` — Log Aktivitas Harian
```
Form interaktif 4 langkah:

Langkah 1 → Pilih proyek (inline keyboard dari Notion DB-1)
Langkah 2 → Deskripsikan aktivitas hari ini
             Contoh: "Melakukan black-box testing pada endpoint /api/login
                      dan /api/register. Menemukan SQL injection potensial."
Langkah 3 → Ringkasan temuan (atau ketik "-" jika tidak ada)
             Contoh: "SQL injection pada parameter username"
Langkah 4 → Jumlah jam kerja hari ini
             Contoh: "6" (angka saja, dalam jam)

✅ Data tersimpan otomatis di Notion DB-4 (Daily Activity Log)
```

#### `/add_vuln` — Tambah Vulnerability Baru
```
Form interaktif 7 langkah:

Langkah 1 → Pilih proyek (inline keyboard)
Langkah 2 → Nama vulnerability
             Contoh: "SQL Injection pada endpoint /api/users"
Langkah 3 → Target/URL yang terdampak
             Contoh: "https://target.com/api/users?id=1"
Langkah 4 → Severity (pilih via inline keyboard)
             Critical / High / Medium / Low / Informational
Langkah 5 → CVSS Score
             Contoh: "9.8"
Langkah 6 → Deskripsi teknis
             Contoh: "Parameter 'id' pada endpoint /api/users rentan
                      terhadap SQL injection karena tidak ada input sanitization..."
Langkah 7 → Rekomendasi perbaikan
             Contoh: "Gunakan prepared statement atau parameterized query.
                      Implementasikan input validation dan sanitization..."

✅ Temuan tersimpan di Notion DB-5 (Vulnerability List)
✅ PM mendapat notifikasi temuan baru (via auto-update)
```

#### `/summary` (On-demand)
```
1. Ketik /summary di grup
2. Bot menampilkan ringkasan untuk tim pentester:
   - Proyek yang sedang berjalan
   - Temuan vulnerability per severity
   - Task yang belum selesai
   - Progress per pentester
```

#### Auto-Update Harian
```
• 08:00 WIB — Briefing pagi:
  - Proyek aktif hari ini
  - Task yang perlu diselesaikan
  - Reminder deadline yang mendekat

• 17:00 WIB — Rekap harian:
  - Aktivitas yang sudah dilog hari ini
  - Siapa yang belum log aktivitas
  - Vulnerability baru yang ditemukan
```

---

## PANDUAN SETUP @BOTFATHER (Ringkasan)

### Urutan Lengkap untuk Setiap Bot:

```
1. /mybots → pilih bot → Edit Bot
   ├── Edit Name         → nama display bot
   ├── Edit Description  → deskripsi panjang (tampil di profil bot)
   ├── Edit About text   → teks singkat (tampil sebelum /start)
   └── Edit Botpic       → upload foto profil bot (opsional)

2. /setcommands → pilih bot → paste command list
   (gunakan format dari bagian A.1 / B.1 / C.1 di atas)

3. /setprivacy → pilih bot
   - Bot A: DISABLE (private bot, tidak perlu baca pesan grup)
   - Bot B: DISABLE (group bot, perlu baca semua pesan di grup)
   - Bot C: DISABLE (group bot, perlu baca semua pesan di grup)

4. /setjoingroups → pilih bot
   - Bot A: DISABLED (hanya private chat dengan PM)
   - Bot B: ENABLED (harus bisa join grup)
   - Bot C: ENABLED (harus bisa join grup)
```

### Deskripsi Bot (untuk /setdescription):

**Bot A:**
```
VAPT PM Assistant adalah asisten pribadi Project Manager untuk 
sistem VAPT (Vulnerability Assessment & Penetration Testing).

Fitur: generate dokumen SOW/ROE/Proposal, input proyek baru, 
update fase proyek, dan dashboard ringkasan proyek.

Bot ini hanya dapat digunakan oleh Project Manager yang terdaftar.
```

**Bot B:**
```
VAPT Sales Bot memberikan update otomatis status proyek VAPT 
kepada tim Sales & Admin setiap pagi dan sore hari.

Update otomatis: 08:00 WIB dan 17:00 WIB.
Gunakan /summary untuk ringkasan proyek kapan saja.
```

**Bot C:**
```
VAPT Pentester Bot membantu tim pentester mendokumentasikan 
aktivitas harian dan temuan vulnerability langsung dari Telegram.

Gunakan /update_activity untuk log harian dan /add_vuln untuk 
mencatat temuan. Hanya untuk pentester yang terdaftar di sistem.
```

---

## REFERENSI CEPAT — FORMAT PERINTAH

| Bot | Perintah | Tipe Interaksi | Output |
|-----|---------|---------------|--------|
| A | `/generate_sow` | Pilih proyek (keyboard) → generate | Dokumen SOW teks |
| A | `/generate_roe` | Pilih proyek (keyboard) → generate | Dokumen ROE teks |
| A | `/generate_proposal` | Form 4 langkah (conversational) | Dokumen Proposal teks |
| A | `/new_project` | Form 12 langkah (conversational) | Konfirmasi + simpan ke Notion |
| A | `/update_phase` | Pilih proyek → pilih fase → pilih status → notes | Update Notion DB-1 |
| A | `/summary` | Langsung | Laporan ringkasan AI |
| B | `/summary` | Langsung | Laporan ringkasan AI (mode sales) |
| B | *(auto)* | Terjadwal 08:00 & 17:00 | Briefing / Rekap harian |
| C | `/summary` | Langsung | Laporan ringkasan AI (mode pentester) |
| C | `/update_activity` | Form 4 langkah (conversational) | Simpan ke Notion DB-4 |
| C | `/add_vuln` | Form 7 langkah (conversational) | Simpan ke Notion DB-5 |
| C | *(auto)* | Terjadwal 08:00 & 17:00 | Briefing / Rekap harian |

---

*VAPT Workflow System | Bot Setup Guide*
