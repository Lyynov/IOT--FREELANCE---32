# Sistem Monitoring Pompa Air Sumur Bor Bertenaga Surya

**Berbasis Fuzzy Logic dan IoT (Internet of Things)**

## 📋 Deskripsi Project

Sistem monitoring dan kontrol otomatis untuk pompa air sumur bor yang menggunakan energi panel surya sebagai sumber daya utama. Sistem ini menggunakan logika fuzzy untuk menentukan kapan pompa harus dihidupkan atau dimatikan berdasarkan kondisi tegangan dan arus dari panel surya dan baterai. Monitoring real-time dilakukan melalui aplikasi Blynk yang dapat diakses dari smartphone.

## 🎯 Fitur Utama

- ✅ **Monitoring Real-time**: Pemantauan tegangan, arus, dan daya panel surya serta baterai
- ✅ **Kontrol Otomatis**: Menggunakan algoritma fuzzy logic untuk efisiensi maksimal
- ✅ **IoT Integration**: Monitoring jarak jauh melalui aplikasi Blynk
- ✅ **Mode Manual**: Override kontrol otomatis jika diperlukan
- ✅ **Proteksi Baterai**: Automatic shutdown saat baterai lemah (<10.5V)
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
   - Copy token ini untuk konfigurasi kode

### 4. Konfigurasi Blynk Dashboard

#### Virtual Pin Assignment:
```
V0  - Tegangan Panel Surya (Value Display)
V1  - Arus Panel Surya (Value Display)
V2  - Tegangan Baterai (Value Display)
V3  - Arus Baterai (Value Display)
V4  - Status Pompa (LED)
V5  - Daya Panel Surya (Value Display)
V6  - Daya Baterai (Value Display)
V7  - Kontrol Manual (Button/Switch)
V10 - Efisiensi Panel (Terminal)
V11 - Total Waktu Pompa (Terminal)
```

#### Widget Configuration:

1. **Value Display Widget (4x):**
   - Pin: V0 (Tegangan Surya), V1 (Arus Surya), V2 (Tegangan Baterai), V3 (Arus Baterai)
   - Units: V, A, V, A
   - Min/Max: 0-25V, 0-10A

2. **LED Widget:**
   - Pin: V4
   - Label: "Status Pompa"
   - Color: Green (ON), Red (OFF)

3. **Gauge Widget (2x):**
   - Pin: V5 (Daya Surya), V6 (Daya Baterai)
   - Units: W
   - Min/Max: 0-200W

4. **Button Widget:**
   - Pin: V7
   - Label: "Manual Control"
   - Mode: Switch

5. **Terminal Widget:**
   - Pin: V10, V11
   - Auto Scroll: ON

## ⚙️ Konfigurasi Kode

### 1. Edit Kredensial

Buka file kode dan ubah bagian berikut dengan kredensial Anda:

```cpp
// Kredensial Blynk - GANTI DENGAN KREDENSIAL ANDA
#define BLYNK_TEMPLATE_ID "TMPL_ID_ANDA"
#define BLYNK_DEVICE_NAME "Monitor Pompa Surya"
#define BLYNK_AUTH_TOKEN "TOKEN_BLYNK_ANDA"

// Kredensial WiFi - GANTI DENGAN KREDENSIAL ANDA
const char* ssid = "NAMA_WIFI_ANDA";
const char* password = "PASSWORD_WIFI_ANDA";
```

### 2. Kalibrasi Sensor

Sesuaikan konstanta kalibrasi berdasarkan sensor yang digunakan:

```cpp
// Konstanta Kalibrasi Sensor
#define RASIO_PEMBAGI_TEGANGAN 11.0       // Sesuaikan dengan resistor yang digunakan
#define SENSITIVITAS_SENSOR_ARUS 0.1      // 100mV/A untuk ACS712-20A
#define OFFSET_SENSOR_ARUS 2.5            // Offset 2.5V pada 0A
```

### 3. Upload Kode

1. Hubungkan ESP32 ke komputer via USB
2. Pilih Board: "ESP32 Dev Module"
3. Pilih Port yang sesuai
4. Upload kode

## 🧠 Sistem Fuzzy Logic

### Input Variables:
- **Tegangan Panel Surya** (V): Rendah (0-15V), Sedang (12-20V), Tinggi (17-24V)
- **Arus Panel Surya** (A): Rendah (0-3A), Sedang (1.5-5A), Tinggi (4-10A)
- **Tegangan Baterai** (V): Rendah (0-15V), Sedang (12-20V), Tinggi (17-24V)
- **Arus Baterai** (A): Rendah (0-3A), Sedang (1.5-5A), Tinggi (4-10A)

### Output Variable:
- **Status Pompa**: HIDUP atau MATI

### Fuzzy Rules:

#### Rules untuk Panel Surya:
1. IF Tegangan=Rendah AND Arus=Rendah THEN Pompa=MATI
2. IF Tegangan=Rendah AND Arus=Sedang THEN Pompa=MATI
3. IF Tegangan=Rendah AND Arus=Tinggi THEN Pompa=MATI
4. IF Tegangan=Sedang AND Arus=Rendah THEN Pompa=MATI
5. IF Tegangan=Sedang AND Arus=Sedang THEN Pompa=HIDUP
6. IF Tegangan=Sedang AND Arus=Tinggi THEN Pompa=HIDUP
7. IF Tegangan=Tinggi AND Arus=Rendah THEN Pompa=MATI
8. IF Tegangan=Tinggi AND Arus=Sedang THEN Pompa=HIDUP
9. IF Tegangan=Tinggi AND Arus=Tinggi THEN Pompa=HIDUP

#### Rules untuk Baterai:
10-18. Rules serupa untuk kondisi baterai

### Logika Keputusan:
- Pompa HIDUP jika panel surya ATAU baterai mencukupi daya
- Proteksi: Pompa MATI paksa jika tegangan baterai < 10.5V

## 📱 Penggunaan Aplikasi

### Mode Otomatis:
- Sistem bekerja otomatis berdasarkan fuzzy logic
- Monitoring real-time melalui dashboard Blynk
- Notifikasi status perubahan pompa

### Mode Manual:
- Aktifkan switch "Manual Control" di Blynk
- Pompa dapat dikendalikan manual
- Sistem kembali otomatis saat switch OFF

### Dashboard Features:
- Real-time monitoring tegangan, arus, daya
- Grafik historis (jika tersedia)
- Status LED pompa
- Statistik efisiensi sistem

## 🔧 Troubleshooting

### Masalah Umum:

#### 1. ESP32 Tidak Terhubung WiFi
**Solusi:**
- Periksa SSID dan password WiFi
- Pastikan sinyal WiFi kuat
- Reset ESP32 dan coba lagi

#### 2. Sensor Tidak Akurat
**Solusi:**
- Kalibrasi ulang konstanta sensor
- Periksa koneksi wiring
- Ganti kapasitor filter jika perlu

#### 3. Blynk Tidak Menerima Data
**Solusi:**
- Periksa Auth Token
- Pastikan koneksi internet stabil
- Cek virtual pin assignment

#### 4. Pompa Tidak Merespon
**Solusi:**
- Periksa koneksi relay
- Test relay manual
- Cek supply tegangan relay

#### 5. Pembacaan Tegangan Salah
**Solusi:**
- Periksa rasio pembagi tegangan
- Kalibrasi dengan multimeter
- Ganti resistor jika rusak

### Debug Mode:
Untuk debugging, monitor Serial dengan baud rate 115200:
```cpp
Serial.begin(115200);
```

## 📊 Spesifikasi Teknis

### Hardware Requirements:
- **Microcontroller**: ESP32 (240MHz, WiFi, Bluetooth)
- **ADC Resolution**: 12-bit (4096 levels)
- **Operating Voltage**: 3.3V
- **Input Voltage**: 5-12V (via USB/Vin)
- **Digital I/O Pins**: 24
- **Analog Input Pins**: 6

### Power Requirements:
- **ESP32**: ~240mA (aktif), ~10µA (deep sleep)
- **Relay Module**: ~70mA per relay
- **Total System**: ~350mA @5V

### Environmental Specs:
- **Operating Temperature**: -40°C to +85°C
- **Humidity**: 5% to 95% RH
- **IP Rating**: IP65 (dengan housing yang tepat)

## 🔒 Fitur Keamanan

### Proteksi Hardware:
- **Over-discharge Protection**: Pompa mati otomatis saat baterai <10.5V
- **Over-current Protection**: Monitoring arus berlebih
- **Reverse Polarity Protection**: Dioda proteksi pada input

### Proteksi Software:
- **Watchdog Timer**: Reset otomatis jika sistem hang
- **Connection Monitoring**: Auto-reconnect WiFi/Blynk
- **Error Handling**: Handling error sensor dan komunikasi

## 📈 Optimasi Performa

### Tips Efisiensi:
1. **Penempatan Panel Surya**: Hadap selatan, sudut 30-45°
2. **Kalibrasi Sensor**: Lakukan kalibrasi berkala
3. **Maintenance**: Bersihkan panel surya secara rutin
4. **Battery Care**: Hindari discharge berlebihan

### Monitoring KPI:
- **Efisiensi Panel**: (Daya Output / Daya Nominal) × 100%
- **Uptime Pompa**: Total waktu operasi pompa
- **Energy Harvesting**: Total energi yang dikumpulkan
- **System Availability**: Waktu sistem online vs offline

## 📋 Maintenance Schedule

### Harian:
- ✅ Cek status sistem via Blynk
- ✅ Monitor level air di tangki

### Mingguan:
- ✅ Bersihkan debu pada panel surya
- ✅ Cek koneksi kabel

### Bulanan:
- ✅ Kalibrasi sensor tegangan/arus
- ✅ Cek kondisi baterai
- ✅ Update firmware jika ada

### 6 Bulan:
- ✅ Ganti komponen yang aus
- ✅ Maintenance pompa
- ✅ Cek grounding sistem

## 📄 Lisensi

Proyek ini dilisensikan under MIT License - lihat file [LICENSE](LICENSE) untuk detail.

## 👨‍💻 Kontributor

- **Developer**: [Nama Anda]
- **Email**: [email@anda.com]
- **Repository**: [GitHub Link]

## 🆘 Support

Jika Anda mengalami masalah atau memiliki pertanyaan:

1. **GitHub Issues**: Buat issue di repository
2. **Email Support**: [email@support.com]
3. **Documentation**: Baca dokumentasi lengkap
4. **Community**: Join forum diskusi

## 🔄 Changelog

### v1.0.0 (Current)
- ✅ Initial release
- ✅ Basic fuzzy logic implementation
- ✅ Blynk integration
- ✅ Real-time monitoring
- ✅ Manual override feature

### Planned Features (v1.1.0):
- 🔄 Historical data logging
- 🔄 Weather integration
- 🔄 SMS notifications
- 🔄 Advanced analytics

## 📚 Referensi

1. **ESP32 Documentation**: [Espressif Docs](https://docs.espressif.com/projects/esp32/en/latest/)
2. **Blynk Documentation**: [Blynk Docs](https://docs.blynk.io/)
3. **Fuzzy Logic Theory**: IEEE Papers on Fuzzy Control Systems
4. **Solar Panel Efficiency**: NREL Solar Resource Data

---

**© 2024 Solar Pump Monitoring System. All rights reserved.**
