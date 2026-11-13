---
tags: [momot, cloud, backend]
repo: CLOUD-COMPANION
---

# Cloud (repo CLOUD-COMPANION, paket `momot-cloud`)

Kembali ke [peta](README.md).

Node.js 20+ tanpa kerangka kerja (`http` bawaan), MySQL 8 / MariaDB 10.5+, dan
ffmpeg untuk mengubah MP3 TTS menjadi PCM. Ada Dockerfile (`node:22-alpine` +
ffmpeg).

## Berkas

| Berkas | Isi |
|---|---|
| `src/server.mjs` | Semua rute, pembatas laju, OTA |
| `src/db.mjs` | Koneksi MySQL; tabel dibuat otomatis saat start (`IF NOT EXISTS`) |
| `src/auth.mjs` | Hash sandi (scrypt) dan token |
| `src/catalog.mjs` | Model, suara, bahasa, `CONFIG_BAWAAN`, dan batas setelan |
| `src/tts.mjs` | TTS ke PCM/mu-law, cache, dan laju yang diizinkan |
| `uji/*.mjs` | Uji terhadap cloud lokal (bukan produksi) |

## Tabel

| Tabel | Isi |
|---|---|
| `users` | Akun; sandi sebagai hash scrypt bergaram |
| `sessions` | Token sesi sebagai hash, berlaku 90 hari |
| `devices` | Perangkat; token perangkat sebagai hash; status terakhir (rssi, ip, firmware, ssid, baterai) |
| `device_shares` | Berbagi perangkat, dengan peran dan masa berlaku |
| `schedules` | Jadwal: `hari` bitmask, jam/menit lokal + zona |
| `events` | Riwayat kejadian untuk tab Aktivitas |

Ada juga data ingatan dan jurnal milik akun (rute `/v1/app/ingatan` dan
`/v1/app/jurnal`).

## Lingkungan

| Variabel | Arti |
|---|---|
| `UPSTREAM_BASE`, `UPSTREAM_KEY` | Penyedia AI (gaya OpenAI) |
| `STT_MODEL` | Model Whisper bawaan |
| `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASS`, `DB_NAME` | MySQL |
| `MOMOT_DATA` | Folder data: cache TTS dan `firmware/` untuk OTA. Dockerfile: `/data` |
| `MOMOT_VERSI` | Versi yang ditampilkan `/health` |
| `FIRMWARE_VERSI` | Versi firmware OTA; kosong = OTA mati |
| `PORT` | Port HTTP (Docker: 8080) |

Nama lama `NORI_DATA` dan `NORI_VERSI` **tidak dibaca lagi**.

## Setelan perangkat

`CONFIG_BAWAAN`: `model`, `voice`, `lang: 'id'`, `vol: 100`, `bright: 100`,
`vad: 60`, `onset: 1`, `silence: 1200`, `persona: ''`.

Batas yang dijepit server: `vol` dan `bright` 0–100, `vad` 12–250, `onset`
1–4, `silence` 400–3000 ms, `persona` maksimal 400 karakter. Model, suara, dan
bahasa harus ada di katalog.

## Pembatas laju (di memori)

- Daftar: 5 per jam per IP.
- Masuk: 20 per 15 menit per IP, dan 8 per 15 menit per email.
- Perangkat: 60 per menit per token.

Kalau kelak berjalan di beberapa salinan, pembatas ini perlu pindah ke Redis.

Rute: [Kontrak API perangkat](kontrak-api-perangkat.md) dan
[Kontrak API aplikasi](kontrak-api-aplikasi.md). OTA: [OTA](ota.md).
