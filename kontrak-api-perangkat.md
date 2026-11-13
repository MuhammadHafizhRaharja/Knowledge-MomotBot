---
tags: [momot, kontrak, api]
---

# Kontrak API perangkat (firmware ke cloud)

Kembali ke [peta](README.md). Semua rute memakai
`Authorization: Bearer <deviceToken>`. Batasnya 60 permintaan per menit per
token.

| Rute | Firmware mengirim | Cloud membalas |
|---|---|---|
| `POST /v1/device/hello` | `{rssi, ip, firmware, ssid, papan, bateraiMv, bateraiPersen}` | `{rev, config, sttModel, firmware?}` |
| `GET /v1/device/config` | | `{rev, config, sttModel}` |
| `POST /v1/device/chat` | `{messages: [{role, content}]}` (gaya OpenAI) | Balasan penyedia apa adanya; firmware membaca `choices[0].message.content` |
| `POST /v1/device/stt` | multipart: `model`, `language`, `response_format=text`, `temperature=0`, `file` (WAV mu-law 8 kHz) | Teks transkrip |
| `POST /v1/device/stt/raw` | Badan mengalir mu-law/PCM16, header `X-Rate` dan `X-Fmt` | Teks; WAV dan multipart disusun cloud. Belum dipakai firmware XIAO |
| `POST /v1/device/tts?fmt=ulaw&rate=8000` | `{input}` | Audio mu-law/PCM, header `X-Rate` dan `X-Duration`; `?wav=1` untuk WAV |
| `GET /v1/device/firmware?papan=<papan>` | | Biner OTA; tanpa `papan` = `s3` |

## Catatan

- `hello` menggabungkan laporan dan pengambilan setelan, supaya hanya satu
  handshake TLS. Perangkat melapor sekitar tiap 30 detik, dan cloud
  menganggapnya online selama laporan terakhir kurang dari 3 menit lalu.
- `chat` memakai `config.model` milik perangkat; cloud menambahkan
  `max_tokens: 400`.
- `stt` meneruskan header `Content-Type` dari perangkat apa adanya. Karena
  itu batas multipart cukup konsisten di dalam firmware saja.
- Laju TTS yang diizinkan: 8000, 16000, 22050, 24000. Nilai lain jatuh ke
  16000.
- `papan` dan `firmware` dipakai untuk tawaran [OTA](ota.md).
