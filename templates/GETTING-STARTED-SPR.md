# Getting Started — SPR Software Package Repository

Welcome to this SPR software package repository. Read this file before doing anything else.

---

## What is this repo?

This is a **software package repository** under a CT-PR project. It contains all source code, tests, configuration, and the package backlog.

Requirements, architecture, and V&V test specs for this package live in the parent CT-PR PM repository.

---

## Where to start

| I am... | Start here |
|---|---|
| New developer on this package | Read `BACKLOG.md` — pick up your first task |
| Looking for requirements | Go to the CT-PR PM repo — find `SRS_SPRXXX.md` |
| Looking for architecture | Go to the CT-PR PM repo — find `Architecture_SPRXXX.md` |
| Running tests | See inside the package folder or `docs/setup.md` |

---

## Repo structure

The following structure is the minimum expected layout. Developers are free to organise additional files as needed, but the items marked **required** must always be present.

```
SPR-XXX/
│
├── GETTING-STARTED-SPR.md      ← you are here (required)
├── BACKLOG.md                   ← task list, always kept up to date (required)
│
├── spr<number>/                 ← main source code folder, named in lowercase
│   │                               e.g. spr072/, spr115/
│   └── ...                      ← all source files live here
│
├── docs/                        ← all project documents (required)
│   ├── setup.md                 ← how to install and run (required)
│   └── ...                      ← SRS, architecture, test logs, any other docs
│
├── misc/                        ← anything that doesn't fit above
│   └── ...                      ← references, datasheets, scratch notes, exports
│
└── ...                          ← any other folders the developer needs
                                    e.g. tests/, models/, config/, scripts/
```

### What is required vs flexible

| Item | Required | Notes |
|---|---|---|
| `GETTING-STARTED-SPR.md` | Yes | This file — do not delete |
| `BACKLOG.md` | Yes | Keep updated every session |
| `spr<number>/` source folder | Yes | Lowercase, named after the package ID |
| `docs/setup.md` | Yes | Must be complete enough for someone to clone and run |
| `docs/` for all documents | Yes | SRS, architecture, logs, specs all go here |
| `misc/` | No | Handy for loose files — use it freely |
| Everything else | Flexible | Structure as suits the project |

---

## BACKLOG.md — the one non-negotiable

`BACKLOG.md` is the developer's source of truth for what to work on. It must:

- Exist from day one
- Be updated every session before logging off
- Reference the same item IDs used in commit messages

Commit message format: `SPR<number>-<item>: short description`
Example: `SPR072-003: add QRS peak detection`

---

## Branch rules

- Work on `dev` branch — never push directly to `main`
- Raise a pull request from `dev` → `main` when ready for review
- `main` is the baselined, reviewed branch — only clean, working code lands here

---

## Contact

**Project Lead:** Ratan Srivastava — ratansrivastava.work@gmail.com
**Organisation:** [CriterionWorks](https://github.com/CriterionWorks)

---

_For current task list always refer to `BACKLOG.md`. This file is for first-time orientation only._
