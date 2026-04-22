# 💧 Automatic Water Pump Controller using LM324 IC

![Project Banner](https://img.shields.io/badge/Project-Water%20Pump%20Controller-blue?style=for-the-badge)
![IC](https://img.shields.io/badge/IC-LM324-green?style=for-the-badge)
![Transistor](https://img.shields.io/badge/Transistor-BC547%20NPN-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Working-brightgreen?style=for-the-badge)

---

## 📌 Project Overview

This project is an **Automatic Water Pump Controller** that monitors water levels inside a tank and automatically **turns the pump ON** when the water drops below **25%** and **turns it OFF** when the tank is **100% full**. It uses the **LM324 quad op-amp IC** as a comparator to detect water levels through submerged probes and drives a relay to control the motor pump — with **zero manual intervention**.

---

## 🎯 Features

- ✅ Automatic pump ON/OFF based on water level
- ✅ 4 water level indicators: 25%, 50%, 75%, 100%
- ✅ Visual LED indicators for each water level
- ✅ Red, Blue, Yellow, Green LEDs for easy status reading
- ✅ Relay-based motor control (safe isolation from mains)
- ✅ Powered by a simple 5V supply
- ✅ Low cost and easy to build

---

## 🧠 Working Principle

### How the LM324 Comparator Works

The **LM324** is a quad op-amp IC — meaning it contains **4 independent op-amp comparators** in a single 14-pin package. Each comparator compares two voltages:

- **Non-inverting input (+)** — connected to the water probe
- **Inverting input (−)** — connected to a fixed reference voltage (~2.5V)

**When water touches a probe:**
> Water creates a conductive path between the probe and the common ground probe at the bottom of the tank. This raises the voltage at the (+) input above the reference voltage → the comparator output goes **HIGH** → the corresponding LED turns ON.

**When water is absent at a probe:**
> No conductive path → (+) input stays near 0V (below reference) → comparator output goes **LOW** → LED stays OFF.

### Pump Logic

| Condition | Action |
|---|---|
| Water level drops below 25% | All 4 comparators output LOW → relay transistor (BC547) turns ON → relay energizes → pump motor starts |
| Water level reaches 100% | 100% comparator output goes HIGH → disables relay transistor → relay de-energizes → pump stops |

---

## 🔩 Components Required

| Component | Value/Part No. | Quantity |
|---|---|---|
| Op-Amp IC | LM324 | 1 |
| NPN Transistor | BC547 | 1 |
| Relay | 5V, Single Channel | 1 |
| Resistor | 10kΩ | 4 |
| Resistor | 1kΩ | 2 |
| Resistor | 220Ω | 4 |
| LED | Red | 1 |
| LED | Blue | 1 |
| LED | Yellow | 1 |
| LED | Green | 1 |
| Flyback Diode | 1N4007 | 1 |
| Water level probes | Stainless steel/copper wire | 5 |
| Power Supply | 5V DC | 1 |
| Motor/Pump | 5V or AC (via relay) | 1 |
| PCB / Breadboard | — | 1 |

---

## 📐 Circuit Description

### 1. 🔋 Power Supply

```
+5V → Pin 4 (LM324 VCC)
GND → Pin 11 (LM324 GND)
```

### 2. 🌊 Water Tank Probes

Five stainless steel probes are placed inside the tank at different heights:

| Probe | Level | Connected To |
|---|---|---|
| Common (bottom) | 0% | GND |
| W1 | 25% | Pin 2 of LM324 |
| W2 | 50% | Pin 6 of LM324 |
| W3 | 75% | Pin 9 of LM324 |
| W4 | 100% | Pin 13 of LM324 |

### 3. 🔁 LM324 Comparator Pin Mapping

```
+5V ──[10kΩ]──┬──[10kΩ]── GND
              │
         Reference ~2.5V ──→ All inverting inputs (Pins 3, 5, 10, 12)
```

| Comparator | (+) Input | (−) Input | Output | LED |
|---|---|---|---|---|
| Op-Amp 1 (25%) | Pin 2 → W1 probe | Pin 3 → 2.5V ref | Pin 1 → L1 | Red |
| Op-Amp 2 (50%) | Pin 6 → W2 probe | Pin 5 → 2.5V ref | Pin 7 → L2 | Blue |
| Op-Amp 3 (75%) | Pin 9 → W3 probe | Pin 10 → 2.5V ref | Pin 8 → L3 | Yellow |
| Op-Amp 4 (100%) | Pin 13 → W4 probe | Pin 12 → 2.5V ref | Pin 14 → L4 | Green |

### 4. 💡 LED Indicator Connections

Each comparator output drives an LED through a current-limiting resistor:

```
Output Pin ──[220Ω]──► LED ──► GND
```

- **Red LED (L1)** → 25% water level
- **Blue LED (L2)** → 50% water level
- **Yellow LED (L3)** → 75% water level
- **Green LED (L4)** → 100% water level (tank full)

### 5. 🔌 Relay Driver — BC547 NPN Transistor

The relay is driven by the comparator output through an NPN transistor (BC547):

```
Comparator Output (Pin 1) ──[1kΩ]──► Base (BC547)
                                      Emitter ──► GND
                                      Collector ──► Relay Coil ──► +5V
                                                        │
                                                   [Flyback Diode] (1N4007, stripe to +5V)
```

- When Pin 1 is **LOW** (water below 25%) → BC547 turns ON → Relay energizes → **Pump starts**
- When Pin 14 is **HIGH** (tank full at 100%) → BC547 turns OFF → Relay de-energizes → **Pump stops**

### 6. ⚙️ Relay Output — Motor Control

```
Relay COM ──────────────► Live wire (AC mains / DC +)
Relay NO (Normally Open) ─► Motor one terminal
Motor other terminal ────► Neutral (AC mains / DC −)
```

The motor is only connected when the relay coil is energized. The **Normally Open (NO)** contact ensures the pump is **off by default** when there is no control signal.

---

## 🖼️ Block Diagram

```
┌─────────────┐     Probe Voltages     ┌─────────────────┐
│  Water Tank  │ ─────────────────────► │  LM324 (4x       │
│  with Probes │                        │  Comparators)    │
│  at 4 levels │ ◄────── Common GND ─── │                  │
└─────────────┘                        └────────┬─────────┘
                                                │ Output signals
                         ┌──────────────────────┼─────────────┐
                         ▼                      ▼             ▼
                   ┌──────────┐          ┌──────────┐   ┌──────────┐
                   │ LED (L1) │          │ LED (L2-4)│   │ BC547    │
                   │ Red 25%  │          │ Blue/Yel/ │   │Transistor│
                   └──────────┘          │ Green    │   └────┬─────┘
                                        └──────────┘        │
                                                       ┌─────▼──────┐
                                                       │   Relay     │
                                                       └─────┬──────┘
                                                             │
                                                       ┌─────▼──────┐
                                                       │    Motor    │
                                                       │    Pump     │
                                                       └────────────┘
```

---

## 🔄 Full Working Flow

```
[POWER ON]
     │
     ▼
[Check Water Level via Probes]
     │
     ├──► Water < 25%?
     │         │── YES ──► All comparators LOW
     │                         │
     │                    BC547 turns ON
     │                         │
     │                    Relay energizes (NO closes)
     │                         │
     │                    ✅ PUMP STARTS
     │
     ├──► Water between 25–99%?
     │         │── YES ──► LEDs light up progressively
     │                    Pump continues running
     │
     └──► Water = 100%?
               │── YES ──► 100% comparator HIGH (Pin 14)
                               │
                          BC547 turns OFF
                               │
                          Relay de-energizes (NO opens)
                               │
                          ✅ PUMP STOPS
```

---

## ⚠️ Safety Notes

- Always use a **flyback diode (1N4007)** across the relay coil to prevent voltage spikes when the relay turns off.
- If controlling an **AC mains motor**, ensure proper insulation and use an appropriately rated relay (e.g., 10A/250VAC).
- The water probes should be made of **stainless steel or copper** to resist corrosion.
- Avoid touching the circuit when connected to AC mains.

---

## 📁 Project Structure

```
water-pump-controller/
│
├── README.md               ← This file
├── circuit_diagram.jpg     ← Hand-drawn circuit diagram
├── schematic/
│   └── schematic.pdf       ← (Optional) Digital schematic
└── images/
    └── prototype.jpg       ← (Optional) Physical prototype photo
```

---

## 🛠️ How to Build

1. **Set up the voltage divider** (two 10kΩ resistors) between +5V and GND. The midpoint gives ~2.5V reference.
2. **Connect reference voltage** to all inverting inputs (Pins 3, 5, 10, 12) of LM324.
3. **Place water probes** in the tank at 25%, 50%, 75%, and 100% heights. Connect the common/bottom probe to GND.
4. **Connect level probes** to LM324 non-inverting inputs (Pins 2, 6, 9, 13) via 10kΩ pull-down resistors to GND.
5. **Connect LEDs** through 220Ω resistors from each output pin (Pins 1, 7, 8, 14) to GND.
6. **Connect BC547 transistor** — base to Pin 1 (25% output) via 1kΩ, collector to relay coil, emitter to GND.
7. **Add flyback diode** across relay coil (anode to collector, cathode to +5V).
8. **Connect motor** via relay's Normally Open (NO) and COM terminals.
9. **Power up** and test with a water container.

---

## 📊 IC LM324 Pinout Reference

```
        ┌──────U──────┐
  OUT1 ─┤ 1       14 ├─ OUT4
   IN1−─┤ 2       13 ├─ IN4−
   IN1+─┤ 3       12 ├─ IN4+
   VCC ─┤ 4       11 ├─ GND
   IN2+─┤ 5       10 ├─ IN3+
   IN2−─┤ 6        9 ├─ IN3−
  OUT2 ─┤ 7        8 ├─ OUT3
        └────────────┘
```

---

## 📜 License

This project is open-source and free to use for educational and personal purposes.

---

## 🙋 Author

> Built as a practical electronics project to demonstrate the use of the **LM324 op-amp as a level comparator** for home automation.
>
**BY :-**<br>
**Smruti Ranjan Rout**<br>
**Electronics and Communication Engineering Student**<br>
**Embedded Systems & IoT Enthusiast**

*Feel free to ⭐ star this repo if you found it helpful!*
