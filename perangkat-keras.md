---
tags: [momot, hardware, xiao-esp32s3]
---

# Perangkat keras: Seeed XIAO ESP32S3

Kembali ke [peta](README.md). Firmware: [Firmware](firmware.md).

Papan sekarang **Seeed XIAO ESP32S3** (8 MB flash, 8 MB PSRAM octal, antena
U.FL). Papan lama, ESP32-S3-WROOM-1 N16R8 di PCB `momot-s3`, masih didukung
firmware tapi tidak dipakai lagi. Sambungan XIAO dibuat dengan kabel dan satu
papan lubang kecil sebagai titik kumpul daya.

## Komponen

| Kode | Komponen |
|---|---|
| U1 | Seeed XIAO ESP32S3 |
| U2 | Modul pengisi IP2312 (USB-C sendiri, penuh 4,2 V) |
| BT1 | LiPo 1S 3,7 V 2000 mAh dengan BMS |
| SW1 | Saklar geser SPDT |
| R1, R2 | 100 kΩ 1%, pembagi tegangan baterai |
| C1 | 100 nF, peredam derau ADC |
| C2 | Elco 100 µF 16 V di kaki daya amplifier |
| M1 | Layar bulat GC9A01 1,28" (SPI) |
| M2 | Mikrofon I2S INMP441 |
| M3 | Amplifier I2S MAX98357A |
| M4 | MPU-6050 (GY-521) |
| LS1 | Speaker 4–8 Ω, 1–3 W |

## Pin

| Pin XIAO | GPIO | Fungsi |
|---|---|---|
| D0 | 1 | ADC baterai (pembagi 100k/100k) |
| D1 | 2 | I2S BCLK, bersama mic dan amp |
| D2 | 3 | I2S WS/LRC, bersama |
| D3 | 4 | Data mic (SD) |
| D4 | 5 | I2C SDA (MPU-6050) |
| D5 | 6 | I2C SCL (MPU-6050) |
| D6 | 43 | Layar DC |
| D7 | 44 | Layar CS |
| D8 | 7 | Layar SCL (SPI clock) |
| D9 | 8 | Data amp (DIN) |
| D10 | 9 | Layar SDA (SPI data) |
| Tombol B | 0 | Tombol bicara |

- XIAO hanya punya 11 pin, jadi mic dan amp **berbagi satu port I2S
  dupleks** (BCLK dan WS sama, data masuk dan keluar terpisah).
- RST layar diikat ke 3V3; driver memakai reset perangkat lunak.
- INT MPU-6050 tidak disambung.
- D6/D7 adalah UART0. ROM mengirim log boot di GPIO43 sesaat setelah reset;
  aman karena tanpa clock di D8 layar tidak menerima data. `Serial` memakai
  USB-C.

## Daya

```
BT1 + ── U2 B+ ── SW1 kaki 1        (baterai selalu terisi, saklar ON atau OFF)
SW1 kaki 2 (tengah) = VBAT ── pad BAT+ XIAO, VIN amplifier, C2 +, pembagi R1/R2
3V3 XIAO ── layar, mic, MPU-6050
```

- **Baterai masuk lewat pad BAT+/BAT− di bawah XIAO, bukan pin 5V.** Pin 5V
  tersambung langsung ke USB-C; baterai di sana akan diisi 5 V tanpa
  pengaturan saat laptop dicolok.
- **Amplifier dari VBAT, bukan 3V3.** Lonjakan arus speaker (±1 A) di
  regulator 3V3 (maks ±700 mA) berebut dengan WiFi dan bisa memicu reset.
- **Pembagi sesudah saklar**, jadi tidak menguras baterai saat OFF.
- Daya tahan: rata-rata 100–150 mA saat WiFi aktif, jadi 2000 mAh cukup untuk
  ±12–18 jam.
- Batas di firmware (`baterai.h`): di bawah 3300 mV Momot tidur, boot butuh
  3400 mV, bacaan di bawah 2500 mV dianggap pembagi tidak terpasang.

## Rakit bertahap

Tiap tahap dites sebelum lanjut:

0. XIAO saja: sketch `arduino/MomotTes`, harus melapor flash 8 MB dan PSRAM.
1. Layar.
2. Mikrofon.
3. Amplifier dan speaker. Sementara VIN ke 3V3, karena belum ada baterai.
4. MPU-6050.
5. Baterai, IP2312, saklar, dan pembagi. Ukur dulu dengan multimeter; baterai
   disambung paling akhir.
6. Firmware lengkap.

Sketch tes lain: `arduino/TesSpeaker` (nada) dan `arduino/TesWajah` (semua
klip wajah).

## Gejala umum

| Gejala | Cek |
|---|---|
| Port USB tidak muncul | Kabel USB-C hanya untuk mengisi |
| Upload gagal | Tahan B, tekan R, lepas B |
| Layar hitam, Serial jalan | SCL ke D8, SDA ke D10, RST ke 3V3 |
| Mic selalu 0 | L/R mic ke GND, SD ke D3 |
| Speaker diam | DIN ke D9, ukur VIN |
| Reset saat suara keras | VIN amp harus di VBAT, C2 terpasang |
| WiFi sering putus | Antena U.FL belum dipasang |
