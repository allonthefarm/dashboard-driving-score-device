# Contributing to Dashboard Driving Score Device

This is a solo, private learning project for now. It is not open to outside contributions. This file records how the owner and Claude sessions work together, so any session (or person) picking up the project cold knows the rules.

## How Work Gets Done

### Main session and sub-sessions
- A **main session** holds the plan and decisions. It keeps the four docs (README, USAGE, CONTRIBUTING, CHANGELOG) up to date.
- **Sub-sessions** each take one small, focused task (for example "set up PlatformIO and blink an LED" or "write the calibration math in Python").
- Sessions cannot be relied on to message each other live. The repo is the shared memory.

### Session notes (how sub-sessions report back)
Before a sub-session ends, it appends an entry to [SESSION_NOTES.md](./SESSION_NOTES.md) with:
- Date and task name
- What was done (files created or changed)
- Decisions made, and anything that should update README, USAGE or CHANGELOG
- Open questions or problems
- What the next step is

The main session reads SESSION_NOTES.md at the start and folds decisions into the four docs.

### Teaching style
The owner is learning. Sessions should explain what each piece of code does and why, assume no C++ background, make small steps the owner can run and see, and avoid dumping large amounts of unexplained code.

## Reporting Bugs
Solo project. Bugs and ideas go in SESSION_NOTES.md or as GitHub Issues in the private repo.

## Suggesting Features
Add them to README's Roadmap or Open Questions & Risks, with a line in CHANGELOG once decided.

## Pull Requests
Not applicable while the project is solo. Work happens on `main` with small commits. If branches are used later, name them by task, for example `calibration-math`.

## Development Setup
```bash
# TBD — written during the first sub-session:
# install VS Code, the PlatformIO extension, Git, and Python 3
```

## Style Guide / Conventions
- Firmware in C++ (Arduino framework, PlatformIO). Logic that does not touch hardware goes in `firmware/lib/core/` so it can be tested on a computer.
- Keep hardware-specific code in `firmware/src/`.
- Scoring weights and thresholds are never hardcoded; they come from config.
- Settings are stored in two groups from the start: car-wide and per-driver (slots for drivers 1-8). Trip records always carry a driver id.
- Helper scripts in Python 3.
- Short, plain comments that explain why.
- **Privacy:** never commit real drive logs that contain GPS positions. Keep real recordings in `data/private/` (ignored by Git). Only fake or scrubbed drives go in `data/samples/`.

## Commit Message Guidelines
Free-form, short, in the imperative ("Add calibration math"). One logical change per commit.

## Review Process
Owner reviews. For learning, the owner should be able to explain any code before it is committed.

## Questions?
Ask in the main session.
