---
tags: [momot, firmware, esp32]
repo: ESP_VoCat
---

# Firmware (repo ESP_VoCat)

Kembali ke [peta](README.md). Hardware: [Perangkat keras](perangkat-keras.md).

## Struktur repo

| Folder | Isi |
|---|---|
| `ESP_VoCat_Main/src/` | Firmware lengkap. Sumber tunggal untuk Arduino dan PlatformIO |
| `ESP_VoCat_Main/ESP_VoCat_Main.ino` | Sengaja kosong: pintu masuk Arduino IDE/arduino-cli; `src/` dikompilasi rekursif |
| `ESP_VoCat_Main/platformio.ini` | env `xiao-esp32s3` (bawaan) dan `esp32-s3-n16r8` (papan lama) |
| `arduino/MomotTes`, `TesSpeaker`, `TesWajah` | Sketch tes perakitan |
| `PERANCANGAN_XIAO.md` | Wiring XIAO kaki demi kaki |
| `pcb/` | PCB lama untuk modul WROOM |

## Modul

| Berkas | Tugas |
|---|---|
| `main.cpp` | Urutan boot dan loop utama |
| `config.h` | Konstanta perangkat: pin, laju sampel, batas waktu. Blok pin XIAO aktif lewat `ARDUINO_XIAO_ESP32S3` atau `-DBOARD_XIAO` |
| `settings.*` | Setelan pemakai di NVS (tidak hilang saat di-flash) |
| `audio.*` | I2S, mikrofon, kalibrasi, antrean nada. Mode dupleks untuk XIAO |
| `voice.*` | Dengar, rekam, lalu kirim ke Whisper |
| `speech.*` | TTS dari cloud, chirp sebagai cadangan |
| `brain.*` | Susun permintaan chat, ingatan percakapan, dan `hello` |
| `cloud.*` | Klien TLS bersama, satu sambungan per giliran bicara |
| `sambung.*` | WiFi yang tahan banting dan pemulihannya |
| `prov_ble.*` | Pendaftaran lewat BLE ([Kontrak BLE](kontrak-ble.md)) |
| `ota.*` | OTA dari cloud ([OTA](ota.md)) |
| `display.*`, `face.*`, `emotion.*` | Layar GC9A01, wajah vektor, pose, dan klip animasi |
| `baterai.h` | Tegangan dan persen LiPo lewat pembagi di D0 |
| `web.*` | Halaman setup 192.168.4.1 (mode AP pemulihan) |
| `cli.*` | Perintah lewat Serial Monitor |
| `pcm.*` | Matematika sampel: mu-law, normalisasi |

## Build

Arduino IDE: board **XIAO_ESP32S3**, PSRAM **OPI PSRAM**, Partition Scheme
**Default with spiffs (3MB APP/1.5MB SPIFFS)**. Library: Adafruit GFX,
Adafruit GC9A01A, ArduinoJson 7.

```bash
arduino-cli compile -b "esp32:esp32:XIAO_ESP32S3:PSRAM=opi,PartitionScheme=default_8MB" ESP_VoCat_Main
```

PlatformIO: `cd ESP_VoCat_Main && pio run -e xiao-esp32s3`.

Ukuran dengan core esp32 3.3.12: ±2,50 MB (74% dari slot 3 MB). Skema 8 MB
punya dua slot aplikasi, jadi OTA bisa.

## Angka penting

| Konstanta | Nilai | Arti |
|---|---|---|
| `FW_VERSION` | `"3.0"` | 3.x = papan S3; dibandingkan dengan `FIRMWARE_VERSI` di cloud |
| `PAPAN` | `"xiao-s3"` / `"s3"` | Kode papan untuk OTA |
| `SAMPLE_RATE` | 16000 | Laju I2S mic |
| `STT_RATE` | 8000 | Laju audio yang diunggah (desimasi 2:1) |
| `STT_ULAW` | 1 | Unggahan dikodekan mu-law (setengah ukuran PCM16) |
| `TTS_RATE_HZ` | 8000 | Laju audio TTS yang diminta |
| `REC_SECONDS` | 5 | Plafon satu rekaman |
| `VAD_MIN_MS` | 700 | Rekaman lebih pendek diabaikan |
| `MP_BOUNDARY` | `----MomotBoundary7f3a91` | Batas multipart unggahan STT |

## Setelan dari cloud

Dibaca dari balasan `hello` bila `rev` berubah: `lang`, `persona`, `vol`,
`bright`, `vad`, `onset`, `silence`, plus `sttModel`. NVS hanya ditulis ulang
bila `rev` naik, supaya flash tidak aus.
