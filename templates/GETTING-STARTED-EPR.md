# Getting Started — EPR Firmware Package Repository

Welcome to this EPR firmware package repository. Read this file before doing anything else.

---

## What is this repo?

This is a **firmware package repository** under a CT-PR project. It contains all firmware source code and references the hardware package (HPR) it runs on.

Hardware design (schematics, PCBs, BOMs) lives in the linked HPR repo — not here.
Requirements, architecture, and V&V specs live in the parent CT-PR PM repository.

---

## Where to start

| I am... | Start here |
|---|---|
| New firmware developer | Read `BACKLOG.md` — pick up your first task |
| Looking for hardware details | Check `hardware/refs.md` — find the linked HPR repo |
| Looking for requirements | Go to the CT-PR PM repo — find `ERS_EPRXXX.md` |
| Looking for which hardware version to target | Check `docs/config-baseline.md` |

---

## Repo structure

The following is the minimum expected layout. Developers are free to organise additional files as needed, but items marked **required** must always be present.

```
EPR-XXX/
│
├── GETTING-STARTED-EPR.md          ← you are here (required)
├── BACKLOG.md                       ← task list, always kept up to date (required)
│
├── firmware/                        ← all firmware source (required)
│   ├── <board-name>/                ← one folder per MCU / board
│   │   ├── src/
│   │   ├── include/
│   │   └── docs/                    ← board-specific notes, flash procedure
│   └── shared/                      ← common drivers / libraries used across boards
│
├── hardware/                        ← hardware references only — no design files here (required)
│   ├── refs.md                      ← links to HPR repo + pinned version (required)
│   └── <HPR-XXX>/                   ← git submodule pointing to linked HPR repo
│
├── docs/                            ← all EPR-level documents (required)
│   ├── config-baseline.md           ← firmware + HPR + PCB version baseline (required)
│   ├── setup.md                     ← how to build and flash (required)
│   └── ...                          ← ERS, architecture, test logs, traceability
│
├── misc/                            ← anything that doesn't fit above
└── ...                              ← any other folders the developer needs
```

---

## Hardware submodule

The HPR repo is linked as a git submodule. After cloning, pull it with:

```bash
git submodule update --init --recursive
```

The submodule is pinned to a specific HPR tag. Do not update it casually — updating means the firmware is now targeting a different hardware version and must be re-tested. Always update `docs/config-baseline.md` when the submodule is bumped.

`hardware/refs.md` documents which HPR version is currently active and why.

---

## Configuration Baseline — `docs/config-baseline.md`

Every EPR release must have a baseline entry documenting the exact combination of firmware and hardware that was tested and approved:

```
## EPR-XXX vX.X — Configuration Baseline

| Component  | Version | Tag / Commit | Notes              |
|------------|---------|--------------|--------------------|
| Firmware   | vX.X.X  | tag: vX.X.X  |                    |
| HPR-XXX    | vX.X.X  | tag: vX.X.X  |                    |
| PCB-CR-XXX | vX.X.X  | tag: vX.X.X  | via HPR submodule  |

Tested on: [date]
Approved by: [name]
```

---

## Toolchain

No specific toolchain is mandated — use what suits the hardware.
Recommended: **VS Code + PlatformIO** or **STM32CubeIDE** for STM32-based targets.
Document your chosen toolchain and exact build steps in `docs/setup.md`.

---

## BACKLOG.md — required

Keep it updated every session. Commit message format:

```
EPR<number>-<item>: short description
Example: EPR001-005: add UART receive interrupt handler
```

---

## Branch rules

- Work on `dev` — never push directly to `main`
- PR from `dev` → `main` when ready for review
- Tag releases from `main` only: `v1.0.0`, `v1.1.0` etc.
- Every release tag must have a corresponding `config-baseline.md` entry

---

## Contact

**Project Lead:** Ratan Srivastava — ratansrivastava.work@gmail.com
**Organisation:** [CriterionWorks](https://github.com/CriterionWorks)

---

_For current task list always refer to `BACKLOG.md`. This file is for first-time orientation only._
