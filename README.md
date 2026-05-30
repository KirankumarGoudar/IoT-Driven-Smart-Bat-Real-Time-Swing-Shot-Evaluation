# IoT-Driven-Smart-Bat-Real-Time-Swing-Shot-Evaluation

# 🏏 IoT-Driven Smart Bat for Real-Time Swing and Shot Evaluation

> An IoT-based intelligent cricket bat that uses embedded sensors and wireless connectivity to deliver real-time feedback on shot timing, impact location, and stroke classification — bringing data-driven coaching to every level of cricket.

---

## 📌 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Hardware Components](#hardware-components)
- [System Architecture](#system-architecture)
- [Block Diagram](#block-diagram)
- [Methodology](#methodology)
- [Shot Classification Logic](#shot-classification-logic)
- [Blynk Dashboard](#blynk-dashboard)
- [Getting Started](#getting-started)
- [Results](#results)
- [Applications](#applications)
- [Challenges](#challenges)
- [Future Works](#future-works)
- [References](#references)
- [Team](#team)
- [License](#license)

---

## Overview

Traditional cricket coaching relies on **subjective observation** and cannot reliably capture micro-errors in bat angle, timing, or body movement. This project solves that gap by embedding sensors directly into a cricket bat, enabling:

- **Objective, real-time feedback** on every shot
- **Impact location detection** — Top Edge / Center / Bottom Edge
- **Stroke classification** — Forward Drive, Right Cover Drive, Left Drive, or Bad Shot
- **Cloud-connected dashboard** via the Blynk IoT platform
- **Buzzer alert** for confirmed center hits

All of this runs on a **NodeMCU (ESP8266)** — affordable, portable, and usable at grassroots or academy level.

---

## Features

- 🎯 **Impact zone detection** — 3 microphone sensors along the blade (Top / Middle / Bottom)
- 🔄 **Stroke classification** — MPU6050 gyroscope + accelerometer for swing path analysis
- 📲 **Blynk IoT dashboard** — real-time results streamed over Wi-Fi to mobile app
- 🔔 **Buzzer feedback** — instant audio cue for center hits
- 🧠 **Training modes** — player selects Forward / Right / Left mode; system validates the stroke
- 🔋 **Li-ion/Li-Po powered** — regulated to 3.3V for long practice sessions
- 📦 **Compact enclosure** — shock-resistant housing mounted on the bat

---

## Hardware Components

| Component | Role |
|-----------|------|
| **NodeMCU (ESP8266)** | Main microcontroller + Wi-Fi connectivity |
| **MPU6050** | 6-axis IMU — accelerometer + gyroscope for motion analysis |
| **Microphone Sensors (×3)** | Ball impact detection at Top, Middle, Bottom of blade |
| **ADC (Built-in ESP8266)** | Converts analog mic signals to digital |
| **Buzzer** | Audio feedback for center hits |
| **Li-ion / Li-Po Battery** | Rechargeable power supply |
| **3.3V Voltage Regulator** | Stable power for NodeMCU and sensors |
| **Breadboard + Jumper Wires** | Prototyping connections |

---

## System Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    SENSOR LAYER                          │
│  Mic (Top) + Mic (Middle) + Mic (Bottom) + MPU6050       │
└────────────────────────┬─────────────────────────────────┘
                         │ Analog + I²C
                         ▼
┌──────────────────────────────────────────────────────────┐
│               PROCESSING LAYER (NodeMCU ESP8266)         │
│  Impact Detection → Shot Classification → Mode Matching  │
│  Buzzer Trigger → Blynk.virtualWrite()                   │
└────────────────────────┬─────────────────────────────────┘
                         │ Wi-Fi
                         ▼
┌──────────────────────────────────────────────────────────┐
│              CLOUD / DISPLAY LAYER                       │
│         Blynk IoT Mobile Dashboard                       │
│   Shot Style | Impact Zone | Feedback Message            │
└──────────────────────────────────────────────────────────┘
```

---

## Block Diagram

```
Ball Impact
    │
    ▼
┌───────────┐   ┌───────────┐   ┌───────────┐
│ Mic (Top) │   │Mic (Mid)  │   │Mic (Bot)  │
└─────┬─────┘   └─────┬─────┘   └─────┬─────┘
      └───────────────┼───────────────┘
                      │ Analog Signal
                      ▼
                   [ ADC ]
                      │
          ┌───────────▼───────────┐
          │   NodeMCU (ESP8266)   │◄──── Power Supply & Battery
          │  Classification Logic │
          └──────┬────────┬───────┘
                 │        │
          ┌──────▼──┐  ┌──▼──────────────────┐
          │  Buzzer │  │  Blynk IoT Interface │
          └─────────┘  │  (Wi-Fi Dashboard)   │
                       └──────────────────────┘
                              ▲
                    ┌─────────┴────────┐
                    │    MPU6050       │
                    │ (Accel + Gyro)   │
                    └──────────────────┘
```

---

## Methodology

```
Step 1 → Initialize NodeMCU, mic sensors, MPU6050, buzzer
         Connect to Blynk mobile app over Wi-Fi

Step 2 → Calibration
         Hold bat at rest → store baseline MPU6050 values as reference

Step 3 → Continuous sensing loop
         Read mic ADC values → detect ball impact
         Classify: Top Edge / Center Hit / Bottom Edge
         Trigger buzzer on Center Hit

Step 4 → Stroke mode selected by player on Blynk (Forward / Right / Left)
         Live MPU6050 data compared to predefined motion patterns
         Label stroke: ✅ Correct or ❌ Bad Shot

Step 5 → Push final result to Blynk dashboard
         Impact Type + Mode + Shot Correctness → player & coach view
```

---

## Shot Classification Logic

| MPU6050 Orientation | Mic Trigger | Classified As |
|---------------------|-------------|---------------|
| Forward pitch angle | Center mic  | ✅ Perfect Shot (Forward Drive) |
| Right tilt          | Any mic     | Right Cover Drive |
| Left tilt           | Any mic     | Left Drive |
| Mismatch with mode  | Any mic     | ❌ Bad Shot |
| Top mic dominant    | —           | Top Edge |
| Bottom mic dominant | —           | Bottom Edge |

---

## Blynk Dashboard

The Blynk mobile app displays the following virtual pins in real time:

| Virtual Pin | Widget | Displays |
|-------------|--------|---------- |
| V0 | Button | Calibration toggle |
| V1 | Button | Front shot mode |
| V2 | Button | Right shot mode |
| V3 | Button | Left shot mode |
| V4 | Label | Shot Style result |
| V5 | Label | Shot Placement (Top/Centre/Bottom) |
| V6 | Label | Feedback message |

---

## Getting Started

### Prerequisites

- Arduino IDE (v1.8.x or later)
- NodeMCU (ESP8266) board package installed
- Blynk app installed on your smartphone
- Required Arduino libraries:
  - `Blynk` — IoT dashboard communication
  - `MPU6050` / `Wire` — I²C motion sensor
  - `ESP8266WiFi` — Wi-Fi connectivity

### Installation

```bash
# Clone the repository
git clone https://github.com/your-username/smart-bat-iot.git
cd smart-bat-iot/src
# Open smart_bat.ino in Arduino IDE
```

### Configuration

Edit the following in `smart_bat.ino`:

```cpp
// Wi-Fi credentials
char ssid[] = "Your_WiFi_SSID";
char pass[] = "Your_WiFi_Password";

// Blynk authentication token (from Blynk app)
char auth[] = "Your_Blynk_Auth_Token";

// Impact detection threshold (tune per bat/sensor)
#define MIC_THRESHOLD   500
#define IMPACT_DEBOUNCE 300   // ms
```

### Upload

1. Connect NodeMCU via USB
2. Select board: `NodeMCU 1.0 (ESP-12E Module)` and correct COM port
3. Click **Upload**
4. Open Blynk app → select your project → press ▶ Play

---

## Results

The prototype successfully demonstrated:

- ✅ Real-time shot classification: **Perfect Shot / Right Cover Drive / Left Drive / Bad Shot**
- ✅ Impact zone detection: **Top Edge / Centre / Bottom Edge tapped**
- ✅ Buzzer trigger on confirmed center hits
- ✅ Live data streaming to Blynk dashboard with minimal latency over Wi-Fi
- ✅ Stable operation through multiple practice sessions on a full-size cricket bat

---

## Applications

- 🏏 **Cricket academies & clubs** — objective player evaluation and structured drills
- 🧑‍🏫 **Coaching** — remote monitoring of multiple students simultaneously
- 🤸 **Self-training** — personal digital coach without needing a coach present
- 🔬 **Sports science research** — live motion and impact data collection over cloud
- 🎓 **IoT/embedded education** — real-world sensor fusion and cloud integration demo

---

## Challenges

- **False trigger mitigation** — careful ADC threshold tuning, sensor positioning, and mechanical isolation to filter bat vibration and ambient noise
- **Wi-Fi stability on a moving bat** — iterative hardware placement and power management tuning to maintain low-latency Blynk connectivity during active swings
- **Compact form factor** — fitting NodeMCU, 3 mic sensors, MPU6050, and battery into a shock-resistant enclosure without affecting bat balance

---

## Future Works

- [ ] AI/ML-based stroke fault detection using stored session data (SVM / LSTM)
- [ ] Swing speed and impact force quantification
- [ ] Long-term performance tracking and player analytics dashboard
- [ ] Integration with smart gloves, shoes, stumps, and smart ball for a complete ecosystem
- [ ] AR/VR overlay and video-synchronization for coaching review
- [ ] Offline mode with SD card data logging for areas without Wi-Fi

---

## References

1. Deshmukh, P. & Kumar, S. — *Design and Implementation of a Smart Cricket Bat with Integrated Sensors and Wireless Data Transmission*, IJSRCSEIT, Vol. 8, Issue 5, pp. 133–145.
2. Agarwal, P. & D'Souza, S. N. — *Cricket Bat Swing Detection Based on 6-Axis IMU*, BMS Institute of Technology, Bengaluru, 2025.
3. Morishita, Y. & Jinji, T. — *Accuracy and Error Trends of Commercially Available Bat Swing Sensors in Baseball*, Sports, 2022. (PMC)
4. Sharma, M., Anand, A., Srivastava, R., & Kaligounder, L. — *Wearable Audio and IMU Based Shot Detection in Racquet Sports*, arXiv, 2018.
5. Fuss, F. K., Doljin, B., & Ferdinands, R. E. D. — *Mobile Computing with a Smart Cricket Ball: Discovery of Novel Performance Parameters*, Sensors, 2021.

---

## Team

| Name | USN |
|------|-----|
| Kirankumar Goudar | 1HK22EC072 |
| Melwin Jain | 1HK22EC088 |
| Mutturaj | 1HK22EC104 |
| Nithin Sadashiv Shipure | 1HK22EC117 |

**Batch No:** 28
**Department:** Electronics and Communication Engineering
**Institution:** HKBK College of Engineering, Nagavara, Bengaluru – 560045
**Course Code:** BEC786 — Major Project Phase 1

**Guide:** Prof. Shruthi B M, Assistant Professor, Dept. of ECE

---

## License

This project is developed for academic purposes under **HKBK College of Engineering, VTU**.
Free to fork and build upon for non-commercial, educational use.

---

<p align="center">
  Built with ❤️ for smarter cricket coaching | HKBKCE Bengaluru
</p>
