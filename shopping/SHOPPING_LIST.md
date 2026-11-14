# Shopping List (v1 hardware)

A quick reference to keep open while shopping. It says what the v1 device needs, what to look for in each part, and what to avoid. Real candidate parts (links, prices, screenshots) are compared in [PARTS_COMPARISON.md](./PARTS_COMPARISON.md).

The README [Hardware table](../README.md#hardware-planned) is the source of truth for what the device is. This list turns it into a buying checklist. Prices are approximate and unchecked until real quotes are in the comparison document.

_Last updated: 2026-11-14_

## At a glance
- **Budget:** ceiling about $500. The v1 estimate is about $95-190.
- **Timing:** Black Friday is Friday, November 27, 2026 (Cyber Monday is November 30). Hobby electronics shops often discount less than big retailers, so compare the sale price against the normal price before buying.
- **Reputable sellers matter most for the GPS, the motion sensor and the battery parts.** Cheap marketplace listings often carry counterfeit or mislabeled chips. For these, prefer well-known electronics shops or the maker's own store.
- **Spares:** for cheap parts (motion sensor, buttons, buzzer, jumper wires), buy one or two extra. A burned part should not stall the project.
- **Do not buy anything that is not on this list** without checking pins, voltage and size first.

## Suggested buying order
Adjust to what is on sale and what the budget allows.

| Wave | What | Why |
| --- | --- | --- |
| 1. Start the prototype | ESP32-S3 board, motion sensor (x2), display, microSD module and card, buttons, buzzer, breadboard, jumper wires | Lets the first hardware sessions begin on a breadboard |
| 2. Sensors and clock | GPS module and antenna, real-time clock, light sensor, barometer | Completes the sensing side |
| 3. Power, sound and case | Speaker and amplifier, car charger and cable, backup battery system, wiring and small parts, case materials | Some of these wait for design decisions (see the battery section) |

## Summary table
| # | Part | Qty | Approx. cost (USD) | Wave | Status |
| --- | --- | --- | --- | --- | --- |
| 1 | [ESP32-S3 board](#1-microcontroller-board) | 1 (2 if cheap) | 8-15 | 1 | ☐ |
| 2 | [Motion sensor (IMU)](#2-motion-sensor-imu) | 2 | 3-20 | 1 | ☐ |
| 3 | [GPS module and antenna](#3-gps-module-and-antenna) | 1 | 15-35 | 2 | ☐ |
| 4 | [Display](#4-display) | 1 | 10-20 | 1 | ☐ |
| 5 | [microSD module and card](#5-microsd-module-and-card) | 1 module, 2 cards | 8-15 | 1 | ☐ |
| 6 | [Buttons](#6-buttons) | 5 (buy 10) | 4-10 | 1 | ☐ |
| 7 | [Buzzer](#7-buzzer) | 2 | 1-2 | 1 | ☐ |
| 8 | [Speaker and amplifier](#8-speaker-and-amplifier) | 1 + 1 | 5-12 | 3 | ☐ |
| 9 | [Car power](#9-car-power) | 1 + 1 | 10-17 | 3 | ☐ |
| 10 | [Backup battery system](#10-backup-battery-system) | see section | 10-25 | 3 | ☐ (research first) |
| 11 | [Real-time clock](#11-real-time-clock) | 1 | 2-8 | 2 | ☐ |
| 12 | [Light sensor](#12-light-sensor) | 1 | 3-6 | 2 | ☐ |
| 13 | [Barometer](#13-barometer) | 1 | 3-8 | 2 | ☐ |
| 14 | [Wiring and small parts](#14-wiring-and-small-parts) | kits | 5-10 | 1 and 3 | ☐ |
| 15 | [Case materials and mount](#15-case-materials-and-mount) | - | 5-15 | 3 | ☐ |
| | **Total (approximate)** | | **about 95-190** | | |

Not buying for v1: Bluetooth slide switch (v2), NFC reader (future), OBD-II / CAN parts (future).

---

## Part details

### 1. Microcontroller board
- **What it does:** the brain. Runs the firmware, reads sensors, drives the screen, logs to the SD card.
- **Buy:** ESP32-S3 development board with 16 MB flash and 8 MB PSRAM (often labeled "N16R8"). Espressif's own ESP32-S3-DevKitC-1 in the N16R8 version is a good reference to compare other boards against.
- **Must have:** USB-C with native USB, nearly all pins broken out to headers, BOOT and RESET buttons, a published pinout diagram or schematic.
- **Check the pins before buying:** the plan needs about 29 usable pins of roughly 34. On boards with 8 MB octal PSRAM, a few pins (typically GPIO 35, 36 and 37) are used by the memory, and some others are reserved for flash or have startup restrictions. Confirm against the seller's pinout that enough pins stay free.
- **Avoid:** tiny "mini" or "zero" boards (they break out too few pins), boards with no pinout, boards where the pin labels do not match.
- **Interface:** n/a. This is the hub.

### 2. Motion sensor (IMU)
- **What it does:** measures acceleration (braking, accelerating, cornering) and rotation. The most important sensor in the project.
- **Buy for the prototype:** MPU-6050 breakout (often sold as "GY-521"). Buy 2.
- **Upgrade later (compare in the comparison doc):** ICM-42688-P or LSM6DSO / LSM6DSOX breakout from a reputable maker. If the price gap is small, consider buying the better one now.
- **Must have:** I2C interface, and the address pin reachable so it can be set to 0x69 (the clock uses 0x68; see the address table below).
- **Watch for:** the MPU-6050 is an old chip and many listings carry clones or a different chip. For a prototype that is acceptable, but expect small differences.
- **Interface:** I2C (shared bus), plus one optional interrupt pin.

### 3. GPS module and antenna
- **What it does:** speed, distance, location and time. Speed and distance depend on it.
- **Buy:** a u-blox MAX-M10S or NEO-M9N breakout, at least 10 Hz position updates, with an external active antenna.
- **Must have:** UART output, 3.3 V logic, a named u-blox chip, an antenna connector (SMA or u.FL) and an antenna that matches it (adapters if not), a backup-power pin or battery so the module keeps its last position for faster starts.
- **Antenna:** active GNSS antenna with a 2-3 m cable and a magnetic or adhesive base. Some windshields have a metal coating that blocks GPS signals. If signal is poor behind the glass, the antenna may need to sit elsewhere (a good thing to test early).
- **Avoid:** NEO-6M style modules (older, usually limited to 5 Hz and weaker), listings that do not name the chip, and unusually cheap "u-blox" modules.
- **Interface:** UART (2 pins), optional PPS pin.

### 4. Display
- **What it does:** shows live driving data, animations, the trip score and menus.
- **Buy:** 2.8"-3.5" IPS SPI color TFT with an ST7789 (240x320) or ST7796 (320x480) controller. No touch.
- **Must have:** IPS panel (wide viewing angle), as bright as possible for sunlight, 3.3 V logic, SPI interface, a backlight pin that can be controlled (needed for auto-dim), a stated operating temperature.
- **Nice to have:** a built-in SD slot (a bonus only; the plan uses a separate microSD module).
- **Avoid:** TN panels (washed out at an angle), 8-bit parallel displays (use too many pins), capacitive touch variants (extra cost and pins), 5 V-only logic without a level shifter.
- **Interface:** SPI (about 6 pins including backlight).

### 5. microSD module and card
- **What it does:** stores trips, events and settings as CSV and INI files.
- **Module:** SPI microSD breakout with a sturdy push-push socket and 3.3 V-compatible logic.
- **Card:** high-endurance microSD (the kind sold for dash cams), 8-32 GB, formatted FAT32. Pick one with a wide temperature range. Buy 2 (one to swap while reading data on the computer).
- **Avoid:** no-name cards (fakes with false capacity are common), cards over 32 GB unless you are happy to format them FAT32.
- **Interface:** SPI on its own bus (4 pins).

### 6. Buttons
- **What it does:** Up, Down, OK, Shortcut and View.
- **Buy:** an assortment of 6x6 mm tactile switches for the breadboard prototype. Final panel-mount buttons wait until the case is designed, so only the prototype ones are needed now.
- **Interface:** 5 GPIO pins (internal pull-ups, no resistors needed).

### 7. Buzzer
- **What it does:** beeps and character sounds.
- **Buy:** passive piezo buzzer (about 3.3 V). A passive buzzer plays different tones when driven by PWM, so it can play short melodies. An active buzzer plays one fixed pitch only.
- **Interface:** 1 GPIO pin.

### 8. Speaker and amplifier
- **What it does:** voice clips and louder alerts (clips arrive in v1.5; the hardware goes in v1).
- **Buy:** I2S amplifier breakout such as the MAX98357A, plus a small 3 W speaker (4 or 8 ohm, around 28-40 mm) with wires.
- **Notes:** mount the speaker away from the motion sensor (sound vibration disturbs it). The amplifier output power depends on its supply voltage (5 V gives more than 3.3 V), so this ties into the power design.
- **Interface:** I2S (3 pins).
- **Timing:** can wait for a later wave.

### 9. Car power
- **What it does:** powers the device from the car.
- **Buy:** USB car charger from a reputable brand rated at least 2 A, plus a short USB-A to USB-C cable (or USB-C to USB-C if the charger has USB-C). A low-profile angled plug helps on a dash.
- **Check in the car first:** does the outlet turn off with the ignition? That decides how the device sees "power cut" (it affects shutdown and trip logic).
- **Separate from this:** a data-capable USB cable for programming the board from the computer.

### 10. Backup battery system
- **What it does:** gives a clean shutdown, a trip summary after the car turns off, and rides through the dip when the engine starts.
- **Research first, buy later.** This is the one part with no obvious off-the-shelf answer. The comparison document should hold two or three charger candidates before anything is bought.
- **Cell:** 1S LiFePO4 (3.2 V nominal, about 3.6 V full), with a protection circuit, from a reputable seller, small capacity is fine (a few hundred to about 1500 mAh). Check the maximum temperatures it is rated for. Never LiPo and never standard 4.2 V lithium-ion.
- **Charger / load-sharing module must:**
  - explicitly support LiFePO4 charging (about 3.6 V full, not 4.2 V),
  - share the load with USB power (power path), so the device runs from USB while the battery charges,
  - have a temperature sensor input for the cell, so charging stops below 0 °C and above about 45 °C,
  - limit charge current to a safe level for the cell (about 0.5C or less),
  - ideally have a status output.
- **Also needed:** a boost or buck-boost converter so the board runs from the battery (a 3.2 V cell is below what the board's own regulator needs), and two small voltage dividers (resistors) to sense USB power loss and battery voltage.
- **Open:** how the board is powered from the battery is not decided (README open question).
- **If no module fits:** tell the main session. This could be a reason to design a small custom board.

### 11. Real-time clock
- **What it does:** keeps the time with the power off and before GPS locks.
- **Buy:** DS3231 breakout from a reputable maker, with a coin-cell holder, and the matching coin cell (CR1220 or CR2032, depending on the board).
- **Watch for:** cheap boards (the common "ZS-042" type) can try to charge a non-rechargeable coin cell. Use a rechargeable LIR2032, or choose a board without that problem.
- **Interface:** I2C at address 0x68 (fixed).

### 12. Light sensor
- **What it does:** auto-dims the screen.
- **Buy:** VEML7700 breakout.
- **Notes:** the case needs a small window or light pipe for it.
- **Interface:** I2C at address 0x10.

### 13. Barometer
- **What it does:** relative altitude change and air temperature.
- **Buy:** BMP390 (preferred for altitude accuracy) or BME280 / BMP280 breakout.
- **Notes:** the case needs a small vent. Heat from the electronics will warm the air temperature reading. (Humidity is not used, so it does not matter if a "BME280" listing turns out to be a BMP280.)
- **Interface:** I2C at address 0x76 or 0x77.

### 14. Wiring and small parts
- **Wave 1 (prototype):** a breadboard (830 tie-point, or two half-size), jumper wire assortment (male-male, male-female, female-female), male and female header strips.
- **Wave 3 (more permanent):** silicone hookup wire (22-26 AWG, stranded), heat-shrink tubing assortment, a resettable fuse and a TVS diode for the USB power line (values to be confirmed with the power design), a small assortment of 1% resistors and capacitors (for voltage dividers and power filtering), perfboard for the first semi-permanent build.
- **Already owned:** multimeter, soldering iron and solder, wire cutters, wire strippers, 3D printer.

### 15. Case materials and mount
- **Filament:** PETG, ASA or ABS. Do not use PLA: it softens around 55-60 °C, and a sunlit dash can be much hotter.
- **Small hardware:** M2 or M3 screws and standoffs, heat-set inserts (sized when the case is designed).
- **Mount:** adhesive mounting pad, non-slip dash mat or vent clip. Decided after the case design.
- **Timing:** wait for the case design before buying anything beyond filament.

---

## I2C address map (must not clash)
| Device | Address | Note |
| --- | --- | --- |
| Real-time clock (DS3231) | 0x68 | fixed |
| Motion sensor | 0x69 | MPU-6050 and ICM-42688-P can use 0x68 or 0x69; set it to 0x69. LSM6DSO uses 0x6A or 0x6B. |
| Light sensor (VEML7700) | 0x10 | fixed |
| Barometer (BMP390 / BME280) | 0x76 or 0x77 | selectable |

## Before you click Buy
1. Is the part on this list, and the right variant?
2. Do the voltage (3.3 V logic) and the interface match?
3. Are there enough free pins on the chosen board?
4. Does the I2C address clash with another device?
5. Is the temperature rating stated and enough for a hot dash?
6. Is the seller reputable, with a return policy, for the GPS, motion sensor and battery parts?
7. Is the connector type right (SMA or u.FL, JST, header pitch)?
8. Is the price per unit clear (multi-packs)? Is the sale price really lower than the normal price?
9. Will it arrive when needed?
