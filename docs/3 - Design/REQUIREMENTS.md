# Requirements Specification
## Azure DevOps Content Polishing Agent – v0.1

**Document Version**: 0.3  
**Status**: Draft  
**Owner**: Abnel Lluberes  
**Related**: [Project Plan](../2%20-%20Planning/PROJECT_PLAN.md) (high-level FR/NFR) · [Project Schedule](../2%20-%20Planning/PROJECT_SCHEDULE.md) (dates and effort)

This document turns the high-level requirements in the Project Plan into testable requirements for v0.1. Every requirement has an ID so tests and design decisions can refer to it.

Marker used below:
- **(Spike)** – depends on Azure DevOps API behavior that the design API spike must verify

---

## 1. Scope

**In v0.1**: scheduled review of work items, three rule families (description formatting, missing-field flags, tag aliases), suggestions on a local dashboard and as work item comments, opt-in apply mode per rule, audit log, Docker packaging.

**Not in v0.1** (see section 9): grammar and spelling fixes, automatic linking, Slack alerts, per-item Apply button, multiple projects or organizations, cloud hosting, user accounts on the dashboard.

---

## 2. Definitions

| Term | Meaning |
|------|---------|
| **Run** (cycle) | One execution of the agent: select items, evaluate rules, deliver suggestions, apply enabled rules |
| **Rule** | A check on one work item that produces zero or more suggestions |
| **Suggestion** | A proposed improvement for one field of one work item, with the current value and, where the rule can compute it, the proposed value |
| **Suggest-only mode** | `DRY_RUN=true`. No work item field is changed. The only possible write is the agent's comment |
| **Apply mode** | `DRY_RUN=false` plus a rule's own switch. The agent writes the proposed value to the field |
| **Quiet period** | Minutes an item must be unchanged before the agent looks at it |
| **Watermark** | The `System.ChangedDate` up to which items have been processed |
| **Finished state** | A state listed in `exclude_states`; items in it are never processed |
| **State store** | Local files in `STATE_DIR`: watermark, revisions written by the agent, suggestion hashes |

---

## 3. Users and Use Cases

**Operator** (the project owner): installs, configures and runs the agent, and reviews its output.  
**Work item authors**: people whose work items are processed. They see the agent only through comments and, in apply mode, field changes.

| ID | Use case | Actor |
|----|----------|-------|
| UC-1 | Review current suggestions with before/after values on the dashboard | Operator |
| UC-2 | Read the agent's comment on a work item and fix the item | Author |
| UC-3 | Trigger a run immediately instead of waiting for the next interval | Operator |
| UC-4 | Turn on apply mode for one rule after reviewing its suggestions | Operator |
| UC-5 | Find out exactly what the agent proposed or changed on an item, and when | Operator |
| UC-6 | See that the agent is healthy, or why it is failing | Operator |

---

## 4. Functional Requirements

### 4.1 Scheduling (RUN)

| ID | Requirement |
|----|-------------|
| RUN-1 | The agent starts a run every `SCHEDULE_INTERVAL_MINUTES` (default 5), within ±30 seconds. |
| RUN-2 | Runs never overlap. If a run is still in progress when the next is due, the next one is skipped and the skip is logged. |
| RUN-3 | The operator can trigger a run from the dashboard. If a run is in progress, the request is rejected with a clear message. |
| RUN-4 | With `RUN_ON_STARTUP=true`, a run starts as soon as the agent has started. |
| RUN-5 | A run processes at most `RATE_LIMIT_PER_CYCLE` items, oldest change first. Remaining items are picked up by later runs. |
| RUN-6 | Each run records: start time, end time, trigger (schedule or manual), items selected, items skipped with reason, suggestions created, changes applied, errors. |

### 4.2 Work Item Selection (SEL)

| ID | Requirement |
|----|-------------|
| SEL-1 | Only items in `AZURE_DEVOPS_PROJECT` are selected. |
| SEL-2 | Only items whose type is in `WORK_ITEM_TYPES` are selected. Types that do not exist in the project are ignored without error. |
| SEL-3 | Items whose state is in `exclude_states` are not selected. |
| SEL-4 | If `area_path` is set, only items in that area path or below it are selected. |
| SEL-5 | Only items with `System.ChangedDate` later than the watermark are selected. |
| SEL-6 | Items changed within the last `QUIET_PERIOD_MINUTES` are not selected, and the watermark never advances past (now − quiet period), so those items are picked up by a later run. |
| SEL-7 | The watermark advances to the `ChangedDate` of the last item fully processed in the run. If the run fails part-way, items not yet processed are selected again next time. |
| SEL-8 | An item whose current revision equals the revision recorded for the agent's own last write or comment is skipped (loop guard). |
| SEL-9 | On first start with an empty state store, the watermark starts at the current time, so only items changed after the agent starts are processed. `INITIAL_LOOKBACK_DAYS` (default 0) can move the starting point back. |

### 4.3 Process Templates (TPL)

| ID | Requirement |
|----|-------------|
| TPL-1 | The agent works on projects using the Agile, Scrum or Basic process. |
| TPL-2 | At startup, and then once per day, the agent reads the fields available for each configured work item type from the API. |
| TPL-3 | A rule is skipped for a work item type when the field it needs does not exist for that type. The skip is shown on the dashboard once per type, not per item. |
| TPL-4 | Field mapping (reference names to be confirmed by the spike): |

| Purpose | Agile | Scrum | Basic |
|---------|-------|-------|-------|
| Requirement-level type | User Story | Product Backlog Item | Issue |
| Description | `System.Description` | `System.Description` | `System.Description` |
| Description for Bugs | `Microsoft.VSTS.TCM.ReproSteps` | `Microsoft.VSTS.TCM.ReproSteps` | n/a (no Bug type) **(Spike)** |
| Acceptance criteria | `Microsoft.VSTS.Common.AcceptanceCriteria` | `Microsoft.VSTS.Common.AcceptanceCriteria` | none **(Spike)** |
| Estimate | `Microsoft.VSTS.Scheduling.StoryPoints` | `Microsoft.VSTS.Scheduling.Effort` | **(Spike)** |
| Tags | `System.Tags` | `System.Tags` | `System.Tags` |

### 4.4 Rule: Description Formatting (FMT)

The rule works on the HTML of the description field (TPL-4). In v0.1 it only removes surplus blank space. It never changes the author's words and never converts HTML to markdown.

| ID | Requirement |
|----|-------------|
| FMT-1 | Remove empty paragraphs and line breaks at the start and end of the description. |
| FMT-2 | Collapse three or more consecutive empty paragraphs or line breaks into one. |
| FMT-3 | Never modify images, tables, links, @mentions, #work-item references or code blocks. If the description contains markup the rule does not recognise, the rule produces no suggestion for that item. |
| FMT-4 | The visible text before and after must be identical apart from whitespace. If it is not, the suggestion is discarded and the discrepancy is logged as an error. |
| FMT-5 | The rule is idempotent: running it on its own output produces no further suggestion. |
| FMT-6 | A formatting suggestion contains the full before and after HTML. |

### 4.5 Rule: Missing Fields (MISS)

Missing fields are only ever flagged. The agent never writes a value into them.

| ID | Requirement |
|----|-------------|
| MISS-1 | Flag an empty description on every configured type. Empty means no visible text after removing HTML tags and whitespace. |
| MISS-2 | Flag empty acceptance criteria on requirement-level types (User Story, Product Backlog Item), Bugs and Features, wherever the field exists for the type (TPL-3). |
| MISS-3 | Flag an empty estimate on requirement-level types (User Story, Product Backlog Item, and Issue if Basic has an estimate field). |
| MISS-4 | `FLAG_MISSING_FIELDS=false` turns the whole rule off. |
| MISS-5 | When the field is filled in later, the suggestion is marked resolved on the dashboard at the next run that processes the item. |

### 4.6 Rule: Tag Aliases (TAG)

| ID | Requirement |
|----|-------------|
| TAG-1 | `TAG_ALIASES` maps an existing tag to its replacement. Matching ignores case and surrounding spaces. |
| TAG-2 | A tag found in the map produces a suggestion to replace it. If the replacement is already on the item, the suggestion is to remove the duplicate. |
| TAG-3 | Tags not in the map are never changed. |
| TAG-4 | The dashboard lists tags that differ only by case, spaces or hyphens (for example `Front End`, `frontend`, `front-end`) as alias candidates, with the number of items using each. This is a report, not a suggestion on any item. |
| TAG-5 | An invalid `TAG_ALIASES` value (not JSON, or an alias chain such as A→B and B→C) stops the agent at startup with a message naming the problem. |

### 4.7 Suggestion Delivery (SUG)

| ID | Requirement |
|----|-------------|
| SUG-1 | Every suggestion appears on the dashboard with work item ID, title, link to the item, rule, field, current value and proposed value (when there is one). |
| SUG-2 | A suggestion has a stable identity: a hash of work item ID, rule, field and proposed value (or the fact that the field is missing). |
| SUG-3 | With `POST_SUGGESTION_COMMENTS=true`, the agent keeps one comment per work item that lists all open suggestions for that item. |
| SUG-4 | When the set of open suggestions for an item changes, the agent updates its existing comment in place instead of adding a new one. If the API cannot update a comment, it posts a new comment only when the set of suggestions has changed. **(Spike)** |
| SUG-5 | The agent's comment carries a marker that identifies it as written by the agent and includes the hash of its suggestion set. The agent uses the marker to find its own comment when the state store has been lost. The marker is hidden if the API preserves hidden markup, otherwise it is a short visible footer. **(Spike)** |
| SUG-6 | The comment states that it was generated automatically, lists each suggestion in one line, and says how to stop the comments (contact the operator). |
| SUG-7 | When all suggestions for an item are resolved, the agent updates its comment to say so and makes no further comment until a new suggestion appears. |
| SUG-8 | Comment text is in English. |
| SUG-9 | After posting or updating a comment, the agent records the item's resulting revision for the loop guard (SEL-8). |

### 4.8 Apply Mode (APP)

| ID | Requirement |
|----|-------------|
| APP-1 | With `DRY_RUN=true`, no work item field is ever written, whatever the other settings are. |
| APP-2 | With `DRY_RUN=false`, formatting suggestions are applied only if `APPLY_FORMATTING=true`, and tag suggestions only if `APPLY_TAG_ALIASES=true`. |
| APP-3 | Missing-field suggestions are never applied. |
| APP-4 | Each update is sent with a `test` operation on the revision that was read. If the update is rejected because the item changed, nothing is written and the item is evaluated again in a later run. |
| APP-5 | The audit entry for a change is written and flushed to disk before the update is sent. After the update, the entry is completed with the result (applied, rejected, failed) and the new revision. |
| APP-6 | All applied changes for one work item in one run are sent in a single update. |
| APP-7 | After a successful update, the agent records the item's new revision for the loop guard (SEL-8). |
| APP-8 | An applied change is not listed in the agent's comment. It appears on the dashboard and in the audit log. |

### 4.9 Audit Log (AUD)

| ID | Requirement |
|----|-------------|
| AUD-1 | Every suggestion created, every comment posted or updated, and every change applied, rejected or failed produces an audit entry. |
| AUD-2 | An entry contains: timestamp (UTC, ISO 8601), run ID, work item ID, revision read, rule, field, action, before value, after value, result, and the new revision when there is one. |
| AUD-3 | Entries are appended to files as one JSON object per line. Existing entries are never modified, except for completing the result of a pending change (APP-5). |
| AUD-4 | A new audit file is started each day. Files older than `AUDIT_RETENTION_DAYS` (default 365) are compressed, not deleted. |
| AUD-5 | The audit log never contains the PAT or any other credential. |

### 4.10 Dashboard (DASH)

| ID | Requirement |
|----|-------------|
| DASH-1 | The dashboard is a single web page served on `DASHBOARD_HOST`:`DASHBOARD_PORT`, with no login. It is intended for localhost only. |
| DASH-2 | Status area: agent state (idle, running, failing), current mode (suggest-only, or which rules are applied), last run time and result, next scheduled run. |
| DASH-3 | Suggestions list: open suggestions (SUG-1), filterable by rule and work item type, newest first, paged at 50 per page. |
| DASH-4 | Recent changes: the last 100 applied or rejected changes with before and after values. |
| DASH-5 | Run history: the last 50 runs with the counts from RUN-6. |
| DASH-6 | Problems area: current errors (for example authentication failure), rules skipped per type (TPL-3), and tag alias candidates (TAG-4). |
| DASH-7 | A "Run now" button triggers a run (RUN-3). The request is a POST carrying a CSRF token issued with the page; requests without a valid token are rejected. |
| DASH-8 | The same data is available as JSON under `/api/` (status, suggestions, changes, runs) for the page's own use. All endpoints other than "run now" are read-only. |
| DASH-9 | The dashboard never shows the PAT. It may show the organization and project names. |
| DASH-10 | With `ENABLE_DASHBOARD=false`, no web server is started. |

### 4.11 Configuration (CFG)

| ID | Requirement |
|----|-------------|
| CFG-1 | All configuration comes from environment variables, optionally loaded from a `.env` file. `.env.example` documents every variable. |
| CFG-2 | Configuration is validated at startup. A missing required value or an invalid value stops the agent with a message naming the variable and the problem. The PAT value is never printed. |
| CFG-3 | Required: `AZURE_DEVOPS_ORG`, `AZURE_DEVOPS_PAT`, `AZURE_DEVOPS_PROJECT`. Everything else has a default. |
| CFG-4 | At startup the agent verifies it can reach the project with the PAT and logs the effective configuration without secrets. |
| CFG-5 | Configuration is read once at startup. Changes take effect after a restart. |

### 4.12 Error Handling (ERR)

| ID | Requirement |
|----|-------------|
| ERR-1 | A failed API call is retried up to `MAX_RETRIES` times with exponential backoff. A `Retry-After` header from the API takes precedence over the computed delay. |
| ERR-2 | An error on one work item does not stop the run. The item is logged, counted as an error, and tried again in a later run. |
| ERR-3 | An authentication or permission failure (expired or revoked PAT) stops the run, puts the agent in the failing state, and shows the cause on the dashboard. The agent keeps running and tries again at the next interval. |
| ERR-4 | If the API is unreachable, the run ends without advancing the watermark. |
| ERR-5 | An item deleted or moved out of the project between selection and processing is skipped without error. |
| ERR-6 | Every error is logged with the run ID, the work item ID when there is one, and the API response status. |

### 4.13 Persistence and Restart (STATE)

| ID | Requirement |
|----|-------------|
| STATE-1 | The state store holds: the watermark, the last revision written by the agent per work item, the suggestion-set hash and comment ID per work item, and the list of open suggestions. |
| STATE-2 | State files are written atomically (write to a temporary file, then rename), so a crash never leaves a half-written file. |
| STATE-3 | After a restart the agent continues from the stored watermark and posts no duplicate comments. |
| STATE-4 | If the state store is missing or unreadable, the agent starts as on first start (SEL-9) and uses the comment marker (SUG-5) to avoid duplicate comments. An unreadable store is reported on the dashboard. |
| STATE-5 | In Docker, `STATE_DIR`, `LOG_DIR` and the audit log directory are volumes, so they survive container replacement. |

---

## 5. Non-Functional Requirements

| ID | Requirement | How it is checked |
|----|-------------|-------------------|
| NFR-PERF-1 | Dashboard page and API responses take less than 500 ms with 10,000 audit entries and 1,000 open suggestions. | Timed test with generated data |
| NFR-PERF-2 | A run over 50 items finishes in under 5 minutes, excluding time spent waiting on `Retry-After`. | Integration test |
| NFR-REL-1 | The agent runs 7 days unattended without a crash. | Soak test |
| NFR-REL-2 | Killing the container at any point loses no audit entry that was already written and leaves the state store readable. | Kill-and-restart test |
| NFR-SEC-1 | The PAT appears in no log, audit entry, dashboard response or error message. | Automated test that searches all outputs for the test PAT |
| NFR-SEC-2 | The PAT needs only the Work Items (Read & Write) scope. | Documented; verified in the spike |
| NFR-SEC-3 | In Docker the dashboard port is published on 127.0.0.1 only. | Review of docker-compose.yml |
| NFR-SEC-4 | Work item content is treated as untrusted: it is escaped when shown on the dashboard. | Test with HTML and script content in a title |
| NFR-MNT-1 | Unit and integration tests cover more than 80% of `src/`. | CI coverage gate |
| NFR-MNT-2 | Public functions have type hints and docstrings; black, ruff, mypy, bandit and pip-audit pass in CI. | CI |
| NFR-PORT-1 | Runs on Python 3.11 or later and as a single Docker container on macOS and Linux. | CI and manual test |

---

## 6. Acceptance Tests for v0.1

The release is accepted when these scenarios pass against a test project for each of Agile, Scrum and Basic:

1. **Suggest-only writes no field**: with default settings, after a run over items with formatting problems, missing fields and aliased tags, no field of any work item has changed, and each item has exactly one agent comment.
2. **No repeat comments**: a second and third run with no item changes post nothing and select nothing.
3. **Quiet period**: an item edited 5 minutes ago is not processed; the same item is processed once it has been unchanged for the quiet period.
4. **Concurrent edit**: in apply mode, an item edited by a person between the agent's read and its write is not overwritten, and the change is recorded as rejected.
5. **Per-rule apply**: with `DRY_RUN=false` and only `APPLY_TAG_ALIASES=true`, tags are changed and descriptions are not.
6. **Restart**: the container is killed mid-run and restarted; no audit entry is lost, no comment is duplicated, and processing continues.
7. **Lost state**: the state store is deleted; after restart no duplicate comments appear.
8. **Expired PAT**: with an invalid PAT the dashboard shows the failing state and the cause, and no PAT value appears anywhere.
9. **Missing field per template**: the estimate rule runs on User Story and Product Backlog Item, the acceptance criteria rule also runs on Bugs and Features, and each is skipped and reported where the field does not exist.
10. **First start**: with an empty state store and default settings, items last changed before the agent started receive no comment.

---

## 7. To Verify in the API Spike

| # | Question | Affects |
|---|----------|---------|
| S-1 | Does adding or updating a comment change the item's revision and `ChangedDate`? | SEL-8, SUG-9 |
| S-2 | Can the agent update its own comment in place, and does an update notify followers? | SUG-4 |
| S-3 | Does the comments API preserve hidden markup (for example an HTML comment) for the marker? | SUG-5 |
| S-4 | Exact field reference names and which types have them, in Agile, Scrum and Basic. Does Basic have an estimate field or a Bug type? | TPL-4, MISS-2, MISS-3 |
| S-5 | Does a WIQL query compare `ChangedDate` with time precision, or only by date? | SEL-5, SEL-6 |
| S-6 | Does the `test` operation on `/rev` reject the update as expected, and with which status code? | APP-4 |
| S-7 | How are tags returned and written (separator, case handling)? | TAG-1, TAG-2 |
| S-8 | What do the rate-limit responses look like (status, `Retry-After`)? | ERR-1 |
| S-9 | Is the beta `azure-devops` SDK sufficient for all of the above, or are plain REST calls simpler? | Architecture |
| S-10 | Minimum PAT scope that allows reading items, updating fields and posting comments. | NFR-SEC-2 |

---

## 8. Traceability to the Project Plan

| Project Plan | This document |
|--------------|---------------|
| FR-1 Scheduled Processing | RUN, SEL |
| FR-2 Content Polishing | FMT, MISS, TAG, APP |
| FR-2a Process Template Support | TPL |
| FR-2b Suggestion Delivery | SUG (refined: one comment per item, updated in place) |
| FR-3 Monitoring Dashboard | DASH |
| FR-4 Audit Logging | AUD |
| FR-5 Error Handling | ERR |
| FR-6 Safe Writes | SEL-5 to SEL-8, APP-4 to APP-7, STATE |
| NFR-1 to NFR-5 | Section 5 |

---

## 9. Out of Scope for v0.1

- Grammar and spelling fixes
- Converting plain-text lines into real lists, and removing pasted font and color styling
- Comment text in languages other than English
- Automatic linking of related items
- Slack or email alerts
- Per-item Apply button on the dashboard
- Rules on fields other than description, acceptance criteria, estimate and tags
- More than one project or organization per agent instance
- Authentication on the dashboard, or exposing it beyond localhost
- Cloud deployment
- Changing configuration without a restart

---

## 10. Decisions

All decisions below were made by the owner. The only open points are the spike questions in section 7.

| # | Decision | Outcome | Requirement |
|---|----------|---------|-------------|
| D-1 | Which formatting changes the agent may suggest | Only trimming empty start/end and collapsing blank gaps; list conversion and style removal deferred | FMT-1, FMT-2 |
| D-2 | Which missing fields are flagged, and on which types | Description on all types; acceptance criteria on stories, backlog items, Bugs and Features; estimate on stories and backlog items | MISS-1 to MISS-3 |
| D-3 | Comment language | English | SUG-8 |
| D-4 | Whether applied changes are also mentioned in the comment | No; dashboard and audit log only | APP-8 |
| D-5 | How far back the first run looks | From the moment the agent starts | SEL-9 |
| D-6 | Audit retention | Compress after 365 days, never delete | AUD-4 |
| D-7 | Comment wording, and what happens when all suggestions are resolved | One line per suggestion; the comment is updated to say "resolved" | SUG-6, SUG-7 |
