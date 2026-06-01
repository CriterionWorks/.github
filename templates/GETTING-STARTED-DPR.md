# Getting Started — DPR Deployment Package Repository

Welcome to this DPR deployment package repository. Read this file before doing anything else.

---

## What is this repo?

This is a **deployment package repository** under a CT-PR project. It owns everything needed to deploy the linked SPR software package onto a target server — Docker, Compose, environment config, CI/CD pipelines, and server-specific settings.

Source code lives in the linked SPR repo — not here.
This repo references SPR at a specific version tag (git submodule).

---

## Where to start

| I am... | Start here |
|---|---|
| New DevOps engineer | Read `BACKLOG.md` — pick up your first task |
| Deploying for the first time | Read `docs/deploy.md` |
| Looking for environment variables | See `env/` folder — copy `.env.template` to `.env` |
| Looking for which SPR version is deployed | Check `docs/deployment-baseline.md` |
| Looking for source code | Go to the linked SPR repo in `app/` submodule |

---

## Repo structure

The following is the minimum expected layout. Items marked **required** must always be present.

```
DPR-XXX/
│
├── GETTING-STARTED-DPR.md          ← you are here (required)
├── BACKLOG.md                       ← task list, always kept up to date (required)
│
├── app/                             ← git submodule → linked SPR repo @ pinned tag
│
├── docker/                          ← all container definitions (required)
│   ├── Dockerfile                   ← application image
│   └── docker-compose.yml           ← service definition with restart: always + healthcheck
│
├── env/                             ← environment configuration (required)
│   ├── .env.template                ← template with all required variables (commit this)
│   └── .env                         ← actual secrets — NEVER commit, add to .gitignore
│
├── nginx/                           ← reverse proxy config (if applicable)
│   └── default.conf
│
├── ci/                              ← CI/CD pipeline definitions (if applicable)
│   └── ...                          ← GitHub Actions, Jenkins, etc.
│
├── docs/                            ← all DPR-level documents (required)
│   ├── deploy.md                    ← step-by-step deployment instructions (required)
│   ├── deployment-baseline.md       ← SPR version + environment baseline (required)
│   └── ...                          ← rollback procedure, monitoring setup, known issues
│
├── misc/                            ← anything that doesn't fit above
└── ...                              ← any other folders the engineer needs
```

---

## SPR submodule

The SPR repo is linked as a git submodule pinned to a specific release tag. After cloning:

```bash
git submodule update --init --recursive
```

Do not update the submodule casually — it means you are deploying a new software version and the deployment baseline must be updated and re-tested. Always update `docs/deployment-baseline.md` when the submodule is bumped.

---

## docker-compose.yml minimum standard

Every service must have `restart: always` and a `healthcheck`:

```yaml
services:
  service_name:
    build: ./docker
    restart: always
    ports:
      - "HOST_PORT:CONTAINER_PORT"
    env_file: ./env/.env
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:PORT/health"]
      interval: 30s
      retries: 3
```

---

## Deployment Baseline — `docs/deployment-baseline.md`

Every DPR release must have a baseline entry:

```
## DPR-XXX vX.X — Deployment Baseline

| Item               | Version / Value       | Notes                        |
|--------------------|-----------------------|------------------------------|
| DPR                | vX.X.X                | tag: vX.X.X                  |
| SPR-XXX            | vX.X.X                | tag: vX.X.X — via submodule  |
| Target server      | e.g. Server 2 / Prod  |                              |
| Docker version     | e.g. 24.0.5           |                              |
| OS                 | e.g. Ubuntu 22.04     |                              |
| Deployed on        | [date]                |                              |
| Deployed by        | [name]                |                              |
| Approved by        | [name]                |                              |
```

---

## Deployment stages

Follow the 3-stage rule — never deploy directly to production:

```
Dev server    → test the deployment works
Staging server → smoke test, confirm all services healthy
Production    → HOD approval required before deploying
```

---

## BACKLOG.md — required

Keep it updated every session. Commit message format:

```
DPR<number>-<item>: short description
Example: DPR001-003: add healthcheck to docker-compose
```

---

## Branch rules

- Work on `dev` — never push directly to `main`
- PR from `dev` → `main` when ready for review
- Tag releases from `main` only: `v1.0.0`, `v1.1.0` etc.
- Every release tag must have a corresponding `deployment-baseline.md` entry

---

## Contact

**Project Lead:** Ratan Srivastava — ratansrivastava.work@gmail.com
**Organisation:** [CriterionWorks](https://github.com/CriterionWorks)

---

_For current task list always refer to `BACKLOG.md`. This file is for first-time orientation only._
