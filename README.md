# 🤖 Robo-Car: Autonomous FSM-Controlled Mechatronics System

> A highly sophisticated, fully autonomous robot control system engineered from scratch for the ENTC Robot Design and Competition 2025. Featuring a completely non-blocking C++ firmware architecture, closed-loop PID motor correction, Finite State Machine (FSM) task management, and real-time OLED system diagnostics.

![Build Status](https://img.shields.io/badge/Status-Completed-success)
![Hardware](https://img.shields.io/badge/Hardware-Arduino%20Mega%202560-blue)
![Framework](https://img.shields.io/badge/Framework-PlatformIO%20%7C%20C%2B%2B-orange)
![Control](https://img.shields.io/badge/Control-Closed--Loop%20%7C%20FSM-yellow)

## 📌 The Challenge: ENTC 2025
Operating completely autonomously within strict 250mm x 250mm dimensional constraints, this robot was engineered to navigate a grueling, multi-stage obstacle course without any external communication or human intervention. The mission profile included:
1. **The Labyrinth of Shadows:** Complex 8x8 grid solving and dynamic obstacle avoidance.
2. **The Ramp of Trials:** Carrying physical blocks to bridge gaps and navigate steep 20-degree inclines.
3. **The Oracle of Runes:** Floor-based barcode scanning and logic decoding.
4. **The Circular Fortress:** Advanced wall-following and autonomous ping-pong ball collection.
5. **The Pendulum of Doom:** Precision timing algorithms to dodge a swinging pendulum.
6. **The Ring of Triumph:** A final projectile shooting task to secure victory.

---

## 🚀 Core Software Innovations
Conquering a 6-stage arena requires a computational brain that can multitask without freezing. Standard `delay()` functions cause catastrophic failures in dynamic environments. This firmware was built from the ground up to eliminate blocking code entirely.

* ✅ **100% Non-Blocking Architecture:** Sensor polling, motor updating, and display rendering run asynchronously.
* ✅ **Finite State Machine (FSM):** Highly flexible, state-based task management ensuring robust execution and easy debugging.
* ✅ **Closed-Loop Kinematics:** Automatic straight-line drift correction utilizing proportional-integral-derivative (PID) control concepts ($kP = 1.5$).
* ✅ **Dual Quadrature Encoding:** Interrupt-based highly accurate wheel tracking for precision odometry and grid navigation.
* ✅ **I2C OLED Dashboard:** Real-time state and diagnostic monitoring via a 128x64 SSD1306 display.

---

## ⚙️ Mechanical & Hardware Engineering
Beyond the software, the physical robot was custom-designed to handle the physical abuse of the arena.

* **Chassis Design:** Custom-lasered multi-tier acrylic chassis for optimized weight distribution and modular component mounting.
* **Actuation Mechanisms:** Custom 3D-printed grabbing and shooting mechanisms designed specifically for manipulating the "Crystals of Power" (blocks) and "Magical Spheres" (ping pong balls).
* **Power Distribution:** Isolated power rails for logic (Arduino Mega) and high-current actuators (Motors) to prevent voltage drops and logic resets during heavy load spikes on the ramp.

### Hardware Wiring Reference

#### Locomotion (Motors)
| Component | Arduino Pin | Function |
| :--- | :--- | :--- |
| **Motor A (Left)** | 4 | Forward |
| | 5 | Backward |
| | 6 | Enable (PWM) |
| **Motor B (Right)** | 7 | Forward |
| | 8 | Backward |
| | 9 | Enable (PWM) |

#### Odometry (Encoders)
| Component | Arduino Pin | Interrupt |
| :--- | :--- | :--- |
| **Encoder A (Left)** | 2 | INT0 |
| | 18 | INT5 |
| **Encoder B (Right)**| 3 | INT1 |
| | 19 | INT4 |

---

## 🧠 Deep Dive: The Finite State Machine (FSM)
Instead of linear, procedural code, the robot's brain operates as a state machine. It evaluates its current objective, monitors its sensors, and transitions smoothly without halting the main CPU loop.

### Core Execution States
| State Enum | Description | Transition Trigger |
| :--- | :--- | :--- |
| `IDLE` | Motors off, awaiting command. | External input or completion of previous task. |
| `MOVING_FORWARD` | Applying forward PWM with active drift correction. | Encoder ticks match target distance parameter. |
| `TURNING` | Differential drive pivoting (Left/Right/180). | Encoder ticks match calculated arc length. |
| `BARCODE_READING`| specialized low-speed mode scanning floor sensors. | 5th bit detected or end-of-line reached. |
| `WALL_FOLLOWING` | Adjusting lateral trajectory based on distance sensors. | Gap detected in the circular fortress wall. |
| `ERROR` | Failsafe mode if wheels slip or sensors timeout. | System reset or self-correction subroutine. |

---

## 💻 Usage & Implementation Examples

### 1. Closed-Loop Straight Line Correction
The motors actively monitor their relative speeds via the interrupts. If the left motor encounters resistance and slows down, the algorithm automatically throttles the right motor down to maintain a perfectly straight trajectory.

```cpp
// Inside motor.h update loop
float error = getMotorASpeed() - getMotorBSpeed();
int correction = error * kP; // Proportional gain

applyPWM(LEFT_MOTOR, baseSpeed + correction);
applyPWM(RIGHT_MOTOR, baseSpeed - correction);
