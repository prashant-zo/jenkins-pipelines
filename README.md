# Jenkins pipelines — DevOps

Declarative Jenkins pipeline examples for learning and reuse: single-agent and multi-stage pipelines with per-stage Docker agents.

---

## Contents

| Section | What you’ll find |
|--------|-------------------|
| [Overview](#overview) | What this repo contains and when to use each example |
| [Prerequisites](#prerequisites) | Jenkins, plugins, and Docker requirements |
| [Repository layout](#repository-layout) | Where each `Jenkinsfile` lives |
| [Examples](#examples) | Pipeline behavior and key syntax |
| [How to run](#how-to-run-in-jenkins) | Wiring a job to these pipelines |

---

## Overview

This repository holds **two minimal pipelines** that demonstrate common Jenkins patterns:

1. **First pipeline** — One agent, one stage. Good starting point for a simple build or smoke check.
2. **Multi-stage / multi-agent** — Stages run on **different Docker images** (backend, frontend, database tooling). Shows `agent none` at the top level and per-stage `agent { docker { ... } }`.

Neither pipeline is tied to a specific application repo; they are **templates** you can copy or point a Jenkins job at.

---

## Prerequisites

- **Jenkins** 2.x with the **Pipeline** plugin (Declarative Pipeline syntax).
- **Docker** available on the Jenkins controller or on agents that run these jobs, plus the ability to run **Docker Pipeline** steps (`docker { image '...' }`).
- Network access from Jenkins to **pull** the images referenced in each `Jenkinsfile` (e.g. `node`, `maven`, `postgres`).

If Docker-in-Docker or a remote Docker host is required in your environment, configure that in Jenkins before running these examples.

---

## Repository layout

```
Jenkins/
├── README.md                          ← you are here
├── first-pipeline/
│   └── Jenkinsfile                    ← single agent, single stage
└── multi-stage-multi-agent/
    └── Jenkinsfile                    ← multiple stages, different agents per stage
```

---

## Examples

### `first-pipeline/`

| Item | Detail |
|------|--------|
| **Agent** | `node:18-alpine` for the whole pipeline |
| **Stages** | `test` — runs `node --version` |
| **Use when** | You want the smallest possible Declarative pipeline with Docker |

**File:** [`first-pipeline/Jenkinsfile`](first-pipeline/Jenkinsfile)

---

### `multi-stage-multi-agent/`

| Stage | Docker image | Sample step |
|-------|----------------|-------------|
| Back-end | `maven:3.8.1-adoptopenjdk-11` | `mvn --version` |
| Front-end | `node:16-alpine` | `node --version` |
| Database check | `postgres:13-alpine` | `psql --version` |

The pipeline uses **`agent none`** at the root so each stage selects its own image. Replace the `sh` steps with real build, test, or migration commands as needed.

**File:** [`multi-stage-multi-agent/Jenkinsfile`](multi-stage-multi-agent/Jenkinsfile)

---

## How to run in Jenkins

1. **Pipeline from SCM**  
   - New Item → Pipeline → Pipeline script from SCM.  
   - Point SCM at this repository and set the **Script Path** to either `first-pipeline/Jenkinsfile` or `multi-stage-multi-agent/Jenkinsfile`.

2. **Multibranch Pipeline**  
   - If each branch should use a different path, configure branch properties or use a root `Jenkinsfile` that delegates; for these demos, a single-branch Pipeline job is enough.

3. **Copy-paste**  
   - Create a Pipeline job with “Pipeline script” and paste the contents of the `Jenkinsfile` you want to try.

After the first run, confirm in the build log that the expected containers start and the version commands succeed.

---

## Contributing

Add new examples as sibling folders with their own `Jenkinsfile` and a one-line note in the [Repository layout](#repository-layout) and [Examples](#examples) sections so others can find them quickly.

---

Author  
Prashant
