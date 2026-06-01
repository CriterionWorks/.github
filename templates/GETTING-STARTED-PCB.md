# Getting Started — PCB-CR Hardware Design Repository

Welcome to this PCB-CR repository. Read this file before doing anything else.

---

## What is this repo?

This is an **individual PCB design repository** under a CT-PR project. It contains everything needed to fabricate and assemble one PCB — schematic, layout, BOM, Gerbers, and PCB-level documents.

This repo is referenced as a submodule by its parent HPR repo. It is designed to stand alone — it has no dependency on firmware or other PCBs.

---

## Where to start

| I am... | Start here |
|---|---|
| New hardware engineer | Read `BACKLOG.md` — pick up your first task |
| Placing a fab order | See `Gerbers/` and `BOM.csv` |
| Looking for the schematic | See `schematic/` |
| Looking for assembly instructions | See `docs/assembly.md` |
| Looking for requirements | Go to the CT-PR PM repo or parent HPR repo |

---

## Repo structure

The following is the minimum expected layout. Items marked **required** must always be present.

```
PCB-CR-XXX/
│
├── GETTING-STARTED-PCB.md          ← you are here (required)
├── BACKLOG.md                       ← task list, always kept up to date (required)
│
├── schematic/                       ← schematic source files (required)
│   └── *.kicad_sch / *.SchDoc / ...
│
├── layout/                          ← PCB layout source files (required)
│   └── *.kicad_pcb / *.PcbDoc / ...
│
├── Gerbers/                         ← fabrication output files (required for release)
│   └── ...
│
├── BOM.csv                          ← bill of materials for this PCB (required)
│
├── docs/                            ← all PCB-level documents (required)
│   ├── assembly.md                  ← assembly and bring-up instructions (required)
│   └── ...                          ← ECO log, test points, errata, inspection checklist
│
├── misc/                            ← anything that doesn't fit above
└── ...                              ← any other folders the engineer needs
                                        e.g. datasheets/, exports/, renders/
```

---

## Versioning and releases

Each fabrication-ready version must be tagged on `main`:

```
v1.0.0  — first fabricated version
v1.1.0  — minor change (component swap, silkscreen fix)
v2.0.0  — major layout or schematic change
```

Gerbers must be regenerated and committed before tagging a release. The parent HPR repo pins this repo at a specific tag — so a clean, tagged release here is what HPR pulls in.

---

## BACKLOG.md — required

Keep it updated every session. Commit message format:

```
PCB-CR-<number>-<item>: short description
Example: PCB-CR-001-004: fix R12 footprint to 0402
```

---

## Branch rules

- Work on `dev` — never push directly to `main`
- PR from `dev` → `main` when ready for review
- Tag releases from `main` only after Gerbers are regenerated and reviewed
- Never delete a release tag — it is a fabrication record

---

## Contact

**Project Lead:** Ratan Srivastava — ratansrivastava.work@gmail.com
**Organisation:** [CriterionWorks](https://github.com/CriterionWorks)

---

_For current task list always refer to `BACKLOG.md`. This file is for first-time orientation only._
