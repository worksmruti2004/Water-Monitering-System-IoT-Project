# Water-Monitering-System-IoT-Project
# 💧 Smart Water Quality Monitoring System

![ESP32](https://img.shields.io/badge/ESP32-IoT-blue?style=for-the-badge&logo=espressif)
![Arduino](https://img.shields.io/badge/Arduino-C++-teal?style=for-the-badge&logo=arduino)
![OLED](https://img.shields.io/badge/OLED-SSD1306-orange?style=for-the-badge)
![Sensors](https://img.shields.io/badge/Sensors-5%20Parameters-green?style=for-the-badge)

**Real-time water quality analysis using ESP32 — measures Temperature, TDS, Turbidity, pH, and Water Level with live OLED display output.**

---

## 📌 Overview

The **Smart Water Quality Monitoring System** is an embedded IoT project that continuously measures five critical water parameters using an ESP32 microcontroller. Sensor readings are displayed live on a **128×64 OLED screen** and logged to the Serial Monitor every 2 seconds.

Designed for applications such as:
- 🚰 Drinking water safety checks
- 🐟 Aquarium & fish tank monitoring
- 🌱 Hydroponics and irrigation systems
- 🏭 Industrial water quality control
- 🌊 Environmental water testing

---

## ✨ Features

- 🌡️ **Temperature Sensing** — Waterproof DS18B20 with OneWire protocol
- 🧪 **TDS Measurement** — Estimates dissolved solids (ppm) via polynomial formula
- 🌫️ **Turbidity Detection** — Measures water clarity in NTU
- 🔬 **pH Sensing** — Converts analog voltage to pH value (0–14 scale)
- 📏 **Water Level Detection** — Classifies level as LOW / MEDIUM / HIGH
- 🖥️ **OLED Live Display** — All readings shown on SSD1306 128×64 screen
- 🔁 **Auto Refresh** — Updates every 2 seconds continuously
- 📡 **Serial Logging** — Full data output at 115200 baud

---

## 🏗️ System Architecture

```
                    ┌──────────────────────────┐
                    │        ESP32 MCU          │
                    │                          │
  DS18B20       ──►│  GPIO 4  (OneWire)        │
  TDS Sensor    ──►│  GPIO 34 (ADC)            │
  Turbidity     ──►│  GPIO 35 (ADC)            │──► OLED Display (I2C)
  pH Sensor     ──►│  GPIO 32 (ADC)            │    SDA → GPIO 21
  Water Level   ──►│  GPIO 33 (ADC)            │    SCL → GPIO 22
                    │                          │
                    └──────────┬───────────────┘
                               │
                               ▼
                    ┌──────────────────────────┐
                    │   SSD1306 OLED Display    │
                    │  Temp | TDS | Turbidity  │
                    │  pH   | Water Level       │
                    └──────────────────────────┘
```

---

## 🔧 Hardware Required

| Component | Quantity | Purpose |
|---|---|---|
| ESP32 Dev Board | 1 | Main microcontroller |
| DS18B20 Temperature Sensor | 1 | Waterproof water temperature sensing |
| TDS Sensor Module | 1 | Measures dissolved solids (ppm) |
| Turbidity Sensor Module | 1 | Measures water clarity (NTU) |
| pH Sensor Module | 1 | Measures acidity / alkalinity |
| Water Level Sensor | 1 | Detects Low / Medium / High water level |
| SSD1306 OLED (128×64) | 1 | Live data display via I2C |
| 4.7kΩ Resistor | 1 | Pull-up for DS18B20 data line |
| Jumper Wires | — | Connections |
| Breadboard | 1 | Prototyping |
| 5V Power Supply | 1 | Power the system |

---

## 📌 Pin Configuration

| Sensor / Module | ESP32 GPIO Pin | Interface |
|---|---|---|
| DS18B20 (Temperature) | GPIO 4 | OneWire (Digital) |
| TDS Sensor | GPIO 34 | ADC (Analog) |
| Turbidity Sensor | GPIO 35 | ADC (Analog) |
| pH Sensor | GPIO 32 | ADC (Analog) |
| Water Level Sensor | GPIO 33 | ADC (Analog) |
| OLED SDA | GPIO 21 | I2C |
| OLED SCL | GPIO 22 | I2C |

> ⚠️ **GPIO 34 & 35 are input-only pins** — do not use them as output. All analog sensors must be powered with **3.3V** (ESP32 ADC is 3.3V max — not 5V tolerant).

---

## 📦 Libraries Required

Install via Arduino IDE → **Sketch → Include Library → Manage Libraries**:

| Library | Install Name | Purpose |
|---|---|---|
| Adafruit GFX Library | `Adafruit GFX Library` | Graphics core for OLED |
| Adafruit SSD1306 | `Adafruit SSD1306` | OLED display driver |
| OneWire | `OneWire` | Communication protocol for DS18B20 |
| DallasTemperature | `DallasTemperature` | Reads DS18B20 temperature data |

> `Wire.h` is built into the ESP32 Arduino core — no separate install needed.

---

## 🛠️ Software Setup

### Prerequisites

- [Arduino IDE](https://www.arduino.cc/en/software) (v1.8+ or v2.x)
- ESP32 board package installed

### Add ESP32 Board to Arduino IDE

1. Open **File → Preferences**
2. In *Additional Board Manager URLs*, paste:
   ```
   https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
   ```
3. Go to **Tools → Board → Boards Manager**, search `esp32`, and install it
4. Select: **Tools → Board → ESP32 Dev Module**

### Installation

1. **Clone this repository:**
   ```bash
   git clone https://github.com/your-username/Smart-Water-Quality-Monitor.git
   cd Smart-Water-Quality-Monitor
   ```

2. **Open the sketch:**
   Open `water_monitor.ino` in Arduino IDE

3. **Select the correct Port:**
   Tools → Port → (your ESP32 COM port)

4. **Upload** the sketch to your ESP32

5. **Open Serial Monitor** at `115200` baud to view live readings

---

## 🔄 How It Works

```
1. Loop Starts Every 2 Seconds
          │
          ▼
2. DS18B20 → requestTemperatures() → getTempCByIndex(0)
          │
          ▼
3. TDS Sensor → analogRead(GPIO34)
   Voltage = raw × (3.3 / 4095)
   TDS (ppm) = (133.42×V³ − 255.86×V² + 857.39×V) × 0.5
          │
          ▼
4. Turbidity Sensor → analogRead(GPIO35)
   NTU = map(raw, 0, 4095, 3000, 0)
          │
          ▼
5. pH Sensor → analogRead(GPIO32)
   Voltage = raw × (3.3 / 4095)
   pH = 7 + ((2.5 − Voltage) / 0.18)
          │
          ▼
6. Water Level → analogRead(GPIO33)
   ┌─────────────────────────────────────┐
   │  raw < 1000   →  "LOW"             │
   │  1000 – 2500  →  "MEDIUM"          │
   │  > 2500       →  "HIGH"            │
   └─────────────────────────────────────┘
          │
          ▼
7. Print All Values → Serial Monitor
          │
          ▼
8. Render All Values → SSD1306 OLED (I2C 0x3C)
          │
          ▼
9. delay(2000) → Loop Repeats
```

---

## 📊 Sensor Measurement Ranges

### 🌡️ Temperature (DS18B20)
| Range | Meaning |
|---|---|
| 0–15 °C | Cold water |
| 20–30 °C | Normal / room temperature |
| > 35 °C | Warm / potentially unsafe |

### 🧪 TDS — Total Dissolved Solids
| Range (ppm) | Water Quality |
|---|---|
| 0–50 | Excellent — Near pure |
| 50–150 | Good — Safe drinking water |
| 150–500 | Fair — Acceptable |
| 500+ | Poor — Needs treatment |

### 🌫️ Turbidity
| Range (NTU) | Water Clarity |
|---|---|
| 0–1 | Crystal clear |
| 1–5 | Acceptable |
| 5–50 | Cloudy — Filter recommended |
| 50+ | Very turbid — Unsafe |

### 🔬 pH
| Range | Classification |
|---|---|
| < 6.5 | Acidic |
| 6.5–7.5 | Neutral (ideal drinking water) |
| 7.5–8.5 | Mildly Alkaline |
| > 8.5 | Highly Alkaline |

### 📏 Water Level
| ADC Raw Value | Status |
|---|---|
| < 1000 | 🔴 LOW |
| 1000 – 2500 | 🟡 MEDIUM |
| > 2500 | 🟢 HIGH |

---

## 🖥️ Serial Monitor Output Example

```
----- WATER MONITORING -----
Temperature: 28.50 C
TDS: 342.00 ppm
Turbidity: 120 NTU
pH: 7.2
Water Level: MEDIUM
```

---

## 📂 Project Structure

```
Smart-Water-Quality-Monitor/
├── water_monitor/
│   └── water_monitor.ino       # Main Arduino sketch
├── docs/
│   ├── circuit_diagram.png     # Wiring diagram
│   └── oled_output.png         # OLED display photo
├── README.md                   # Project documentation
└── LICENSE                     # MIT License
```

---

## ⚠️ Important Notes

- All analog sensor modules must use **3.3V** — ESP32 ADC pins are **not 5V tolerant**
- DS18B20 requires a **4.7kΩ pull-up resistor** between DATA and VCC
- pH readings are approximate — **calibrate** with pH 4, 7, and 10 buffer solutions for accuracy
- OLED I2C address defaults to `0x3C` — change in code if your display uses `0x3D`
- For better stability, consider averaging **5–10 ADC samples** per sensor per cycle to reduce noise

---

## 🔮 Future Improvements

- [ ] Add Wi-Fi and push data to Blynk / ThingSpeak / Firebase
- [ ] Alert notifications (buzzer / LED) when readings go out of safe range
- [ ] Store historical data on a MicroSD card
- [ ] Battery-powered + waterproof enclosure for field deployment
- [ ] Multi-sample ADC averaging for improved accuracy
- [ ] Mobile dashboard via MQTT protocol

---

## 👥 Author

Developed as part of an embedded IoT project for real-time water quality analysis.

**BY :-**<br>
**Smruti Ranjan Rout**<br>
**Electronics and Communication Engineering Student**<br>
**Embedded Systems & IoT Enthusiast**

*Feel free to ⭐ star this repo if you found it helpful!*

---

## 🌟 Acknowledgements

- [Adafruit SSD1306 Library](https://github.com/adafruit/Adafruit_SSD1306)
- [DallasTemperature Library](https://github.com/milesburton/Arduino-Temperature-Control-Library)
- [ESP32 Arduino Core](https://github.com/espressif/arduino-esp32)

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).

---

> Built with ❤️ using ESP32 and Arduino
