# GETTING STARTED — CT-PR Project (Root Level)

This guide walks through every step to spin up a new CT-PR project from scratch: ID allocation, folder creation, PM documents, package registration, GitHub repo, and Gate 0 checklist.

Follow these steps in order. Do not skip steps — each one is a prerequisite for the next.

---

## Step 1 — Allocate the CT-PR number

1. Open the Google Sheets register (URL in `_hub/google script/secrets/sheets-webapp.txt`)
2. Read the **ProductList** tab — find the last CT-PR-XXXX number in use
3. Increment by 1 — that is your project ID
4. Write a new row to the ProductList tab immediately:
   - Column: Project ID, Name, Status = `Draft`, Owner, Date Created
5. **Never invent a number** — always derive from the sheet

---

## Step 2 — Create the local PM folder

```
C:\Users\Bio Medical\Office Management\CT-PR-XXXX_NickName\
├── PM-1_PROJECT-REGISTER.md          ← open first
├── PACKAGES\
├── BACKLOG\
├── SPRINTS\
├── CONFIG\
├── RISK\
├── BLOCKERS\
├── MEETINGS\
├── STAKEHOLDER-UPDATES\
├── TRACEABILITY\
├── DHF\
├── DAR\
│   ├── GENERAL\
│   └── GATE-0\
├── COMMS\
│   ├── SUMMARIES\
│   ├── PATENT\
│   └── PRESENTATIONS\
└── GATES\
    ├── GATE-0\
    ├── GATE-1\
    ├── GATE-2\
    ├── GATE-3\
    ├── GATE-4\
    └── GATE-5\
```

> **Note:** If a GitHub repo is created later (Step 6), move all contents to `officeworks\CT-PR-XXXX-ProjectName\` and push. Never maintain both in parallel.

---

## Step 3 — Open the PM-1 Project Register

Template: `_hub/templates/pm/PM-1_PROJECT-REGISTER_template.md`

Fill in:
- Project ID, name, nickname
- Owner / PM
- Regulation category (RC-LOW / RC-MEDIUM / RC-HIGH)
- Start date
- Leave all gate dates blank — fill as they are reached

Save as: `PM-1_PROJECT-REGISTER_CT-PR-XXXX.md`

---

## Step 4 — Write the Lite Charter (Gate 0 document)

Template: `_hub/templates/pm/PM-11_LITE-CHARTER_template.md`

The Lite Charter answers three questions in plain language:
- **What** is this product?
- **Who** is it for?
- **Why** are we building it?

No technology, no package names, no architecture. Business language only.

Save as: `PM-11_LITE-CHARTER_CT-PR-XXXX.md` → file in `GATES\GATE-0\` at baseline.

---

## Step 5 — Identify packages (draft PM-2)

Template: `_hub/templates/pm/PM-2_PACKAGE-REGISTER_template.md`

At Gate 0 this is a draft — list the disciplines expected to be involved:

| Package type | When required |
|-------------|--------------|
| EPR (firmware) | Any project with embedded firmware |
| HPR (hardware unit) | Any project with a PCB-based hardware assembly |
| PCB (individual PCB) | Each distinct PCB in the hardware assembly |
| SPR (software) | Any desktop / mobile / cloud / embedded application |
| DPR (deployment) | Any SPR that runs on a server or cloud environment |
| MPR (mechanical) | Any project with enclosure, housing, or mechanical assembly |
| TPR (test) | Every project — test protocols and V&V |
| RPR (regulatory) | Any project targeting CDSCO / EU MDR / FDA |
| CPR (clinical) | Any project with clinical claims or usability studies |
| QAR (quality) | Every project — quality requirements and audit |

Assign a lead to each package and a draft ID (confirmed after Gate 0 GO decision).

Save as: `PM-2_PACKAGE-REGISTER_CT-PR-XXXX.md`

---

## Step 6 — Create the GitHub repo (CT-PR level)

Repo naming convention: `CT-PR-XXXX-ProjectNickname` (kebab-case)

```powershell
gh repo create CriterionWorks/CT-PR-XXXX-ProjectNickname `
  --private `
  --description "CT-PR-XXXX: <one-line description>"
```

Then:
1. Clone to `C:\Users\Bio Medical\officeworks\CT-PR-XXXX-ProjectNickname\`
2. Create `main` and `dev` branches — `main` = baselined releases only; all work on `dev`
3. Move PM folder contents into the repo root
4. Initial commit: PM-1 register + Lite Charter + README
5. Push `dev` branch

> If the repo is not created yet, PM docs stay in `Office Management\` until it is.

For branching conventions → `_hub/workflows/github.md`

---

## Step 7 — Allocate and register package IDs

After Gate 0 GO decision — confirm each package and assign real IDs from the sheet.

For each package:
1. Read the relevant tab (EPR, SPR, HPR, PCB, DPR, MPR, TPR, RPR, CPR) — find the last used ID
2. Increment by 1
3. Write the new row to the sheet immediately
4. Update PM-2 Package Register with confirmed IDs

**Never invent a package ID** — always derive from the sheet.

For each package that needs a GitHub repo → `_hub/workflows/github.md` for naming conventions and `GETTING-STARTED-<type>.md` for repo structure.

---

## Step 8 — Open the Risk Management File (PM-6)

Template: `_hub/templates/pm/PM-6_RISK-MANAGEMENT-FILE_template.md`

Open at Gate 0 GO decision. The risk file is **never closed** — it runs the entire lifecycle.

Save as: `RISK\PM-6_RISK-MANAGEMENT-FILE_CT-PR-XXXX.md`

---

## Step 9 — Gate 0 checklist

Complete all items before scheduling the Gate 0 review meeting.

| # | Item | Owner | Done |
|---|------|-------|------|
| 1 | CT-PR number allocated and written to ProductList sheet | PM | [ ] |
| 2 | PM-1 Project Register created | PM | [ ] |
| 3 | PM-11 Lite Charter written — plain language, no tech detail | PM | [ ] |
| 4 | PM-2 Package Register (draft) — disciplines identified, leads assigned | PM | [ ] |
| 5 | PM-6 Risk Management File opened | QM | [ ] |
| 6 | Regulation category confirmed (RC-LOW / RC-MEDIUM / RC-HIGH) | RPR Lead | [ ] |
| 7 | GitHub repo created (or noted as pending) | PM | [ ] |
| 8 | Gate 0 artifacts filed in `GATES\GATE-0\` | PM | [ ] |
| 9 | Gate 0 GO / NO-GO / PIVOT decision recorded in Lite Charter §6 | HOD | [ ] |

---

## Step 10 — After Gate 0 GO: begin Phase 1

Phase 1 documents are opened **after** Gate 0 GO. Not before.

| Document | Template | Notes |
|----------|---------|-------|
| DS-5 URS | `DS-5_URS_template.md` | Start immediately after GO |
| DS-4 DDP | `DS-4_DDP_template.md` | Alongside URS |
| PM-11.1 Full Charter | `PM-11.1_FULL-CHARTER_template.md` | Replaces Lite Charter |
| DS-6 MRS | `MRS_template.md` | After URS Part A, B, D are drafted |
| PM-8 Traceability Matrix | `PM-8_TRACEABILITY-MATRIX_template.md` | Seeded from URS Part E |
| Package req docs (ERS/SRS etc.) | `_hub/templates/dev/` | After MRS allocates requirements |

For the full Phase 1 → Gate 1 procedure → `_hub/workflows/pm.md` §3  
For package-level deliverables per gate → `_hub/workflows/dev-packages.md` §3

---

## Key rules summary

- **ID first, work second** — never create a folder, document, or repo without a registered ID
- **Sheet is source of truth** — always read current IDs from the Google Sheet; never invent them
- **Gate discipline** — Phase 1 docs are not opened until Gate 0 GO; Phase 2 docs are not opened until Gate 1 PASS
- **main = baselined only** — all active work on `dev`; nothing merges to `main` without gate approval
- **DOCUMENTATION_MASTER_STANDARD.md** — all controlled documents follow the naming convention in `officeworks\CT-PR-0174\docs\BUSINESS\COMMON\DOCUMENTATION_MASTER_STANDARD.md`
- **MDL registration** — all controlled documents must be registered in the Master Document List before Active status

---

_Template version: 1.0 | Last updated: 2026-06-01_
