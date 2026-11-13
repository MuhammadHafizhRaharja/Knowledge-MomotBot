---
tags: [momot, aplikasi, capacitor]
repo: Ai-Companions-apis
---

# Aplikasi (repo Ai-Companions-apis, paket `momot-app`)

Kembali ke [peta](README.md).

HTML/CSS/JS biasa tanpa kerangka kerja UI, dibangun dengan Vite dan dibungkus
Capacitor 7 untuk Android dan iOS. Seluruh layar bisa diuji di Chrome
desktop. Chrome punya Web Bluetooth, jadi pendaftaran ke Momot sungguhan
bisa dicoba dari laptop.

## Berkas inti

| Berkas | Isi |
|---|---|
| `src/kontrak.js` | UUID BLE, awalan nama, alamat cloud bawaan. Satu-satunya sumber kontrak di aplikasi |
| `src/ble.js` | Dua jalur: plugin `@capacitor-community/bluetooth-le` di HP, Web Bluetooth di browser |
| `src/api.js` | Klien cloud ([Kontrak API aplikasi](kontrak-api-aplikasi.md)) |
| `src/layar-tambah.js` | Alur pendaftaran empat langkah |
| `src/layar-detail.js` | Detail perangkat: status, setelan, jadwal, berbagi |
| `src/main.js` | Titik masuk dan daftar perangkat |
| `src/i18n.js` | Teks multi-bahasa |
| `src/gaya.css` | Token warna |

## Layar

```
Masuk / Daftar
├── Perangkat     daftar; ubin redup = luring
│   ├── Tambah    alur BLE empat langkah
│   └── Detail    Setelan · Jadwal · Berbagi
├── Aktivitas     riwayat dan notifikasi per hari
└── Saya          akun, ekspor data, hapus akun
```

Navigasi memakai tab di bawah, karena layar dipegang satu tangan. Tombol
kembali Android ditangani.

## Status yang ditampilkan

Dari cloud: `online`, `lastSeen`, `firmware`, `ip`, `rssi`, `ssid`,
`bateraiMv`, `bateraiPersen`, `peran`. Meter emosi hanya tampil bila
perangkat melaporkan `happy`; cloud belum mengirimnya, jadi kartu ringkas
menampilkan "—".

## Perintah

```bash
npm install
npm run dev        # http://localhost:5173
npm run build      # ke www/
npm run android    # build + cap sync + Android Studio
npm run ios        # build + cap sync + Xcode
```

## Izin platform

- Android 12+: `BLUETOOTH_SCAN` dengan `neverForLocation`, jadi aplikasi
  tidak perlu minta izin lokasi. Ditambah `BLUETOOTH_CONNECT` dan `INTERNET`.
- iOS: `NSBluetoothAlwaysUsageDescription` dengan alasan yang jelas.

Kontrak dengan perangkat: [Kontrak BLE](kontrak-ble.md).
