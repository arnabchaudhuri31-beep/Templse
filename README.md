# 🌡️ Templse: Cloud Integrated Health Monitoring System
## 🩺 Overview

**Templse** (Temperature + Pulse) is a real-time, contactless IoT-based health monitoring system that measures **body temperature** and **heart rate (BPM)** using an **ESP32** microcontroller.  
The system integrates **cloud connectivity (Blynk & ThingSpeak)** for live data streaming and visualization, while providing **on-device OLED feedback** with error handling and alert mechanisms.

This repository contains the final firmware  and related resources that implement the methods detailed in the published IEEE paper.

---

## ⚙️ Features

- 🔥 **Dual Cloud Uploads**  
  Real-time data transmission to **ThingSpeak (HTTP)** and **Blynk.Cloud (MQTT)** for visualization and remote access.

- 💓 **Accurate Heart Rate Estimation**  
  Implements adaptive thresholding, moving-average smoothing, and signal validation to achieve ±2–3 BPM accuracy.

- 🌡️ **Contactless Temperature Measurement**  
  Utilizes the **MLX90614 IR sensor** for non-invasive, hygienic measurement with ±0.3 °C accuracy.

- 🖥️ **On-Device Display & Alerts**  
  Displays readings on an **OLED (SSD1306)**, with LEDs and buzzer feedback for fever detection.

- 🧠 **Error Handling & Recovery**  
  Built-in logic for timeout recovery, invalid (NaN) data filtering, Wi-Fi connection checks, and automatic system reset.

- 🕒 **Asynchronous Task Scheduling**  
  Employs `millisDelay` to manage sensor reading, display, and network updates concurrently without blocking.

---

## 🧩 System Architecture
[MLX90614 IR Sensor] ─┐
├──> ESP32 DevKit V1 ───> OLED Display
[PPG Pulse Sensor] ───┘ │
├──> LEDs + Buzzer
├──> ThingSpeak (HTTP REST API)
└──> Blynk.Cloud (MQTT Virtual Pins)


---

## 🧱 Hardware Components

| Component | Description |
|------------|-------------|
| **ESP32 DevKit V1** | Core MCU handling data processing and Wi-Fi transmission |
| **GY-906 MLX90614ESF** | Non-contact infrared temperature sensor |
| **SEN-11574 Pulse Sensor** | PPG-based heart rate sensor |
| **0.96″ OLED (SSD1306)** | Real-time temperature and BPM display |
| **LEDs (Red/Green) + Buzzer** | Visual and audible alert system |
| **Power Supply** | Dual 18650 Li-ion cells, TP4056 charger, MT3608 boost converter |

---

## 🧠 Firmware Overview

### 👨‍💻 Language & Platform
- Written in **C/C++** using the **Arduino Framework**
- Compatible with **ESP32 DevKit V1**

### 📁 Core Modules
| Module | Description |
|---------|--------------|
| **Sensor Handler** | Reads data from MLX90614 and PPG sensors |
| **Signal Processor** | Filters, thresholds, and stabilizes BPM |
| **Error Manager** | Handles timeouts, NaNs, and resets |
| **Display Controller** | Updates OLED, LEDs, and buzzer |
| **Cloud Connector** | Sends data via ThingSpeak (HTTP) & Blynk (MQTT) |

---

## 🧮 Algorithmic Highlights

| Process | Technique | Function |
|----------|------------|----------|
| **Baseline Drift Removal** | Running min/max tracking | Removes low-frequency drift in PPG |
| **Adaptive Thresholding** | Dynamic mid-level detection | Ensures consistent beat recognition |
| **Moving Average Filter** | 10-sample rolling buffer | Smooths BPM readings |
| **Peak Validation** | Range-limited acceptance | Rejects false spikes (<40 BPM / >200 BPM) |
| **Timeout Reset** | 3 s measurement timeout | Prevents system hang on faulty reads |

---

## ☁️ Cloud Integration

### 🔗 **ThingSpeak (HTTP REST)**
- Updates via `HTTPClient`:

- Stores historical trends for analysis.
- Latency: ~1–2 seconds per upload.

### 🔗 **Blynk.Cloud (MQTT)**
- Virtual pins used:  
`V0` → Temperature, `V1` → BPM  
- Live dashboard on mobile/web with real-time telemetry updates.
- Maintains persistent connection via `Blynk.run()` loop.

---

## 📊 Performance Summary

| Metric | Observed Performance | Reference |
|---------|----------------------|------------|
| **Temperature Accuracy** | ± 0.3 °C | Digital thermometer |
| **Heart Rate Accuracy** | ± 2–3 BPM | Clinical oximeter |
| **Cloud Upload Delay** | < 2 s | Wi-Fi (2.4 GHz) |
| **Stability Lag** | 5–10 s | Moving-average filter |

---

## 🧰 Setup Guide

1. **Clone the repository:**
2. Install Arduino Libraries:
  WiFi.h
  HTTPClient.h
  Adafruit_MLX90614.h
  Adafruit_SSD1306.h
  millisDelay.h

BlynkSimpleEsp32.h
3. Configure Credentials:
  const char* ssid = "YOUR_WIFI";
  const char* password = "YOUR_PASSWORD";
  #define BLYNK_AUTH_TOKEN "YOUR_BLYNK_TOKEN"
  const String apiKey = "YOUR_THINGSPEAK_API_KEY";

4. Upload Firmware:
   Open the .ino file → Select ESP32 DevKit V1 board → Upload → Open Serial Monitor at 115200 baud.

5. Monitor Cloud Dashboards:
   View real-time data on Blynk App
   View long-term trends on ThingSpeak Channel


