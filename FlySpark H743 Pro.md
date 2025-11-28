# ⚡ FlySpark H743 Pro Flight Controller Stack

High performance H7 flight controller stack for X8 cinelifters, long‑range and heavy‑lift FPV platforms.

- Manufacturer: **FlySpark**  
- Product: **FlySpark H743 Pro Flight Controller**  
- Website: **https://flyspark.in**

> This document is designed to be dropped directly into a GitHub repository as `README.md` or `README.mdx`.  
> It uses only GitHub‑compatible markdown, emojis and tables. No custom CSS is required.

---

## 🚀 Highlights

- H7 processor running at 480 MHz
- Dual ICM42688 IMUs for redundancy and cleaner attitude estimation
- SPL06 barometer for precise altitude control
- 16 MB onboard flash for Blackbox or ArduPilot logs
- 4–12S LiPo support with integrated voltage and current sensing
- Triple BEC rails: 3.3 V, 5 V at 3 A, 12 V at 3 A (switchable VTX rail)
- Up to 14 PWM‑capable outputs (8 motors, 4 servo or aux, plus OSD and LED)
- Dual camera inputs with RC‑controlled switching
- Controllable 12 V VTX power rail for safe bench work
- Rich I/O: up to 8 UARTs, 1 CAN port, I²C, analog RSSI input
- Designed for 7–15 inch frames, X8 builds, VTOL and heavy‑lift use cases

---

## 📦 Summary Specification

| Item                      | Spec                                                                 |
|---------------------------|----------------------------------------------------------------------|
| Product name              | FlySpark H743 Pro Flight Controller                                  |
| MCU                       | STM32H743, ARM Cortex‑M7, 32‑bit, 480 MHz                           |
| IMUs                      | 2 × ICM42688 (6‑axis gyro + accelerometer)                          |
| Barometer                 | SPL06‑001                                                            |
| Onboard flash             | 16 MB external flash (Blackbox or firmware logs)                    |
| Input voltage             | 4–12S LiPo (VBAT)                                                   |
| BEC rails                 | 3.3 V, 5 V at 3 A, 12 V at 3 A                                      |
| UARTs                     | Up to 8                                                             |
| CAN                       | 1 × CAN port                                                        |
| PWM outputs               | Up to 14 (M1–M8, S1–S4, OSD, LED)                                   |
| Bidirectional DShot       | M1–M8, S1, S2                                                       |
| PCB size                  | 36 × 36 mm                                                          |
| Mounting pattern          | 30.5 × 30.5 mm (M3)                                                 |
| USB                       | USB Type‑C                                                          |
| Typical weight            | About 8.7–10 g (depending on connectors)                            |

---

## 🧠 Processing And Memory

### MCU

- STM32H743, ARM Cortex‑M7, 32‑bit
- Clock speed: 480 MHz
- Single‑precision FPU and DSP extensions
- Internal flash for firmware image
- Sufficient RAM for advanced filtering, logging, CAN, telemetry and custom logic

### Onboard Flash (Blackbox)

- Type: external NOR flash
- Capacity: 16 MB
- Use cases:
  - High‑rate gyro and RC signal logging
  - Motor and system state logging
  - ArduPilot style dataflash logs

---

## 🧭 Sensors

| Sensor type | Part        | Notes                                                    |
|------------|-------------|----------------------------------------------------------|
| IMU 1      | ICM42688    | 6‑axis gyro+accel, primary attitude source               |
| IMU 2      | ICM42688    | Redundant IMU for noise rejection and fallback           |
| Barometer  | SPL06‑001   | Digital barometer for altitude estimation                |

- Dual IMUs allow better vibration rejection and redundancy.  
- The SPL06 barometer provides high‑resolution altitude information for hover, cruise and VTOL.

---

## 🔋 Power System

### BEC Rails (On FC)

| Rail     | Voltage | Max current (typical) | Recommended use                               |
|----------|---------|-----------------------|-----------------------------------------------|
| VBAT     | 4–12S   | –                     | Main battery input (shared with ESC stack)    |
| 3.3 V    | 3.3 V   | 0.5 A                 | MCU and digital sensors                       |
| 5 V      | 5 V     | 3 A                   | Receivers, GPS, LEDs, light accessories       |
| 12 V     | 12 V    | 3 A                   | Analog or HD video systems and cameras        |

> Note: exact regulator models and small passive components may vary by production batch.  
> Rail voltages and current ratings remain within the spec listed above and are qualified for 4–12S operation.

### Battery Monitoring

- Built‑in voltage sensing for up to 12S packs
- External current sensor input, typically rated to around 130 A (actual limit depends on ESC)

Recommended default parameters when using ArduPilot‑style battery monitoring:

```text
BATT_MONITOR    = 4
BATT_VOLT_PIN   = 11
BATT_CURR_PIN   = 10
BATT_VOLT_MULT  = 16.0
BATT_AMP_PERVLT = 83.3
```

### VTX Power Control

- The 12 V VTX rail is controlled by a dedicated GPIO line on the MCU
- Firmware can map this GPIO to a relay function, for example:
  - Relay high: 12 V VTX rail on
  - Relay low: 12 V VTX rail off

Typical uses:

- Safely power down VTX on the bench
- Reduce heat when idle
- Power cycle VTX from transmitter or mission logic

---

## 🔌 Connectivity And I O

### UARTs

The hardware supports up to 8 UARTs, exposed as Rn / Tn pads and connectors.

Common use layout:

| UART     | Typical role                         |
|----------|--------------------------------------|
| USB      | Configuration and console            |
| UART 1   | GPS 1                                |
| UART 2   | Telemetry (MAVLink or similar)       |
| UART 3   | ESC telemetry                        |
| UART 4   | HD VTX or DisplayPort                |
| UART 5   | RC input (SBUS, CRSF, FPort, SRXL2)  |
| UART 6   | Extra GPS or telemetry link          |
| UART 7   | User device                          |
| UART 8   | User device                          |

Actual mapping depends on firmware configuration. In Betaflight these appear simply as UART1–UART8 and can be freely assigned.

### RC Input

- Default RC input uses UART 5 receive pin, with an inverted SBUS pad wired to the same UART
- Supported protocols include:
  - SBUS (via dedicated SBUS pad to R5)
  - FPort (with half‑duplex option enabled in firmware)
  - CRSF and ExpressLRS (using RX5 and TX5)
  - SRXL2 (using T5 with half‑duplex option)
  - Other serial RC protocols supported by the chosen firmware

### CAN Bus

- 1 × CAN port is available
- Supports DroneCAN peripherals such as GPS, compasses, ESCs and other CAN devices

### I²C And Compass

- No onboard compass is provided, to avoid magnetic interference from power electronics
- External compass connects via SDA and SCL pads on the I²C bus, typically combined with the GPS module

### RSSI

- Analog RSSI input is available on an ADC pin (labelled RSSI on the board)
- Digital RSSI is available via many modern RC protocols (for example CRSF and ExpressLRS)

Configuration examples:

- Analog RSSI: enable analog RSSI in firmware and select the RSSI ADC pin
- Digital RSSI: select digital RSSI source in firmware (for example CRSF link quality)

---

## 🔄 Outputs, Motor Groups And DShot

The FlySpark H743 Pro provides up to 14 PWM‑capable outputs:

- M1–M8: motor outputs (8 channels)
- S1–S4: servo or auxiliary outputs (4 channels)
- OSD: extra PWM‑capable pad associated with OSD timing
- LED: output for addressable LED strips or DShot LED support

### Digital Protocol Support

- Bidirectional DShot supported on M1–M8, S1 and S2
- PWM‑only on OSD, S3 and S4
- LED pad supports serial LED strips and DShot LED features

### Output Grouping

On the non‑Pro H743 layout used by this board, outputs are grouped internally. Channels in the same group must share the same output protocol and update rate.

| Group | Channels | Notes                                      |
|-------|----------|--------------------------------------------|
| 1     | M1–M4    | Main motor group 1                         |
| 2     | M5–M8    | Main motor group 2                         |
| 3     | S1–S2    | Aux outputs, bidirectional DShot capable   |
| 4     | S3–S4    | Aux outputs, PWM only                      |
| 5     | OSD      | OSD related timing channel                 |
| 6     | LED      | LED strip or DShot LED output              |

> Important: if any channel in a group uses DShot, all channels in that group must use DShot.

---

## 🎥 Video, Cameras And OSD

### Camera Inputs

- Two dedicated analog camera connectors: CAM1 and CAM2
- Camera switching is controlled by a GPIO line on the MCU, mapped to a relay in firmware
- The relay state selects which camera feed is routed to the OSD and VTX path

Typical use:

- CAM1: primary FPV camera  
- CAM2: secondary FPV or survey camera

### VTX And HD Video

- 12 V rail for VTX and camera power
- VTX power can be switched by GPIO (see VTX power control section)
- Dedicated connector for HD VTX systems such as modern digital video units
- Jumper pads on the board allow the HD VTX connector to be reconfigured for analog VTX use, sharing the same physical header

### OSD

- Onboard OSD chip compatible with common analog OSD implementations
- Supports firmware‑driven overlay of flight data on analog video
- Can be used together with digital OSD protocols such as DisplayPort where supported by firmware

---

## 📐 Mechanical Details

### Flight Controller Board

| Parameter       | Value                                      |
|----------------|---------------------------------------------|
| PCB size       | 36 × 36 mm                                  |
| Mount pattern  | 30.5 × 30.5 mm (M3)                         |
| PCB thickness  | About 1.7 mm                                |
| Connector type | Mix of edge pads and board‑to‑board headers |
| USB            | USB Type‑C                                  |
| Weight         | About 8.7–10 g                              |

### 4‑in‑1 ESC (Stack Family)

FlySpark offers several ESC options that form a stack with the H743 Pro FC. Exact ESC specifications depend on the chosen current rating.

Typical mechanical values:

| Parameter                    | Value                       |
|-----------------------------|-----------------------------|
| ESC PCB size                | About 56 × 59 mm            |
| ESC mount pattern           | 30.5 × 30.5 mm (M3)         |
| ESC PCB thickness           | About 6.5 mm                |
| Approximate stack height    | About 15.8 mm (FC + ESC)    |

---

## 🧱 FlySpark H743 Pro Stack Variants

FlySpark plans the following stack variants based on the same H743 Pro FC:

| Stack SKU                               | ESC rating (per motor, typical) | Cell count (LiPo) | Recommended use case                            |
|----------------------------------------|----------------------------------|-------------------|-----------------------------------------------|
| FlySpark H743 Pro + 80 A 4‑in‑1 ESC    | About 80 A continuous            | 4–12S             | 7–10 inch long‑range frames, mid‑weight X8    |
| FlySpark H743 Pro + 100 A 4‑in‑1 ESC   | About 100 A continuous           | 4–12S             | Heavy 7–10 inch quads, compact cinelifters    |
| FlySpark H743 Pro + 120 A 4‑in‑1 ESC   | About 120 A continuous           | 4–12S             | 10–15 inch X8, heavy‑lift and cinematic rigs  |

Common ESC family features:

- 4‑in‑1 layout for clean wiring
- Integrated current sensing with telemetry back to the FC
- TVS and surge protection components
- High current MOSFETs matched to the rating (80 A, 100 A, 120 A)
- Support for digital motor protocols including DShot and bidirectional DShot

> Final ESC specs depend on the selected MOSFETs and firmware (for example Bluejay, BLHeli S or BLHeli 32).  
> Refer to the ESC‑specific datasheet in this repository once the design is locked.

---

## 🧷 Wiring Overview (High Level)

The following is a high level summary of typical connections. For detailed frame‑specific wiring, see the wiring diagrams section of this repository.

### Typical Connections

- Motors
  - M1–M4 to the first 4‑in‑1 ESC
  - M5–M8 to the second 4‑in‑1 ESC for X8 builds
- Receiver
  - Connect ELRS or CRSF receiver to 5 V, GND, RX5, TX5 (or use SBUS pad for SBUS receivers)
- GPS And Compass
  - GPS to UART 1 (TX/RX) plus power and ground
  - Compass to SDA and SCL on the I²C bus
- VTX (Analog)
  - 12 V, GND and video signal through CAM/VTX pads or HD connector with jumper set for analog
  - Optional SmartAudio or Tramp control via a spare UART
- LED Strip
  - LED pad, 5 V and GND to addressable LED strip
- Buzzer
  - BUZ plus and BUZ minus pads to active buzzer

---

## 🛡️ Safety And Installation Notes

### Stack Installation

- Maintain clear separation between the FC USB‑C connector and the ESC board or pads
- If the stack order is changed or boards are flipped, adjust the silicone spacers so that no metal part can contact the ESC
- Short circuits caused by board‑to‑board contact are considered incorrect installation and are not covered by typical warranty terms

### Best Practices

- Use soft silicone grommets in all four mounting holes to reduce vibration reaching the IMUs
- Keep high current battery and motor wires as short as practical and twist where appropriate
- Install a suitable low‑ESR capacitor on the main battery input, especially for 6–12S setups
- Route receiver, GPS and compass wiring away from power wiring to reduce interference
- Use firmware logging and blackbox traces to fine tune filters and PID gains for your frame

---

## 🧪 Recommended Use Cases

- 7–10 inch long‑range quads on 6–12S packs
- 10–15 inch X8 cinematic or industrial platforms
- VTOL and fixed‑wing aircraft that need many outputs and rich I O
- Heavy‑lift drones that drive gimbals or servo payloads via S1–S4

The rich UART set, CAN port, dual IMUs, dual camera inputs and flexible power rails make the FlySpark H743 Pro an excellent base for both autonomous ArduPilot style builds and powerful Betaflight‑based X8 rigs.

---

## 🏷️ Brand And Support

| Field          | Detail                                              |
|----------------|------------------------------------------------------|
| Brand          | FlySpark                                            |
| Product line   | FlySpark H743 Pro Flight Controller And Stack       |
| Website        | https://flyspark.in                                 |
| Documentation  | Wiring diagrams, presets and updates on flyspark.in |
| Firmware       | H7 class Betaflight and ArduPilot builds            |

If you use this board in an open source frame or project, please link back to this repository or to **https://flyspark.in** so others can find compatible firmware settings and wiring diagrams.
