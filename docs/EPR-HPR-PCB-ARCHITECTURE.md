# EPR / HPR / PCB-CR — Hardware Package Architecture

**Document type:** Reference  
**Audience:** Engineers, PM, QA — anyone joining or reviewing a hardware-based project  
**Last updated:** 2026-06-01

---

## The Core Idea

A hardware-based product at Criterion is split into three distinct package levels. Each level has its own GitHub repo, its own version history, its own baseline document, and its own owner.

```
CT-PR-XXXX  (Project)
│
├── EPR-XXX   Firmware Package          ← runs ON the hardware
│     └── hardware/ (submodule)
│           └── HPR-XXX               ← hardware unit it targets
│                 └── pcbs/ (submodules)
│                       ├── PCB-CR-XXX  ← individual PCB #1
│                       └── PCB-CR-YYY  ← individual PCB #2
│
├── SPR-XXX   Software Package         ← cloud / desktop / mobile app
├── MPR-XXX   Mechanical Package       ← enclosure, housing
├── TPR-XXX   Test Package             ← V&V protocols
└── RPR-XXX   Regulatory Package       ← CDSCO / EU MDR
```

The three hardware levels are:

| Level | Code | What it contains | Who owns it |
|-------|------|-----------------|-------------|
| Individual PCB | PCB-CR-XXX | One PCB — schematic, layout, Gerbers, BOM | Hardware engineer |
| Hardware unit | HPR-XXX | Group of PCBs combined — combined BOM, combined schematic | Hardware lead |
| Firmware | EPR-XXX | Firmware that runs on the HPR hardware unit | Firmware engineer |

---

## Level 1 — PCB-CR (Individual PCB)

**What it is:** The smallest independent hardware unit. One PCB-CR repo = one PCB board.

**Why separate?** A single product can have multiple PCBs (e.g. a main board, a sensor board, a power board). Each one changes independently. Separating them means:
- You can revision PCB-CR-002 (power board) to v1.2 without touching PCB-CR-001 (main board)
- You can reuse PCB-CR-002 in a completely different project
- Gerber history is clean and traceable per board

**What lives here:**

```
PCB-CR-XXX/
├── schematic/          ← source schematic (KiCad / Altium / etc.)
├── layout/             ← PCB layout source
├── Gerbers/            ← fabrication output — committed at every release
├── BOM.csv             ← bill of materials for THIS board only
└── docs/
    ├── assembly.md                ← how to assemble and test
    └── fabrication-baseline.md   ← EDA tool version, fab specs, fab house, date sent
```

**Key rule:** PCB-CR stands alone. It has no dependency on firmware. It can be cloned, reviewed, and sent to a fab house without any other repo.

**Versioning:**
```
v1.0.0  — first fabricated version
v1.1.0  — minor change (component swap, silkscreen fix)
v2.0.0  — major schematic or layout change
```
Gerbers must be regenerated and committed before any release tag. Never delete a release tag — it is a permanent fabrication record.

---

## Level 2 — HPR (Hardware Package / Hardware Unit)

**What it is:** A logical grouping of one or more PCBs that together form a hardware unit. The HPR repo does not contain individual PCB design files — it aggregates them.

**Why a separate level?** When a product has multiple PCBs:
- Someone needs to own the combined BOM (parts across all boards)
- Someone needs to own the top-level schematic (how boards connect to each other)
- The firmware engineer needs a single stable reference point for the hardware — not five separate PCB-CR repos

The HPR is that reference point.

**What lives here:**

```
HPR-XXX/
├── pcbs/
│   ├── PCB-CR-XXX/         ← git submodule → PCB-CR-XXX repo @ pinned tag
│   ├── PCB-CR-YYY/         ← git submodule → PCB-CR-YYY repo @ pinned tag
│   └── refs.md             ← which PCB-CR version is pinned and why
├── schematic-combined/     ← top-level schematic: shows inter-board connections
├── BOM-combined.csv        ← aggregated BOM: all components across all PCBs
└── docs/
    ├── setup.md                 ← assembly procedure, board bring-up, test points
    └── version-baseline.md      ← HPR version + each PCB-CR version locked
```

**How PCB-CR repos are linked (git submodules):**

The HPR repo does not copy PCB files — it pins to a specific tagged release of each PCB-CR repo using git submodules.

```
# After cloning an HPR repo, pull all submodules:
git submodule update --init --recursive
```

When HPR-001 is at v2.0, it might pin:
- PCB-CR-001 @ v1.3 (main board)
- PCB-CR-002 @ v1.2 (power board)

If a PCB-CR is updated (e.g. PCB-CR-002 moves to v1.3), the HPR engineer:
1. Updates the submodule pin in HPR
2. Regenerates BOM-combined.csv
3. Reviews combined schematic for any interface changes
4. Updates docs/version-baseline.md
5. Tags a new HPR release

This means the combined BOM and schematic always match the pinned PCB-CR versions exactly — no drift.

**Version Baseline — `docs/version-baseline.md`:**

```
## HPR-XXX v2.0 — Version Baseline

| Component  | Version | Tag / Commit | Notes              |
|------------|---------|--------------|--------------------|
| PCB-CR-XXX | v1.3.0  | tag: v1.3.0  | Main board         |
| PCB-CR-YYY | v1.2.0  | tag: v1.2.0  | Power supply board |

Combined BOM version: v2.0
Combined schematic version: v2.0
Approved by:
Date:
```

---

## Level 3 — EPR (Firmware Package)

**What it is:** All firmware source code that runs on the HPR hardware unit. The EPR repo references its target hardware (HPR) as a git submodule.

**Why reference HPR as a submodule?** Firmware must always target a specific, known hardware version. Without this link:
- A firmware update might be tested against HPR v1.0 but deployed on HPR v2.0
- The firmware engineer has no single place to see which PCBs are under the hardware they are writing code for

By linking HPR as a submodule, the EPR always knows exactly which hardware version it is targeting.

**What lives here:**

```
EPR-XXX/
├── firmware/
│   ├── <board-name>/       ← one folder per MCU or board
│   │   ├── src/
│   │   ├── include/
│   │   └── docs/           ← board-specific flash procedure, notes
│   └── shared/             ← common drivers / libraries shared across boards
├── hardware/
│   ├── refs.md             ← which HPR version is targeted and why
│   └── HPR-XXX/            ← git submodule → HPR repo @ pinned tag
└── docs/
    ├── setup.md             ← how to build and flash (required before first commit)
    └── config-baseline.md   ← firmware + HPR + PCB versions locked together
```

**Configuration Baseline — `docs/config-baseline.md`:**

This is the EPR-level baseline document. It records the exact combination of firmware version, HPR version, and (via HPR) each individual PCB version that was tested and approved together.

```
## EPR-XXX v1.0 — Configuration Baseline

| Component  | Version | Tag / Commit | Notes              |
|------------|---------|--------------|--------------------|
| Firmware   | v1.0.0  | tag: v1.0.0  |                    |
| HPR-XXX    | v2.0.0  | tag: v2.0.0  |                    |
| PCB-CR-XXX | v1.3.0  | tag: v1.3.0  | via HPR submodule  |
| PCB-CR-YYY | v1.2.0  | tag: v1.2.0  | via HPR submodule  |

Tested on: [date]
Approved by: [name]
```

This document is what a QA engineer, a regulatory reviewer, or a future firmware engineer needs to reproduce the exact tested configuration.

---

## How the Versions Flow Together

```
PCB-CR-001 v1.3  ──┐
PCB-CR-002 v1.2  ──┤── HPR-001 v2.0  ──── EPR-001 v1.0
                    │
                  HPR version-baseline.md   EPR config-baseline.md
                  records PCB versions       records HPR + PCB versions
```

**Upgrade scenario — one PCB changes:**

1. Hardware engineer fixes a bug in PCB-CR-002 → tags v1.3
2. HPR lead bumps the PCB-CR-002 submodule to v1.3, updates BOM-combined, version-baseline → tags HPR v2.1
3. Firmware engineer bumps the HPR submodule to v2.1, re-tests firmware → tags EPR v1.1 with updated config-baseline

Each level knows exactly what changed and when. No version guesswork.

---

## Ownership Boundaries

| Item | Lives in | Never in |
|------|---------|---------|
| PCB schematic source | PCB-CR repo | HPR or EPR repos |
| Individual PCB BOM | PCB-CR repo | HPR or EPR repos |
| Individual PCB Gerbers | PCB-CR repo | HPR or EPR repos |
| Combined (top-level) schematic | HPR repo | PCB-CR or EPR repos |
| Combined BOM | HPR repo | PCB-CR or EPR repos |
| Assembly / bring-up procedure | HPR repo | EPR repo |
| Firmware source code | EPR repo | HPR or PCB-CR repos |
| Toolchain / build instructions | EPR repo | HPR or PCB-CR repos |
| ERS (firmware requirements) | CT-PR PM repo | Code repos |
| PCB-CR version pinning | HPR `pcbs/refs.md` | Anywhere else |
| HPR version pinning | EPR `hardware/refs.md` | Anywhere else |

---

## GitHub Repository Naming

| Package | Naming convention | Example |
|---------|------------------|---------|
| PCB design | `PCB-CR-NNN-ShortName` | `PCB-CR-001-MainBoard` |
| Hardware unit | `HPR-NNN-ShortName` | `HPR-001-PatientMonitorHW` |
| Firmware | `EPR-NNN-ShortName` | `EPR-056-BodyMovementDetection` |

All repos are private under the **CriterionWorks** GitHub organisation.

---

## Registry (Google Sheet)

Each package is registered in its own tab of the Product Development Register before the repo is created.

| Tab | Registers | Key columns |
|-----|---------|------------|
| PCB | PCB-CR-NNN packages | Package ID, Repo Link, EDA Tool, Parent Project Code |
| HPR | HPR-NNN packages | Package ID, Repo Link, Referenced PCB-CR IDs, Parent Project Code |
| EPR | EPR-NNN packages | Package ID, Repo Link, Referenced HPR ID, Parent Project Code |

The HPR row must list all PCB-CR IDs it groups. The EPR row must reference the HPR ID it targets. This gives a full traceability chain in the sheet: EPR → HPR → PCB-CR(s).

---

## Quick Reference

| Question | Where to look |
|----------|-------------|
| Which PCBs are in this hardware unit? | HPR repo → `pcbs/refs.md` or `docs/version-baseline.md` |
| What version of the power board is in production? | EPR repo → `docs/config-baseline.md` |
| Where is the Gerber for PCB-CR-002 v1.2? | PCB-CR-002 repo → `Gerbers/`, tag `v1.2.0` |
| What is the combined BOM for HPR-001 v2.0? | HPR-001 repo → `BOM-combined.csv`, tag `v2.0.0` |
| What hardware did EPR-056 v1.0 ship with? | EPR-056 repo → `docs/config-baseline.md` |
| Where are the firmware requirements? | CT-PR PM repo → `PACKAGES/EPR-XXX/ERS_EPRXXX.md` |

---

_Reference: `_hub/templates/GETTING-STARTED-EPR.md`, `GETTING-STARTED-HPR.md`, `GETTING-STARTED-PCB.md`_  
_Workflow: `_hub/workflows/dev-packages.md` §2_
