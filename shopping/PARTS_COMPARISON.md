# Parts Comparison

Candidate parts for the v1 build, organized by component, so the options can be compared side by side. What each component must do is in [SHOPPING_LIST.md](./SHOPPING_LIST.md); this document holds the real products, with links, prices and screenshots.

This document is filled in by the shopping session: the owner gives a product link and a screenshot of the page, and the session adds a row under the right component.

_Last updated: 2026-11-14 (empty template)_

## How to add an entry (for the shopping session)

1. **Find the component.** Ask the owner if it is not obvious which section the product belongs to. If the product is a kit or covers several components, add it to each relevant section and say so.
2. **Save the screenshot** to `screenshots/<component-folder>/<short-name>_<YYYY-MM-DD>.png`. The folder name is the section's anchor name (for example `screenshots/display/`). Do not save screenshots that show account details, addresses or cart contents.
3. **Read the page.** Work from the screenshot, and try the link as well (shop sites often block automated reading; if the link cannot be opened, say so). Never invent a specification. If the page does not show something, write "not shown".
4. **Fill in the row** using the column rules below.
5. **Check it against the must-haves** in the section header and in the matching block of the shopping list. Mark each as ✅ met, ❌ not met, or ❓ not shown / unclear.
6. **Check for red flags:** chip not named, possible counterfeit or clone, marketplace seller with no history, no temperature rating, 5 V-only logic, wrong connector, too few pins, multi-pack priced as one unit, long shipping time, no returns, a "sale" that is not really lower than the normal price.
7. **Check compatibility with the rest of the build:** voltage, interface, I2C address (see the address map in the shopping list), pin count, connector, physical size.
8. **Compare with the other rows in the same section** in two or three sentences (what it does better, what it does worse, price difference), and suggest a status. The owner decides; only change a status to Chosen or Rejected when the owner says so.
9. **Update the Budget tracker** only for parts marked Chosen.
10. **Commit and push** after each batch of entries. Before the session ends, add a SESSION_NOTES.md entry (done, decisions, open questions, next step). Do not edit README, USAGE, CONTRIBUTING or CHANGELOG; list what should change under "Docs to update".

### Column rules
| Column | What to write |
| --- | --- |
| Candidate | Product name as listed, with the link. |
| Seller / price | Seller, price **per unit**, and the date checked. Note if it is a sale price, and add shipping or tax if shown. |
| Key specs | Only what the page shows (chip, size, voltage, interface, rating, capacity). |
| Must-haves | ✅ ❌ ❓ against each must-have, short. |
| Pros | Short. |
| Cons / risks | Short, including red flags. |
| Screenshot | Relative link to the saved image. |
| Status | Candidate, Shortlist, Chosen, Rejected or Bought. |

## Budget tracker (chosen parts only)
Ceiling: about $500. The v1 estimate in the README is about $95-190.

| Component | Chosen part | Qty | Unit price | Line total | Bought |
| --- | --- | --- | --- | --- | --- |
| 1. Microcontroller board | | | | | ☐ |
| 2. Motion sensor (IMU) | | | | | ☐ |
| 3a. GPS module | | | | | ☐ |
| 3b. GPS antenna | | | | | ☐ |
| 4. Display | | | | | ☐ |
| 5a. microSD module | | | | | ☐ |
| 5b. microSD card | | | | | ☐ |
| 6. Buttons | | | | | ☐ |
| 7. Buzzer | | | | | ☐ |
| 8a. Audio amplifier | | | | | ☐ |
| 8b. Speaker | | | | | ☐ |
| 9a. USB car charger | | | | | ☐ |
| 9b. USB cables | | | | | ☐ |
| 10a. Backup battery cell | | | | | ☐ |
| 10b. Battery charger / load-sharing module | | | | | ☐ |
| 10c. Boost or buck-boost converter | | | | | ☐ |
| 11. Real-time clock | | | | | ☐ |
| 12. Light sensor | | | | | ☐ |
| 13. Barometer | | | | | ☐ |
| 14. Wiring and small parts | | | | | ☐ |
| 15. Case materials and mount | | | | | ☐ |
| **Total** | | | | | |

---

## 1. Microcontroller board
<a id="microcontroller-board"></a>
**Must-haves:** ESP32-S3, 16 MB flash, 8 MB PSRAM; USB-C; most pins broken out; published pinout; enough free pins (about 29 needed).
**Shopping list:** [part 1](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 2. Motion sensor (IMU)
<a id="motion-sensor-imu"></a>
**Must-haves:** I2C; address pin reachable (set to 0x69); named chip; reputable seller. Compare the cheap MPU-6050 with ICM-42688-P and LSM6DSO / LSM6DSOX.
**Shopping list:** [part 2](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 3a. GPS module
<a id="gps-module"></a>
**Must-haves:** u-blox MAX-M10S or NEO-M9N; at least 10 Hz; UART; 3.3 V logic; antenna connector; backup power pin.
**Shopping list:** [part 3](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 3b. GPS antenna
<a id="gps-antenna"></a>
**Must-haves:** Active GNSS antenna; connector matches the module (SMA or u.FL, adapter if not); 2-3 m cable; magnetic or adhesive base.
**Shopping list:** [part 3](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 4. Display
<a id="display"></a>
**Must-haves:** 2.8-3.5 inch IPS SPI TFT; ST7789 or ST7796; no touch; 3.3 V logic; controllable backlight pin; bright; stated temperature range.
**Shopping list:** [part 4](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 5a. microSD module
<a id="microsd-module"></a>
**Must-haves:** SPI; 3.3 V compatible; sturdy push-push socket.
**Shopping list:** [part 5](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 5b. microSD card
<a id="microsd-card"></a>
**Must-haves:** High-endurance; 8-32 GB; FAT32; wide temperature range; genuine (reputable seller).
**Shopping list:** [part 5](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 6. Buttons
<a id="buttons"></a>
**Must-haves:** Prototype: assortment of 6x6 mm tactile switches. Final panel-mount buttons wait for the case design.
**Shopping list:** [part 6](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 7. Buzzer
<a id="buzzer"></a>
**Must-haves:** Passive piezo, about 3.3 V (plays tones).
**Shopping list:** [part 7](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 8a. Audio amplifier
<a id="audio-amplifier"></a>
**Must-haves:** I2S amplifier (MAX98357A or similar); check supply voltage range and output power.
**Shopping list:** [part 8](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 8b. Speaker
<a id="speaker"></a>
**Must-haves:** 3 W, 4 or 8 ohm, about 28-40 mm, with wires or connector; sized for the case.
**Shopping list:** [part 8](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 9a. USB car charger
<a id="car-charger"></a>
**Must-haves:** At least 2 A, reputable brand, compact.
**Shopping list:** [part 9](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 9b. USB cables
<a id="usb-cable"></a>
**Must-haves:** Short USB-A (or C) to USB-C for the car; separate data-capable cable for programming.
**Shopping list:** [part 9](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 10a. Backup battery cell
<a id="battery-cell"></a>
**Must-haves:** 1S LiFePO4, protected, small capacity, reputable seller, stated temperature limits. Never LiPo or 4.2 V lithium-ion.
**Shopping list:** [part 10](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 10b. Battery charger / load-sharing module
<a id="battery-charger"></a>
**Must-haves:** Explicit LiFePO4 support (about 3.6 V); power path (load sharing); temperature sensor input; safe charge current; status output. Research two or three before buying.
**Shopping list:** [part 10](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 10c. Boost or buck-boost converter
<a id="boost-regulator"></a>
**Must-haves:** Runs the board from the battery; handles the switch between USB and battery; efficient at low load. Depends on the power design (open).
**Shopping list:** [part 10](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 11. Real-time clock
<a id="real-time-clock"></a>
**Must-haves:** DS3231 from a reputable maker; coin-cell holder; matching coin cell (watch for the charging-circuit problem on cheap boards).
**Shopping list:** [part 11](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 12. Light sensor
<a id="light-sensor"></a>
**Must-haves:** VEML7700 breakout (I2C 0x10).
**Shopping list:** [part 12](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 13. Barometer
<a id="barometer"></a>
**Must-haves:** BMP390 (preferred) or BME280 / BMP280 breakout (I2C 0x76 or 0x77).
**Shopping list:** [part 13](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 14. Wiring and small parts
<a id="wiring-and-small-parts"></a>
**Must-haves:** Breadboard, jumper wires, headers, hookup wire, heat-shrink, fuse, TVS diode, resistors, capacitors, perfboard.
**Shopping list:** [part 14](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 

## 15. Case materials and mount
<a id="case-and-mount"></a>
**Must-haves:** PETG, ASA or ABS filament (not PLA); screws, standoffs, heat-set inserts; mount (adhesive pad, dash mat or vent clip).
**Shopping list:** [part 15](./SHOPPING_LIST.md)

| Candidate | Seller / price | Key specs | Must-haves | Pros | Cons / risks | Screenshot | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| _No candidates yet_ | | | | | | | |

**Comparison notes:** 
