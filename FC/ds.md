Below is your **re-ordered, “top-brand style” README** (SpeedyBee-like: hero → proof bullets → feature tiles → specs → wiring → box contents → setup). This structure mirrors what the big guys do on their product pages: punchy “why it matters” blocks first, then the trust-building spec tables later. ([SpeedyBee][1])

I also fixed a couple of wording traps so you don’t accidentally promise something that can bite you later.

---

# ⚡ FlySpark H743 Pro Flight Controller Stack

**H7 performance FC stack built for X8 cinelifters, long-range, VTOL, and heavy-lift builds.**
Brand: **FlySpark** • Website: `https://flyspark.in`

---

## ✅ At a Glance

| Core                   | Sensors                                 | Power                                              | Video                                | I/O                                      |
| ---------------------- | --------------------------------------- | -------------------------------------------------- | ------------------------------------ | ---------------------------------------- |
| **STM32H743 @ 480MHz** | **Dual ICM42688 IMUs** + **SPL06 baro** | **4–12S VBAT** + **5V 3A** + **12V 3A (VTX rail)** | **Dual camera in** + RC switch + OSD | **Up to 8 UARTs** + **CAN** + I²C + RSSI |

---

## 🚀 Why This Stack Exists

Big frames don’t fail because “CPU is slow.” They fail because of **noise, power events, messy wiring, and weak expandability** when you add GPS/compass, dual cameras, servos, and telemetry.

**H743 Pro is built for that reality:**

* **Redundant motion sensing (dual IMU)** for cleaner attitude estimation under vibration.
* **Serious power rails** for camera/VTX + peripherals.
* **Enough ports** to run a full “mission build” without hacks.

---

## 🔥 Highlights That Actually Matter

### 🧠 Processing & Logging

* **STM32H743 (Cortex-M7) @ 480MHz**
* **16MB onboard flash** for Blackbox / firmware logs

### 🧭 Sensor Stack (Stability First)

* **2× ICM42688 IMUs** (primary + redundant)
* **SPL06-001 barometer** for altitude estimation (hover/cruise/VTOL)

### 🔋 Power You Can Trust

* **4–12S input**
* **3.3V + 5V (3A) + 12V (3A)** rails
* **Switchable 12V rail** (ideal for VTX safety on bench)

> Switchable camera/VTX power and dual-camera switching are exactly the kind of “builder quality-of-life” features that show up on serious FCs. ([Mateksys][2])

### 🎥 Video & Camera Control

* **Dual camera inputs (CAM1/CAM2)**
* **RC-controlled camera switching**
* Supports analog OSD + digital workflows (firmware dependent)

### 🔌 Expandability (No Compromises)

* **Up to 8 UARTs**
* **1× CAN** for DroneCAN peripherals
* **I²C** for GPS/compass modules
* **Analog RSSI input** + digital RSSI via CRSF/ELRS

---

## 🧱 Who This Is For

**Built for:**

* 10–15″ **X8** and heavy-lift cinematic rigs
* 7–10″ long-range builds with full telemetry + GPS + accessories
* VTOL / fixed-wing builds needing **more outputs + more serial devices**

**Not ideal for:**

* Ultra-compact micros where you don’t need dual IMU / heavy I/O

---

## 📦 Summary Specifications

| Item          | Spec                                                            |
| ------------- | --------------------------------------------------------------- |
| Product       | FlySpark H743 Pro Flight Controller                             |
| MCU           | STM32H743, Cortex-M7, **480 MHz**                               |
| IMUs          | **2 × ICM42688**                                                |
| Barometer     | SPL06-001                                                       |
| Flash         | **16 MB** external NOR                                          |
| Input Voltage | **4–12S LiPo (VBAT)**                                           |
| BEC Rails     | 3.3 V, **5 V @ 3 A**, **12 V @ 3 A**                            |
| UARTs         | Up to **8**                                                     |
| CAN           | **1 × CAN**                                                     |
| Outputs       | Up to **14 outputs** (8 motors + 4 aux/servo + LED + extra pad) |
| DShot         | Bi-directional DShot on **M1–M8, S1, S2**                       |
| USB           | USB Type-C                                                      |
| PCB Size      | 36 × 36 mm                                                      |
| Mounting      | 30.5 × 30.5 mm (M3)                                             |
| Weight        | ~8.7–10 g (connector dependent)                                 |

---

## 🔋 Power System Details

### Rails

| Rail  | Rating | Recommended Use            |
| ----- | -----: | -------------------------- |
| 3.3 V |  0.5 A | MCU + sensors              |
| 5 V   |    3 A | RX, GPS, LEDs, accessories |
| 12 V  |    3 A | VTX + cameras              |

### Battery Monitoring

* VBAT sensing: up to **12S**
* Current sensing: depends on final ESC/current sensor implementation (stack dependent)

**ArduPilot-style example params (verify per target / hwdef):**

```text
BATT_MONITOR    = 4
BATT_VOLT_PIN   = 11
BATT_CURR_PIN   = 10
BATT_VOLT_MULT  = 16.0
BATT_AMP_PERVLT = 83.3
```

### Switchable VTX Power (Bench-Safe)

* 12V VTX rail controlled via MCU GPIO (map to Relay in firmware)
* Typical uses: bench safety, heat reduction, remote power-cycle

---

## 🔌 Connectivity & I/O

### UART Layout (Typical)

| Port    | Common Use                                   |
| ------- | -------------------------------------------- |
| UART1   | GPS                                          |
| UART2   | Telemetry (MAVLink etc.)                     |
| UART3   | ESC telemetry                                |
| UART4   | HD VTX / DisplayPort                         |
| UART5   | RC input (SBUS/CRSF/ELRS/FPort/SRXL2)        |
| UART6–8 | Expansion (extra GPS, peripherals, payloads) |

### CAN

* 1× CAN port (DroneCAN peripherals: GPS, compass, devices)

### I²C (External Compass)

* No onboard compass (good — avoids magnetic interference)
* Use external compass via SDA/SCL pads (often integrated with GPS)

---

## 🔄 Outputs, Motor Groups & DShot (Read This Before Wiring)

* **M1–M8:** motor outputs
* **S1–S4:** servo/aux outputs
* **LED pad:** addressable LED output (and related features depending on firmware)

**Protocol grouping rule:** channels inside a group must share protocol/update rate (example grouping below).

> If any channel in a group uses DShot, all channels in that group must use DShot.

| Group | Channels                     |
| ----- | ---------------------------- |
| 1     | M1–M4                        |
| 2     | M5–M8                        |
| 3     | S1–S2                        |
| 4     | S3–S4                        |
| 5     | Extra pad (timing-sensitive) |
| 6     | LED                          |

---

## 🎥 Video, Cameras & OSD

### Dual Camera Inputs

* CAM1 + CAM2 connectors
* GPIO switching via firmware relay mapping

### HD / Analog Compatibility

* Dedicated HD VTX connector
* Jumper pads allow reconfiguring that header for analog VTX use (same physical port)

### OSD

* Onboard OSD implementation for analog overlay
* Digital OSD protocols (e.g., DisplayPort) depend on firmware + wiring

---

## 📐 Mechanical

### FC

| Parameter | Value               |
| --------- | ------------------- |
| PCB size  | 36 × 36 mm          |
| Mounting  | 30.5 × 30.5 mm (M3) |
| Thickness | ~1.7 mm             |
| USB       | Type-C              |
| Weight    | ~8.7–10 g           |

### ESC (Stack Family, Typical)

| Parameter    | Value               |
| ------------ | ------------------- |
| ESC PCB size | ~56 × 59 mm         |
| Mounting     | 30.5 × 30.5 mm (M3) |
| Stack height | ~15.8 mm (FC + ESC) |

---

## 🧱 Stack Variants

| Stack SKU                  | ESC Rating (typical) |  LiPo | Best For                  |
| -------------------------- | -------------------: | ----: | ------------------------- |
| H743 Pro + **80A** 4-in-1  |            80A cont. | 4–12S | 7–10″ LR / mid-weight X8  |
| H743 Pro + **100A** 4-in-1 |           100A cont. | 4–12S | heavy 7–10″ / cinelifters |
| H743 Pro + **120A** 4-in-1 |           120A cont. | 4–12S | 10–15″ X8 heavy-lift      |

> Pro stacks in this class often emphasize surge protection (TVS + low-ESR caps) and thermal management because that’s what keeps 12S rigs alive. ([SpeedyBee][1])

---

## 🧷 Wiring Overview (High Level)

* **Receiver (ELRS/CRSF):** 5V, GND, RX5, TX5
* **GPS:** UART1 TX/RX + power
* **Compass:** SDA/SCL pads (I²C)
* **VTX:** 12V rail + control UART (SmartAudio/Tramp) as needed
* **LED:** LED pad + 5V + GND
* **Buzzer:** BUZ+/BUZ- pads

---

## 🛡️ Safety & Installation Notes

* Use **soft grommets** (IMU vibration isolation matters).
* Keep high-current leads short; add **low-ESR capacitor** on main input for 6–12S.
* Route RX/GPS/compass wiring away from power wiring.

---

## 📦 What’s in the Box

(Add your real list here)

* 1× FlySpark H743 Pro FC
* Cables / harnesses (ESC, VTX, GPS)
* Silicone grommets + hardware

---

## 🧪 “10-Minute Bring-Up” Checklist

1. Flash firmware target (Betaflight / iNav / ArduPilot)
2. Set ports (GPS, RC, VTX, ESC telemetry)
3. Verify IMU orientation
4. Confirm VBAT reads correctly
5. Test VTX rail switch (bench safe)
6. Test camera switch (CAM1/CAM2)

---

# ⚠️ Brutally Honest Fixes / Things To Confirm (so you don’t overpromise)

These are the spots that can hurt trust if they’re wrong:

1. **“14 PWM outputs including OSD”**
   LED pad isn’t really “PWM,” and “OSD output” wording confuses people (OSD is a chip/function). If that “extra pad” is actually **Camera Control (CC)** or something timing-related, rename it.

2. **Current sensing wording is inconsistent**
   One place says “integrated voltage and current sensing,” later says “external current sensor input.” Decide and lock it:

   * VBAT sensing on FC ✅
   * Current sensing via ESC ✅ (most common)
   * Or onboard current sensor ✅ (if true)

3. **ArduPilot battery pins**
   Those BATT_* values must match your actual hwdef/target. Keep them labeled as “example / verify”.

If you want, paste your **actual pinout / connector map** next (even rough), and I’ll generate the *clean “graphical” pin tables + wiring diagrams section* so it reads like a premium product manual.

[1]: https://www.speedybee.com/speedybee-f7-v3-flight-controller/?utm_source=chatgpt.com "SpeedyBee F7 V3 Flight Controller - Speedy Bee"
[2]: https://www.mateksys.com/downloads/H743-WING_Manual.pdf?utm_source=chatgpt.com "FLIGHT CONTROLLER H743-WING"
