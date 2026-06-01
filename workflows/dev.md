# WORKFLOW -- Development

Read `workflows/core.md` first. This file adds rules specific to development sessions.

For discipline-specific package documents (EPR/ERS, SPR/SRS, MPR/DRS, TPR/TRS, RPR/RRS, CPR/CRS), applicable standards, and gate-by-gate deliverables per package type -> `workflows/dev-packages.md`.

## 0. Package ID identification — mandatory before any new package

Before creating or referencing any package (EPR, HPR, PCB, SPR, DPR, MPR, TPR, RPR, CPR), always look up the ID via the Google Sheets API:

- **Sheet:** https://docs.google.com/spreadsheets/d/15UJZ6a_3B43mXX6QYeTVrl3R70X_iulqn4aw2VPtWlE/edit
- **API:** `_hub/google script/secrets/sheets-webapp.txt`
- Read the relevant tab (EPR, SPR, MPR etc.), find the last used ID, increment by 1
- Never invent a package number — always derive it from the sheet
- Write the new row back to the sheet via the API before creating the repo

---

## 1. Package documents -- when to open and use them

Every development package (EPR, HPR, PCB, SPR, DPR, MPR, TPR, RPR, CPR) has a required primary document. This is not optional paperwork -- it is the design input that justifies what you build and what you test. Templates are in `_hub/templates/dev/`.

### Trigger points

| Trigger | What to do |
|---------|-----------|
| New package created (added to PM-2) | Copy the relevant template to `Office Management\CT-PR-XXX\PACKAGES\<code>\<DocID>_<code>.md`. Fill frontmatter. Leave body as draft. |
| Backlog item moves to `[~]` (in development) | The corresponding package document section must be drafted before writing code. E.g. starting EPR-12 (ADC driver) -> ERS §4 (Electrical Requirements) and §5 (Interface Requirements) must exist first. |
| Gate 1 approaching | ERS / SRS / DRS / TRS / RRS / CRS must each be at v0.1 minimum -- requirements seeded from MRS, applicable standards listed, traceability table started. |
| Gate 2 approaching | Architecture and design sections of each package document must be complete. TPR test protocols (DS-12) drafted. |
| Gate 3 approaching | All verification/validation results filled into TPR. Traceability matrix fully linked. |

### Which template to open

| Your role / task | Template |
|-----------------|---------|
| Firmware for a hardware unit | `EPR_template.md` → produces ERS |
| Hardware unit grouping multiple PCBs | `GETTING-STARTED-HPR.md` → HPR repo structure |
| Individual PCB design | `GETTING-STARTED-PCB.md` → PCB repo structure |
| Mobile app, desktop app, embedded SW, cloud service | `SPR_template.md` → produces SRS |
| Deploying a software package to a server | `GETTING-STARTED-DPR.md` → DPR repo structure |
| Enclosure, mechanical assembly, housing | `MPR_template.md` → produces DRS |
| Writing test protocols, running V&V | `TPR_template.md` → produces TRS |
| Regulatory pathway, standards compliance | `RPR_template.md` → produces RRS |
| Clinical claims, usability studies, biocompatibility | `CPR_template.md` → produces CRS |

### Rule

No backlog item moves to `[~]` on a new package without a draft of the relevant document section existing. The document does not need to be complete -- but the requirements that the backlog item implements must be written down before the implementation starts.

---

## 2. Epic / Backlog -> Codebase mapping

Every project repo must contain a `docs/backlog-map.md` file that maps each epic and backlog item to the code it touches.

### File: `docs/backlog-map.md`

Structure:

```
# Backlog -> Codebase Map

## <EPIC-ID>: <Epic title>

### <ITEM-ID>: <Backlog item title>
- Status: [ ] / [~] / [V] / [x]
- Files / modules:
  - `src/module/file.c` -- reason this file is affected
  - `include/header.h`
- Entry points: `function_name()` in `file.c:42`
- Notes: any non-obvious scope decisions
```

Rules:
- Create `docs/backlog-map.md` when the first backlog item goes into development (`[~]`).
- Update the map every session -- before logging the session, not after.
- When a backlog item closes (`[x]`), mark it closed in the map too (don't delete it -- history matters).
- If a file is touched that isn't listed in any backlog item, that's a scope creep signal -- flag it.

### Commit message format

Every commit must reference the backlog item it belongs to:

```
<ITEM-ID>: <short imperative description>

e.g.  EPR-12: add ADC driver init sequence
      CT-PR-042-SRS-3: implement alarm threshold validation
```

- Use the most specific ID available (backlog item > epic > project).
- If a commit spans multiple items, list them: `EPR-12, EPR-13: refactor shared timer logic`.

### Status.md backlog entries

Each backlog item in `status.md` must include a `Files:` line once it moves to `[~]`:

```
[~] EPR-12 -- Add ADC driver
    Files: src/drivers/adc.c, include/adc.h
```

### Code comments (entry points only)

Tag the primary entry point of a backlog item with a one-line comment:

```c
/* EPR-12: ADC driver init */
void adc_init(void) {
```

- Only tag the top-level entry point -- not every line touched.
- Remove the tag if the item is superseded or the function is renamed.

---

## Testing stages -- overview

There are six distinct stages. Board Bringup and Unit Test are prerequisites handled by the developer before any formal testing begins. The test team takes over from Integration Test onwards.

```
DEVELOPER
──────────────────────────────────────────────────────────────────
Board Bringup → Unit Test → HIT
      ↓               ↓          ↓
  hardware         logic ok   runs on hw
  streams ok       on PC      module scope

TEST TEAM
──────────────────────────────────────────────────────────────────
Integration Test → System Test → Verification → Validation
        ↓                ↓              ↓               ↓
  modules work       full flow      meets SRS      solves real
  together           end-to-end     on paper       problem in field
```

| Stage | Who | Where | Produces |
|-------|-----|-------|---------|
| **Board Bringup** | Developer | Real target hardware | Bringup doc + session log entry |
| **Unit Test** | Developer | PC, mocked dependencies | Session log entry |
| **HIT** | Developer | Real target hardware, module scope | Session log entry |
| **Integration Test** | Test team | Lab, multiple modules together | Test log |
| **System Test** | Test team | Lab, full system end-to-end | Test log |
| **Verification** | Test team / QA | Controlled lab, traceable, against SRS | Formal VER record (DHF) |
| **Validation** | Test team / QA + user | Field, complete system, real use case | Formal VAL record (DHF) |

**Key rule:** Board Bringup, Unit Test, and HIT are part of development -- the developer runs them, no formal record required beyond the session log. Integration Test onwards is the test team's responsibility and produces formal records.

---

## 3. Unit testing

**Who:** Developer. **When:** During coding, on PC. **No hardware required.**

Unit tests verify the logic of a single module in isolation using mocked dependencies. They are part of development -- not a formal test stage.

### What to test

Test functions that contain branching logic (`if`, `switch`, loops) or implement a safety-critical path. Skip simple getters/setters and vendor HAL/framework boilerplate.

Minimum bar is set by SW Safety Class (defined in SRS before coding starts):

| Class | Requirement |
|-------|------------|
| A | No unit tests mandated |
| B | All branching logic tested; 80-85% branch coverage |
| C | All units tested; 100% branch coverage; code review mandatory |

### Tool stack

| Stack | Python (SPR) | Firmware C (EPR) |
|-------|-------------|-----------------|
| Framework | pytest | Ceedling (Unity + CMock) |
| Mocking | unittest.mock | CMock -- auto-generates mocks from `.h` headers |
| Coverage | pytest-cov | gcov + lcov |
| Runs on | PC | PC (host build, not target MCU) |

### When

- Developer writes and runs unit tests as each backlog item is coded -- not at the end.
- Unit tests must pass before HIT is run.
- Results logged in the session file.

Full detail, patterns, and examples -> `_knowledge/general-dev/unit-testing.md`

---

## 4. Board Bringup

**Who:** Developer. **When:** Once, before any HIT on that hardware. **Real hardware required.**

Board Bringup is a one-time commissioning activity performed when new hardware is introduced into the project. The goal is to confirm the hardware is detectable by the OS, drivers are installed and working, and a basic hello-world test streams live data. It is a prerequisite for HIT — you cannot run HIT on hardware that has not been brought up.

### What bringup covers

- OS-level hardware detection (e.g. `lsusb`, device nodes)
- Driver and SDK installation (including any source builds, patches, or udev rules)
- Running a minimal standalone script that confirms the hardware is streaming (e.g. `tools/hello_camera.py`)
- Documenting the confirmed working configuration (OS, driver version, flags, venv, workarounds)

### What bringup is not

- Bringup is **not** HIT — no software module from the backlog is under test
- Bringup is **not** formal V&V — no DHF record, no SRS requirement being verified
- Bringup does **not** move any backlog item status

### Output

A `docs/board-bringup-<hardware>.md` file in the repo. Minimum contents:

1. Hardware and OS environment (exact versions)
2. Known constraints and gotchas
3. Step-by-step installation — exact commands, including any patches or workarounds
4. Verification command and expected output
5. Common errors and fixes

### Rule

No HIT may be run on a hardware component until its bringup document exists and the hello-world test has been confirmed working.

### Stage flow

```
Board Bringup (one-time per hardware) → Unit Test → HIT → [V] → Formal V&V
```

---

## 5. Hardware Integration Test (HIT)

**Who:** Developer. **When:** After unit tests pass, still in `[~]`. **Real hardware required.**

HIT confirms the module runs correctly on the actual target hardware. It catches driver compatibility, timing, and real hardware behaviour that unit tests on PC cannot catch.

### Rule

No backlog item moves from `[~]` to `[V]` without:
1. Unit tests passing on PC.
2. HIT passing on target hardware (as applicable to the item — e.g. Pi4-2 + L515 + arm for SPR-113).

### What HIT is not

- HIT is **not** board bringup -- bringup must already be complete before HIT runs.
- HIT is **not** formal V&V -- no signed test record, no DHF entry.
- HIT is **not** an integration test -- it tests only the module just implemented.
- HIT is **not** a system test -- no end-to-end flow.
- HIT logs go in the session file only.

### Stage flow (mandatory)

```
[ ] Pending -> [~] In Development [unit tests -> HIT] -> [V] V&V Backlog -> [x] Closed
```

---

## 6. Integration Test, System Test, Verification, Validation

**Who:** Test team (not the developer). **When:** After all backlog items reach `[V]`.

These stages are the test team's responsibility. Criteria for all four must be defined in the SRS **before** the first backlog item moves to `[~]`.

| Stage | Scope | Environment | Formal record |
|-------|-------|-------------|--------------|
| **Integration Test** | Multiple modules working together | Lab | Test log |
| **System Test** | Full system, end-to-end flow | Lab | Test log |
| **Verification** | Does the system meet SRS requirements? | Controlled lab, against spec | VER record (DHF) |
| **Validation** | Does the system solve the real user problem? | Field, complete system, real conditions | VAL record (DHF) |

### Where criteria and records live

| What | Where | Owner |
|------|-------|-------|
| Acceptance criteria per requirement (what must be true to pass) | `SRS_<ID>.md` / `ERS_<ID>.md` — one row per requirement | Dev lead |
| Unit test results + HIT logs (informal, developer-run) | Session log + `docs/` inside EPR / SPR repo | Developer |
| Software release record + baseline | `docs/software-baseline.md` inside SPR repo | Dev lead |
| Config baseline (firmware + HPR + PCB versions) | `docs/config-baseline.md` inside EPR repo | Firmware lead |
| **Formal test protocols (DS-12)** | **TPR repo → `GATES/GATE-3/DS-12_TEST-PROTOCOLS/`** | **Test lead** |
| **Integration + system test records** | **TPR repo** | **Test team** |
| **Verification records (VER → DHF)** | **TPR repo** | **QA / test team** |
| **Validation records (VAL → DHF)** | **TPR repo** | **QA / test team** |
| V&V Backlog tracking | `_hub/projects/<project>/status.md` — one row per `[V]` item | PM |

**Rule:** SRS / ERS define *what* must be verified (acceptance criteria). TPR owns *how* it is tested (protocols) and *whether it passed* (records). Nothing formal flows back into the development repo — traceability is maintained by TPR referencing SRS/ERS requirement IDs.

---

## 7. API documentation -- mandatory for every API project

Every project that exposes HTTP endpoints must have a `docs/api.md` file created **before the first endpoint is coded**. It is the design input for the API -- not a post-facto description.

### Minimum contents of api.md

1. **Base URL** -- host, port, any path prefix
2. **Authentication** -- none, API key, JWT, etc.
3. **Endpoints table** -- method, path, description, one line each
4. **Request / response schema** -- for each endpoint: required fields, types, defaults
5. **Error responses** -- status codes and what triggers them
6. **Changelog** -- version + date + what changed

### Rule

No endpoint moves to `[~]` (in development) without its entry existing in `docs/api.md`. The schema does not need to be final -- but the method, path, and purpose must be written before the code is written.

### Backlog item

- [ ] Retrofit `docs/api.md` for gemma-deployment (Axon) -- all current endpoints are undocumented

---

## 8. Setup and installation -- mandatory in every package repo

Every package repo must have a `docs/setup.md` (or a Setup section in README) **before the first code commit**. It is not written after the project is complete -- it is written when the repo is created.

### Minimum contents of setup.md

1. **Prerequisites** -- OS, Python version, hardware required, other services that must be running
2. **Installation** -- exact commands to install dependencies (`pip install -r requirements.txt`, apt packages, etc.)
3. **First-time setup** -- anything that must be done once before the software runs (export a model, build a binary, configure a device, etc.)
4. **How to run** -- the single command to start the software in normal mode
5. **Development / PC mode** -- how to run without real hardware (mock drivers, recorded data, simulators)
6. **Configuration** -- key parameters in config file and what they do (or a pointer to the config file)

### Rule

If someone clones the repo and follows `docs/setup.md` step by step, they must be able to run the software. If they cannot, the setup doc is incomplete -- fix it before the next push.

---

_Last updated: 2026-05-22._

