# Project Charter
## Azure DevOps Content Polishing Agent

**Document Version**: 1.3  
**Date**: November 15, 2026  
**Project Manager & Owner**: Abnel Lluberes  
**Sponsor**: Abnel Lluberes  
**Status**: Approved

---

## 1. Project Title
Azure DevOps Work Item Content Polishing Agent

## 2. Business Justification
Keeping Azure DevOps work items well documented takes steady manual effort. This solo project builds an agent that reviews work items and improves their content consistently, starting in a safe suggest-only mode:
- Formatting work item descriptions
- Flagging missing required fields (acceptance criteria, story points)
- Standardizing tags

The project is built for personal use first, with possible commercial use later (Apache 2.0 license).

**Expected Benefits**:
- Less manual time spent polishing work items
- Consistent formatting and tagging across work items
- Complete audit trail of every proposed and applied change
- A reusable, documented tool that can be offered commercially

## 3. Project Description
A self-contained Docker agent that runs every 5 minutes to:
- Connect to an Azure DevOps organization
- Fetch work items changed since the last run that match configurable filters
- Apply content polishing rules (suggest-only by default)
- Provide a local web dashboard for monitoring and manual triggers
- Maintain detailed audit logs of all changes

## 4. High-Level Requirements (v0.1 MVP)
- [ ] Scheduled execution every 5 minutes
- [ ] Suggest-only / dry-run mode as the default; applying changes is opt-in per rule
- [ ] Suggestions shown on the dashboard and posted once per item as a comment
- [ ] Description formatting
- [ ] Missing-field flags (no auto-filled estimates or criteria)
- [ ] Tag standardization
- [ ] Local web dashboard with a protected manual trigger
- [ ] Complete audit logging
- [ ] Docker containerization
- [ ] Support for multiple work item types

**Deferred to v0.2**: grammar/spelling fixes, automatic linking of related items, Slack notifications, per-item Apply button on the dashboard.

## 5. Success Criteria
- Agent runs 7 days unattended without a crash
- Dashboard response time <500ms
- All changes logged with before/after comparison
- No work item is changed without an audit entry
- In suggest-only mode no work item field is changed (the only write is one optional comment per suggestion)
- Documentation complete and clear
- Zero data loss on container restart

## 6. Project Constraints
- Solo project: one person in every role, secondary to the owner's main project (alluberes.com)
- No fixed deadline: schedule dates are forecasts based on ~10 hours per week
- Local deployment only (no cloud hosting required)
- Must integrate with an existing Azure DevOps organization
- Requires PAT (Personal Access Token) authentication
- Single-container deployment model

## 7. Assumptions
- Azure DevOps organization is accessible and stable
- A separate test project is available for development and integration testing
- PAT will be provided securely via environment variables
- No complex persistence layer needed (file-based logs acceptable)
- Dashboard is localhost-only for this phase

## 8. High-Level Timeline
Initiation (complete) → Planning → Design → Implementation → Testing → Documentation & Release, followed by a contingency buffer.

There is no fixed deadline. Forecast dates and effort are kept only in [PROJECT_SCHEDULE.md](../2%20-%20Planning/PROJECT_SCHEDULE.md), so they cannot drift between documents.

## 9. Authorized By
- **Project Sponsor**: Abnel Lluberes  Date: November 15, 2026
- **Project Manager**: Abnel Lluberes  Date: November 15, 2026
- **Technical Lead**: Abnel Lluberes  Date: November 15, 2026

## 10. Key Stakeholders
| Role | Name | Contact | Interest |
|------|------|---------|----------|
| Sponsor, Project Manager, Product Owner, Developer | Abnel Lluberes | abnel@alluberes.com | All aspects (solo project) |
| Azure DevOps users whose work items are processed | — | — | Accurate, non-disruptive changes |

---

**Sign-off**: This charter formally authorizes this project.
