# GitHub Actions Notification Setup — CT-PR-0195

## Purpose

Email notifications to `ratansrivastava.work@gmail.com` for all CriterionWorks repo activity:
- Push (any branch)
- Pull Requests (opened, closed, merged, review requested)
- Issues (opened, closed, commented)
- Releases (published)

---

## One-Time Setup

### Step 1 — Gmail App Password

1. Go to `myaccount.google.com/apppasswords`
2. Sign in with `ratansrivastava.work@gmail.com`
3. App name: `GitHub Actions CriterionWorks`
4. Copy the 16-character password

### Step 2 — Add Org-Level Secrets

1. Go to `github.com/organizations/CriterionWorks/settings/secrets/actions`
2. Add two secrets:

| Name | Value |
|---|---|
| `GMAIL_USER` | `ratansrivastava.work@gmail.com` |
| `GMAIL_PASS` | 16-char app password from Step 1 |

Set once at org level — all repos use them via `secrets: inherit`.

---

## Adding Notifications to a Repo

Create `.github/workflows/events.yml` in the target repo:

```yaml
name: Notify CriterionWorks

on:
  push:
  pull_request:
    types: [opened, closed, synchronize, review_requested, ready_for_review]
  issues:
    types: [opened, closed, reopened, edited]
  release:
    types: [published]

jobs:
  notify:
    uses: CriterionWorks/.github/.github/workflows/notify.yml@main
    secrets: inherit
```

Commit and push. Done.

---

## Repo Status

| Repo | events.yml added | Date |
|---|---|---|
| CT-PR-0174-Production-QMS-ERP-Management-System | ✓ | 2026-05-26 |
| CT-PR-0195-Vault | ✓ | 2026-05-26 |
| TPR-001-SPR109-DICOM-Setup | ✓ | 2026-05-26 |
| CT-PR-0194-Server-3-Management | ✓ | 2026-05-26 |
| SPR-113-RoboticArmControl | ✓ | 2026-05-26 |
| SPR-114-QMS-Platform-Core | ✓ | 2026-05-26 |
| CT-PR-0198-Autonomous-Robot-with-Robotic-Arm | ✓ | 2026-05-26 |
| EPR-121-RoboticArmElectronics | ✓ | 2026-05-26 |
| MPR-028-AutonomousRobot-Chassis | ✓ | 2026-05-26 |
| MPR-032-RoboticArm-3DOF | ✓ | 2026-05-26 |
| TPR-002-SPR110-ECG-Decoding | ✓ | 2026-05-26 |
| SPR-110-TC35-ECG-Parameter-Decoding | ✓ | 2026-05-26 |
| SPR-106-BabyCryAnalysisSystem | ✓ | 2026-05-26 |
| CT-PR-0185-Integration-of-Phillips-TC-35-ECG-Machine | ✓ | 2026-05-26 |
| SPR-109-Philips-TC35-ECG-DICOM-Setup | ✓ | 2026-05-26 |
| SPR-061-FootSoleDetectionForGCSMeasurement | ✓ | 2026-05-26 |
| SPR-102-BodyWaterRetentionCalculation | ✓ | 2026-05-26 |
| CT-PR-0180-Integration-of-AQT-90-Flex | ✓ | 2026-05-26 |

---

## Maintenance

- Update email format: edit `notify.yml` in this repo — all 18 repos pick up the change
- Add a new repo: copy `events.yml` into its `.github/workflows/`
- Change notification email: update `GMAIL_USER` secret + `to:` in `notify.yml`
- App password expired: regenerate at `myaccount.google.com/apppasswords`, update `GMAIL_PASS` secret
