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
- Not designed for multiple cars or multiple drivers in v1.

## Open Questions & Risks
- **Budget.** The owner's ceiling is about **$500**; if the plan goes past that, re-evaluate. The v1 build is estimated at about $85-160 and the full build at about $100-210 (all prices approximate and unchecked), so there is plenty of room. Parts are still split into "v1" and "add later" to keep the first build simple.
- **Scope creep.** The feature list has grown a lot. Mitigation: v1 is small (see Roadmap); everything else waits until v1 works.
- **Scoring formula is a placeholder.** Thresholds (for example 0.30 g hard braking) are untested starting values. The owner plans to rework scoring later. Weights live in config, not hardcoded.
- **Real-world data not yet collected.** Plan: record real drives with a phone sensor-logger app to tune thresholds before hardware exists.
- **Mounting and vibration** can ruin IMU readings. Mitigated by filtering, rigid mounting, and guided calibration; only testable with real hardware.
- **GPS reception** on a dash (windshield coating, case placement) is unproven until tested. External antenna is planned.
- **Power loss can corrupt the SD card.** Decided: supercapacitor hold-up is in v1, plus the log flushes about once per second. Risk to verify with real hardware: hold-up time must be long enough to close the file and save counters (a few seconds); a power-loss sense input is needed to trigger the shutdown routine.
- **Battery safety.** LiPo in a hot dashboard is unsafe (parked cars can pass 60 °C). Use supercapacitor, or LiFePO4 with temperature cutoffs, if a battery is ever added.
- **Pin count.** About 26 of roughly 34 usable pins are planned. Adding much more needs an I2C expander.
- **IMU choice.** MPU-6050 for the first prototype (cheap, well documented); ICM-42688-P or LSM6DSO is the upgrade. BNO055 has reported I2C problems on ESP32. Final pick open.
- **Real-time clock.** Decided: include a DS3231 RTC in v1 (shares the I2C bus, no extra pins). Risk: cheap DS3231 boards sometimes try to charge a non-rechargeable CR2032 coin cell; use a quality breakout or a rechargeable LIR2032 cell, or remove the charge resistor.
- **GPS module memory.** The GPS module remembers its last position on its own (it uses it for fast fixes). Decided: not cleared, unless testing on real hardware shows it is needed for the setting to work properly.
- **Auto trip detection rules** (start on motion, end after how long stopped) are not defined yet.
- **Mounting legality and safety.** Dash and windshield mounting rules vary by location; the menu lock while moving exists for safety, and is optional for passenger use. Owner is responsible for checking local rules.
- **Bluetooth firmware updates (OTA)** are the hardest feature and lowest priority; needs dual flash slots and a rollback plan.
- **Sub-session reporting** relies on SESSION_NOTES.md in the repo; no live session-to-session messaging.
- **Single source of truth.** A planning spec also exists as a Claude doc ("Dashboard Driving Score Device — Spec"). After kickoff, this repo is the source of truth; the doc is reference only.
- **Screen choice.** 2.8"-3.5" IPS SPI TFT is the plan; exact model and driver (ST7789 vs ST7796) not chosen.

## Features
_One line each. Full behavior is in [USAGE.md](./USAGE.md). "v1" = first usable version._
- **(v1)** Live tracking: acceleration, braking, turning force, speed, average speed, trip distance, tilt angle.
- **(v1)** Trip driving score at trip end (starts at 100, loses points per event; placeholder formula).
- **(v1)** Manual trip start/stop button, with auto trip detection as a fallback.
- **(v1)** Multiple screens with animations driven by sensor input (g-ball, brake/accel bar, tilt indicator) and automatic screen switching.
- **(v1)** Five buttons: Up, Down, OK, Shortcut (assignable), View (switch screens).
- **(v1)** Button-driven settings menu; nearly every setting changeable on the device.
- **(v1)** Optional menu lock while moving, with a passenger-mode toggle.
- **(v1)** SD card logging to CSV: one trip file per trip plus summary.csv; settings to turn logging on/off and choose overwrite-oldest or stop when full.
- **(v1)** Setting to turn off location logging: GPS still powers speed, distance and scoring, but position is never saved.
- **(v1)** Supercapacitor hold-up for clean shutdown on power loss, and a real-time clock (DS3231) for correct timestamps.
- **(v1)** Guided manual calibration (still, accelerate to ~20 mph and brake, left turn, right turn) with beeps and progress bar; saved to flash.
- **(v1)** Auto-level (fallback) and manual level adjust (pitch/roll offset, "zero here", nudge with buttons).
- **(v1.5)** 0-60 timer with its own CSV file (zero_to_sixty.csv).
- **(v1.5)** Fill-up tracker with GPS-based mileage counters, MPG per tank, and fuel_fillups.csv.
- **(v1.5)** Fuel economy score (trip efficiency score and tank MPG score), separate from the driving score.
- **(v2)** Bluetooth Low Energy configuration with a physical on/off switch.
- **(v2)** Optional small LiFePO4 battery so the unit stays on after power-off (trip summary display) and can be used on a desk.
- **(v2 or v3)** Trip management in the menu: erase particular trips, clear all trips, or erase particular data from a trip (for example only its GPS location data).
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
All prices approximate and unchecked. v1 build is estimated at about $85-160; full build about $100-210.

| Part | Suggested | v1? | Approx. cost (USD) |
| --- | --- | --- | --- |
| Microcontroller | ESP32-S3 dev board, 16 MB flash, 8 MB PSRAM | v1 | 8-15 |
| IMU | MPU-6050 for first prototype; ICM-42688-P or LSM6DSO later | v1 | 3-20 |
| GPS | u-blox MAX-M10S or NEO-M9N, 10 Hz or better, external antenna | v1 | 15-35 |
| Display | 2.8"-3.5" IPS SPI TFT (ST7789 or ST7796), no touch | v1 | 10-20 |
| Storage | microSD module (own bus) and a high-endurance 8-32 GB card | v1 | 8-15 |
| Buttons | 5 tactile or panel-mount buttons | v1 | 4-10 |
| Buzzer | Piezo | v1 | 1-2 |
| Power | Quality 2 A USB car charger and USB-C cable | v1 | 10-17 |
| Enclosure and mount | 3D-printed case and dash mount (or purchased case) | v1 | 5-15 |
| Wiring and misc | Headers, wire, fuse, TVS diode | v1 | 5-10 |
| Real-time clock | DS3231 breakout with coin cell (quality board or rechargeable LIR2032; see risks) | v1 | 2-8 |
| Supercapacitor hold-up | Supercap(s) with charge/protection circuit and power-loss sense input | v1 | 8-20 |
| Backup battery (optional) | Small LiFePO4 with a LiFePO4 charger, power-path, temperature sensor and cutoffs. Never LiPo | v2 | 8-20 |
| BLE switch | Slide switch | v2 | 1 |
| Light sensor | VEML7700 | later | 3-6 |
| Barometer | BMP390 or BME280 | later | 3-8 |
| CAN transceiver / ELM327 | For OBD-II | future | 5-15 |

## Getting Started

### Prerequisites
- A computer (Windows, macOS or Linux) with VS Code and the PlatformIO extension. _TBD: owner's operating system._
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
1. **Now:** finish planning; set up GitHub; define SESSION_NOTES workflow.
2. **No-hardware phase:** learn tools; calibration math in Python, then C++; scoring engine tested on fake and phone-recorded drives; trip state machine and button menu; screen layouts in a simulator; trip viewer.
3. **v1 (hardware arrives):** hardware layer, live screens and animations, driving score, SD logging, guided calibration, level adjust, five-button menu, real-time clock, supercapacitor hold-up.
4. **v1.5:** 0-60 timer, fill-up tracker, fuel economy score.
5. **v2:** Bluetooth configuration with physical switch; optional LiFePO4 backup battery.
6. **v2 or v3:** trip management menu (erase particular trips, clear all trips, erase particular data such as location from a trip).
7. **Future:** build the catalog of possible screen data from the sensors; phone viewing/download over Bluetooth, computer trip viewer polish, OBD-II, local Wi-Fi download, OTA firmware updates (lowest priority).

## Contributing
See [CONTRIBUTING.md](./CONTRIBUTING.md) for how to get involved.

## Changelog
See [CHANGELOG.md](./CHANGELOG.md) for a history of decisions and changes.

## License
_TBD — repo is private and solo for now; pick a license if it goes public._
