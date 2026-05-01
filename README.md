# Water-Monitoring-System-IoT-Project
# 💧 Smart Water Quality Monitoring System

![ESP32](https://img.shields.io/badge/ESP32-IoT-blue?style=for-the-badge&logo=espressif)
![Arduino](https://img.shields.io/badge/Arduino-C++-teal?style=for-the-badge&logo=arduino)
![OLED](https://img.shields.io/badge/OLED-SSD1306-orange?style=for-the-badge)
![Sensors](https://img.shields.io/badge/Sensors-5%20Parameters-green?style=for-the-badge)

**Real-time water quality analysis using ESP32 — measures Temperature, TDS, Turbidity, pH, and Water Depth with live OLED display output.**

---

## 📌 Overview

The **Smart Water Quality Monitoring System** is an embedded IoT project that continuously measures five critical water parameters using an ESP32 microcontroller. Sensor readings are displayed live on a **128×64 OLED screen** and logged to the Serial Monitor.

Designed for applications such as:
- 🚰 Drinking water safety checks
- 🐟 Aquarium & fish tank monitoring
- 🌱 Hydroponics and irrigation systems
- 🏭 Industrial water quality control
- 🌊 Environmental water testing

---

## ✨ Features

- 🌡️ **Temperature Sensing** — Waterproof DS18B20 with OneWire protocol
- 🧪 **TDS Measurement** — Estimates dissolved solids (ppm) via cubic polynomial formula (40-sample average)
- 🌫️ **Turbidity Detection** — Measures water clarity in NTU via voltage-based formula
- 🔬 **pH Sensing** — Converts analog voltage to pH value (0–14 scale) with 20-sample averaging
- 📏 **Water Level / Depth** — Live distance measurement via waterproof ultrasonic sensor (JSN-SR04T / A02YYUW) on GPIO 5 & 18, with calibration factor applied
- 🖥️ **OLED Live Display** — All readings shown sequentially on SSD1306 128×64 screen
- 🔁 **Auto Refresh** — Cycles through all parameters continuously
- 📡 **Serial Logging** — Full data output at 115200 baud

---

## 🏗️ System Architecture

```
                    ┌──────────────────────────┐
                    │        ESP32 MCU          │
                    │                          │
  DS18B20       ──►│  GPIO 4  (OneWire)        │
  TDS Sensor    ──►│  GPIO 32 (ADC)            │
  Turbidity     ──►│  GPIO 35 (ADC)            │──► OLED Display (I2C)
  pH Sensor     ──►│  GPIO 34 (ADC)            │    SDA → GPIO 21
  Ultrasonic     ◄─│  GPIO 5  (TRIG)           │    SCL → GPIO 22
  (JSN-SR04T)   ──►│  GPIO 18 (ECHO)           │
                    └──────────┬───────────────┘
                               │
                               ▼
                    ┌──────────────────────────┐
                    │   SSD1306 OLED Display    │
                    │  TDS | Turbidity | Level  │
                    │  Temp | pH                │
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
| Waterproof Ultrasonic Sensor (JSN-SR04T or A02YYUW) | 1 | Measures water level / depth (cm) |
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
| TDS Sensor | GPIO 32 | ADC (Analog) |
| Turbidity Sensor | GPIO 35 | ADC (Analog) |
| pH Sensor | GPIO 34 | ADC (Analog) |
| Ultrasonic TRIG (JSN-SR04T / A02YYUW) | GPIO 5 | Digital Output |
| Ultrasonic ECHO (JSN-SR04T / A02YYUW) | GPIO 18 | Digital Input |
| OLED SDA | GPIO 21 | I2C |
| OLED SCL | GPIO 22 | I2C |

> ⚠️ **GPIO 34 & 35 are input-only pins** — do not use them as output. All analog sensors must be powered with **3.3V** (ESP32 ADC is 3.3V max — not 5V tolerant). ADC resolution is set to **12-bit** with **11dB attenuation** in code.

> 📡 **Ultrasonic Sensor — Use Waterproof Variants Only:** Standard HC-SR04 modules are not suitable for water level sensing. Use waterproof models such as the **JSN-SR04T** or **A02YYUW**, which are sealed for wet/submerged-surface environments. These sensors work by emitting a 40kHz pulse from the TRIG pin and measuring the echo return time on the ECHO pin. Pressure transducers are an alternative for direct depth measurement in closed tanks.

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
Loop Starts
    │
    ▼
1. TDS Sensor → 40 analogRead samples from GPIO 32 (10ms apart)
   Average ADC → Voltage = avg × (3.3 / 4095)
   TDS (ppm) = (133.42×V³ − 255.86×V² + 857.39×V) × 0.5
    │
    ▼
2. Turbidity Sensor → 40 analogRead samples from GPIO 35 (10ms apart)
   Average ADC → Voltage = avg × (3.3 / 4095)
   NTU = (1.67 − Voltage) × 300    [clamped to 0 minimum]
    │
    ▼
3. Water Level → Waterproof Ultrasonic Sensor (JSN-SR04T / A02YYUW)
   TRIG (GPIO 5) → 10µs HIGH pulse → sensor fires 40kHz burst
   ECHO (GPIO 18) → pulseIn() measures return time in microseconds
   Raw distance (cm) = duration × 0.0343 / 2
   Corrected distance (cm) = raw × 0.65   [calibration factor]
   Result printed to Serial Monitor every 500ms
    │
    ▼
4. DS18B20 → requestTemperatures() → getTempCByIndex(0)
    │
    ▼
5. pH Sensor → 20 analogRead samples from GPIO 34 (10ms apart)
   Average ADC → Voltage = avg × (3.3 / 4095)
   pH = 7 + ((2.50 − Voltage) × 3.5)    [clamped 0–14]
    │
    ▼
6. Each parameter printed to Serial Monitor
   Each parameter rendered on SSD1306 OLED (I2C 0x3C)
   1-second delay between each parameter display
    │
    ▼
Loop Repeats
```

---

## 📊 Sensor Measurement Ranges & Quality Labels

### 🧪 TDS — Total Dissolved Solids (GPIO 32, 40 samples)

| Range (ppm) | Quality Label |
|---|---|
| 0 – 50 | PURE |
| 51 – 150 | EXCELLENT |
| 151 – 300 | GOOD |
| 301 – 500 | ACCEPT |
| 501 – 900 | POOR |
| > 900 | BAD |

### 🌫️ Turbidity (GPIO 35, 40 samples)

Formula: `NTU = (1.67 − Voltage) × 300`

| Range (NTU) | Quality Label |
|---|---|
| 0 – 1 | VERY CLEAR |
| 2 – 5 | GOOD |
| 6 – 10 | ACCEPTABLE |
| 11 – 20 | CLOUDY |
| 21 – 50 | DIRTY |
| > 50 | VERY DIRTY |

### 📏 Water Level — Ultrasonic Sensor (TRIG: GPIO 5 | ECHO: GPIO 18)

The system uses a **waterproof ultrasonic sensor** (JSN-SR04T or A02YYUW) to measure water surface distance in real time. These sealed sensors are specifically designed for wet environments and detect surfaces via reflected 40kHz sound pulses — unlike standard HC-SR04 modules which are not water-safe.

**Distance Formula:**
```
Raw Distance (cm)       = duration × 0.0343 / 2
Corrected Distance (cm) = Raw Distance × 0.65
```
The `0.65` calibration factor compensates for sensor mounting angle, container geometry, or measured offset — adjust this value to match your physical setup.

**Reading interval:** 500ms

**Alternative:** Pressure transducers can be used instead of ultrasonic sensors for direct depth measurement in sealed or turbulent tanks where acoustic reflections may be unreliable.

### 🌡️ Temperature (DS18B20, GPIO 4)

Raw temperature in °C is displayed directly — no quality classification applied.

### 🔬 pH (GPIO 34, 20 samples)

Formula: `pH = 7 + ((2.50 − Voltage) × 3.5)`

| Range | Classification |
|---|---|
| 0.0 – 2.9 | VERY ACIDIC |
| 3.0 – 4.9 | ACIDIC |
| 5.0 – 6.4 | SLIGHT ACID |
| 6.5 – 7.5 | GOOD |
| 7.6 – 8.5 | SLIGHT ALK |
| 8.6 – 10.0 | ALKALINE |
| > 10.0 | VERY ALK |

---

## 🖥️ Serial Monitor Output Example

```
TDS: 342.00 ppm -> ACCEPT
Voltage: 1.45 V  Turbidity: 6.00 NTU -> ACCEPTABLE
Corrected Distance: 4.5 cm
Temperature: 28.50 C
pH Value: 7.20 -> GOOD
```

---

## 🖥️ OLED Display Sequence

Each parameter is shown full-screen for 1 second in this order:

| Screen | Content Shown |
|---|---|
| 1 | **TDS** — value in ppm + quality label |
| 2 | **TURBIDITY** — value in NTU + quality label |
| 3 | **WATER LEVEL** — corrected distance in cm (ultrasonic) |
| 4 | **TEMP** — temperature in °C |
| 5 | **PH** — value + status label |

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
- ADC is configured for **12-bit resolution** (`analogReadResolution(12)`) and **11dB attenuation** (`analogSetAttenuation(ADC_11db)`) — do not change these without recalculating sensor formulas
- pH readings are approximate — **calibrate** with pH 4, 7, and 10 buffer solutions and adjust the formula constants for your specific module
- OLED I2C address is set to `0x3C` — change in code if your display uses `0x3D`
- TDS averaging uses **40 samples**, pH uses **20 samples** — increase for better noise rejection in electrically noisy environments
- Use **waterproof ultrasonic sensors only** (JSN-SR04T or A02YYUW) — standard HC-SR04 modules will be damaged by moisture and water splash
- The **calibration factor of 0.65** in the distance formula is specific to the current physical setup — adjust it to match your sensor's mounting height and tank geometry
- Ensure the ultrasonic sensor beam path is **clear and unobstructed** — foam, turbulence, or angled surfaces can cause inaccurate echo readings; pressure transducers are a more reliable alternative in such conditions

---

## 🔮 Future Improvements

- [ ] Add water level classification (LOW / MEDIUM / HIGH) based on distance thresholds
- [ ] Multi-sample averaging for ultrasonic readings to reduce noise
- [ ] Add Wi-Fi and push data to Blynk / ThingSpeak / Firebase
- [ ] Alert notifications (buzzer / LED) when readings go out of safe range
- [ ] Store historical data on a MicroSD card
- [ ] Battery-powered + waterproof enclosure for field deployment
- [ ] Temperature compensation for TDS readings
- [ ] Mobile dashboard via MQTT protocol

---

## 👥 Author

Developed as part of an embedded IoT project for real-time water quality analysis.

**BY :-**  
**Smruti Ranjan Rout**  
**Electronics and Communication Engineering Student**  
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
