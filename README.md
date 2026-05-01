# Dashboard - v2.0
**Squadra Corse PoliTo — Formula Student (Season SC25)**

[![Hardware](https://img.shields.io/badge/Hardware-PCB_Design-blue)](#)
[![Microcontroller](https://img.shields.io/badge/MCU-STM32F446-orange)](#)
[![Status](https://img.shields.io/badge/Status-Manufactured_&_Tested-brightgreen)](#)

## 🏎️ System Overview

This repository contains the hardware design and specifications for the Dashboard version 2.0, designed for the Squadra Corse PoliTo SC25 race car. 

The DASH system serves as the primary Human Machine Interface (HMI) for the driver, providing real-time telemetry and critical system states via a TFT display. Additionally, it acts as a critical node in the vehicle's low-voltage system, handling external LED driving for System Critical Signals (SCS), reading driver inputs from the steering wheel, and directly controlling vehicle actuators like the cooling pumps and fans. 

Due to the packaging constraints of the cockpit, the board was engineered to be as compact and thin as possible while maintaining robust automotive-grade protection on all I/O.

## ✨ Key Features & Hardware Specifications

* **Microcontroller:** STM32F446VET6 (100-pin package), chosen specifically for its high internal RAM capacity to ensure smooth GUI rendering on the TFT screen.
* **Power Supply:** Custom 24V to 5V step-down converter using the TPS5430DDA buck regulator, followed by a 3.3V LDO for logic-level logic. Features Over-Voltage Protection (OVP) via Zener diodes and Over-Current Protection (OCP) via inline fuses.
* **Communications:** Dual CAN 2.0B interfaces utilizing SN65HVD232DRG4 transceivers.
* **External Memory:** Onboard SST25VF080B Synchronous NOR Flash via SPI, reserved for future data/failure logging implementations.
* **Display Interface:** SPI interface with dedicated chip select and data/command lines, driving an LCD TFT screen.

## 🧠 Subsystem Engineering & Design Highlights

### 1. Robust CAN Bus Implementation
To ensure maximum reliability in the electrically noisy environment of a race car, the dual CAN bus lines feature extensive conditioning.
* **Split Termination:** Instead of a single 120Ω resistor, the termination utilizes two 60.4Ω resistors paired with a 560pF capacitor to create a low-pass filter, significantly reducing line noise.
* **Isolation & Protection:** An inline common-mode choke (L4) filters common-mode noise, while TVS diodes provide Over-Voltage Protection against voltage spikes. 

### 2. Actuator Control (PWM & DAC)
The DASH directly manages the vehicle's cooling systems.
* **Cooling Fans:** Driven via a 0-5V PWM signal. Since the MCU outputs 3.3V, an n-channel MOSFET is used to switch a 5V supply, providing the correct dynamics for the fans.
* **Cooling Pump:** Controlled via a DAC output. The 0-3.3V MCU DAC signal is amplified to 0-5V using a TSV991IYLT operational amplifier in a non-inverting configuration (Gain = 1.5).

### 3. Signal Conditioning & Input Protection
The board reads various inputs from the Steering Slave board (rotary switches, push-buttons, and the Ready-To-Drive button) and the Shutdown Circuit (SDC).
* **Debouncing & Filtering:** All mechanical switch inputs pass through hardware Low-Pass Filters (e.g., 10Hz cutoff for rotary switches, 160Hz for push-buttons) to ensure clean logic transitions before entering the MCU.
* **High-Voltage Sensing:** The SDC sensing circuit safely reads up to 30V by utilizing a 1.24MΩ/150kΩ voltage divider, paired with TVS protection and decoupling buffers (TSV358IDT).

## 🛠️ Known Issues & Iterative Improvements (Errata)

*Engineering is an iterative process. Below are known bugs in v2.0 and their respective hardware patches for the next revision.*

* **System Critical Signal (SCS) LED Logic Flip:** The Formula Student rules dictate that the AMS and IMD indicator LEDs must be "Normally ON". In the v2.0 design, an n-MOS/p-MOS cascade was used, which inadvertently created a "Normally OFF" logic state. 
  * **The Fix:** In future revisions, the AMS and IMD driving circuits will be restructured to mirror the TS_OFF LED configuration, utilizing a pull-up resistor to ensure the LED emits light when no signal is applied.

## 📸 Manufacturing, Soldering, & Testing

I personally handled the assembly, SMD soldering, and bench-testing of this prototype. 

* **Check out the [`/Media`](./Media) folder** for high-resolution images of the bare PCB, time-lapses of the stencil/reflow process and the final soldered board.

## Future Implementations
Two versions of the same board are reported inside this repository: 
 - V2.0: Base version; 
 - V2.1: Improved version with the inclusion of components for Autonomous Vehicle; 

## 📁 Repository Structure
```text
├── Dashboard V2.0/
│   ├── Gerbers/                  # Production files 
│   ├── BOM/                      # Bill of Materials
│   ├── Schematics/               # PDF exports of the circuit design
│   ├── Step/                     # 3D Model of the board
├── Dashboard V2.1/
│   ├── Gerbers/                  # Production files 
│   ├── BOM/                      # Bill of Materials
│   ├── Schematics/               # PDF exports of the circuit design
│   ├── Step/                     # 3D Model of the board
├── Media/                        # Photos and videos of soldering, assembly, and testing
├── Dashboard_v2_Wiki_Report.pdf  # PDF copy of the technical report available on Squadra Corse PoliTo's Wiki
└── README.md
