# Dashboard Driving Score Device

> An offline, dash-mounted device (ESP32 + GPS + IMU + small screen) that tracks how smoothly you drive, scores each trip, and logs everything to an SD card. It never connects to the internet.

_Project name is a placeholder. Rename any time; if renamed, replace it across all four docs in one pass._

## Table of Contents
- [About](#about)
- [Open Questions & Risks](#open-questions--risks)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [Changelog](#changelog)
- [License](#license)

## About
A small device sits on a car's dashboard and measures driving dynamics: acceleration and braking force, turning force, speed, average speed, trip distance, and tilt angle. At the end of each trip it gives a driving score. Everything is stored locally on an SD card for later review. The device is deliberately offline: no Wi-Fi to the internet, no cloud, no accounts. Bluetooth (later versions) is local-only and sits behind a physical on/off switch.

It is a personal learning-and-building project. The owner wants a working device for their own car and wants to learn embedded programming along the way. The planned feature set is larger than the first version (see [Roadmap](#roadmap)); the first version is intentionally small.

The detailed behavior of every screen, button, flow and file format is in [USAGE.md](./USAGE.md). This README is the scope statement.

### Build Approach
- **Chosen approach: the owner builds it themselves, with AI (Claude) as tutor and guide.** The owner writes and runs the code and wires the hardware; Claude explains each step, writes example code, and reviews.
- **Owner background:** has written one Python program with AI help; no C++ experience. Explanations should assume no C++ knowledge and no electronics background beyond basics.
- **Hardware is not purchased yet.** The owner is saving up (budget ceiling about $500; re-evaluate beyond that). Work before then is software that does not need hardware: portable logic tested on a computer, simulated or phone-recorded data, and the computer-side trip viewer.
- **Work is organized as one "main" planning session plus short focused sub-sessions.** Each sub-session is given one small task, and before it ends it writes what it did and decided to [SESSION_NOTES.md](./SESSION_NOTES.md) in this repo. The main session reads that file at its start. Sessions cannot be relied on to message each other live; the repo is the shared memory.
- **Docs standard:** because an AI may pick this up with no memory of the planning conversation, these docs assume nothing can be inferred. If a decision is settled, it belongs in a file.

### Goals
- A working dash device that gives an honest driving score and shows live driving data, for the owner's own car.
- Everything works with no internet connection.
- All data is saved to an SD card in plain CSV files that open in Excel, Google Sheets, or Python.
- Most settings can be changed with buttons on the device itself.
- The owner learns C++ and embedded development by building it, in small understandable steps.

### Non-Goals
- No internet connection, cloud sync, accounts or analytics. Ever.
- Not a certified safety, insurance or fleet telematics device. The score is for personal feedback.
- Never writes to the car's data bus (OBD-II reading only, and only in a later phase).
- No touchscreen (not usable while driving).
- No guarantee of exact fuel economy; real MPG comes from fill-ups, trip MPG is an estimate.
- Not designed for multiple cars in v1. Drivers: v1 only logs which driver drove each trip; per-driver settings and NFC recognition are later.
- No iPhone support. Phone features target Android only.

## Open Questions & Risks
- **Budget.** The owner's ceiling is about **$500**; if the plan goes past that, re-evaluate. The v1 build is estimated at about $95-190 and the full build at about $110-225 (all prices approximate and unchecked), so there is plenty of room. Parts are still split into "v1" and "add later" to keep the first build simple.
- **Scope creep.** The feature list has grown a lot. Mitigation: v1 is small (see Roadmap); everything else waits until v1 works.
- **Scoring formula is a placeholder.** Thresholds (for example 0.30 g hard braking) are untested starting values. The owner plans to rework scoring later. Weights live in config, not hardcoded.
- **Real-world data not yet collected.** Plan: record real drives with a phone sensor-logger app to tune thresholds before hardware exists.
- **Mounting and vibration** can ruin IMU readings. Mitigated by filtering, rigid mounting, and guided calibration; only testable with real hardware.
- **GPS reception** on a dash (windshield coating, case placement) is unproven until tested. External antenna is planned.
- **Power loss can corrupt the SD card.** Decided: a small LiFePO4 backup battery is in v1 (instead of a supercapacitor), plus the log is flushed about once per second. The battery gives the time to close the file and save counters. Risk: if the battery is flat, disabled by heat or cold, or missing, power loss is instant like a plain USB device, so the once-per-second flush stays as the fallback and the device shows a battery warning.
- **Battery safety and wear.** LiPo in a hot dashboard is unsafe (parked cars can pass 60 °C), so the v1 battery is LiFePO4, with a charger that supports it and a temperature sensor on the cell. LiFePO4 should not be charged above roughly 45 °C or below 0 °C, so in a hot or freezing car it simply will not charge and the device runs from USB power. Heat also shortens battery life (possibly a few years in a hot car). When parked and unplugged, the device must fully power down after a set time so the battery does not drain flat, and wake when USB power returns. Buy the cell and charger module from a reputable seller.
- **Pin count.** About 29 of roughly 34 usable pins are planned (including about 3 for the speaker's audio chip and 1 to read battery voltage). The barometer and light sensor share the I2C bus and need no extra pins. The real number of usable pins is probably lower on the 8 MB PSRAM board variant (docs review item 15, still to decide). Adding much more needs an I2C expander.
- **IMU choice.** MPU-6050 for the first prototype (cheap, well documented); ICM-42688-P or LSM6DSO is the upgrade. BNO055 has reported I2C problems on ESP32. Final pick open.
- **Real-time clock.** Decided: include a DS3231 RTC in v1 (shares the I2C bus, no extra pins). Risk: cheap DS3231 boards sometimes try to charge a non-rechargeable CR2032 coin cell; use a quality breakout or a rechargeable LIR2032 cell, or remove the charge resistor.
- **Scoring rework direction (not final).** Escalating penalties: each event is scored by severity tier and duration (longer costs more, with slowing growth); more events cost more; penalties are not divided by trip distance; a steadiness component gives credit for calm driving; the road itself never affects the driving score. Real-world g values need checking: everyday driving is typically under about 0.3 g, hard braking about 0.4-0.6 g, and most road cars top out near 0.8-1.0 g in corners, so examples of 1.5-2 g are race-car territory. Thresholds will be set from real recorded drives. Risk: bumps cause false accelerometer spikes, so events are cross-checked against GPS speed and gyro yaw rate. See USAGE.md Driving Score.
- **Ride character (idea).** A ride mix (smooth, rough, spirited) plus a comfort score, deliberately not merged into one number. Needs vertical vibration and jerk, so the v1 log captures vertical g. Open: thresholds (untested), how subjective comfort is handled, a dash-mounted sensor approximates cabin comfort, and a sports car on a rough road is ambiguous.
- **Driver profiles.** v1 logs the driver number only. Open for later: what is per driver versus per car (calibration, level offset, fuel counters and mileage are per car), and NFC (reader about $5-12, tags or keyfobs; a phone as the tag is unreliable with common readers; a menu fallback is always needed).
- **GPS module memory.** The GPS module remembers its last position on its own (it uses it for fast fixes). Decided: not cleared, unless testing on real hardware shows it is needed for the setting to work properly.
- **Passenger-mode gesture.** The quick way to turn the menu lock off while moving is to be decided. Long presses must fire at the normal time (not on release), so the earlier idea of holding Up and Down together for 3 seconds needs a different design or a different gesture.
- **Speaker.** Decided: speaker hardware is in v1 (audio amplifier chip and small speaker) alongside the buzzer, which stays for beeps and character; voice clips and spoken notifications are v1.5; speaker settings are to be determined. Risks: it adds about 3 pins to a tight pin budget, and sound vibration can disturb the motion sensor, so the speaker must be mounted away from the IMU and the case designed to isolate it.
- **Auto trip start and end.** Decided: off by default; trips start and end with the Shortcut button. When turned on, a trip ends after a configurable stop time (default 10 minutes). Open: the start rule (speed threshold), and what happens when power is lost and returns. The device can tell how long it was off from the real-time clock plus a saved shutdown time (or the last logged row if the shutdown save failed), so a power-off shorter than the stop time could continue the same trip. The owner will discuss this before it is designed.
- **Battery power design.** Decided: small LiFePO4 battery in v1 as a base feature, no supercapacitor. Open: pick a specific charger / load-sharing module that supports LiFePO4 and has a temperature sensor input (compare two or three before the shopping list), battery size, how the board is powered from it (boost or regulator), and how long the post-trip summary stays on (setting, default 60 seconds).
- **Custom PCB (future consideration, undecided).** Not for v1. A custom circuit board for the v2 hardware revision is worth considering: it removes loose wires (heat and vibration are hard on jumper leads), shrinks the electronics so the case is easier, mounts the motion sensor rigidly and square, makes extra units quick to build (a few other people may want one if v1 works well), and is a good project to learn PCB design on (KiCad is free). Cautions: boards cost little (about $5-30 for a small batch) but take 1-2 weeks to arrive and the first version often needs a second try; pin mistakes are costly, so the pin budget must be checked first; if the modules fit together neatly, a PCB may not be needed. Plan: during v1, keep a clean record of which pin connects to what (it becomes the schematic), then decide after the v1 accuracy check. The backup-battery charger and power section may be the first reason to want one if no ready-made module fits.
- **Config file format (proposed).** INI style (`config.ini`). Alternatives: JSON (very standard, but no comments and one typo breaks the file) or TOML (modern, but heavier on a small device). To be confirmed by the owner.
- **Heat and sun.** A parked dashboard can pass 60 °C, which affects the screen, SD card, backup battery and the clock's coin cell, and sunlight can wash out the display. Check temperature ratings and pick a bright screen before buying.
- **Mounting legality and safety.** Dash and windshield mounting rules vary by location; the menu lock while moving exists for safety, and is optional for passenger use. Owner is responsible for checking local rules.
- **Bluetooth firmware updates (OTA)** are the hardest feature and lowest priority; needs dual flash slots and a rollback plan.
- **Sub-session reporting** relies on SESSION_NOTES.md in the repo; no live session-to-session messaging.
- **Single source of truth.** A planning spec also exists as a Claude doc ("Dashboard Driving Score Device — Spec"). After kickoff, this repo is the source of truth; the doc is reference only.
- **Screen choice.** 2.8"-3.5" IPS SPI TFT is the plan; exact model and driver (ST7789 vs ST7796) not chosen.

## Features
_One line each. Full behavior is in [USAGE.md](./USAGE.md). "v1" = first usable version._
- **(v1)** Live tracking: acceleration, braking, turning force, speed, average speed, trip distance, tilt angle.
- **(v1)** Trip driving score at trip end (starts at 100; each event costs points by severity tier and duration, and more events cost more; placeholder numbers).
- **(v1)** Manual trip start/stop button, with optional auto trip start and end (off by default; stop time configurable, default 10 minutes).
- **(v1)** Multiple screens with animations driven by sensor input (g-ball, brake/accel bar, tilt indicator) and automatic screen switching.
- **(v1)** Five buttons: Up, Down, OK, Shortcut (assignable), View (switch screens).
- **(v1)** Button-driven settings menu; nearly every setting changeable on the device.
- **(v1)** Optional menu lock while moving, with a passenger-mode toggle.
- **(v1)** SD card logging to CSV: one trip file per trip, summary.csv and events.csv; each row summarizes its interval at full sensor rate; settings to turn logging on/off and choose overwrite-oldest or stop when full.
- **(v1)** Config file on the SD card (config.ini) for bulk settings such as scoring weights and tiers, plus an "Export settings to SD" action.
- **(v1)** Setting to turn off location logging: GPS still powers speed, distance and scoring, but position is never saved.
- **(v1)** Small LiFePO4 backup battery (charger with load sharing and temperature cutoff): clean shutdown on power loss, trip summary shown after power-off, rides through engine-start power dips, and fully powers down when parked.
- **(v1)** Real-time clock (DS3231) for correct timestamps.
- **(v1)** Guided manual calibration (still, accelerate to ~20 mph and brake, left turn, right turn) with beeps and progress bar; saved to flash.
- **(v1)** Auto-level (fallback) and manual level adjust (pitch/roll offset, "zero here", nudge with buttons).
- **(v1)** Driver ID: choose Driver 1-8 in the menu; the driver is logged with each trip. Settings are stored in car-wide and per-driver groups from the start so per-driver settings can be added later.
- **(v1)** Speaker hardware (audio amplifier chip and small speaker) installed and wired alongside the buzzer; the firmware is designed to support it, with no voice features yet.
- **(v1)** First-time setup on first start: units, time zone and daylight saving, clock, driver, and an offer to calibrate.
- **(v1)** Light sensor auto-dims the screen; barometer provides relative altitude change and air temperature (logged and available for screens).
- **(After v1)** Steadiness credit in the driving score: credit for the share of moving time driven calmly.
- **(v1.5)** 0-60 timer with its own CSV file (zero_to_sixty.csv).
- **(v1.5)** Fill-up tracker with GPS-based mileage counters, MPG per tank, and fuel_fillups.csv.
- **(v1.5)** Fuel economy score (trip efficiency score, plus a tank MPG score when fill-ups are logged), separate from the driving score.
- **(v1.5)** Estimated MPG per trip that works with or without the fill-up tracker; when fill-ups are logged, the device compares the estimate with the real tank MPG.
- **(v1.5)** Spoken notifications from pre-recorded voice clips (for example spoken calibration steps); speaker settings to be determined. The buzzer stays for beeps.
- **(v2)** Bluetooth Low Energy configuration with a physical on/off switch.
- **(v2 or v3)** Trip management in the menu: erase particular trips, clear all trips, or erase particular data from a trip (for example only its GPS location data).
- **(Future, optional)** NFC driver recognition and per-driver settings. Not sure NFC is worth doing.
- **(Future)** Ride character: a ride mix (share of time smooth, rough, spirited) and a comfort score, separate from the driving and fuel scores.
- **(Future)** Catalog of every piece of information the sensors can provide, to choose what goes on dashboard screens (for example heading, time, altitude). A starter list is in USAGE.md.
- **(Future)** Computer trip viewer: route on a map colored by speed/g-force with synced charts.
- **(Future)** Phone viewing and downloading of past trips over Bluetooth (whole or in part).
- **(Future)** OBD-II input (speed, throttle, fuel rate).
- **(Future, lowest priority)** Firmware updates over Bluetooth.
- **(Future)** Local-only Wi-Fi access point for fast downloads, USB mass storage mode, speaker voice prompts.

## Tech Stack
- **Language:** C++ using the Arduino framework, built with PlatformIO in VS Code (decided).
- **Microcontroller:** ESP32-S3 (dual core, BLE). One core for display/UI, one for sensing and logging.
- **Display library:** LVGL or TFT_eSPI (open).
- **Bluetooth stack:** NimBLE (v2).
- **Computer-side tools:** Python for calibration experiments, synthetic trip generators, and the trip viewer (or a single HTML page).
- **Testing:** the "brain" logic (scoring, calibration math, state machine, MPG math) is written as plain C++ with no hardware calls so it can be tested on a computer with PlatformIO native tests.
- **Repo:** GitHub, private for now.

### Hardware (planned)
All prices approximate and unchecked. v1 build is estimated at about $95-190; full build about $110-225.

**Design principle (decided):** v1 hardware includes the base features (backup battery, speaker, barometer, light sensor), so they never need a rebuild. Nice-to-have extras (the Bluetooth switch, NFC reader, OBD-II) are not planned for in v1 hardware and may need small hardware additions later.

| Part | Suggested | v1? | Approx. cost (USD) |
| --- | --- | --- | --- |
| Microcontroller | ESP32-S3 dev board, 16 MB flash, 8 MB PSRAM | v1 | 8-15 |
| IMU | MPU-6050 for first prototype; ICM-42688-P or LSM6DSO later | v1 | 3-20 |
| GPS | u-blox MAX-M10S or NEO-M9N, 10 Hz or better, external antenna | v1 | 15-35 |
| Display | 2.8"-3.5" IPS SPI TFT (ST7789 or ST7796), no touch | v1 | 10-20 |
| Storage | microSD module (own bus) and a high-endurance 8-32 GB card | v1 | 8-15 |
| Buttons | 5 tactile or panel-mount buttons | v1 | 4-10 |
| Buzzer | Piezo (kept for beeps and character) | v1 | 1-2 |
| Speaker and audio amplifier | I2S amplifier chip (for example MAX98357A) with a small 3 W speaker, mounted away from the IMU | v1 hardware; voice clips v1.5 | 5-12 |
| Power | Quality 2 A USB car charger and USB-C cable | v1 | 10-17 |
| Enclosure and mount | 3D-printed case and dash mount (or purchased case) | v1 | 5-15 |
| Wiring and misc | Headers, wire, fuse, TVS diode | v1 | 5-10 |
| Real-time clock | DS3231 breakout with coin cell (quality board or rechargeable LIR2032; see risks) | v1 | 2-8 |
| Backup battery | Small LiFePO4 cell (1S, with protection) and a charger / load-sharing module that explicitly supports LiFePO4 and has a temperature sensor input, plus a boost or regulator to power the board. Never LiPo | v1 | 10-25 |
| BLE switch | Slide switch, added when Bluetooth is built (not part of v1 hardware) | v2 | 1 |
| Light sensor | VEML7700 (auto-dims the screen; shares the I2C bus) | v1 | 3-6 |
| Barometer | BMP390 or BME280 (relative altitude change and air temperature; shares the I2C bus) | v1 | 3-8 |
| NFC reader (optional) | PN532 module with NTAG cards or keyfobs | future | 5-12 |
| CAN transceiver / ELM327 | For OBD-II | future | 5-15 |

### Shopping list notes (read before buying)
- **Pins:** check the exact board's pinout first. The 8 MB PSRAM variant (octal) probably uses extra pins, so the usable pins are likely fewer than the roughly 34 assumed. Consider a board without octal PSRAM, or confirm every needed pin stays free: display, SD card, 5 buttons, speaker audio chip, buzzer, power-loss sense, GPS.
- **Heat and sun:** check the operating temperature ratings of the screen, SD card, backup battery and the real-time clock's coin cell (a parked dash can pass 60 °C), and prefer a bright IPS screen that is readable in sunlight.
- **Real-time clock:** buy a quality DS3231 board, or use a rechargeable LIR2032 cell, because cheap boards can try to charge a non-rechargeable coin cell.
- **Speaker:** mount it away from the motion sensor.
- **I2C addresses:** the DS3231 clock is fixed at address 0x68, and the MPU-6050 and many other IMUs also default to 0x68. Set the IMU's address pin so it uses 0x69 (check the datasheet), or the two will conflict on the shared bus.
- **Backup battery (v1):** LiFePO4 only, never LiPo. The charger module must explicitly support LiFePO4 (about 3.6 V full charge), share the load with USB power, and have a temperature sensor input for the cell. Pick a specific module before buying; compare two or three.
- **USB cable:** get one that carries data, not a charge-only cable, for programming the board.
- **Case filament:** PLA softens at roughly 55-60 °C and a sunlit dash gets hotter than that, so print the case in PETG, ASA or ABS.
- **Shopping documents:** the buying checklist with specs and must-haves is [shopping/SHOPPING_LIST.md](./shopping/SHOPPING_LIST.md); real candidate parts are compared in [shopping/PARTS_COMPARISON.md](./shopping/PARTS_COMPARISON.md).

### Tools and prototyping supplies
| Item | Status |
| --- | --- |
| Multimeter, soldering iron and solder, wire cutters, wire strippers | Already owned |
| 3D printer and filament (for the case and mount) | Already owned |
| Breadboard | Need to buy (about $5-10, approximate) |
| Jumper wires | Check what is on hand; buy a kit if needed (about $5-10, approximate) |
| Filament for the case | Check what is on hand: PETG, ASA or ABS (not PLA) |

## Getting Started

### Prerequisites
- The owner's computer: Windows 11, with VS Code (already installed) and the PlatformIO extension (to be added).
- Python 3 for helper scripts.
- Git and a GitHub account.
- Hardware is not needed to start; see [Build Approach](#build-approach).

### Installation
```bash
# TBD — exact setup steps will be written when the first sub-session walks through
# installing VS Code + PlatformIO and building a first test program.
```

## Usage
The finished device is described in full in [USAGE.md](./USAGE.md). In short: plug it in, it finds GPS, press Shortcut to start a trip (or just drive), and when the trip ends it shows a driving score. All data is on the SD card.

## Project Structure
```
dashboard-driving-score-device/
├── README.md            what and why, scope, risks
├── USAGE.md             full behavior spec
├── CONTRIBUTING.md      how work gets done (solo + sessions)
├── CHANGELOG.md         decisions and changes log
├── SESSION_NOTES.md     sub-sessions write results here
├── shopping/            hardware shopping documents
│   ├── SHOPPING_LIST.md      what to buy, with specs and must-haves
│   ├── PARTS_COMPARISON.md   candidate parts compared, by component
│   ├── SESSION_PROMPT.md     prompt that starts the shopping session
│   └── screenshots/          product page screenshots (no personal info)
├── .gitignore           keeps build output and private drive logs out of Git
└── (planned, not created yet)
    ├── firmware/        PlatformIO project (C++)
    │   ├── src/         hardware layer (sensors, display, SD, buttons)
    │   ├── lib/core/    hardware-free logic (scoring, calibration, state machine, MPG)
    │   └── test/        tests that run on the computer
    ├── tools/           Python scripts: fake drive generator, CSV tools
    ├── viewer/          computer-side trip viewer
    ├── data/samples/    fake or scrubbed example drives (no real locations)
    └── data/private/    your real drive recordings; ignored by Git
```

## Roadmap
1. **Now:** finish planning (docs review in progress) and shop for v1 hardware, timed for Black Friday 2026 (see `shopping/`). The GitHub repo and the SESSION_NOTES workflow are set up.
2. **No-hardware phase:** learn tools; calibration math in Python, then C++; scoring engine tested on fake and phone-recorded drives; trip state machine and button menu; screen layouts in a simulator; trip viewer.
3. **v1 (hardware arrives):** hardware layer, live screens and animations, driving score, SD logging, guided calibration, level adjust, five-button menu, real-time clock, LiFePO4 backup battery, speaker hardware, barometer and light sensor.
4. **v1 accuracy check (once the hardware works):** compare the device side by side with a phone sensor app (g-forces and tilt) and with the car's speedometer and a phone's GPS speed; record the differences; adjust calibration, filtering and thresholds until the readings can be trusted.
5. **v1.5:** 0-60 timer, fill-up tracker, fuel economy score, voice clips and spoken notifications.
6. **v2:** Bluetooth configuration; needs a small hardware addition (a physical on/off switch). A custom PCB for this hardware revision is being considered (undecided; see Open Questions).
7. **v2 or v3:** trip management menu (erase particular trips, clear all trips, erase particular data such as location from a trip).
8. **Future:** build the catalog of possible screen data from the sensors; optional NFC driver profiles; a comfort / ride-character rating; phone viewing/download over Bluetooth, computer trip viewer polish, OBD-II, local Wi-Fi download, OTA firmware updates (lowest priority).

## Contributing
See [CONTRIBUTING.md](./CONTRIBUTING.md) for how to get involved.

## Changelog
See [CHANGELOG.md](./CHANGELOG.md) for a history of decisions and changes.

## License
_TBD — repo is private and solo for now; pick a license if it goes public._
