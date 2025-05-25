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

## 📐 Diagram Wiring (Detail Lengkap)

### ESP32 NodeMCU Pinout Configuration:
```
ESP32 DevKit v1 - 30 Pin Layout:
┌─────────────────────────────────────────────────────────────┐
│  EN     │ 3V3 │ VP(36) │ VN(39) │ D34 │ D35 │ D32 │ D33    │
│  D23    │ GND │ D22    │ D21    │ D19 │ D18 │ D5  │ D17    │
│         │     │        │        │     │     │     │        │
│  D4     │ D0  │ D2     │ D15    │ D8  │ D7  │ D6  │ D20    │
│  RX2    │ TX2 │ D26    │ D25    │ D27 │ D14 │ D12 │ D13    │
└─────────────────────────────────────────────────────────────┘

Pin Assignment:
• D34 (ADC1_CH6) ←→ Sensor Tegangan Panel Surya
• D35 (ADC1_CH7) ←→ Sensor Arus Panel Surya (ACS712)
• D32 (ADC1_CH4) ←→ Sensor Tegangan Baterai  
• D33 (ADC1_CH5) ←→ Sensor Arus Baterai (ACS712)
• D2  (GPIO2)    ←→ Relay Control Pin (Pompa)
• D4  (GPIO4)    ←→ LED Status Sistem
• 3V3            ←→ VCC untuk sensor & pembagi tegangan
• GND            ←→ Ground bersama semua komponen
```

### 🔌 Wiring Diagram Lengkap:

#### 1. Sistem Daya Utama:
```
Panel Surya (12V-18V)
    │
    ├─(+)─→ Charge Controller Input (+)
    └─(-)─→ Charge Controller Input (-)
                │
                ├─(+)─→ Baterai 12V (+)
                └─(-)─→ Baterai 12V (-)
```

#### 2. Sensor Tegangan Panel Surya:
```
Panel Surya (+) ─┬─→ R1 (10kΩ) ─┬─→ R2 (1kΩ) ─→ GND
                 │               │
                 │               ├─→ C1 (100nF) ─→ GND
                 │               │
                 │               └─→ ESP32 Pin 34 (ADC)
                 │
                 └─→ LED Indikator (Opsional)
```

#### 3. Sensor Arus Panel Surya (ACS712-20A):
```
Panel Surya (+) ─→ ACS712 IP+ Terminal
ACS712 IP- Terminal ─→ Charge Controller Input
ACS712 VCC ─→ ESP32 3.3V
ACS712 GND ─→ ESP32 GND  
ACS712 OUT ─→ ESP32 Pin 35 (ADC)
```

#### 4. Sensor Tegangan Baterai:
```
Baterai (+) ─┬─→ R3 (10kΩ) ─┬─→ R4 (1kΩ) ─→ GND
             │               │
             │               ├─→ C2 (100nF) ─→ GND
             │               │
             │               └─→ ESP32 Pin 32 (ADC)
             │
             └─→ Fuse 10A (Proteksi)
```

#### 5. Sensor Arus Baterai (ACS712-20A):
```
Baterai (+) ─→ ACS712 IP+ Terminal  
ACS712 IP- Terminal ─→ Relay Module VCC
ACS712 VCC ─→ ESP32 3.3V
ACS712 GND ─→ ESP32 GND
ACS712 OUT ─→ ESP32 Pin 33 (ADC)
```

#### 6. Relay Module & Pompa:
```
ESP32 Pin 2 ─→ Relay IN1
ESP32 3.3V ─→ Relay VCC
ESP32 GND ─→ Relay GND

Baterai (+) ─→ Relay COM (Common)
Relay NO (Normally Open) ─→ Pompa (+)
Pompa (-) ─→ Baterai (-)
```

#### 7. LED Status & Indikator:
```
ESP32 Pin 4 ─→ R5 (220Ω) ─→ LED (+)
LED (-) ─→ ESP32 GND
```

### 🔧 Detail Komponen & Spesifikasi:

#### **Pembagi Tegangan (Voltage Divider):**
```
Formula: Vout = Vin × (R2 / (R1 + R2))
Dengan R1=10kΩ, R2=1kΩ:
Vout = Vin × (1kΩ / 11kΩ) = Vin × 0.091

Contoh:
- Panel Surya 18V → ADC = 18V × 0.091 = 1.64V ✓
- Baterai 14V → ADC = 14V × 0.091 = 1.27V ✓
- Maximum Safe: 24V → ADC = 24V × 0.091 = 2.18V ✓

Note: ESP32 ADC maksimum 3.3V, jadi aman untuk tegangan hingga 36V
```

#### **Sensor Arus ACS712-20A:**
```
Spesifikasi:
• Supply Voltage: 5V (tapi bisa 3.3V dengan akurasi berkurang)
• Sensitivity: 100mV/A
• Zero Current Output: VCC/2 (2.5V di 5V, 1.65V di 3.3V)
• Range: -20A to +20A
• Bandwidth: DC to 20kHz

Formula Konversi:
Arus (A) = (Vadc - Vzero) / Sensitivity
Dimana:
- Vadc = Tegangan yang dibaca ADC
- Vzero = 1.65V (untuk supply 3.3V)  
- Sensitivity = 0.1V/A
```

#### **Relay Module 4-Channel:**
```
Spesifikasi:
• Control Voltage: 3.3V - 5V
• Trigger: LOW (Active Low)
• Contact Rating: 10A 250VAC / 10A 30VDC
• Isolation: 1500V rms
• LED Indikator: Ya (per channel)

Wiring:
VCC ─→ ESP32 3.3V (atau 5V jika tersedia)
GND ─→ ESP32 GND
IN1 ─→ ESP32 Pin 2 (Control Signal)
```

### 🛡️ Proteksi & Keamanan:

#### **1. Proteksi Over-Voltage:**
```
Zener Diode 3V ─┬─→ ESP32 ADC Pin
                │
                └─→ R (1kΩ) ─→ GND
```

#### **2. Proteksi Reverse Polarity:**
```
Input (+) ─→ Diode 1N4007 ─→ Circuit (+)
Input (-) ─→ Circuit (-)
```

#### **3. Filter Noise:**
```
Signal ─→ R (100Ω) ─→ ADC Pin
              │
              C (100nF) ─→ GND
```

### 📋 BOM (Bill of Materials):

| No | Komponen | Qty | Spesifikasi | Fungsi |
|----|----------|-----|-------------|---------|
| 1  | ESP32 NodeMCU | 1 | 30-pin DevKit | Kontroller utama |
| 2  | ACS712-20A | 2 | Current sensor | Sensor arus |
| 3  | Resistor 10kΩ | 2 | 1/4W, 5% | Pembagi tegangan |
| 4  | Resistor 1kΩ | 2 | 1/4W, 5% | Pembagi tegangan |
| 5  | Resistor 220Ω | 1 | 1/4W, 5% | LED limiter |
| 6  | Resistor 100Ω | 4 | 1/4W, 5% | Filter noise |
| 7  | Kapasitor 100nF | 4 | Ceramic | Filter noise |
| 8  | Relay Module | 1 | 4-Channel, 10A | Switch pompa |
| 9  | LED 5mm | 1 | Red/Green | Status indikator |
| 10 | Diode 1N4007 | 2 | 1A, 1000V | Proteksi |
| 11 | Zener 3.3V | 2 | 1W | Over-voltage protect |
| 12 | Terminal Block | 4 | 2-pin, 10A | Koneksi |
| 13 | Breadboard/PCB | 1 | Full-size | Base |
| 14 | Jumper Wire | 1 set | Male-Male/Female | Koneksi |

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

#### **📚 Daftar Library yang Dibutuhkan:**

| No | Library Name | Version | Author | Fungsi |
|----|--------------|---------|---------|---------|
| 1  | **Blynk** | v1.3.2+ | Volodymyr Shymanskyy | IoT Communication |
| 2  | **WiFi** | Built-in | Espressif | WiFi Connection |
| 3  | **SimpleTimer** | v1.0.0+ | Marcello Romani | Timer Management |

#### **🔧 Cara Install Library Step-by-Step:**

**Method 1: Via Arduino IDE Library Manager (Recommended)**

1. **Buka Arduino IDE**
2. **Akses Library Manager:**
   ```
   Tools → Manage Libraries... (atau Ctrl+Shift+I)
   ```

3. **Install Blynk Library:**
   ```
   • Ketik "Blynk" di search box
   • Pilih "Blynk" by Volodymyr Shymanskyy  
   • Klik "Install"
   • Tunggu hingga selesai
   ```

4. **Install SimpleTimer Library:**
   ```
   • Ketik "SimpleTimer" di search box
   • Pilih "SimpleTimer" by Marcello Romani
   • Klik "Install" 
   • Tunggu hingga selesai
   ```

5. **Verifikasi WiFi Library:**
   ```
   WiFi library sudah built-in di ESP32 core
   Tidak perlu install terpisah
   ```

**Method 2: Manual Installation (Advanced)**

Jika method 1 gagal, download manual:

1. **Download Blynk Library:**
   ```
   URL: https://github.com/blynkkk/blynk-library/archive/master.zip
   Extract ke: Arduino/libraries/Blynk/
   ```

2. **Download SimpleTimer:**
   ```  
   URL: https://github.com/marcelloromani/SimpleTimer/archive/master.zip
   Extract ke: Arduino/libraries/SimpleTimer/
   ```

3. **Restart Arduino IDE**

#### **✅ Verifikasi Instalasi:**

Test dengan include statements:
```cpp
#include <WiFi.h>          // ✓ Built-in ESP32
#include <BlynkSimpleEsp32.h>  // ✓ Blynk Library  
#include <SimpleTimer.h>   // ✓ SimpleTimer Library
```

Jika tidak ada error merah, instalasi berhasil!

#### **📖 Library Dependencies Detail:**

**1. Blynk Library:**
```cpp
Files yang di-include:
• BlynkSimpleEsp32.h - Main Blynk ESP32 support
• Blynk/BlynkApi.h - Core Blynk API functions  
• Blynk/BlynkProtocol.h - Communication protocol
• Blynk/BlynkTimer.h - Built-in timer functions

Functions yang digunakan:
• Blynk.begin() - Initialize connection
• Blynk.run() - Main loop processor
• Blynk.virtualWrite() - Send data to app
• BLYNK_WRITE() - Receive data from app
```

**2. WiFi Library (ESP32 Built-in):**
```cpp
Files yang di-include:
• WiFi.h - Main WiFi functions
• WiFiClient.h - TCP client support
• WiFiServer.h - TCP server support

Functions yang digunakan:
• WiFi.begin() - Connect to network
• WiFi.status() - Check connection status  
• WiFi.localIP() - Get assigned IP
• WiFi.reconnect() - Reconnect if disconnected
```

**3. SimpleTimer Library:**
```cpp
Files yang di-include:
• SimpleTimer.h - Timer management

Functions yang digunakan:
• setInterval() - Set recurring timer
• run() - Execute timer callbacks
• deleteTimer() - Remove timer
• setTimeout() - One-time timer
```

#### **🚨 Troubleshooting Library Issues:**

**Problem 1: "Blynk.h not found"**
```
Solution:
1. Pastikan library terinstall di folder yang benar
2. Restart Arduino IDE  
3. Cek di File → Preferences → Sketchbook location
4. Manual install jika perlu
```

**Problem 2: "SimpleTimer.h not found"**  
```
Solution:
1. Download manual dari GitHub
2. Extract ke folder libraries
3. Restart Arduino IDE
4. Compile ulang sketch
```

**Problem 3: "WiFi.h not found"**
```
Solution:
1. Pastikan ESP32 board package terinstall
2. Pilih board "ESP32 Dev Module"
3. Update ESP32 core jika perlu
4. Reinstall ESP32 board package
```

**Problem 4: Compilation Errors**
```
Common Issues:
• Version incompatibility → Update semua library
• Missing dependencies → Install semua required library  
• Board not selected → Pilih "ESP32 Dev Module"
• Wrong port → Pilih COM port yang benar
```

#### **📊 Library Size & Memory Usage:**

| Library | Flash Usage | RAM Usage | Notes |
|---------|-------------|-----------|-------|
| Blynk | ~45KB | ~8KB | Core IoT functions |
| WiFi | ~25KB | ~4KB | Built-in, efficient |
| SimpleTimer | ~2KB | ~1KB | Lightweight timer |
| **Total** | **~72KB** | **~13KB** | Tersisa banyak space |

ESP32 Specifications:
- Flash Memory: 4MB (4096KB)  
- SRAM: 520KB
- Usage: <2% Flash, <3% RAM ✓

#### **🔄 Keep Libraries Updated:**

Untuk update library secara berkala:
```
1. Tools → Manage Libraries
2. Filter: "Updatable"  
3. Update library yang tersedia
4. Test kode setelah update
5. Rollback jika ada masalah
```

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
