# Project Management Plan
## Azure DevOps Content Polishing Agent

**Document Version**: 2.1  
**Date**: November 15, 2026  
**Project Manager & Owner**: Abnel Lluberes  
**Status**: Draft (Pending Baseline)

---

## 1. Executive Summary

This document consolidates all planning information for the Azure DevOps Content Polishing Agent, a solo project. The v0.1 MVP is a containerized Python agent that reviews Azure DevOps work items every 5 minutes, proposes or applies content improvements (suggest-only by default), and provides a local monitoring dashboard.

**Key Facts**:
- **Start Date**: November 15, 2026
- **Timeline & Effort**: see [PROJECT_SCHEDULE.md](./PROJECT_SCHEDULE.md), the only document that carries dates and hours
- **Team**: 1 person (all roles), part-time; alluberes.com is the owner's main project
- **Deadline**: None; schedule dates are forecasts, not commitments
- **Budget**: Personal time only; no paid infrastructure or tools (open-source stack, runs locally)
- **Risk Level**: Medium

---

## 2. Project Scope

### In Scope – v0.1 MVP ✓
- Suggest-only / dry-run mode as the default; applying changes is opt-in per rule
- Description formatting
- Missing-field flags (acceptance criteria, story points) – flagged, never auto-filled
- Tag standardization
- Local Flask web dashboard with monitoring
- Protected manual trigger
- Complete audit logging
- Docker containerization
- Support for User Stories, Tasks, Bugs, Epics, Features

### Deferred to v0.2
- Grammar/spelling fixes
- Automatic linking of related items
- Slack alerting
- Per-item Apply button on the dashboard

### Out of Scope (Future Phases) ✗
- Cloud deployment (Azure Container Instances)
- Multi-organization support
- Custom workflow automation
- Advanced analytics dashboards
- Mobile app

### Assumptions
- Azure DevOps organization accessible & stable
- A separate test project is available for development and testing
- PAT will be provided securely
- No multi-tenant architecture needed
- File-based logging sufficient (no external DB)
- Localhost-only dashboard acceptable

---

## 3. Objectives & Success Criteria

| Objective | Success Criteria | Measurement |
|-----------|------------------|-------------|
| Automate content polishing | Agent runs every 5 minutes for 7 days unattended without a crash | 7-day soak test |
| Provide visibility | Dashboard displays results <500ms | Load test |
| Enable manual override | Manual trigger works within 2 min | Manual test |
| Maintain audit trail | All changes logged before/after | 100% of changes audited |
| Safe by default | No work item field changed unless apply mode is enabled; the only write in suggest-only mode is one optional comment per suggestion | Dry-run test on a real project |
| Professional delivery | Complete documentation with setup guides | Self-review checklist |
| Production-ready | Zero data loss on container restart | Restart test passes |

---

## 4. High-Level Requirements

Detailed requirements will be written in `docs/3 - Design/REQUIREMENTS.md`.

### Functional Requirements (FR)

**FR-1: Scheduled Processing**
- Agent must execute every 5 minutes (±30 seconds)
- Must process work items changed since the last run that match configurable filters
- Must skip items changed within the quiet period (default 30 minutes), so items someone is still writing are left alone
- Default filters must work on Agile, Scrum and Basic: exclude finished states (Closed, Done, Removed) instead of naming one template's active state
- Must be restartable without data loss

**FR-2: Content Polishing (v0.1)**
- Suggest-only by default (`DRY_RUN=true`): no work item field is changed
- Apply mode is opt-in per rule (`APPLY_FORMATTING`, `APPLY_TAG_ALIASES`, both off by default); rules that are not enabled stay suggestions
- Format descriptions: description fields are HTML and stay HTML (normalize structure and whitespace; never convert to markdown; never rewrite the author's wording)
- Flag missing required fields (acceptance criteria, estimate); never auto-fill them
- Standardize tags only through a configured alias map (e.g. `Front End`, `frontend` → `front-end`); tags not in the map are reported on the dashboard but never changed, so existing queries and boards keep working
- *(v0.2)* Fix grammar/spelling issues
- *(v0.2)* Detect and create links between related items

**FR-2a: Process Template Support**
- Support the Agile, Scrum and Basic process templates
- Read each work item type's available fields from the Azure DevOps API instead of hardcoding them; skip a rule when its field does not exist for that type
- Map the estimate field per template (Agile: Story Points; Scrum: Effort; Basic: verified during the design API spike) and the description field per type (e.g. Bugs use Repro Steps)

**FR-2b: Suggestion Delivery**
- Show every proposed change on the dashboard with before/after values
- Also post the suggestion as a work item comment (configurable, on by default); this is the only write made in suggest-only mode
- Each comment carries a hidden marker identifying the agent and the suggestion, so duplicates are avoided even if local state is lost
- Post a given suggestion only once: store a hash of the suggestion per work item locally and post again only if the suggestion changes

**FR-3: Monitoring Dashboard**
- Display last run execution time, count of items processed, success/failure rate
- Show list of recently processed items with proposed or applied changes
- Manual trigger button, protected by a CSRF token so other websites cannot trigger a run
- Localhost-only (no external access)

**FR-4: Audit Logging**
- Log every proposed and applied change with timestamp, work item ID, before/after values
- Store logs in JSON format (file-based)
- Enable audit trail review

**FR-5: Error Handling**
- Graceful handling of Azure DevOps API failures
- Retry logic with exponential backoff, honoring `Retry-After` headers
- Log and surface persistent failures on the dashboard

**FR-6: Safe Writes**
- Write the audit entry before applying any change
- Every update includes a JSON Patch `test` operation on the work item revision (`/rev`); if someone changed the item since it was read, the update is rejected, nothing is overwritten, and the item is re-evaluated on the next run
- Loop prevention: store locally the revision produced by each agent write or comment, and skip an item while that is still its latest revision (the agent runs with the owner's PAT, so its edits cannot be told apart by `ChangedBy`)
- Fetch only items changed since the last successful run (`System.ChangedDate` watermark stored locally)

### Non-Functional Requirements (NFR)

**NFR-1: Performance**
- Dashboard response time: <500ms
- Processing cycle: <5 minutes total

**NFR-2: Reliability**
- Runs 7 days unattended without a crash
- No data loss on container restart
- Graceful degradation if Azure API unavailable

**NFR-3: Security**
- PAT stored in environment variables (not in code)
- No credentials in logs or dashboard
- Localhost-only access for dashboard

**NFR-4: Scalability**
- Work through a backlog of 1000 changed work items across cycles (per-cycle cap is configurable)
- Archive logs when >1GB

**NFR-5: Maintainability**
- Code documented with docstrings
- Clear separation of concerns
- Testable design (unit/integration test coverage >80%)

---

## 5. Key Deliverables

Due dates are in [PROJECT_SCHEDULE.md](./PROJECT_SCHEDULE.md).

| Deliverable | Description | Owner |
|-------------|-------------|-------|
| Project Charter | Formal authorization (✅ approved Nov 15, 2026) | Abnel Lluberes |
| Planning Documents | Schedule, Risk Register, Plan | Abnel Lluberes |
| Requirements | Detailed v0.1 requirements | Abnel Lluberes |
| Architecture Design | Technical design & component diagrams | Abnel Lluberes |
| Source Code + Unit Tests | Complete v0.1 implementation in src/ | Abnel Lluberes |
| Docker Image | Containerized, runnable agent | Abnel Lluberes |
| Integration & E2E Tests | Tests against a test project, dry run on a real project | Abnel Lluberes |
| User Documentation | Setup guide, operations manual | Abnel Lluberes |
| Technical Documentation | Architecture, dashboard API reference | Abnel Lluberes |
| Release Package | Tagged v0.1.0 with release notes | Abnel Lluberes |

---

## 6. Communication Plan

As a solo project, communication is limited to self-tracking:

- **Weekly status check** (Sunday): hours spent vs. plan, completed tasks, blockers, next week's tasks
- **Milestone notes**: short entry in `docs/5 - Monitoring & Controlling/STATUS_REPORTS.md` at each milestone
- **Users of the processed Azure DevOps project**: informed before the first run in apply mode

---

## 7. Change Management Process

### Change Procedure
1. **Identify** the change (new feature, scope change, date change)
2. **Record** it in the v0.2 backlog or as a change entry (template below)
3. **Assess** impact on hours and target date
4. **Decide**: accept into v0.1 only if it fits the remaining buffer; otherwise defer to v0.2

### Change Entry Template
```
Change ID: [CR-###]
Date: [Date]
Description: [What changes]
Reason: [Why is this needed?]
Impact: [Hours added/removed, target date effect]
Decision: ☐ Accepted for v0.1  ☐ Deferred to v0.2  ☐ Rejected
```

---

## 8. Quality Management Plan

### Quality Standards
- Code follows PEP 8 style guide (black)
- All public functions documented with docstrings and type hints
- Test coverage: >80% (unit + integration)
- Every change goes through a pull request with CI passing and a self-review checklist
- Documentation reviewed against the checklist before release

### Quality Assurance Activities
- Unit tests written with each module
- CI on every push (tests, lint, type check, pip-audit)
- Integration testing against a test project
- End-to-end dry run on a real project before enabling apply mode

### Acceptance Criteria
- No open critical bugs
- All tests passing
- Performance targets met
- Documentation complete
- pip-audit and bandit clean

---

## 9. Resource Plan

| Role | Person | Effort |
|------|--------|--------|
| All roles (PM, design, development, QA, DevOps, documentation) | Abnel Lluberes | Part-time; capacity and effort are in PROJECT_SCHEDULE.md |

### Resource Constraints
- Single person: no backup if unavailable
- Reduced availability during the year-end holiday weeks

---

## 10. Risk Management (Summary)

See RISK_REGISTER.md for complete risk analysis.

**Top Risks**:
1. Weekly hours fall short (single person, secondary project) – moves the forecast; no deadline at stake
2. Azure API rate limiting (Medium prob, High impact)
3. PAT token expiration (Low prob, Critical impact)
4. Scope creep keeps pushing the forecast release out (Medium prob, Medium impact)

**Contingency**: a scheduled buffer of about 20% of planned effort (see PROJECT_SCHEDULE.md)

---

## 11. References

- **[PROJECT_CHARTER.md](../1%20-%20Initiation/PROJECT_CHARTER.md)** – Formal authorization
- **[PROJECT_SCHEDULE.md](./PROJECT_SCHEDULE.md)** – Detailed timeline & milestones
- **[RISK_REGISTER.md](./RISK_REGISTER.md)** – Risk identification & response strategies
- **docs/3 - Design/ARCHITECTURE.md** – Technical design details (planned)
- **docs/3 - Design/REQUIREMENTS.md** – Detailed functional requirements (planned)

---

## 12. Approval & Sign-off

| Role | Name | Date |
|------|------|------|
| Sponsor / Project Manager / Tech Lead | Abnel Lluberes | _____ |

---

**Document Control**:
- Version: 2.1
- Created: November 15, 2026
- Last Updated: November 15, 2026
- Status: Draft (Pending Baseline)
- Next Review: At the planning baseline
