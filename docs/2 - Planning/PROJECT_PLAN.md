# Project Management Plan
## Azure DevOps Content Polishing Agent

**Document Version**: 2.0  
**Date**: November 15, 2026  
**Project Manager & Owner**: Abnel Lluberes  
**Status**: Draft (Pending Baseline – Nov 22, 2026)

---

## 1. Executive Summary

This document consolidates all planning information for the Azure DevOps Content Polishing Agent, a solo project. The v0.1 MVP is a containerized Python agent that reviews Azure DevOps work items every 5 minutes, proposes or applies content improvements (suggest-only by default), and provides a local monitoring dashboard.

**Key Facts**:
- **Start Date**: November 15, 2026
- **Planned v0.1.0 Release**: February 28, 2027
- **Target Completion (including buffer)**: March 21, 2027
- **Team**: 1 person (all roles), ~10 hours per week
- **Effort**: 136 hours planned + 27 hours buffer = 163 hours
- **Budget**: Personal time only; no paid infrastructure or tools (open-source stack, runs locally)
- **Risk Level**: Medium

---

## 2. Project Scope

### In Scope – v0.1 MVP ✓
- Suggest-only / dry-run mode as the default; applying changes is opt-in
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
| Automate content polishing | Agent runs reliably every 5 minutes | 99% uptime in testing |
| Provide visibility | Dashboard displays results <500ms | Load test |
| Enable manual override | Manual trigger works within 2 min | Manual test |
| Maintain audit trail | All changes logged before/after | 100% of changes audited |
| Safe by default | No work item changed unless apply mode is enabled | Dry-run test on a real project |
| Professional delivery | Complete documentation with setup guides | Self-review checklist |
| Production-ready | Zero data loss on container restart | Restart test passes |

---

## 4. High-Level Requirements

Detailed requirements will be written in `docs/3 - Design/REQUIREMENTS.md` (due Nov 29, 2026).

### Functional Requirements (FR)

**FR-1: Scheduled Processing**
- Agent must execute every 5 minutes (±30 seconds)
- Must process work items changed since the last run that match configurable filters
- Must be restartable without data loss

**FR-2: Content Polishing (v0.1)**
- Suggest-only by default; apply mode is opt-in
- Format descriptions
- Flag missing required fields (acceptance criteria, story points)
- Standardize tags to lowercase-hyphenated format
- *(v0.2)* Fix grammar/spelling issues
- *(v0.2)* Detect and create links between related items

**FR-3: Monitoring Dashboard**
- Display last run execution time, count of items processed, success/failure rate
- Show list of recently processed items with proposed or applied changes
- Manual trigger button, protected against cross-site requests
- Localhost-only (no external access)

**FR-4: Audit Logging**
- Log every proposed and applied change with timestamp, work item ID, before/after values
- Store logs in JSON format (file-based)
- Enable audit trail review

**FR-5: Error Handling**
- Graceful handling of Azure DevOps API failures
- Retry logic with exponential backoff
- Log and surface persistent failures on the dashboard

### Non-Functional Requirements (NFR)

**NFR-1: Performance**
- Dashboard response time: <500ms
- Processing cycle: <5 minutes total

**NFR-2: Reliability**
- Uptime: 99% during testing
- No data loss on container restart
- Graceful degradation if Azure API unavailable

**NFR-3: Security**
- PAT stored in environment variables (not in code)
- No credentials in logs or dashboard
- Localhost-only access for dashboard

**NFR-4: Scalability**
- Support up to 1000 work items per cycle
- Archive logs when >1GB

**NFR-5: Maintainability**
- Code documented with docstrings
- Clear separation of concerns
- Testable design (unit/integration test coverage >80%)

---

## 5. Key Deliverables

| Deliverable | Description | Completion Date | Owner |
|-------------|-------------|-----------------|-------|
| Project Charter | Formal authorization | Nov 15, 2026 ✅ | Abnel Lluberes |
| Planning Documents | Schedule, Risk Register, Plan | Nov 22, 2026 | Abnel Lluberes |
| Requirements | Detailed v0.1 requirements | Nov 29, 2026 | Abnel Lluberes |
| Architecture Design | Technical design & component diagrams | Dec 6, 2026 | Abnel Lluberes |
| Source Code + Unit Tests | Complete v0.1 implementation in src/ | Jan 31, 2027 | Abnel Lluberes |
| Docker Image | Containerized, runnable agent | Jan 31, 2027 | Abnel Lluberes |
| Integration & E2E Tests | Tests against a test project, dry run on a real project | Feb 14, 2027 | Abnel Lluberes |
| User Documentation | Setup guide, operations manual | Feb 21, 2027 | Abnel Lluberes |
| Technical Documentation | Architecture, dashboard API reference | Feb 28, 2027 | Abnel Lluberes |
| Release Package | Tagged v0.1.0 with release notes | Feb 28, 2027 | Abnel Lluberes |

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
| All roles (PM, design, development, QA, DevOps, documentation) | Abnel Lluberes | ~10 h/week; 136 h planned + 27 h buffer |

### Resource Constraints
- Single person: no backup if unavailable
- Reduced availability during the weeks of Dec 21 and Dec 28

---

## 10. Risk Management (Summary)

See RISK_REGISTER.md for complete risk analysis.

**Top Risks**:
1. Weekly hours fall short (single person, side project)
2. Azure API rate limiting (Medium prob, High impact)
3. PAT token expiration (Low prob, Critical impact)
4. Scope creep delays the release (Medium prob, High impact)

**Contingency**: 27 hours of buffer, scheduled Mar 1 – Mar 21, 2027

---

## 11. References

- **[PROJECT_CHARTER.md](../1%20-%20Initiation/PROJECT_CHARTER.md)** – Formal authorization
- **[PROJECT_SCHEDULE.md](./PROJECT_SCHEDULE.md)** – Detailed timeline & milestones
- **[RISK_REGISTER.md](./RISK_REGISTER.md)** – Risk identification & response strategies
- **docs/3 - Design/ARCHITECTURE.md** – Technical design details (due Dec 6, 2026)
- **docs/3 - Design/REQUIREMENTS.md** – Detailed functional requirements (due Nov 29, 2026)

---

## 12. Approval & Sign-off

| Role | Name | Date |
|------|------|------|
| Sponsor / Project Manager / Tech Lead | Abnel Lluberes | _____ |

---

**Document Control**:
- Version: 2.0
- Created: November 15, 2026
- Last Updated: November 15, 2026
- Status: Draft (Pending Baseline)
- Next Review: November 22, 2026
