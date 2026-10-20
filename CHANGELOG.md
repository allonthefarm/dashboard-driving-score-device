# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]
_While this project is still being planned, this section doubles as a running log of decisions: new scope, changed direction, things ruled out. Once the first version actually ships, rename this section to the version number and date, and start a fresh Unreleased section above it._

### Added
- 2026-10-20: Project kicked off with placeholder name "Dashboard Driving Score Device"; four planning docs created; private GitHub repo planned.
- Core concept: offline dash-mounted ESP32 device tracking acceleration, braking, turning force, speed, average speed, trip distance and tilt angle, with a trip driving score.
- Hardware plan: ESP32-S3, IMU, 10 Hz GPS, 2.8"-3.5" IPS TFT, microSD, five buttons, buzzer, USB car power (see README Hardware table).
- Five buttons decided: Up, Down, OK, Shortcut (assignable), View.
- Button-driven settings menu; nearly all settings changeable on the device, Bluetooth as a convenience.
- Optional menu lock while moving (default on, 5 mph) with passenger-mode toggle (hold Up and Down for 3 s).
- Guided manual calibration (still, accelerate to ~20 mph, brake, left turn, right turn) plus auto-level fallback.
- Manual level adjust (pitch/roll offset, "zero here", button nudge), capped at ±15°, kept separate from mounting calibration.
- SD logging: per-trip CSV plus summary.csv; settings for logging on/off and overwrite-or-stop when full.
- 0-60 timer with zero_to_sixty.csv (v1.5).
- Fill-up tracker with GPS mile counters, full/partial fill-ups, full-to-full MPG, fuel_fillups.csv (v1.5).
- Separate fuel economy score: trip efficiency score and tank MPG score, with estimated per-trip MPG (v1.5).
- Supercapacitor hold-up for clean shutdown; optional LiFePO4 battery later, never LiPo in a hot dashboard (see Changed for the version each landed in).
- Bluetooth Low Energy configuration with a physical on/off switch (v2).
- Future ideas logged: computer trip viewer (map plus synced charts), phone viewing and download over Bluetooth, OBD-II input, local Wi-Fi download, USB mass storage mode, OTA firmware updates (lowest priority).
- Build approach decided: owner builds it with AI as tutor; sub-session workflow with SESSION_NOTES.md as shared memory.
- 2026-10-20: `.gitignore` added (PlatformIO output, Python files, OS junk, secrets) plus a privacy rule: real drive logs go in `data/private/` and are never committed.
- 2026-10-20: Trip management menu planned for v2 or v3 (erase particular trips, clear all trips, erase particular data such as location from a trip). No erase action in v1.
- 2026-10-20: Future task logged: build a catalog of all information available from the sensors that could go on dashboard screens (starter list in USAGE.md).
- 2026-10-20: Setting to turn off location logging (v1). No features are disabled; GPS still drives speed, distance, scoring, 0-60 and fill-up mileage, but positions are never saved, only held briefly in RAM.

### Changed
- 2026-10-20: Location logging default confirmed On; GPS module's own last-position memory is not cleared unless needed.
- 2026-10-20: Budget ceiling set to about $500 (re-evaluate beyond that); v1 estimated at about $85-160.
- 2026-10-20: Supercapacitor hold-up moved from v1.5 into v1 so shutdowns are never rough; optional LiFePO4 backup battery moved to v2.
- 2026-10-20: DS3231 real-time clock included in v1 (correct timestamps before GPS lock, no internet time source).
- 2026-10-20: Development environment confirmed: Windows 11 with VS Code already installed; PlatformIO extension to be added.
- Microcontroller upgraded in plan from ESP32 to ESP32-S3 (16 MB flash, 8 MB PSRAM) for headroom.
- GPS and display upgraded in plan (10 Hz module with external antenna; 2.8"-3.5" IPS).
- Menu lock changed from always-on to an optional setting.
- Scoring formula marked as placeholder, to be reworked by the owner later; weights live in config.

### Deprecated
-

### Removed
-

### Fixed
-

### Security
-
