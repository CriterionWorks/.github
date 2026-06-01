# Getting Started — HPR Hardware Package Repository

Welcome to this HPR hardware package repository. Read this file before doing anything else.

---

## What is this repo?

This is a **hardware package repository** under a CT-PR project. It groups one or more PCBs (PCB-CR repos) that belong together as a hardware unit — combined schematic, combined BOM, and hardware-level documents live here.

Individual PCB design files (schematic, layout, Gerbers) live in their own PCB-CR repos, linked here as git submodules.
Firmware lives in the linked EPR repo — not here.
Requirements and V&V specs live in the parent CT-PR PM repository.

---

## Where to start

| I am... | Start here |
|---|---|
| New hardware engineer | Read `BACKLOG.md` — pick up your first task |
| Looking for a specific PCB | Check `pcbs/` — each PCB-CR is a submodule |
| Looking for the full BOM | See `BOM-combined.csv` |
| Looking for the top-level schematic | See `schematic-combined` |
| Looking for requirements | Go to the CT-PR PM repo — find `ERS_HPRXXX.md` |

---

## Repo structure

The following is the minimum expected layout. Items marked **required** must always be present.

```
HPR-XXX/
│
├── GETTING-STARTED-HPR.md          ← you are here (required)
├── BACKLOG.md                       ← task list, always kept up to date (required)
│
├── pcbs/                            ← PCB-CR submodules (required)
│   ├── PCB-CR-XXX/                  ← git submodule → PCB-CR-XXX repo @ pinned tag
│   ├── PCB-CR-YYY/                  ← git submodule → PCB-CR-YYY repo @ pinned tag
│   └── refs.md                      ← lists all linked PCB-CR repos + pinned versions
│
├── schematic-combined/              ← top-level schematic tying all PCBs together (required)
├── BOM-combined.csv                 ← aggregated BOM across all PCB-CR BOMs (required)
│
├── docs/                            ← all HPR-level documents (required)
│   ├── setup.md                     ← assembly, bring-up, test procedure (required)
│   ├── version-baseline.md          ← HPR version + all PCB-CR versions locked (required)
│   └── ...                          ← interface docs, connector maps, power architecture
│
├── misc/                            ← anything that doesn't fit above
└── ...                              ← any other folders the engineer needs
```

---

## PCB submodules

Each PCB-CR repo is linked as a git submodule pinned to a specific tag. After cloning:

```bash
git submodule update --init --recursive
```

Do not update a PCB-CR submodule casually — it means the hardware baseline has changed and the combined BOM and schematic must be reviewed. Always update `docs/version-baseline.md` and `BOM-combined.csv` when any submodule is bumped.

`pcbs/refs.md` always documents which PCB-CR version is currently active and why.

---

## Version Baseline — `docs/version-baseline.md`

Every HPR release must have a baseline entry:

```
## HPR-XXX vX.X — Version Baseline

| Component  | Version | Tag / Commit | Notes                  |
|------------|---------|--------------|------------------------|
| PCB-CR-XXX | vX.X.X  | tag: vX.X.X  | Analog front end       |
| PCB-CR-YYY | vX.X.X  | tag: vX.X.X  | Power supply board     |

Combined BOM version: vX.X
Combined schematic version: vX.X
Approved by: [name]
Date: [date]
```

---

## BACKLOG.md — required

Keep it updated every session. Commit message format:

```
HPR<number>-<item>: short description
Example: HPR001-003: update combined BOM after PCB-CR-002 v1.2 bump
```

---

## Branch rules

- Work on `dev` — never push directly to `main`
- PR from `dev` → `main` when ready for review
- Tag releases from `main` only: `v1.0.0`, `v1.1.0` etc.
- Every release tag must have a corresponding `version-baseline.md` entry

---

## Contact

**Project Lead:** Ratan Srivastava — ratansrivastava.work@gmail.com
**Organisation:** [CriterionWorks](https://github.com/CriterionWorks)

---

_For current task list always refer to `BACKLOG.md`. This file is for first-time orientation only._
