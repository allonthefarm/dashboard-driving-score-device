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
