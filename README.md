# 🤖 Jarvis — Cellya Claire Floating Desktop AI Assistant

> Asisten AI desktop transparan, always-on-top, dengan overlay melayang + chat console, multi-provider LLM, 16 autonomous tools, music player YouTube, voice TTS, vision screenshot, dan otomatisasi Google Classroom.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![PyQt6](https://img.shields.io/badge/UI-PyQt6-green)
![Platform](https://img.shields.io/badge/Platform-Windows-lightgrey)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 📑 Daftar Isi

- [✨ Fitur Utama](#-fitur-utama)
- [🧠 Model & Provider AI](#-model--provider-ai)
- [🛠️ 16 Autonomous Tools](#️-16-autonomous-tools)
- [🎵 Play Music (Panduan Lengkap)](#-play-music-panduan-lengkap)
- [🖥️ Panduan UI / Cara Menggunakan](#️-panduan-ui--cara-menggunakan)
- [⚙️ Instalasi](#️-instalasi)
- [🔑 Konfigurasi `.env`](#-konfigurasi-env)
- [🔧 Konfigurasi `settings.json`](#-konfigurasi-settingsjson)
- [📁 Struktur Project](#-struktur-project)
- [🔒 Keamanan & Batasan Workspace](#-keamanan--batasan-workspace)
- [⌨️ Contoh Prompt / Perintah](#️-contoh-prompt--perintah)
- [❓ Troubleshooting](#-troubleshooting)
- [🗺️ Roadmap](#️-roadmap)

---

## ✨ Fitur Utama

### 1. 🫧 Floating Overlay Bubble
- Bubble kecil transparan yang selalu di atas (`always-on-top`), bisa di-drag ke mana saja.
- **Double-click** untuk buka/tutup chat console.
- Status visual: `idle` (biru), `thinking` (kuning), `working` (ungu/orange saat tool berjalan).
- Klik kanan: ganti personality, clear chat, exit.
- Posisi tersimpan otomatis di `config/settings.json` → `overlay_geometry`.

### 2. 💬 Chat Console (ChatWindow)
- Window frameless modern dengan tema `dark-glass`, bisa di-drag & resize dari semua sisi.
- Streaming jawaban per-token (tidak nunggu selesai).
- Status badge real-time: `Thinking...`, `Running tool: read_file...`, `Analyzing tool response...`.
- Markdown-ish rendering untuk kode, plus tombol **Snap** untuk screenshot → vision.
- Tombol header: **Model Selector, Voice ON/OFF, Stats, Close**.
- Sidebar lipat (tombol **Menu**) dengan 3 tab:
  - **Chats** — multi-session history, new/delete/load chat.
  - **Files** — file explorer mini: create, open, edit (built-in text editor), delete, refresh.
  - **Music** — full music player (lihat bawah).

### 3. 🧠 Multi-Provider AI + Auto-Switch
Pilih model dari dropdown, atau biarkan AI routing otomatis:

| Provider | Model di Dropdown | Kegunaan |
|---|---|---|
| `gravity` | `Claude 3.5 Sonnet (Agent)` → `ag/gemini-pro-agent` | Coding agent paling kuat, full tool-calling |
| `gravity` | `Claude 3.5 Flash / Haiku (Agent)` | Lebih cepat / murah |
| `web_proxy` | `Claude Web (Free)` / `Gemini Web (Free)` | Gratis via proxy browser, chat ringan non-tool |
| `groq` | `Llama 3.3 70B` | Cepat, gratis tier besar |
| `gemini` | `Gemini 2.5 Flash / Pro (Direct)` | Vision + tool-calling resmi Google |

> Jika kamu chat ringan via Web Proxy tapi tiba-tiba minta tool (`write file`, `run command`, kirim gambar), Jarvis otomatis switch ke Official API (Agentic Mode).

### 4. 📂 File Operations + Project Awareness
- `get_project_tree`, `read_file`, `write_file`, `delete_file`, `list_directory_contents`.
- AI wajib `read_file` dulu sebelum `write_file` (anti-halusinasi isi file).
- Auto-refresh daftar file setelah AI menulis dokumen.

### 5. 💻 Terminal Executor (dengan Izin)
- `execute_terminal_command` (PowerShell / CMD, timeout 30 dtk).
- **Setiap perintah memunculkan dialog Yes/No** di UI sebelum jalan.
- Mode `background: true` otomatis untuk GUI app (`chrome`, `notepad`, `spotify`, `code`, dll).

### 6. 🌐 Web & Browser Automation
- `open_resource` — buka URL / Spotify / Roblox / VS Code / folder lokal + opsi `autosave` (auto `git add + commit`).
- `browser_launch` — buka Chrome pakai profil lokal user.
- `read_webpage_summary` — download HTML → strip tags → ringkasan teks bersih.
- `get_active_browser_info` — deteksi window aktif + ambil URL-nya.
- `WebProxy` — chat gratis via Claude/Gemini web (`core/web_proxy.py`).
- Script bantuan: `run_chrome_debug.bat` untuk relaunch Chrome dengan `--remote-debugging-port=9222`.

### 7. 🎓 Google Classroom + Forms Scraper
- `classroom_scrape`:
  - `get_courses` — daftar kelas
  - `get_assignments` — daftar tugas + deadline
  - `get_assignment_detail` — detail tugas
  - `download_attachments` — download lampiran ke `~/Downloads/Jarvis_Classroom/`
- `forms_parse` — buka link Google Forms → ekstrak semua pertanyaan & pilihan.
- Implementasi di `core/automator/classroom.py`, `forms.py`, `browser.py` (Playwright).

### 8. 📄 Document Generator
- `document_generate` dengan format: `docx`, `pdf`, `csv`, `xlsx`, `pptx`.
- Output default ke `Cellya_Outputs/`.
- Mendukung tabel styled, multi-sheet + formula Excel (`=SUM(B2:B10)`), dan slide deck.
- Implementasi: `core/automator/docgen.py` (reportlab, python-docx, openpyxl, python-pptx).

### 9. 🎵 YouTube Music Player (Built-in)
Lihat [panduan lengkap di bawah](#-play-music-panduan-lengkap). Ringkasnya:
- Search YouTube langsung dari tab **Music**, double-click untuk putar.
- Queue, Next/Prev, Repeat OFF→ONE→ALL, Shuffle, Clear Queue, volume + seek bar.
- Backend VLC (utama) + fallback Pygame mixer.
- Streaming via `yt-dlp` tanpa download.

### 10. 🔊 Voice TTS (Text-to-Speech)
- Tombol **Voice OFF/ON** di header. Saat ON, setiap jawaban AI dibacakan (`core/tts.py`).
- Mengirim pesan baru otomatis menghentikan suara yang sedang jalan.

### 11. 📸 Vision / Screenshot
- Tombol **Snap**: sembunyikan window 250ms → screenshot layar utama → attach sebagai `base64 PNG` ke pesan berikutnya.
- Bisa juga kirim gambar dan AI (Gemini/Gravity) akan memahaminya.
- Contoh: *“jelaskan error di screenshot ini”*.

### 12. 🧾 Session History + Learning Memory
- Semua chat tersimpan sebagai file JSON di `history/` (`core/history_manager.py`).
- Bisa ganti-ganti session tanpa kehilangan konteks.
- `add_learning_point` — AI bisa menyimpan insight permanen ke `learnings/` (mis. preferensi user, solusi bug). Otomatis disuntik ke system prompt berikutnya.
- `UsageTracker` — panel **Stats** menampilkan RPD/TPD Groq, Gravity, Gemini (`config/usage_stats.json`).

### 13. 🎭 Personalities (Cellya Claire)
Folder `personalities/`:
- `serious.json` — sopan, formal, arsitek software ketat (temperature 0.2). Wajib analisis 6 langkah sebelum coding: market comparison → roadmap → component design → DB schema & concurrency → estimasi storage/QPS → implementasi penuh tanpa TODO.
- `fun.json` — ceria, manja, ekspresif, banyak emoji (temperature 0.8).
- Ganti via klik kanan overlay → personality. Judul chat ikut berubah otomatis.

---

## 🧠 Model & Provider AI

Logika di `core/ai_engine.py` → `run_chat_stream()`:

```
User prompt
  → web_proxy? cek _is_agentic() (keyword: write file, run command, classroom, youtube, dst / ada gambar)
      → ya: switch ke gravity/gemini agentic
      → tidak: stream via web_proxy.stream_claude_web / stream_gemini_web
  → groq / gravity / gemini: loop max 10x tool-calling → execute_tool() → stream jawaban
```

Token diestimasi lokal (`len/4`) dan dicatat via `UsageTracker.increment()`.

---

## 🛠️ 16 Autonomous Tools

| # | Tool | Deskripsi | Contoh prompt |
|---|---|---|---|
| 1 | `get_project_tree` | Struktur direktori workspace | *“tampilkan struktur project ini”* |
| 2 | `read_file` | Baca isi file | *“bacakan main.py”* |
| 3 | `write_file` | Buat/edit file | *“buatkan notes.txt berisi todo”* |
| 4 | `delete_file` | Hapus file/folder | *“hapus file lama itu”* |
| 5 | `list_directory_contents` | List isi folder + size + mtime | *“apa isi folder Cellya_Outputs?”* |
| 6 | `execute_terminal_command` | Jalankan PowerShell/CMD (minta izin) | *“install requests via pip”* |
| 7 | `open_resource` | Buka URL/app/VS Code + git autosave | *“bukakan github.com, autosave dulu”* |
| 8 | `search_youtube` | Cari video (judul, channel, durasi, URL) | *“carikan tutorial FastAPI”* |
| 9 | `control_media` | `play_pause/next/prev/volume_up/volume_down/volume_mute` | *“next lagu spotify”* |
| 10 | `get_active_browser_info` | Ambil judul + URL browser aktif | *“aku lagi buka apa di browser?”* |
| 11 | `read_webpage_summary` | Baca isi teks webpage | *“ringkas isi https://...”* |
| 12 | `add_learning_point` | Simpan memori permanen | *“ingat: aku pakai Postgres, bukan MySQL”* |
| 13 | `browser_launch` | Buka Chrome profil lokal | *“bukakan classroom.google.com”* |
| 14 | `classroom_scrape` | `get_courses/get_assignments/get_assignment_detail/download_attachments` | *“list tugasku di classroom”* |
| 15 | `forms_parse` | Ekstrak soal Google Forms | *“parse form ini: https://forms.gle/...”* |
| 16 | `document_generate` | Buat docx/pdf/csv/xlsx/pptx | *“buatkan laporan PDF tugas 1”* |

---

## 🎵 Play Music (Panduan Lengkap)

Tab **Music** ada di sidebar (klik **Menu** → tab **Music**). Engine: `core/audio_player.py` (`CellyaAudioEngine`) + UI `ui/chat_window.py` (tab Music).

### Cara Memutar Lagu
1. Buka chat → klik **Menu** → tab **Music**.
2. Ketik judul di kolom `Search YouTube...` → **Search** (atau Enter).
3. Tunggu hasil muncul di list → **double-click** lagu → status berubah `Loading: ...` → `Playing: ...`.
4. Lagu otomatis masuk **Queue**.

### Kontrol yang Tersedia
| Tombol / Slider | Fungsi |
|---|---|
| `Play / Pause` | Pause / resume lagu aktif |
| `Prev / Next` | Pindah lagu di queue (`play_prev()` / `play_next()`) |
| `Stop` | Berhenti total |
| `Repeat: OFF → ONE → ALL` | `OFF`=berhenti di akhir, `ONE`=ulang 1 lagu, `ALL`=ulang semua queue |
| `Shuffle: ON/OFF` | Acak urutan queue (lagu aktif tetap pertama) |
| `Clear Queue` | Hapus semua antrian |
| Progress slider (0–1000) | Seek / lompat posisi (`set_position(0.0–1.0)`) — update tiap 1 detik |
| Volume slider (0–100) | `set_volume()` ke VLC / Pygame |
| `Queue: x/y tracks` | Posisi lagu aktif / total antrian |

### Auto-Next & Error
- Saat lagu selesai (event VLC `MediaPlayerEndReached`), `on_track_ended` → `auto_next()` menghormati mode Repeat/Shuffle.
- Jika stream gagal (mis. YouTube 403), label menampilkan `Error: ...` dan tombol kembali ke `Play`.

### Butuh Apa Agar Musik Bunyi?
```bash
pip install yt-dlp python-vlc pygame
# + install VLC Desktop 64-bit (https://www.videolan.org/vlc/)
#   agar python-vlc menemukan libvlc.dll
```

> Tanpa VLC, otomatis fallback ke `pygame.mixer` (fitur seek terbatas). Tanpa keduanya, muncul error *“Tidak ada audio backend”*.

### Play Musik via Chat (AI Tool)
Kamu juga bisa minta via chat, AI memakai `search_youtube` + `control_media` / `open_resource`:
- *“carikan lofi hip hop 1 jam”*
- *“bukakan spotify:”*
- *“pause musikku”*

---

## 🖥️ Panduan UI / Cara Menggunakan

### Menjalankan
```bash
# cara 1 — langsung
python main.py

# cara 2 — dengan workspace custom
python main.py --workspace "D:\Kuliah\Semester5"

# cara 3 — double-click
run_jarvis.bat
```

### Alur Chat Dasar
1. Double-click bubble → chat muncul di samping bubble.
2. Pilih model di dropdown (disarankan `Claude 3.5 Sonnet (Agent)` untuk coding).
3. Ketik → **Enter** kirim, **Shift+Enter** baris baru.
4. Saat AI kerja, badge menunjukkan tool yang jalan. Dialog izin muncul untuk tiap terminal command → klik **Yes/No**.
5. Klik **Stats** untuk cek kuota harian.

### Vision
Klik **Snap** → thumbnail muncul di atas input → ketik pertanyaan → Send.

### Files Tab
- **+ Create File** → isi path relatif (mis. `Cellya_Outputs/catatan.txt`) → editor terbuka.
- Double-click file → buka dengan aplikasi default OS. **Edit** hanya untuk file teks (`.txt .py .json .md .csv .html`).
- **Refresh** setelah AI generate dokumen (atau otomatis setelah response selesai).

### System Tray
Klik kanan ikon tray → Show/Hide Overlay, Clear Conversation, Exit.

---

## ⚙️ Instalasi

### Syarat
- Windows 10/11 64-bit, Python 3.10+ , Git, Google Chrome, VLC (disarankan).

### Langkah
```bash
git clone https://github.com/USERNAME/jarvis-cellya.git
cd jarvis-cellya

python -m venv venv
venv\Scripts\activate

pip install -r requirements.txt
# requirements: PyQt6, PyQt6-WebEngine, groq, python-dotenv,
# google-genai, openai, requests, pypdf, pywin32, playwright,
# python-docx, psutil, reportlab, openpyxl, python-pptx, yt-dlp, python-vlc

# untuk browser automation (sekali saja)
playwright install chromium

# siapkan env
copy .env.example .env
# lalu isi API keys (lihat bawah)

python main.py
```

### Chrome Remote Debugging (opsional, untuk `get_active_browser_info` + proxy stabil)
Double-click `run_chrome_debug.bat` → Chrome relaunch dengan `--remote-debugging-port=9222`.
Atau manual:
```bat
chrome.exe --remote-debugging-port=9222
```

---

## 🔑 Konfigurasi `.env`

Buat file `.env` di root project (sejajar `main.py`). **Jangan commit file ini.**

```env
# API Keys for Jarvis Assistant
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxxxxxx
GEMINI_API_KEY=AIzaSyxxxxxxxxxxxxxxxxxxxx
GRAVITY_API_KEY=grav_xxxxxxxxxxxxxxxxxxxx
GRAVITY_API_BASE=https://api.gravity.com/api/v1

# Workspace Root Override (Optional)
# JARVIS_WORKSPACE_DIR=D:\path\to\your\workspace
```

| Key | Wajib? | Cara Dapat | Dipakai Untuk |
|---|---|---|---|
| `GROQ_API_KEY` | ✅ jika pakai Llama/Groq | https://console.groq.com → API Keys → Create | Provider `groq` (`llama-3.3-70b-versatile`) |
| `GEMINI_API_KEY` | ✅ jika pakai Gemini Direct | https://aistudio.google.com → Get API Key | Provider `gemini` + vision |
| `GRAVITY_API_KEY` | ✅ jika pakai Claude Agent | Dashboard Gravity provider kamu | Provider `gravity` (`ag/gemini-pro-agent` dll) |
| `GRAVITY_API_BASE` | Opsional | Default `http://localhost:20128/api/v1` | Base URL OpenAI-compatible Gravity |
| `JARVIS_WORKSPACE_DIR` | Opsional | Path lokal | Override `safe_workspace_dir` tanpa edit JSON |

> Nilai yang diawali `your_` atau kosong dianggap belum dikonfigurasi — provider terkait akan menampilkan error ramah di chat (*“API Key is not set... check your .env”*), bukan crash.

**Tips aman untuk GitHub:**
- Pastikan `.gitignore` berisi `.env` dan `config/settings.json` (repo ini sudah).
- Upload file contoh bernama `.env.example` (tanpa key asli) agar orang lain tahu formatnya:
```bash
copy .env .env.example
# lalu ganti semua value dengan xxxx / your_key_here
```

---

## 🔧 Konfigurasi `settings.json`

File `config/settings.json` dibuat otomatis saat pertama jalan. Contoh:

```json
{
  "api_provider": "groq",
  "groq_model": "llama-3.3-70b-versatile",
  "gemini_model": "gemini-1.5-flash",
  "temperature": 0.7,
  "safe_workspace_dir": "C:\\Users\\heker\\Project\\Jarvis",
  "theme": "dark-glass",
  "opacity": 0.85,
  "always_on_top": true,
  "voice_mode": false,
  "active_personality": "serious",
  "avatar_mode": "serious",
  "chrome_profile_directory": "Default"
}
```

| Key | Fungsi |
|---|---|
| `safe_workspace_dir` | Root aman file ops. Bisa dioverride via `--workspace` / `JARVIS_WORKSPACE_DIR` |
| `temperature` | Kreativitas default (ditiban personality: serious 0.2, fun 0.8) |
| `active_personality` | `serious` / `fun` (atau custom JSON baru di `personalities/`) |
| `voice_mode` | `true` = bacakan tiap jawaban |
| `chrome_profile_directory` | Nama folder profil Chrome (`Default`, `Profile 1`, ...) |
| `overlay_geometry` / `chat_geometry` | Posisi window terakhir (otomatis) |

---

## 📁 Struktur Project

```
Jarvis/
├── main.py                  # Entry point, tray icon, session wiring
├── requirements.txt
├── .env                     # (tidak di-commit) API keys
├── .env.example             # (buat sendiri) template untuk GitHub
├── run_jarvis.bat           # Launcher Windows
├── run_chrome_debug.bat     # Relaunch Chrome + remote debugging
├── config/
│   ├── __init__.py          # load/save settings + personalities
│   ├── settings.json        # (tidak di-commit) preferensi lokal
│   └── usage_stats.json     # kuota harian
├── core/
│   ├── ai_engine.py         # LLM routing + 16 tool executors
│   ├── prompts.py           # SYSTEM_PROMPT + TOOLS_SCHEMA
│   ├── file_manager.py      # sandbox path + CRUD aman
│   ├── audio_player.py      # music engine (VLC/pygame, queue/shuffle/repeat)
│   ├── music_player.py      # engine lama (legacy)
│   ├── tts.py               # text-to-speech
│   ├── history_manager.py   # simpan/load chat sessions
│   ├── learning_tracker.py  # memori permanen learnings/
│   ├── usage_tracker.py     # counter API harian
│   ├── web_proxy.py         # Claude/Gemini web free proxy
│   └── automator/
│       ├── browser.py       # Playwright BrowserManager
│       ├── classroom.py     # Google Classroom scraper
│       ├── forms.py         # Google Forms parser
│       └── docgen.py        # docx/pdf/xlsx/pptx generator
├── ui/
│   ├── overlay_widget.py    # bubble melayang
│   ├── chat_window.py       # chat + sidebar + music tab + stats
│   ├── music_player_widget.py # widget musik standalone (legacy)
│   └── styles.py            # dark-glass QSS
├── personalities/
│   ├── serious.json
│   └── fun.json
├── history/                 # chat sessions (JSON per session)
├── learnings/               # memori permanen AI
├── Cellya_Outputs/          # output dokumen AI
├── assets/                  # lottie animasi overlay
└── tests/                   # test script
```

---

## 🔒 Keamanan & Batasan Workspace

- AI hanya boleh akses: **primary workspace** + **Downloads** + **Program Files**. Di luar itu ditolak sopan tanpa tool call.
- Path relatif selalu di-resolve ke `safe_workspace_dir` (`AIEngine.resolve_path()`).
- `execute_terminal_command` selalu minta persetujuan + timeout 30 dtk.
- `open_resource` whitelist: `http/https/www`, `spotify:`, `roblox-player:`, `vscode:`, `mailto:`, `slack:`, `zoommtg:`, app `chrome/spotify/roblox/code`, atau folder aman → dibuka di VS Code.

---

## ⌨️ Contoh Prompt / Perintah

```text
tampilkan struktur project ini
bacakan file core/ai_engine.py baris 1-50
buatkan file Cellya_Outputs/todo.txt berisi 3 task
jalankan pip list di powershell
bukakan https://github.com, autosave dulu dengan pesan "before browsing"
carikan video lofi girl di youtube
ringkas isi https://en.wikipedia.org/wiki/Large_language_model
list kelasku di classroom
parse form ini: https://forms.gle/xxxx
buatkan laporan PDF tugasku di Cellya_Outputs/Laporan.docx
ingat: aku lebih suka jawaban singkat tanpa emoji
putarkan lagu YOASOBI - Idol   (via tab Music lebih andal)
```

---

## ❓ Troubleshooting

| Gejala | Penyebab & Solusi |
|---|---|
| `Groq API Key is not set` | `.env` belum diisi / nama key salah. Cek `load_dotenv()` terbaca dari root. Restart app setelah edit `.env`. |
| Chat Web Proxy tidak bisa tool | Normal — Web Proxy hanya chat ringan. Pilih model `(Agent)` / `(Direct)` untuk tool. |
| Musik tidak bunyi / `403 Forbidden` | Update `yt-dlp`: `pip install -U yt-dlp`. Pastikan VLC 64-bit terinstall. Coba lagu lain. |
| `No audio backend` | Install `pip install python-vlc pygame` + VLC Desktop. |
| Playwright error / browser tidak terbuka | `playwright install chromium`, tutup Chrome lalu jalankan `run_chrome_debug.bat`. Cek `chrome_profile_directory` di settings. |
| Screenshot hitam / gagal | Pastikan 1 monitor utama aktif. Coba lagi setelah 1 detik. |
| `Access Denied ... outside workspace` | Path di luar sandbox. Pindahkan file ke workspace atau jalankan `--workspace` menunjuk folder itu. |
| Overlay hilang | Cek system tray → Show Overlay. Hapus `overlay_geometry` di settings.json lalu restart. |

---

## 🗺️ Roadmap

- [ ] Playlist persisten (SQLite `music_data.db` → UI queue save/load)
- [ ] Lirik lagu + thumbnail di Now Playing
- [ ] Global hotkey (mis. `Ctrl+Shift+J`) untuk toggle chat
- [ ] Build `.exe` via PyInstaller + auto-updater
- [ ] Unit test `tests/` → CI GitHub Actions

---

## 📜 Lisensi

MIT — bebas dipakai, modifikasi, dan distribusi. Lihat `LICENSE` (buat sendiri via `gh license` / https://choosealicense.com).

## 🙏 Kredit

- PyQt6, Groq, Google Gemini, yt-dlp, VLC, Playwright
- Karakter **Cellya Claire** dari *Seirei Gensouki* sebagai inspirasi personality.

---
*Dibuat dengan ❤️ di Windows. Kalau README ini membantu, kasih ⭐ di GitHub ya!*
