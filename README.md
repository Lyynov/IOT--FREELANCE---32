# Sistem Monitoring Pompa Air Sumur Bor Bertenaga Surya

**Berbasis Fuzzy Logic dan IoT (Internet of Things)**

## 📋 Deskripsi Project

Sistem monitoring dan kontrol otomatis untuk pompa air sumur bor yang menggunakan energi panel surya sebagai sumber daya utama. Sistem ini menggunakan logika fuzzy untuk menentukan kapan pompa harus dihidupkan atau dimatikan berdasarkan kondisi tegangan dan arus dari panel surya dan baterai. Monitoring real-time dilakukan melalui aplikasi Blynk yang dapat diakses dari smartphone.

## 🎯 Fitur Utama

- ✅ **Monitoring Real-time**: Pemantauan tegangan, arus, dan daya panel surya serta baterai
- ✅ **Kontrol Otomatis**: Menggunakan algoritma fuzzy logic untuk efisiensi maksimal
- ✅ **IoT Integration**: Monitoring jarak jauh melalui aplikasi Blynk
- ✅ **Mode Manual**: Override kontrol otomatis jika diperlukan
- ✅ **Proteksi Baterai**: Automatic shutdown saat baterai lemah
- ✅ **Statistik Sistem**: Tracking efisiensi dan waktu operasional
- ✅ **Auto Reconnect**: Reconnect otomatis WiFi jika terputus

## 🔧 Komponen Hardware

### Komponen Utama:
1. **ESP32 NodeMCU** - Mikrokontroler utama
2. **Panel Surya** - Sumber energi utama (minimal 50W)
3. **Baterai 12V** - Penyimpan energi
4. **Pompa Air DC 12V** - Pompa sumur bor
5. **Relay Module 4-Channel** - Kontrol switching pompa
6. **Sensor Tegangan** - Monitoring voltase
7. **Sensor Arus ACS712-20A** - Monitoring arus listrik
8. **Charge Controller** - Pengatur pengisian baterai
9. **Inverter** (opsional) - Jika pompa AC

### Komponen Pendukung:
- Resistor 10kΩ dan 1kΩ (pembagi tegangan)
- Kapasitor 100nF (filter noise)
- LED indikator
- Terminal block
- Kabel dan connector
- Box/housing tahan cuaca

## 📐 Diagram Wiring

```
ESP32 NodeMCU Pinout:
┌─────────────────────────────────────┐
│  PIN 34 ←→ Sensor Tegangan Surya    │
│  PIN 35 ←→ Sensor Arus Surya        │
│  PIN 32 ←→ Sensor Tegangan Baterai  │
│  PIN 33 ←→ Sensor Arus Baterai      │
│  PIN 2  ←→ Relay Pompa              │
│  PIN 4  ←→ LED Status               │
│  GND    ←→ Ground bersama            │
│  3.3V   ←→ VCC sensor               │
└─────────────────────────────────────┘
```

### Skema Lengkap:

```
Panel Surya (+) ──┬─→ Charge Controller (+) ──→ Baterai (+)
                  │
                  └─→ Sensor Arus Surya ──→ ESP32 Pin 35
                      │
                      └─→ Pembagi Tegangan ──→ ESP32 Pin 34

Baterai (+) ──┬─→ Sensor Arus Baterai ──→ ESP32 Pin 33
              │
              └─→ Pembagi Tegangan ──→ ESP32 Pin 32

Baterai (+) ──→ Relay Module ──→ Pompa (+)
ESP32 Pin 2 ──→ Relay Control
Ground      ──→ Ground bersama semua komponen
```

### Detail Pembagi Tegangan:
```
Vin (+) ──┬─→ R1 (10kΩ) ──┬─→ R2 (1kΩ) ──→ GND
          │                │
          │                └─→ ESP32 ADC Pin
          │
          └─→ Kapasitor 100nF ──→ GND
```

## 🛠️ Instalasi Software

### 1. Persiapan Arduino IDE

1. **Download dan Install Arduino IDE** dari [arduino.cc](https://www.arduino.cc/en/software)

2. **Tambahkan ESP32 Board Manager:**
   - Buka Arduino IDE
   - File → Preferences
   - Tambahkan URL ini di "Additional Board Manager URLs":
     ```
     https://dl.espressif.com/dl/package_esp32_index.json
     ```

3. **Install ESP32 Board:**
   - Tools → Board → Board Manager
   - Cari "ESP32" dan install "ESP32 by Espressif Systems"

### 2. Install Library Dependencies

Install library berikut melalui Library Manager (Tools → Manage Libraries):

```cpp
// Library yang dibutuhkan:
1. "Blynk" by Volodymyr Shymanskyy
2. "WiFi" (sudah built-in ESP32)
3. "SimpleTimer" by Marcello Romani
```

**Cara Install:**
- Buka Tools → Manage Libraries
- Cari nama library di search box
- Klik "Install" pada library yang sesuai

### 3. Setup Akun Blynk

1. **Download Aplikasi Blynk:**
   - Android: [Google Play Store](https://play.google.com/store/apps/details?id=cloud.blynk)
   - iOS: [App Store](https://apps.apple.com/app/blynk-control-arduino-raspberry/id808760481)

2. **Buat Akun Blynk:**
   - Buka aplikasi Blynk
   - Sign up dengan email
   - Verifikasi email

3. **Buat Project Baru:**
   - Tap "New Project"
   - Project Name: "Monitor Pompa Surya"
   - Device: "ESP32"
   - Connection: "WiFi"
   - Tap "Create"

4. **Dapatkan Auth Token:**
   - Setelah project dibuat, Auth Token akan dikirim ke email
