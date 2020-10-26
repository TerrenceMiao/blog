---
title: Risk Management for CI/CD processes
date: 2020-10-26 16:55:13
tags:
---

Consider a full development and deployment cycle, and the potential risks involved during the different stages in CDP (CI / Continuous Integration, CD / Continuous Delivery, CDP / Continuous Deployment):

* Code

| Role           | Details |
| -------------- | --------|
| Stakeholders   | Individual Developer |
|                | Pair Programming Mentor |
|                | DBA |
|                | Security Team |
| Failure Points | Logic flaws |
|                | Security flaws |
|                | Code standards issues |
| Safeguards     | Test Driven Development |
|                | Red/Green/Refactor |
|                | Linting tools |
|                | Testing Docker containers |
|                | Pair programming |
|                | Query analysis |
|                | Static code analysis |

* Commit

| Role           | Details |
| -------------- | --------|
| Stakeholders   | Security Team Member for sign-off |
|                | Engineering Team Lead for sign-off |
| Failure Points | Force pushes |
|                | Merge conflicts |
| Safeguards     | Master branch protections |
|                | 3 member sign-off before master merge |
|                | Commit hooks |

* Test

| Role           | Details |
| -------------- | --------|
| Stakeholders   | Individual Developer |
|                | QA Team |
| Failure Points | Broken tests |
|                | Stale tests |
|                | False positive tests |
| Safeguards     | Weekly failure testing triage meeting to catch broken tests |
|                | Daily cron runs of test suite against mock prod environment |

* Deployment

| Role           | Details |
| -------------- | --------|
| Stakeholders   | SysOps Team |
|                | Individual Developers |
|                | Support Team |
|                | Customers |
| Failure Points | Broken deployments |
|                | Dropped customer traffic |
| Safeguards     | Blue/Green deployment |
|                | Traffic re-routing |
|                | Pre deployment spare instance warmup |
|                | Communicate out to support in order to verify proper staffing levels |

* Runtime

| Role           | Details |
| -------------- | --------|
| Stakeholders   | Security Team |
|                | SysOps Team |
|                | Engineering Teams |
|                | Support Team |
|                | Customers |
| Failure Points | High resource usage |
|                | Slow queries |
|                | Malicious actors |
|                | MProvider downtime |
| Safeguards     | Communicate out to support for new feature awareness and appropriate categories for issues regarding the component |
|                | System resource alarms for various metrics and slow DB log alerts |
|                | Instant maintenance page switchover capabilities |
|                | Status page on redundant providers |
|                | Application firewalls |
|                | Database replicas |