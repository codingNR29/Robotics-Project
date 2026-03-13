# 🤖 Robo-Car: Autonomous FSM-Controlled Mechatronics System

> [cite_start]A highly sophisticated, fully autonomous robot control system built for the ENTC Robot Design and Competition 2025[cite: 593, 594, 595, 757]. Featuring a non-blocking C++ architecture, closed-loop PID motor control, Finite State Machine (FSM) task management, and real-time OLED diagnostics.

![Build Status](https://img.shields.io/badge/Status-Completed-success)
![Hardware](https://img.shields.io/badge/Hardware-Arduino%20Mega%202560-blue)
![Framework](https://img.shields.io/badge/Framework-PlatformIO%20%7C%20C%2B%2B-orange)
![Control](https://img.shields.io/badge/Control-Closed--Loop%20%7C%20FSM-yellow)

## 📌 The Challenge: ENTC 2025
[cite_start]Operating completely autonomously [cite: 757] [cite_start]within strict 250mm x 250mm dimensional constraints [cite: 761][cite_start], this robot was engineered to navigate a grueling 6-stage obstacle course[cite: 608]:
1. [cite_start]**The Labyrinth of Shadows:** Complex 8x8 grid solving and obstacle avoidance[cite: 614, 622].
2. [cite_start]**The Ramp of Trials:** Carrying blocks to bridge gaps and navigate steep inclines[cite: 644, 646].
3. [cite_start]**The Oracle of Runes:** Floor-based barcode scanning and decoding[cite: 677, 680].
4. [cite_start]**The Circular Fortress:** Wall-following and autonomous ping-pong ball collection[cite: 691, 700].
5. [cite_start]**The Pendulum of Doom:** Precision timing to dodge a swinging pendulum[cite: 704, 705, 708].
6. [cite_start]**The Ring of Triumph:** Projectile shooting task to secure the final victory[cite: 724, 725, 727].

---

## 🚀 Core Features
Conquering a multi-stage arena requires a brain that can multitask without freezing. This firmware was built from the ground up to eliminate `delay()` functions entirely.

* ✅ **Non-Blocking Architecture:** All operations (sensors, motors, display) run asynchronously.
* ✅ **Finite State Machine (FSM):** Highly flexible, state-based task management ensuring robust execution.
* ✅ **Closed-Loop Motor Control:** Automatic straight-line drift correction utilizing proportional control ($kP = 1.5$).
* ✅ **Dual Quadrature Encoding:** Interrupt-based highly accurate wheel tracking for precision odometry.
* ✅ **I2C OLED Dashboard:** Real-time state and diagnostic monitoring via a 128x64 SSD1306 display.
* ✅ **Grid-Based Navigation:** Custom high-level movement functions mapped directly to the arena's layout.

---

## ⚙️ Hardware Architecture & Wiring

### Locomotion (Motors)
| Component | Arduino Pin | Function |
| :--- | :--- | :--- |
| **Motor A (Left)** | 4 | Forward |
| | 5 | Backward |
| | 6 | Enable (PWM) |
| **Motor B (Right)** | 7 | Forward |
| | 8 | Backward |
| | 9 | Enable (PWM) |

### Odometry (Encoders)
| Component | Arduino Pin | Interrupt |
| :--- | :--- | :--- |
| **Encoder A (Left)** | 2 | INT0 |
| | 18 | INT5 |
| **Encoder B (Right)**| 3 | INT1 |
| | 19 | INT4 |

### User Interface (Display)
| Component | Arduino Pin | Notes |
| :--- | :--- | :--- |
| **OLED (SSD1306)** | 20 (SDA) | I2C Address: `0x3C` |
| | 21 (SCL) | |

---

## 🧠 Software Architecture

### Core Components Directory
```text
include/
├── Encoders.h       # Interrupt-based encoder reading and velocity tracking
├── motor.h          # Low-level motor drivers with closed-loop PID correction
├── Navigation.h     # High-level grid movement and rotation logic
├── RobotFSM.h       # The Finite State Machine brain
└── Display.h        # Non-blocking OLED UI rendering
