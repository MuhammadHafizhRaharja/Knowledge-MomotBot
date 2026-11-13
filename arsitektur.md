---
tags: [momot, arsitektur]
---

# Arsitektur dan alur data

Kembali ke [peta](README.md).

```
Aplikasi HP ──BLE──▶ Momot (XIAO ESP32S3)     sekali, saat mendaftar
    │                    │                     (WiFi + deviceId + deviceToken + alamat cloud)
    │                    │
    └──HTTPS──▶  momot-cloud  ◀──HTTPS──┘      setiap hari
                     │
               ┌─────┴──────┐
             MySQL     penyedia AI (chat, Whisper, TTS)
```

## Tiga pihak

- **Firmware** ([catatan](firmware.md)): merekam suara, mengirimnya ke cloud
  untuk ditranskripsi, meminta jawaban model, lalu memutar suara balasan.
  Menampilkan wajah beranimasi di layar GC9A01.
- **Cloud** ([catatan](cloud.md)): memegang akun, perangkat, setelan, jadwal,
  berbagi, dan riwayat. Memegang kunci penyedia AI dan meneruskan permintaan
  perangkat ke sana. Menawarkan OTA.
- **Aplikasi** ([catatan](aplikasi.md)): mendaftarkan perangkat lewat BLE,
  menampilkan status, dan mengubah setelan lewat cloud.

## Urutan pendaftaran

1. Aplikasi memanggil `POST /v1/app/devices` di cloud.
2. Cloud membalas `device` dan `provisioning: {deviceId, deviceToken, cloud}`.
   Token mentah hanya muncul sekali di sini; cloud menyimpan hash-nya.
3. Aplikasi menulis satu JSON ke karakteristik PROV: `{ssid, pass, cloud,
   deviceId, deviceToken}`. Lihat [Kontrak BLE](kontrak-ble.md).
4. Momot menyimpan ke NVS, mengabarkan `saved`, lalu restart. Putusnya BLE di
   titik ini tanda berhasil.
5. Momot menyambung WiFi dan memanggil `POST /v1/device/hello`. Cloud menandai
   perangkat online.

Urutan 1 sebelum 3 tidak boleh ditukar: tanpa token, Momot tersambung WiFi
tapi tidak tahu harus melapor ke siapa.

## Satu giliran bicara

1. Tombol B ditekan, atau VAD mendeteksi suara.
2. Rekam PCM 16 kHz, desimasi ke 8 kHz, gerbang suara, pangkas sunyi,
   normalisasi, lalu ubah ke mu-law.
3. `POST /v1/device/stt`: multipart berisi WAV mu-law 8 kHz. Cloud
   meneruskannya ke Whisper apa adanya.
4. `POST /v1/device/chat`: `{messages}` gaya OpenAI. Cloud memilih model dari
   setelan perangkat.
5. `POST /v1/device/tts?fmt=ulaw&rate=8000`: `{input}`. Cloud membalas
   mu-law, lalu perangkat memutarnya lewat MAX98357A.

Detail tiap rute ada di [Kontrak API perangkat](kontrak-api-perangkat.md).

## Yang tidak lewat cloud

Sandi WiFi berjalan dari HP ke Momot lewat BLE dan berhenti di NVS perangkat.
Yang lewat cloud hanya setelan: model, suara, bahasa, volume, dan sejenisnya.
