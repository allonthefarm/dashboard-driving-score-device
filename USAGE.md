# How the Dashboard Driving Score Device Works

_This document describes the complete experience of using the finished device: every major flow, from first power-on through a scored trip. It is the spec a builder would read to know what must exist and how it should behave. It does not cover installation (see [README.md](./README.md#getting-started)) or how the build is approached. Each section is tagged with the version it ships in: **v1**, **v1.5**, **v2**, or **Future** (see the README roadmap)._

## Who's Using This
One owner who drives their own car and wants feedback on how smoothly they drive. The driver starts and stops trips and glances at the screen. Sometimes a passenger operates the menus (hence the optional menu lock). The owner also configures the device, calibrates it, and reviews logged data on a computer.

## The Core Experience
Plug the device into the car's USB power. It boots, finds GPS, and shows live driving data on a color screen: speed, g-force animations, tilt. Each trip is tracked and logged. When the trip ends, the device shows a driving score and counts of events like hard braking. Everything is saved to an SD card as CSV files. Nothing ever goes to the internet.

## Hardware Summary (what the user sees and touches)
- Color screen, 2.8"-3.5", no touch.
- Five buttons: **Up, Down, OK, Shortcut, View**.
- Buzzer for beeps.
- SD card slot.
- USB power input.
- (v2) A slide switch that turns Bluetooth on or off.

## Controls

### Button map
| Button | Short press | Long press |
| --- | --- | --- |
| Up | Previous menu item, or increase a value (hold to repeat) | Jump to top of a list |
| Down | Next menu item, or decrease a value (hold to repeat) | Jump to bottom of a list |
| OK | Open the menu, select, confirm | Back one level |
| Shortcut | Default: start or stop a trip | Default: arm the 0-60 timer (v1.5; until then, no action) |
| View | Next screen | Turn auto screen switching on or off |

- Short and long press of the Shortcut button can each be assigned to: start/stop trip, arm 0-60 (v1.5), log a fill-up (v1.5), mark an event (a timestamped bookmark in the log), dim the display, start calibration, or zero the level here.
- Pressing View pauses auto screen switching for a short time (default 10 seconds), then it resumes (if auto switching is on).
- Buttons are debounced. One press produces one action.

### Menu lock while moving (optional)
- Setting "Menu lock while moving": **On** by default, with a speed threshold of **5 mph**.
- While locked and the car is moving above the threshold, the menu cannot be opened; only View and Shortcut work.
- **Passenger mode:** holding **Up and Down together for 3 seconds** toggles the lock off or on, even while moving. A short beep confirms each change. Setting "Passenger mode shortcut" can be turned off.
- After a power cycle the lock returns to its saved setting (On unless the user set it to stay Off).
- Calibration is started while stopped and then runs hands-free with beeps (see below).

## Walkthrough: Powering On (v1)
1. The car supplies USB power. The device boots and shows a short splash screen with the firmware version.
2. It checks the SD card. A small status icon shows card OK, card missing, or card full.
3. It loads saved settings and calibration from internal flash. If no calibration exists, a "Not calibrated" notice shows on the status bar and the device uses auto-level only.
4. It searches for GPS. The status bar shows the GPS state (searching, fix, number of satellites optional). Speed, distance and average speed show "--" until there is a fix.
5. The device enters **Idle** and shows the live screens.
6. Clock: the real-time clock (DS3231) supplies the time immediately at boot, so files get correct timestamps before GPS locks. GPS time corrects the RTC whenever a fix is available. Time zone is chosen at first boot. If the RTC lost power (dead coin cell), the device shows "Clock not set" until GPS gives the time.

## Walkthrough: A Normal Trip (v1)
1. **Start.** Either the user presses Shortcut (default action), or the device auto-detects a trip when speed rises above a threshold (auto detect can be turned off). The screen shows "Trip started" briefly. A new trip file is created on the SD card, named by date and time, for example `2026-10-18_1349.csv`.
2. **During the trip.** The live screens update. Speed, average speed, trip distance and trip timer show on the Speed screen. The g-ball moves with lateral and longitudinal g. The brake/accel bar fills with force. Events (hard brake, harsh acceleration, hard cornering) are detected, flagged in the log, and cost points.
3. **Auto screen switching** (if on): the device shows the g-force view during hard cornering, the brake bar during hard braking, and the Speed screen when cruising, with a hold of 2-3 seconds so it doesn't flicker.
4. **End.** Either the user presses Shortcut again, or auto detect ends the trip after the car has been stopped for a set time (default to be decided; see README open questions), or power is cut (the car turns off).
5. **Summary.** The Trip Summary screen shows the driving score, distance, average speed, duration, and counts of each event type. One row is added to `summary.csv`. The device returns to Idle after the user presses any button or after a timeout.
6. **If power is cut** (car turned off), the power-loss sense input triggers a shutdown routine running on supercapacitor hold-up: end the trip, flush and close the SD file cleanly, write the summary row, and save counters and settings to flash. The screen shows the summary while the hold-up lasts. On the next boot, the last trip's score is shown briefly. (v2: with the optional battery, the unit stays on after power-off to keep showing the summary.)

## Walkthrough: Screens (v1)
Screens (cycled with View):
- **Speed:** current speed, average speed, trip distance, trip timer.
- **G-force:** a ball that slides with lateral and longitudinal g.
- **Brake / accel bar:** a bar that fills with braking or acceleration force.
- **Tilt:** pitch and roll angle indicator.
- **Trip summary:** score, distance, average speed, duration, event counts (shown after a trip ends, and reachable from the menu for the last trip).
- **0-60 (v1.5):** armed state, live timer, result.

Always-visible status bar: GPS state, SD state (logging, missing, full), trip active indicator, calibration status. (v2: Bluetooth on/off.)

Animations are driven directly by sensor input. Screen brightness is adjustable; with the optional light sensor (later), it dims automatically.

## Walkthrough: Guided Calibration (v1)
Purpose: the device can be mounted at any angle, so readings must be rotated into the car's frame (forward, left, down). Run it in an empty parking lot. A passenger can run it.
1. Start from the menu (Calibration), the Shortcut button if assigned, or later over Bluetooth. The car must be stopped.
2. **Step 1, sit still and level:** "Hold still." A progress bar fills over a few seconds. Gravity gives "down"; gyro bias is measured and subtracted.
3. **Step 2, accelerate to about 20 mph:** "Accelerate straight to 20 mph." A progress bar shows the push registering. The push direction with gravity removed is "forward"; GPS speed rising confirms the direction.
4. **Step 3, brake to a stop:** confirms forward and checks consistency.
5. **Step 4, turn left, then right:** "Turn left until it registers", then "Turn right until it registers." Left/right comes from down × forward; the gyro's yaw sign must flip between turns. If it doesn't, the mounting may be mirrored or the data is bad, and the device says so.
6. Each step beeps when it registers and shows a checkmark. The user never needs to look at the screen while driving.
7. Weak or inconsistent runs are rejected with a message like "Too gentle, try again." A minimum peak of about 0.15 g is required.
8. On success, the rotation is saved to internal flash and survives power-off. The screen says "Calibration saved."
9. **Cancel** any time by holding OK. Cancelling leaves the previous calibration unchanged.
10. **Drift check:** if the resting gravity vector later changes noticeably (the mount was bumped), the device suggests recalibrating.

## Walkthrough: Level Adjust (v1)
Purpose: a fallback for when auto-level reads wrong or the car sits on a slope. It stores a pitch and roll offset separate from the mounting calibration, so fixing one never breaks the other.
1. Menu → Level adjust. The screen shows live pitch and roll.
2. Three ways to set the offset:
   - **Nudge:** Up/Down change the offset in 0.1° steps (hold to repeat).
   - **Zero here:** on ground the user knows is level, press and hold OK; the current tilt becomes zero.
   - **Type values** over Bluetooth (v2).
3. Reference tip: a phone level app should lie flat on the dash next to the device or on the car floor. A phone measures the phone, not the car.
4. Offset is limited to ±15°. A "Reset to auto" option restores the automatic level. The manual offset overrides auto-level only when enabled.
5. Hills change pitch constantly while driving, so the offset only fixes the static reference; live gravity from the filtered signal handles slopes.

## Walkthrough: The Settings Menu (v1)
OK opens the menu (unless locked). Up/Down move, OK selects, long-press OK goes back. Menu groups: Settings, Calibration, Level adjust, Logging, Display, About (and Bluetooth in v2, Fuel in v1.5).

### Settings table
Defaults are starting points to tune. "Buttons: No" means bulk or text entry, available only over Bluetooth (v2) or in the config file on the SD card.

| Setting | Default | Buttons | BLE (v2) |
| --- | --- | --- | --- |
| Units (mph/km/h, gallons/liters) | mph | Yes | Yes |
| Logging on/off | On | Yes | Yes |
| Log location (GPS position) to SD card | On | Yes | Yes |
| When SD card is full | Stop | Yes | Yes |
| Overwrite free-space threshold | 50 MB | No | Yes |
| Log rate (Hz) | 10 | Yes | Yes |
| Auto screen switching | On | Yes | Yes |
| Screen hold time (s) | 2 | Yes | Yes |
| Brightness | 70% | Yes | Yes |
| Hard brake threshold (g) | 0.30 | Yes | Yes |
| Harsh acceleration threshold (g) | 0.30 | Yes | Yes |
| Hard cornering threshold (g) | 0.35 | Yes | Yes |
| Speed limit alert / penalty | Off | Yes | Yes |
| Scoring weights | See Scoring | No | Yes (and config file) |
| Level offset (pitch/roll) | Auto | Yes | Yes |
| Run guided calibration | n/a | Yes | Yes |
| Auto trip detect | On | Yes | Yes |
| Fuel economy calibration (fill-up gallons, miles) | None | Yes | Yes |
| Device name / BLE pairing code | Default | No | Yes |
| Factory reset | n/a | Yes (confirm) | Yes |
| Shortcut button action (short / long press) | Trip start-stop / arm 0-60 | Yes | Yes |
| Menu lock while moving (on/off), lock speed | On, 5 mph | Yes | Yes |
| Time zone and daylight saving | Ask at first boot | Yes | Yes |
| Auto-dim with light sensor | On (when sensor present) | Yes | Yes |
| Show trip summary after power-off (seconds; v2, needs the optional battery) | 60 | Yes | Yes |
| Fuel tank size | None | Yes | Yes |
| Reference MPG (EPA combined) | None | Yes | Yes |
| Ask for odometer at fill-up | Off | Yes | Yes |
| Fuel efficiency thresholds (efficient top speed, idle grace period) | 65 mph, 30 s | Yes | Yes (and config file) |
| Passenger mode shortcut (hold Up and Down) | On | Yes | Yes |

Scoring weights and thresholds can also be edited in a config file on the SD card, so tuning does not need a reflash.

## Walkthrough: Data Logging (v1)
Logging runs in the background during any trip and is not a separate state. It never blocks sensing or animation (SD writes go through a RAM queue on the second core).

### Files on the SD card
| File | Contents |
| --- | --- |
| One trip file per trip, named by date and time, for example `2026-10-18_1349.csv` | Header with firmware version and calibration values, then rows at 10-20 Hz: timestamp, lat, lon (blank when location logging is off), GPS fix quality, speed, longitudinal g, lateral g, yaw rate, pitch, roll, vertical g (gravity removed), event flag (OBD fields later) |
| `summary.csv`, one row per trip | Date, duration, distance, average speed, driving score, fuel economy score (v1.5), event counts, idle seconds, speed-swing energy per mile, time above efficient speed, estimated MPG (v1.5) |
| `zero_to_sixty.csv` (v1.5), one row per run | Date and time, time to 60, peak g, GPS quality at start and end, rolling-start flag, valid/cancelled flag |
| `fuel_fillups.csv` (v1.5), one row per fill-up | Date and time, gallons, full or partial, price per gallon (optional), odometer (optional), GPS miles since last fill-up, GPS miles since last full fill-up, tank MPG (blank until a full fill-up closes the interval), missed-fill flag, notes |

Raw events are kept so old trips can be re-scored under new rules. Cancelled and invalid 0-60 runs are logged with a flag.

### Storage behavior
- **Logging on/off** is a setting. When off, the SD card is not touched.
- **When full:** Stop (show a "card full" icon) or Overwrite (delete the oldest whole trip files until there is room; never delete part of a file). Overwrite starts when free space drops below the threshold (default 50 MB), not at zero.
- Format: FAT32; use a quality 8-32 GB card. A 15-column CSV at 10 Hz is about 1.5-3 MB per hour.
- **If the SD card is missing:** the device still works and shows a "no card" icon. (v1.5: the last several fill-ups are kept in internal flash and copied to the card later.)
- Writes are buffered in RAM and flushed about once per second.

### Location logging setting (v1)
The setting "Log location to SD card" (default On) controls whether positions are ever saved. When **Off**:
- Latitude, longitude and absolute altitude are never written to the SD card or internal flash. The columns stay in trip files but are blank.
- **No feature is disabled.** The GPS still runs live and still drives speed, average speed, trip distance, the driving score, the 0-60 timer, fill-up mileage counters and the clock. Those need only speed and the change between fixes, not saved positions.
- Position data exists only in RAM, only as long as needed: the device keeps the previous fix to work out distance, replaces it with the next one, and keeps no history. It stores no trail, last-known position, or home/away places anywhere.
- Distance and mileage counters are still saved, since they are not locations. Relative altitude change (for fuel estimates) may be kept; absolute altitude is not.
- Changes take effect immediately. Turning it off mid-trip only affects rows from that point on; already-logged points stay on the card.
- Trips logged with location off have no map in the computer trip viewer, only the charts.
- The GPS module's own backup memory may remember its last position. This is left alone unless testing shows it must be cleared for the setting to work properly.

## Walkthrough: Driving Score (v1)
**Status: placeholder.** The owner will rework scoring later; weights and thresholds must live in config (flash and the SD config file), never hardcoded.
- Score starts at 100 per trip and loses points per event: hard braking, harsh acceleration, hard cornering, and optionally speeding.
- The score is shown on the Trip Summary screen and written to `summary.csv`.
- Because raw events are logged, old trips can be re-scored with new rules.
- **Planned rework direction (not final):** escalating penalties by severity tier for each event type, so extreme events cost disproportionately more (for example a small penalty at a moderate level, much larger penalties at higher levels). An event is one continuous stretch above a threshold, scored by its peak, so one long corner is not counted hundreds of times. Tiers are separate for braking, acceleration and cornering, live in config, and are tuned with real recorded drives. Open: whether a trip's penalties are totaled as-is or scaled by distance, and whether alternate scoring profiles (for example Normal and Spirited) are wanted.

## Walkthrough: 0-60 Timer (v1.5)
1. Arm it with the assigned Shortcut press, or from the menu. The 0-60 screen shows "Armed."
2. While stopped, the device waits. Timing starts at first movement (detected with the IMU for precision) and stops when GPS speed crosses 60 mph (interpolated between fixes; needs a 10 Hz GPS).
3. The result and peak g show on screen. A row is written to `zero_to_sixty.csv`.
4. Runs that did not start from a stop, or lost GPS quality, are saved with an invalid flag.

## Walkthrough: Fill-up Tracker (v1.5)
Works without OBD. The device counts GPS miles itself, so at the pump the user only enters what the pump shows.
1. **Automatic counters** (kept in flash, saved at trip end and on power loss): lifetime GPS miles, miles since last fill-up, miles since last full fill-up.
2. At the pump, use the Shortcut button (if assigned to "log a fill-up") or the menu. The device pre-fills both mile counters from GPS.
3. Enter gallons (hold Up/Down to repeat). Choose Full or Partial. Optionally enter price per gallon and odometer.
4. Confirm. The record goes to `fuel_fillups.csv` and the counters reset.
5. **MPG math:** full-to-full. Tank MPG is the miles between two full fill-ups divided by all gallons added over that stretch, including partial fills. A partial fill produces no MPG number until the next full fill-up. A "missed a fill-up" flag excludes that stretch.
6. **GPS gaps:** GPS miles only count while the device was powered with a fix, so they can read low. Entering the odometer lets the device compare, flag a gap, and offer to use the odometer figure. It also tracks the odometer-to-GPS ratio over time.
7. Shown on the device: MPG per tank, running and lifetime averages, cost per mile (if prices are entered), miles since last fill-up, and a range estimate.

## Walkthrough: Fuel Economy Score (v1.5)
A separate 0-100 score from the driving score, in two parts. All thresholds and weights are starting points in config.
1. **Trip efficiency score.** Works on every trip. Starts at 100 and loses points for: harsh acceleration events, hard braking events, speed swings (energy added by accelerating per mile, computed as the sum of increases in v² divided by 2, per mile, from GPS speed), time above the efficient speed (default 65 mph), and idling (stopped longer than a grace period, default 30 s, while the trip is active; without OBD the device cannot tell if the engine is off).
2. **Tank MPG score.** From the fill-up tracker. Scored against the reference MPG (EPA combined, entered once): 50 at the reference, 100 at 25% better, 0 at 25% worse. With no reference, each tank is compared to the owner's own average.
3. **Estimated MPG per trip.** After about three full tanks, the device builds a baseline from the owner's own MPG, adjusts each trip by speed-swing and speed relative to average, then rescales so trips in a tank add up to the pump figure. Hills use barometer or GPS altitude. Labeled "estimated" on screen.
4. **With OBD (future):** fuel rate (PID 0x5E) or mass airflow gives measured fuel per trip and replaces the estimate.

## Walkthrough: Bluetooth (v2)
- Bluetooth Low Energy is built into the ESP32. A physical slide switch controls it: when off, the firmware never starts the BLE radio.
- Over Bluetooth the user can change any setting (same list as the buttons), enter level offsets, start calibration, and edit scoring weights, using a generic phone app first (for example nRF Connect) and a simple web-bluetooth page later.
- **Future:** view past trips on the phone; download whole trips or parts of trips; update firmware over Bluetooth (lowest priority, needs dual flash slots and rollback; until then, update by USB).

## Walkthrough: Trip Management (v2 or v3)
Not in v1. In v1, trips are deleted by removing the SD card and deleting files on a computer. Later, the menu gets a Trips section:
1. **Browse trips:** a list by date and time, with score and distance, newest first.
2. **Erase a trip:** pick a trip, confirm, and its file is deleted and its row removed from `summary.csv` (and related rows in other files, where applicable).
3. **Clear all trips:** a confirmed action that deletes all trip files and empties `summary.csv`. 0-60 and fill-up records are kept unless the user also chooses to clear them separately.
4. **Erase particular data from a trip:** pick a trip and a kind of data to remove, for example only the location data (lat, lon). The rest of the trip, including the score and charts, stays. The file is rewritten safely (write a new file, then replace the old one) so a power loss cannot corrupt it.
5. Every erase asks for confirmation, and is blocked while a trip is active or the car is moving (unless passenger mode is on).

## Future: Comfort / Ride-Character Rating
A third rating, separate from the driving score and the fuel score. It describes how the ride *feels*, which is different from how well you drove. Idea: two measures, which together place the ride among three kinds:
- **Smoothness:** how gentle the ride is. Built from abruptness of starts and stops (jerk, the rate of change of acceleration), and vertical vibration from the road (bumpiness).
- **Intensity:** how much g-force the ride has (the same cornering, braking and acceleration data the driving score uses).
- **Comfortable:** high smoothness, low intensity (a luxury sedan).
- **Rough:** low smoothness, such as off-roading in a Jeep.
- **Spirited:** high intensity but still smooth and controlled (a sports car on a curvy back road). Not comfort-seeking, but not uncomfortable either.
Possible display: a dot on a small map with smoothness on one axis and intensity on the other, with the three zones labeled, shown in the trip summary. Open: one rating or the two-axis view; thresholds; how a dash-mounted sensor approximates cabin comfort. To keep this possible, v1 trip files already log vertical g with gravity removed.

## Future: Driver Profiles (NFC, optional)
Idea, not committed. A small NFC reader recognizes a tag or keyfob to select the driver.
1. Tap a driver's tag on the device; the screen shows the driver's name or color and a brief confirmation.
2. Trips are tagged with the driver in `summary.csv`, and the Trip Summary and history can show scores per driver.
3. If no tag is tapped, the device uses the last driver or asks, and a menu fallback always lets the user pick a driver without a tag.
4. Open: what is per driver (scores, history, shortcut settings, units) versus per car (calibration, level offset, fill-up counters and mileage).

## Future: Screen Data Catalog
The sensors can provide more than the v1 screens show. A future task is to build a complete list of everything available from the GPS, IMU and other sensors, mark what is useful to show on a screen, and then decide new screens. Starter list (to be expanded and checked against the real hardware):
- **From GPS:** heading (direction of travel, valid when moving), time of day, date, altitude, number of satellites, fix quality, speed accuracy.
- **From the IMU:** pitch, roll, peak g since trip start, current g, yaw rate.
- **From the barometer (later hardware):** altitude change, air temperature, pressure trend.
- **From trip math:** maximum speed, elapsed time, stopped time, moving average speed, best 0-60 time, estimated range (v1.5).
- **From the power system:** supercapacitor voltage, input voltage.

## Walkthrough: Computer Trip Viewer (Future)
A page or small program run on the owner's computer, with the SD card data:
1. The user opens it and drops a trip CSV in.
2. The route is drawn on a map, colored by speed, braking, or g-force.
3. Hovering or scrubbing a timeline moves a marker along the route and shows all values at that point, with synced charts for speed, acceleration and turning.
4. Trips logged with location off show charts only, with no map. GPS fix quality flags bad stretches. Map tiles need the computer to be online; offline tiles are a later option.

## Edge Cases & Error States
- **No GPS fix:** speed, distance and average show "--"; trips can still start; the IMU keeps working; log rows have a fix-quality flag. 0-60 is disabled.
- **No SD card or card full:** device still works; status icon shows the problem; with Overwrite set, oldest whole trip files are deleted first.
- **SD card write fails mid-trip:** show an error icon; keep running; retry; never freeze the screen.
- **Not calibrated:** status bar notice; readings use auto-level and may be rotated wrong; recommend calibrating.
- **Calibration fails or is cancelled:** previous calibration stays.
- **Power cut mid-trip:** the supercapacitor hold-up closes the file cleanly and saves the summary. If hold-up fails, at most about 1 second of log is lost because the log is flushed about once per second.
- **Mount bumped:** gravity drift triggers a "recalibrate?" suggestion.
- **Menu locked while driving:** only View and Shortcut work; passenger mode can toggle the lock.
- **Time unknown:** if the RTC lost power and GPS has no fix yet, files are named by a boot counter until time is known, then renamed or flagged.
- **Corrupt or missing settings:** fall back to defaults and say so; factory reset available in the menu.
- **Location logging turned off mid-trip:** later rows have blank lat/lon; the trip score and distance are unaffected.
- **Absurd sensor values:** ignore outliers (for example a spike beyond physical limits) and flag the row.

## Out of Scope
- Any internet connection, cloud, accounts, or telemetry.
- Touchscreen input.
- Writing to the car's data bus.
- Insurance-grade or certified scoring.
- Multiple vehicles or driver profiles (v1).
- Voice assistant features.
- Deleting trips or data from the device itself (v1; added in v2 or v3, see Trip Management).
- Exact per-trip MPG without OBD (estimates only).
