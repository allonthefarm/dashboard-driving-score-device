# Session Notes

Sub-sessions append an entry here before they end. The main session reads this file at the start and folds decisions into README, USAGE, CONTRIBUTING and CHANGELOG. Newest entries at the bottom.

Entry template:

```
## YYYY-MM-DD — Task name
- Done:
- Decisions:
- Docs to update:
- Open questions:
- Next step:
```

## 2026-10-20 — Project kickoff (main session)
- Done: Created README, USAGE, CONTRIBUTING, CHANGELOG, SESSION_NOTES from the planning conversation and the Claude doc "Dashboard Driving Score Device — Spec".
- Decisions: Build myself with AI as tutor; C++ with PlatformIO; private repo; v1 = live screens, driving score, SD logging, guided calibration, level adjust.
- Docs to update: none.
- Open questions: see README "Open Questions & Risks".
- Update 2026-10-20 (later): budget ceiling ~$500; supercap hold-up and DS3231 RTC are in v1; LiFePO4 battery is v2; owner is on Windows 11 with VS Code already set up (used for Python).
- Next step: agree on the first sub-session task (suggested: install VS Code + PlatformIO and run a first program) (owner is on Windows 11).

## 2026-11-14 — Docs review, first batch of fixes (main session)
- Done: Full review of the four docs produced 20 findings. Applied: driver-profile Out of Scope wording, Level adjust "zero here" gesture, stale settings row, missing settings, README/CHANGELOG cleanup, and a standalone estimated-MPG design.
- Decisions: Passenger-mode gesture is to be decided (long presses must not fire on release). Estimated MPG must work without the fill-up tracker and be compared with real tank MPG when fill-ups exist. The Claude spec doc is left as reference only.
- Docs to update: none outstanding from this batch.
- Open questions: review items 7-13 and 14-20 are still to be decided (logging detail, events file, v1 scoring scope, fuel inputs in v1, config file, trip start/end, first-boot flow, supercap, pin budget, heat, phone type, time zone, tools, accuracy check). Speaker idea undecided.
- Next step: owner confirms this batch is correct, then go through the rest of the review list.

## 2026-11-14 — Speaker decision (main session)
- Done: Added speaker hardware to v1 plan, kept the buzzer, deferred voice clips and speaker settings to v1.5 / to be determined.
- Decisions: Speaker (audio amplifier chip plus small speaker) is v1 hardware only; buzzer stays; speaker mounted away from the IMU.
- Docs to update: none.
- Open questions: pin budget now about 29 of roughly 34 (review item 15 still open).
- Next step: continue with the rest of the docs review list (items 7-20).

## 2026-11-14 — Docs review, second batch (main session)
- Done: Applied review items 7-13 and 15-20 (item 14 left open by the owner). Added events.csv, interval-summarized log rows, config.ini plan, first-time setup, time zone and daylight saving, auto trip defaults, v1 scoring scope, fuel inputs logged in v1, shopping list notes, tools list, accuracy-check roadmap step.
- Decisions: Auto trip start and end off by default (stop time default 10 minutes). Android only. Owner already has multimeter, soldering iron, 3D printer, cutters and strippers; needs a breadboard and maybe jumper wires.
- Docs to update: none outstanding.
- Open questions: config file format (INI proposed), supercapacitor hold-up (owner deciding), power-off/on trip continuity, auto trip start rule, pin budget on the chosen board.
- Next step: owner reviews this batch; then go over scoring numbers when ready, or begin the first no-hardware sub-session (PlatformIO setup).

## 2026-11-14 — Backup power decision (main session)
- Done: Replaced the supercapacitor with a v1 LiFePO4 backup battery; moved the Bluetooth switch into v1 hardware; docs updated.
- Decisions: No hardware changes between v1 and v2 (v2 is firmware only). Battery only, no supercapacitor.
- Docs to update: none.
- Open questions: pick a specific LiFePO4-capable charger / load-sharing module and cell (compare two or three); whether to also add the light sensor, barometer, and reserve connections for NFC and OBD so later additions need no rebuild; power-off/on trip continuity.
- Next step: owner answers the "reserve hardware for later features" question.

## 2026-11-14 — Hardware scope for v1 (main session)
- Done: Barometer and light sensor added to v1 hardware; Bluetooth switch moved back to v2; NFC and OBD not planned for in v1 hardware; docs updated.
- Decisions: Battery, speaker, barometer and light sensor are base features and belong in v1 hardware. Bluetooth, NFC and OBD are nice-to-have extras that may need small hardware additions later.
- Docs to update: none.
- Open questions: specific battery charger module and cell; auto trip start rule and power-off/on trip continuity; config file format confirmation; pin check on the chosen board.
- Next step: owner chooses what to tackle next (first no-hardware sub-session, scoring numbers, or battery module research).
