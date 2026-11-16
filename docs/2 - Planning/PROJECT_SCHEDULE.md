# Project Schedule
## Azure DevOps Content Polishing Agent

**Document Version**: 2.2  
**Date**: November 15, 2026  
**Scope**: v0.1 MVP (solo project)  
**Deadline**: None. Every date in this document is a forecast, not a commitment. The goal is an early version in February 2027 on a best-effort basis; alluberes.com is the owner's main project and takes priority.  
**Capacity Assumption (for the forecast)**: ~10 hours per week; ~5 hours in the weeks of Dec 21 and Dec 28 (holidays)  
**Status**: Draft (Pending Baseline – Nov 22, 2026)

**This is the only document that carries dates and effort hours.** Other documents link here instead of repeating them.

---

## 1. Execution Phase Timeline

Durations are effort hours, not calendar days. Dates are the week a task starts and the Sunday of the week it finishes.

### Phase 1: Initiation – Complete (Nov 15, 2026)
| Task | Effort | Status |
|------|--------|--------|
| Project Charter approved | — | ✅ Nov 15, 2026 |

### Phase 2: Planning (Nov 16 – Nov 22, 2026)
| Task | Effort | Start | End |
|------|--------|-------|-----|
| Apply safety decisions to requirements | 2 h | Nov 16 | ✅ Nov 15 |
| Self-review & baseline planning docs | 2 h | Nov 16 | Nov 22 |
| **Phase Total** | **4 h** | | |

### Phase 3: Design (Nov 16 – Dec 6, 2026)
| Task | Effort | Start | End | Dependencies |
|------|--------|-------|-----|--------------|
| REQUIREMENTS.md (v0.1 spec, Azure DevOps field mapping per process template) | 10 h | Nov 16 | Nov 29 | Planning |
| ARCHITECTURE.md (components, data flow, concurrency & loop prevention) | 10 h | Nov 23 | Dec 6 | Requirements |
| Create a free test Azure DevOps organization with Agile, Scrum and Basic projects | part of spike | Nov 30 | Dec 6 | – |
| API spike against the test projects (rev `test` op, comments API, field names per template, SDK vs. plain REST decision) | 4 h | Nov 30 | Dec 6 | Requirements, test projects |
| **Phase Total** | **24 h** | | | |

### Phase 4: Implementation (Nov 30, 2026 – Jan 31, 2027)
Unit tests are written alongside each module, not afterwards.

| Task | Effort | Start | End | Dependencies |
|------|--------|-------|-----|--------------|
| Project setup + CI (lint, tests, pip-audit) | 6 h | Nov 30 | Dec 13 | – |
| Config loading + Azure DevOps client (retries, backoff) | 14 h | Dec 7 | Dec 20 | Setup, API spike |
| Filters + incremental WIQL query (ChangedDate watermark) | 8 h | Dec 14 | Jan 3 | Azure client |
| Audit logger (before/after JSON) | 8 h | Dec 28 | Jan 10 | Setup |
| Polishing rules: formatting, missing-field flags, tags | 20 h | Jan 4 | Jan 24 | Filters, Audit logger |
| Scheduler / main loop | 4 h | Jan 18 | Jan 24 | Polishing rules |
| Dashboard + protected manual trigger | 8 h | Jan 18 | Jan 31 | Audit logger, Scheduler |
| Dockerfile + docker-compose | 4 h | Jan 25 | Jan 31 | All modules |
| **Phase Total** | **72 h** | | | |

### Phase 5: Testing (Feb 1 – Feb 14, 2027)
| Task | Effort | Start | End |
|------|--------|-------|-----|
| Integration tests against the test project | 8 h | Feb 1 | Feb 7 |
| End-to-end dry run on a real project | 8 h | Feb 1 | Feb 14 |
| Bug fixes | 4 h | Feb 8 | Feb 14 |
| **Phase Total** | **20 h** | | |

### Phase 6: Documentation & Release (Feb 15 – Feb 28, 2027)
| Task | Effort | Start | End |
|------|--------|-------|-----|
| Setup & operations guide | 8 h | Feb 15 | Feb 21 |
| Dashboard API reference | 4 h | Feb 15 | Feb 28 |
| Release notes + tag v0.1.0 | 4 h | Feb 22 | Feb 28 |
| **Phase Total** | **16 h** | | |

### Contingency Buffer (Mar 1 – Mar 21, 2027)
| Item | Effort |
|------|--------|
| Buffer (~20% of planned effort) | 27 h |

**Planned effort**: 136 h · **With buffer**: 163 h

---

## 2. Critical Path
```
Planning (4 h)
  ↓
Requirements (10 h) → Architecture (10 h) + API spike (4 h)
  ↓
Setup + CI (6 h) → Azure client (14 h) → Filters (8 h) → Polishing rules (20 h) → Scheduler (4 h) → Dashboard (8 h) → Docker (4 h)
                   (Audit logger (8 h) must be done before Polishing rules)
  ↓
Testing (20 h)
  ↓
Documentation & Release (16 h)
```

As a solo project, every task is effectively on the critical path; overlapping dates only mean a task starts in the same week the previous one ends.

---

## 3. Milestones
| Milestone | Forecast Date | Status |
|-----------|---------------|--------|
| Project Charter Approved | Nov 15, 2026 | ✅ Complete |
| Planning Baseline Set | Nov 22, 2026 | Forecast |
| Design Complete + re-estimate checkpoint | Dec 6, 2026 | Forecast |
| Feature Complete (v0.1) | Jan 31, 2027 | Forecast |
| Testing Complete | Feb 14, 2027 | Forecast |
| v0.1.0 Released | Feb 28, 2027 | Forecast |
| Latest forecast if the whole buffer is used | Mar 21, 2027 | Forecast |

---

## 4. Resource Allocation
| Role | Person | Effort |
|------|--------|--------|
| All roles (PM, design, development, QA, DevOps, documentation) | Abnel Lluberes | 136 h planned + 27 h buffer, ~10 h/week |

---

## 5. Schedule Risks & Contingencies
| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|-----------|
| Weekly hours fall below 10 (main project takes priority) | High | Low | No deadline is at stake: track hours weekly and move the forecast dates |
| Azure API limitations found late | Medium | Medium | API spike during design (by Dec 6) |
| Scope creep | Medium | High | v0.2 backlog for anything not in the v0.1 list |
| Technical blockers | Medium | Medium | Time-box investigations to 2 h, then simplify |

**Contingency Buffer**: 27 hours (~3 weeks at 10 h/week), scheduled Mar 1 – Mar 21, 2027.

---

## 6. Schedule Tracking
- **Status check**: Weekly (Sunday), hours spent vs. planned, recorded in `docs/5 - Monitoring & Controlling/STATUS_REPORTS.md`
- **Variance Threshold**: ±5 hours cumulative
- **Forecast Update**: when actual hours differ from plan by more than 10, move the forecast dates in this document

---

## 7. Estimate Basis & Re-estimate Checkpoint

The task estimates are top-down: they were scaled from the original team plan, not built bottom-up. Scope was also added after estimating without adding hours: suggestion comments with duplicate protection, the local state store, support for three process templates, the quiet period and the tag alias map.

- **Checkpoint**: at Design Complete (Dec 6, 2026), re-estimate every implementation task using what the API spike showed, and update this document
- **Until then**: compare actual and planned hours every week; if the first three weeks run more than 25% over, raise the estimates and move the forecast before implementation starts
- **Expectation**: the added scope is likely to use part of the contingency buffer

---

**Approved By**: _________________ Date: _________

**Notes**:
- Estimates are effort hours for one person
- The buffer is part of the calendar: the latest forecast date already includes it
- Dates assume the stated weekly capacity; fewer hours in a week simply move the forecast
- See PROJECT_PLAN.md for scope and requirements
