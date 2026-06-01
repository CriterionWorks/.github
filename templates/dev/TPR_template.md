---
document_id: TRS_TPR-XXX
package_id: TPR-XXX
project_id: CT-PR-XXX
level: Project | Package          # set one: Project = system-wide, Package = single package
linked_package: SPR-XXX / EPR-XXX / HPR-XXX / PCB-CR-XXX / DPR-XXX   # if Package level
version: 0.1
status: Draft
author:
written_by:                       # developer (pkg level) / QA engineer (system level)
reviewed_by:
approved_by:
date_created: YYYY-MM-DD
date_reviewed:
---

# Test Requirements Specification (TRS)
## TPR-XXX | Project: CT-PR-XXX

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | YYYY-MM-DD | | Initial draft |

---

## 1. Purpose, Scope and Level

### 1.1 Level

State whether this is a **Project-level** or **Package-level** TPR:

**Project-level TPR** — covers the full system: integration, system, verification, and validation.
Written by: QA engineer / test engineer, independent from development.

**Package-level TPR** — covers formal verification records for one package (SPR, EPR, HPR, PCB-CR, DPR).
Written by: test lead / QA, independent from the developer who built the package.

> **Important — what does NOT live here:**
> - Informal unit test logs and HIT session logs → stay in EPR / SPR repo `docs/` and session files
> - Software release records and baselines → stay in SPR `docs/software-baseline.md`
> - Config baselines (firmware + hardware versions) → stay in EPR `docs/config-baseline.md`
> - Requirements and acceptance criteria → stay in ERS / SRS in the CT-PR PM repo
>
> TPR owns: **formal test protocols (DS-12)** and **formal test results / records (DS-13)** that enter the DHF.
> SRS / ERS define *what* must pass. TPR defines *how* it is tested and records *whether it passed*.

> Set `level:` in the frontmatter above accordingly.

### 1.2 Scope

State what this TPR covers and what is explicitly out of scope.

**Packages / systems under test:**

| Package ID | Type | Name | Scope |
|------------|------|------|-------|
| SPR-XXX | Software | | |
| EPR-XXX | Firmware | | |
| HPR-XXX | Hardware unit | | |
| PCB-CR-XXX | PCB design | | |
| DPR-XXX | Deployment | | |

**Out of scope:**

---

## 2. Test Authorship and Responsibility

| Test stage | Written by | Executed by | Reviewed by | Gate |
|---|---|---|---|---|
| PCB inspection / continuity | PCB hardware engineer | Hardware engineer | Project lead | Gate 2 |
| PCB bring-up | Hardware engineer | Hardware engineer | Project lead | Gate 2 |
| Firmware unit test | EPR developer | EPR developer | Project lead | Gate 3 |
| Hardware Integration Test (HIT) | EPR developer | EPR developer | Project lead | Gate 3 |
| Software unit test | SPR developer | SPR developer | Project lead | Gate 3 |
| Deployment smoke test | DPR engineer | DPR engineer | Project lead | Gate 3 |
| Integration test | Lead engineer | Test team | Project lead | Gate 3 |
| System test | QA / test engineer | Test team | Project lead | Gate 3 |
| Verification | QA / test engineer (independent from dev) | Test team | HOD | Gate 3 |
| Validation | QA + clinical / project lead | Test team + users | HOD | Gate 4 |

**Rule:** Verification and Validation test plans must be written and executed by someone independent from the developer who built the package. This is required under ISO 13485.

---

## 3. Applicable Standards

| Standard | Title | Applicability |
|----------|-------|---------------|
| IEC 60601-1 | General Safety and Performance | Electrical safety tests |
| IEC 60601-1-2 | EMC | EMC tests |
| IEC 60601-1-6 | Usability | Usability tests |
| IEC 60601-1-8 | Alarm systems | If device has alarms |
| IEC 62304 | SW Lifecycle | Software verification tests |
| IEC 62366 | Usability Engineering | Summative usability testing |
| ISO 14971 | Risk Management | V&V must address all identified risks |
| ISO 10993 | Biological Evaluation | If patient-contact materials |
| ISO 14155 | Clinical Investigation | If clinical trial required |
| ISO 13485 | QMS | Independence requirement for V&V |

---

## 4. Test Categories

### 4.1 Package-level tests

| Category | Applies to | Description | Written by | Gate |
|---|---|---|---|---|
| PCB inspection | PCB-CR | Visual inspection, continuity, DFM check | HW engineer | Gate 2 |
| PCB bring-up | PCB-CR | Power-on, signal probing, basic functionality | HW engineer | Gate 2 |
| Firmware unit test | EPR | Individual function logic, on PC | EPR developer | Gate 3 |
| Hardware Integration Test (HIT) | EPR | Firmware on real target hardware, module scope | EPR developer | Gate 3 |
| Software unit test | SPR | Individual module logic, mocked dependencies | SPR developer | Gate 3 |
| Deployment smoke test | DPR | Container starts, healthcheck passes, env correct | DPR engineer | Gate 3 |

### 4.2 Project-level tests

| Category | Description | Written by | Gate |
|---|---|---|---|
| Integration test | Multiple packages working together | Lead engineer | Gate 3 |
| System test | Full device, end-to-end, lab environment | QA / test engineer | Gate 3 |
| Electrical safety | IEC 60601-1 — earth, leakage, dielectric | QA / test engineer | Gate 3 |
| EMC | IEC 60601-1-2 — emissions + immunity | External lab / QA | Gate 3 |
| Mechanical | IP, drop, vibration, sharp edges | QA / test engineer | Gate 3 |
| Usability | IEC 62366 summative test | QA + clinical | Gate 3 |
| Biocompatibility | ISO 10993 | External lab | Gate 3 |
| Verification | Does system meet all requirements? | QA (independent) | Gate 3 |
| Validation | Does system solve the real user problem? | QA + clinical | Gate 4 |

---

## 5. Test Equipment and Reference Instruments

| Instrument | Model | Reference | Calibration due | Asset / Serial no. |
|------------|-------|-----------|-----------------|-------------------|
| Vital signs simulator | Fluke ProSim 8 | [`_instruments/prosim8/overview.md`](../../../_instruments/prosim8/overview.md) | | |
| | | | | |

**Calibration requirement:** All instruments must be within calibration period on the date of testing. Verify from `_instruments/<name>/overview.md` before starting any test session.

---

## 6. Test Environment Requirements

| Requirement | Details |
|-------------|---------|
| Temperature | |
| Humidity | |
| External lab required | Yes / No — name lab if yes |
| Reference standards used | |

---

## 7. Test Protocols

Each test category in Section 4 must have a test protocol. Protocols are written as DS-12 documents and stored in `GATES/GATE-3/DS-12_TEST-PROTOCOLS/`.

| Protocol ID | Title | Package(s) tested | Standard ref | Instruments | Written by | Location |
|-------------|-------|------------------|--------------|-------------|------------|----------|
| TP-001 | | | | | | |
| TP-002 | | | | | | |

Each protocol must contain:
1. **Objective** — what property is being verified / validated
2. **Pass / fail criteria** — explicit and measurable
3. **Test setup** — equipment, configuration, environmental conditions
4. **Step-by-step procedure** — numbered, repeatable
5. **Data recording table** — what values to capture
6. **Deviation procedure** — what to do if a step cannot be completed as written

---

## 8. Verification Requirements

Verification answers: *"Did we build the product right?"* — outputs checked against specified requirements.

| Verification item | Requirement ref (SRS/ERS/DRS/TRS) | Method | Acceptance criteria | Status |
|------------------|-----------------------------------|--------|---------------------|--------|
| | | Inspection / Analysis / Test / Demo | | |

---

## 9. Validation Requirements

Validation answers: *"Did we build the right product?"* — device checked against user needs and intended use.

| Validation item | URS ref | Method | Acceptance criteria | Status |
|----------------|---------|--------|---------------------|--------|
| | | | | |

---

## 10. Risk-Based Test Coverage

All risks in PM-6 with residual risk Medium or above must have a test that verifies the risk control is effective.

| Risk ID (PM-6) | Hazard summary | Risk control | Test that verifies control | Protocol ref |
|----------------|---------------|-------------|--------------------------|-------------|
| | | | | |

---

## 11. Known Issues During Testing

| Issue ID | Description | Severity | Found in | Status | Resolved in |
|----------|-------------|----------|----------|--------|-------------|
| | | Critical / Major / Minor | TP-XXX | Open / Closed | |

---

## 12. Traceability

| TRS Req ID | Test requirement | Source ref | Protocol (DS-12) | Result (DS-13) |
|------------|-----------------|------------|------------------|----------------|
| TRS-001 | | | | |

---

## 13. Package Deliverables Checklist

| Deliverable | Standard ref | Level | Gate | Status |
|-------------|-------------|-------|------|--------|
| This TRS | | Project + Package | Gate 1 | |
| PCB inspection record | | PCB-CR | Gate 2 | |
| PCB bring-up record | | PCB-CR / HPR | Gate 2 | |
| Unit test results | IEC 62304 §5.5 | SPR / EPR | Gate 3 | |
| HIT results | | EPR | Gate 3 | |
| Deployment smoke test results | | DPR | Gate 3 | |
| Test protocols (DS-12, one per category) | | Project | Gate 3 | |
| Integration test report | | Project | Gate 3 | |
| System test report | | Project | Gate 3 | |
| Verification report (DS-13) | IEC 62304 §5.7 | Project | Gate 3 | |
| Usability test records (DS-14) | IEC 62366-1 §5.9 | Project | Gate 3 | |
| EMC test report (DS-15) | IEC 60601-1-2 | Project | Gate 3 | |
| Biocompatibility report | ISO 10993 | Project | Gate 3 | |
| Risk-test coverage matrix | ISO 14971 | Project | Gate 3 | |
| Validation report | ISO 14971 | Project | Gate 4 | |
| V&V sign-off record | ISO 13485 | Project | Gate 4 | |

---

## 14. V&V Readiness Checklist

- [ ] All requirements in SRS / ERS / DRS / TRS have at least one verification method assigned
- [ ] All risks in PM-6 (Medium and above) have a test that verifies the risk control
- [ ] PCB bring-up completed and recorded for all HPR / PCB-CR packages
- [ ] All unit tests passing for SPR and EPR packages
- [ ] All HIT results recorded for EPR packages
- [ ] Deployment smoke test passing for DPR packages
- [ ] All instruments in Section 5 are calibrated and within calibration date
- [ ] External lab engagements (EMC, biocompatibility) are booked
- [ ] Usability summative test participants recruited (IEC 62366-1 §5.9)
- [ ] V&V test authors are independent from the developers (ISO 13485 requirement)
- [ ] All known issues from integration testing resolved or accepted with justification
- [ ] Traceability matrix updated — every requirement linked to a test result

---

_Template version: 2.0 | Last updated: 2026-06-01_
