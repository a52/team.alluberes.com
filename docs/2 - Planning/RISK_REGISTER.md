# Risk Register
## Azure DevOps Content Polishing Agent

**Document Version**: 2.0  
**Date**: November 15, 2026  
**Last Updated**: November 15, 2026  
**Owner**: Abnel Lluberes

---

## 1. Risk Identification & Assessment Matrix

### Critical Risks (Probability ≥ High OR Impact = Severe)

| ID | Risk Description | Probability | Impact | Priority | Owner | Mitigation | Contingency |
|----|------------------|-------------|--------|----------|-------|-----------|------------|
| R001 | Azure DevOps API rate limiting or throttling during testing/production | Medium | High | **Critical** | Abnel Lluberes | Implement exponential backoff; test API limits in the design API spike | Reduce polling frequency if limits hit |
| R002 | PAT token expires or becomes invalid; breaks production agent | Low | Critical | **Critical** | Abnel Lluberes | Store token securely in env vars; create rotation procedure; alert on expiry | Quick token refresh procedure; manual trigger capability |
| R003 | Work item linking logic creates circular dependencies or breaks relationships *(linking deferred to v0.2)* | Medium | High | **Critical** | Abnel Lluberes | Rigorous unit testing (>90% coverage); validate graph integrity | Manual review & audit log inspection |

### High Risks (Probability = Medium & Impact = High)

| ID | Risk Description | Probability | Impact | Priority | Owner | Mitigation | Contingency |
|----|------------------|-------------|--------|----------|-------|-----------|------------|
| R005 | Dashboard performance degrades with large audit logs | Medium | High | **High** | Abnel Lluberes | Implement log rotation & archiving with the audit logger; pagination in UI | Implement simple in-memory cache; compress logs |
| R006 | Docker container fails on first production pull/run | Medium | High | **High** | Abnel Lluberes | Test container build on clean machine; document all dependencies | Quick rollback to previous version |
| R007 | Natural Language processing for grammar/standardization produces false positives *(grammar deferred to v0.2)* | Medium | High | **High** | Abnel Lluberes | Use conservative rule sets; require manual approval for changes; extensive testing | Disable NLP features in v0.1; enable in v0.2 |

### Medium Risks (Various combinations)

| ID | Risk Description | Probability | Impact | Priority | Owner | Mitigation | Contingency |
|----|------------------|-------------|--------|----------|-------|-----------|------------|
| R004 | Scope creep keeps pushing the forecast release out (no fixed deadline) | Medium | Medium | **Medium** | Abnel Lluberes | Weekly scope check; anything not in the v0.1 list goes to the v0.2 backlog | Re-prioritize features; shift non-critical to v0.2 |
| R008 | Weekly hours fall short or the project stalls (solo; alluberes.com takes priority; no fixed deadline) | High | Medium | **Medium** | Abnel Lluberes | Track hours weekly; keep documentation current so work can resume quickly | Use the contingency buffer; move features to v0.2; move the forecast dates |
| R009 | Requirements misalignment; built features don't meet expectations | Medium | Medium | **Medium** | Abnel Lluberes | Review suggest-only output on a real project before enabling apply mode | Pivot features; gather feedback for v0.2 |
| R010 | Testing reveals major architectural issues requiring refactoring | Low | High | **Medium** | Abnel Lluberes | API spike during design; prototype core flows early | Use buffer hours for refactoring |
| R011 | Slack webhook integration fails (optional feature, deferred to v0.2) | Low | Medium | **Medium** | Abnel Lluberes | Test during v0.2 | Surface alerts on the dashboard only |
| R012 | Documentation incomplete at release; operational knowledge lost | Medium | Medium | **Medium** | Abnel Lluberes | Document each module as it is built | Use buffer hours for documentation |

### Low Risks (Lower probability/impact combinations)

| ID | Risk Description | Probability | Impact | Priority | Owner | Mitigation | Contingency |
|----|------------------|-------------|--------|----------|-------|-----------|------------|
| R013 | Third-party library updates break compatibility | Low | Medium | Low | Abnel Lluberes | Pin dependency versions in requirements.txt; monitor for security issues | Quick downgrade; use fork if necessary |
| R014 | GitHub Actions CI/CD pipeline configuration issues | Low | Medium | Low | Abnel Lluberes | Simple initial setup; iterate on CI/CD post-launch | Manual deployment process; deploy from local |
| R016 | Suggestion comments create notification noise or revision churn for the team | Medium | Medium | Low | Abnel Lluberes | Post each suggestion once (hash tracked locally, hidden marker in the comment); wait for the quiet period before commenting; comments can be turned off with POST_SUGGESTION_COMMENTS | Turn comments off; keep dashboard-only suggestions |
| R017 | Field names differ between Agile, Scrum and Basic templates; rules target the wrong field | Medium | Medium | Low | Abnel Lluberes | Read available fields per work item type from the API; verify mappings in the design API spike | Skip the rule for that type and report it on the dashboard |
| R018 | Estimates were not built bottom-up and scope was added after estimating (comments, state store, three templates) | Medium | Medium | Low | Abnel Lluberes | Re-estimate at the end of design; compare actual vs. planned hours weekly in STATUS_REPORTS.md | Use the contingency buffer; move features to v0.2 |
| R015 | Work items deleted in Azure DevOps during agent processing | Low | Medium | Low | Abnel Lluberes | Implement graceful error handling; log events; alert on failures | Audit trail shows what would have been changed |

---

## 2. Risk Response Strategies

### Risk Responses by Type

**Avoid** (Eliminate risk):
- R003: Design work item linking to prevent circular dependencies

**Mitigate** (Reduce probability or impact):
- R001: Implement rate limiting & backoff (reduces both P & I)
- R007: Use conservative rule sets; manual approval (reduces impact)
- R009: Weekly demos (reduces probability of misalignment)

**Contingency** (Prepare response if occurs):
- R002: Create token refresh procedure
- R005: Implement log rotation
- R008: Use buffer hours; move features to v0.2

**Accept** (Monitor but do nothing proactive):
- R013: Library updates (low risk, small impact)
- R015: Deleted items (very low probability)

---

## 3. Contingency Reserves

**Schedule Reserve (Buffer)**:
- Size and dates: see the contingency buffer in PROJECT_SCHEDULE.md (about 20% of planned effort)
- Used for: reduced weekly hours, technical blockers, testing overruns

**Budget Reserve**: Not applicable (solo project, open-source stack, no paid infrastructure)

---

## 4. Risk Monitoring Plan

**Monitoring Activities**:
- Weekly risk check during the Sunday status check
- Dashboard indicator: Red/Yellow/Green by probability & impact
- Any Critical risk that occurs triggers a re-plan

**Trigger Events** (require immediate review):
- Any API failure
- Token expiration
- Test failure rate >10%
- Actual hours differ from plan by more than 10 (update the forecast)
- Scope change requests

**Risk Review Schedule**:
- Initial: at the planning baseline
- Regular: Every Sunday (weekly status check)
- Immediately when a trigger event occurs

---

## 5. Risk Owner Responsibilities

The risk owner (Abnel Lluberes) will:
1. Review all risks weekly
2. Update status: Green (controlled), Yellow (elevated), Red (critical)
3. Re-plan if a Critical risk occurs
4. Track actual vs. planned impact
5. Update contingency plans as needed

---

## 6. Post-Project Risk Review

Post-implementation review will:
- Document which risks actually occurred
- Analyze mitigation effectiveness
- Conduct lessons learned session

---

**Approved By**: _________________ Date: _________

**Next Review Date**: At the planning baseline (see PROJECT_SCHEDULE.md)

**Notes**:
- This register is a living document; update weekly
- Use color coding: Green (✓), Yellow (⚠), Red (🔴)
- Cross-reference with PROJECT_PLAN.md for task-level risks
- Re-plan immediately if a Red (Critical) risk occurs
