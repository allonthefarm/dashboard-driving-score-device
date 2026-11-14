# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]
_While this project is still being planned, this section doubles as a running log of decisions: new scope, changed direction, things ruled out. Once the first version actually ships, rename this section to the version number and date, and start a fresh Unreleased section above it._

### Added
- Core concept: offline dash-mounted ESP32 device tracking acceleration, braking, turning force, speed, average speed, trip distance and tilt angle, with a trip driving score.
- Hardware plan: ESP32-S3, IMU, 10 Hz GPS, 2.8"-3.5" IPS TFT, microSD, five buttons, buzzer, USB car power (see README Hardware table).
- Five buttons decided: Up, Down, OK, Shortcut (assignable), View.
- Button-driven settings menu; nearly all settings changeable on the device, Bluetooth as a convenience.
- Optional menu lock while moving (default on, 5 mph) with a passenger-mode toggle (gesture to be decided).
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
- 2026-10-20: Project kicked off with placeholder name "Dashboard Driving Score Device"; four planning docs created; private GitHub repo planned.
- 2026-10-20: `.gitignore` added (PlatformIO output, Python files, OS junk, secrets) plus a privacy rule: real drive logs go in `data/private/` and are never committed.
- 2026-10-20: Trip management menu planned for v2 or v3 (erase particular trips, clear all trips, erase particular data such as location from a trip). No erase action in v1.
- 2026-10-20: Future task logged: build a catalog of all information available from the sensors that could go on dashboard screens (starter list in USAGE.md).
- 2026-10-20: Setting to turn off location logging (v1). No features are disabled; GPS still drives speed, distance, scoring, 0-60 and fill-up mileage, but positions are never saved, only held briefly in RAM.
- 2026-11-01: Minimal driver profiles in v1: Driver 1-8 selected in the menu and logged with every trip (trip header and summary.csv). Settings structured as car-wide and per-driver groups from the start. NFC and per-driver settings remain future.
- 2026-11-01: Future ideas logged: optional NFC driver profiles; comfort / ride-character rating (smoothness plus intensity, placing a ride as comfortable, rough or spirited). Neither is committed.
- 2026-11-01: Scoring rework direction: severity tiers and duration per event, more events cost more, penalties not scaled by distance, a steadiness credit for calm driving, and road roughness never affects the driving score. Events cross-checked against GPS and gyro to ignore bump spikes.
- 2026-11-01: Ride character (future): ride mix of smooth, rough and spirited time plus a comfort score, deliberately not combined into one number.
- 2026-11-14: Estimated MPG per trip works with or without the fill-up tracker (baseline from the owner's reference MPG, or from their own tanks when available). When fill-ups exist, the raw estimate is compared with real tank MPG, and both raw and calibrated estimates are saved.
- 2026-11-14: Settings table gains auto trip end delay (default to be decided), screen pause after View, speed limit value, and an optional driver name.
- 2026-11-14: Speaker hardware in v1 (audio amplifier chip and small speaker) alongside the buzzer, which stays for beeps and character. Voice clips and spoken notifications are v1.5; speaker settings to be determined.
- 2026-11-14: Trip file rows now summarize each interval at full sensor rate (average and peak g, vertical vibration level, maximum jerk), so road vibration and short events are not missed at 10 Hz.
- 2026-11-14: New `events.csv` (one row per event with type, peak, duration, and whether GPS or gyro confirmed it), so tiered scoring and re-scoring have the data they need.
- 2026-11-14: Fuel-score inputs (idle seconds, speed-swing energy per mile, time above efficient speed) are logged from v1, ahead of the v1.5 fuel score.
- 2026-11-14: Config file on the SD card (`config.ini`, INI style proposed): read at boot, SD values override saved settings only when the file has changed, bad lines ignored with a notice, "Export settings to SD" action, and a format version number at the start of every CSV and the config file.
- 2026-11-14: First-time setup on first start (units, time zone and daylight saving, clock, driver, optional calibration), and time zone and daylight saving added to settings. Row timestamps are UTC; file names and screens use local time.
- 2026-11-14: Roadmap step added: v1 accuracy check against a phone sensor app, the speedometer and phone GPS.
- 2026-11-14: README gains shopping list notes (pin check, heat and sun, clock cell, speaker placement, LiFePO4 only, data USB cable) and a tools list (breadboard to buy; jumper wires to check).
- 2026-11-14: Custom PCB logged as a future consideration for the v2 hardware revision (undecided; not for v1). Reasons: reliability in a hot, vibrating car, size, a rigid IMU mount, easy repeat builds if others want one, and a learning project. Plan: keep a pin-to-pin record during v1 as the future schematic, and decide after the v1 accuracy check.
- 2026-11-14: `shopping/` folder added: SHOPPING_LIST.md (buying checklist with specs and must-haves), PARTS_COMPARISON.md (candidate parts compared by component, with screenshots), and SESSION_PROMPT.md (starts the shopping session). Shopping is timed for Black Friday 2026 (November 27).
- 2026-11-14: Case filament note: print the case in PETG, ASA or ABS, not PLA (heat).

### Changed
- Microcontroller upgraded in plan from ESP32 to ESP32-S3 (16 MB flash, 8 MB PSRAM) for headroom.
- GPS and display upgraded in plan (10 Hz module with external antenna; 2.8"-3.5" IPS).
- Menu lock changed from always-on to an optional setting.
- Scoring formula marked as placeholder, to be reworked by the owner later; weights live in config.
- 2026-10-20: Location logging default confirmed On; GPS module's own last-position memory is not cleared unless needed.
- 2026-10-20: Budget ceiling set to about $500 (re-evaluate beyond that); v1 estimated at about $85-160.
- 2026-10-20: Supercapacitor hold-up moved from v1.5 into v1 so shutdowns are never rough; optional LiFePO4 backup battery moved to v2.
- 2026-10-20: DS3231 real-time clock included in v1 (correct timestamps before GPS lock, no internet time source).
- 2026-10-20: Development environment confirmed: Windows 11 with VS Code already installed; PlatformIO extension to be added.
- 2026-11-01: Trip files now log vertical g (gravity removed) in v1, so a comfort rating can be computed from old trips later.
- 2026-11-14: Level adjust "zero here" is now a short OK press with a confirmation (it was hold OK, which conflicted with long-press Back).
- 2026-11-14: Passenger-mode gesture (was hold Up and Down for 3 s) is back to to-be-decided; long presses never fire on release.
- 2026-11-14: Replaced the stale "Fuel economy calibration" setting with "Calibrate estimated MPG from fill-up results"; the estimate stays independent of the fill-up tracker.
- 2026-11-14: Docs cleanup: Out of Scope reworded for the v1 driver ID; README prerequisites and roadmap brought up to date; features and changelog put in order.
- 2026-11-14: v1 cost estimate raised to about $90-170 (full build about $105-220) to include the speaker; pin estimate raised by about 3.
- 2026-11-14: v1 scoring scope: tiered events plus event count (no steadiness credit until after v1 works).
- 2026-11-14: Auto trip start and end is now off by default; stop time is configurable with a default of 10 minutes. Power-off/on continuity is to be discussed.
- 2026-11-14: Idle grace period in the fuel efficiency score raised from 30 seconds to 2 minutes (a guess, to tune) so ordinary red lights do not count as idling.
- 2026-11-14: Android only for phone features; iPhone is not supported.
- 2026-11-14: Supercapacitor hold-up replaced by a small LiFePO4 backup battery in v1 (charger with load sharing, temperature sensor, full power-down when parked). It gives time for a clean shutdown, a trip summary after power-off, and ride-through of engine-start dips.
- 2026-11-14: Design principle: v1 hardware includes everything v1 and v2 need, so v2 is firmware only. The Bluetooth slide switch and the backup battery move into v1 hardware; the optional v2 battery feature is removed.
- 2026-11-14: v1 cost estimate adjusted to about $90-175; pin estimate to about 30 of roughly 34 (adds battery voltage sensing).
- 2026-11-14: Light sensor (auto-dim) and barometer (relative altitude change, air temperature) moved into v1 hardware; the barometer's readings are logged in trip files. They share the I2C bus, so no extra pins.
- 2026-11-14: Bluetooth switch moved back to v2 hardware, and NFC and OBD are not planned for in v1 hardware. The design principle is narrowed: v1 includes the base features (battery, speaker, barometer, light sensor) so they never need a rebuild; nice-to-have extras may need small hardware additions later.
- 2026-11-14: v1 cost estimate now about $95-190 (full build about $110-225); pin estimate about 29 of roughly 34.
- 2026-11-14: Shopping note added: the DS3231 clock and many IMUs share I2C address 0x68, so the IMU's address must be changed to 0x69.

### Deprecated
-

### Removed
-

### Fixed
-

### Security
-
