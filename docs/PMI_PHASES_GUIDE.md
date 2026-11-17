# PMI Project Management Phases Guide
## Azure DevOps Content Polishing Agent

This document explains how this project applies the Project Management Institute (PMI) process groups and how to navigate the documentation structure.

PMBOK defines five process groups: Initiating, Planning, Executing, Monitoring & Controlling, and Closing. This project adapts them into five phases: Design is added as a project-specific phase before execution, and Closing documents (lessons learned, sign-off) live in the `5 - Monitoring & Controlling/` folder.

**Dates and effort are kept only in [PROJECT_SCHEDULE.md](./2%20-%20Planning/PROJECT_SCHEDULE.md).** This guide shows phase order and status only.

---

## 📊 Project Lifecycle (5 Phases, adapted from PMI)

```
Phase 1: INITIATION          Phase 2: PLANNING          Phase 3: DESIGN/EXECUTION
    ↓                             ↓                               ↓
    │ Authorize                   │ Plan & Schedule              │ Build & Execute
    │ Define Project              │ Identify Risks              │ Monitor & Control
    │ Get Stakeholder Buy-in      │ Allocate Resources          │ Make Adjustments
    │                             │ Baseline Scope              │ Deliver Work
    │                             │                             │
    └─────────────────────────────┴─────────────────────────────┴──────────────────→
                                                                    
                    Phase 4: MONITORING & CONTROLLING     Phase 5: CLOSURE
                                    ↓                              ↓
                         Track Progress vs. Plan          Transition to Operations
                         Manage Changes                   Final Documentation
                         Control Risks                    Lessons Learned
                         Ensure Quality                   Project Sign-off
```

---

## 🗂️ Documentation Folder Structure

Your project follows this PMI-aligned organization:

```
docs/
│
├── 1 - Initiation/                    ✅ INITIATE THE PROJECT
│   └── PROJECT_CHARTER.md             
│       • Business case & justification
│       • Project goals & constraints
│       • High-level requirements
│       • Stakeholder register
│       • Authorization & sign-off
│
├── 2 - Planning/                      ✅ PLAN THE PROJECT
│   ├── PROJECT_SCHEDULE.md            
│   │   • Detailed timeline (all 5 phases)
│   │   • Milestones & critical path
│   │   • Resource allocation
│   │   • Contingency buffer
│   │
│   ├── RISK_REGISTER.md               
│   │   • Risk identification
│   │   • Probability/Impact matrix
│   │   • Mitigation strategies
│   │   • Contingency plans
│   │
│   └── PROJECT_PLAN.md                
│       • Consolidated management plan
│       • Scope, objectives, requirements
│       • Quality & communication plans
│       • Change management process
│       • Stakeholder communication matrix
│
├── 3 - Design/                        📝 DESIGN THE SOLUTION (Upcoming)
│   ├── ARCHITECTURE.md                
│   │   • System design & components
│   │   • Data flow diagrams
│   │   • Technology decisions
│   │
│   ├── REQUIREMENTS.md                
│   │   • Detailed functional specs
│   │   • Non-functional requirements
│   │   • Use cases & user stories
│   │   • Acceptance criteria
│   │
│   └── TECHNICAL_SPECIFICATIONS.md    
│       • API specifications
│       • Database design
│       • Security considerations
│
├── 4 - Implementation/                📝 EXECUTE & MONITOR (Upcoming)
│   ├── DEPLOYMENT_GUIDE.md            
│   │   • Setup procedures
│   │   • Configuration steps
│   │   • Environment setup
│   │
│   ├── OPERATIONS_MANUAL.md           
│   │   • Day-to-day operations
│   │   • Troubleshooting guide
│   │   • Maintenance procedures
│   │
│   └── BUILD_LOG.md                   
│       • Progress tracking
│       • Issues & resolutions
│       • Change log
│
└── 5 - Monitoring & Controlling/     📝 MONITOR & CLOSE (Upcoming)
    ├── PERFORMANCE_METRICS.md         
    │   • KPI tracking
    │   • Quality metrics
    │   • Schedule & budget status
    │
    ├── STATUS_REPORTS.md              (local only, not in git)
    │   • Weekly/monthly updates
    │   • Risk status
    │   • Change tracking
    │
    └── LESSONS_LEARNED.md             
        • What went well
        • What could improve
        • Recommendations for future projects
```

---

## 📅 Phase Overview

### Phase 1: Initiation
**Status**: ✅ Complete (Charter approved Nov 15, 2026)  
**Focus**: Authorization

**Documents in `1 - Initiation/`:**
- ✅ PROJECT_CHARTER.md

**Key Activities**:
- [x] Get charter approved (solo: owner is sponsor)
- [x] Obtain formal authorization

**Success Criteria**:
- Charter signed & approved

---

### Phase 2: Planning
**Status**: 🟡 In Progress (documents drafted; baseline pending)  
**Focus**: Schedule, risks, resource allocation

**Documents in `2 - Planning/`:**
- ✅ PROJECT_SCHEDULE.md
- ✅ RISK_REGISTER.md
- ✅ PROJECT_PLAN.md

**Key Activities**:
- [x] Apply safety decisions to requirements
- [ ] Review & approve all planning documents
- [ ] Establish baselines (schedule, scope, effort)
- [ ] Start weekly hours tracking

**Success Criteria**:
- All planning documents reviewed & approved
- Contingency plans in place
- Design phase ready to start

---

### Phase 3: Design
**Status**: 📝 Upcoming  
**Focus**: Architecture & detailed requirements

**Documents to create in `3 - Design/`:**
- ARCHITECTURE.md (System design, components, data flow)
- REQUIREMENTS.md (Detailed functional specs, use cases)
- TECHNICAL_SPECIFICATIONS.md (APIs, database, security)

**Key Activities**:
- [ ] Create system architecture
- [ ] Define detailed requirements
- [ ] Create design documentation
- [ ] API spike against a test Azure DevOps project
- [ ] Self-review & approval

**Success Criteria**:
- Architecture approved
- Requirements clear & unambiguous
- Ready to start coding

---

### Phase 4: Implementation & Testing
**Status**: 📝 Upcoming  
**Focus**: Development, testing

**Documents to create in `4 - Implementation/`:**
- DEPLOYMENT_GUIDE.md (Setup & configuration)
- OPERATIONS_MANUAL.md (Day-to-day operations)
- BUILD_LOG.md (Progress tracking)

**Key Activities**:
- [ ] Develop source code (src/ folder)
- [ ] Create Docker container (build/ folder)
- [ ] Unit tests with each module; integration & end-to-end tests
- [ ] Self-review & CI quality checks
- [ ] Performance testing

**Success Criteria**:
- Code complete & working
- Tests passing (>80% coverage)
- Docker image built & tested
- Ready for UAT

---

### Phase 5: Release, Monitoring & Closure
**Status**: 📝 Upcoming  
**Focus**: Documentation, release, monitoring, closure

**Documents to create in `5 - Monitoring & Controlling/`:**
- PERFORMANCE_METRICS.md (KPIs & health checks)
- STATUS_REPORTS.md (Weekly/monthly updates; local only, not in git)
- LESSONS_LEARNED.md (Post-project review)

**Key Activities**:
- [ ] v0.1.0 release
- [ ] Production deployment
- [ ] Monitor initial performance
- [ ] Document lessons learned
- [ ] Project sign-off

**Success Criteria**:
- All tests passed
- Agent running in production
- Documentation complete
- Stakeholder sign-off obtained

---

## 🧭 Navigation Quick Guide

### I need to...

**Understand what this project is about**
→ Start with `README.md` and `docs/1 - Initiation/PROJECT_CHARTER.md`

**Know the timeline & milestones**
→ See `docs/2 - Planning/PROJECT_SCHEDULE.md`

**Understand project risks**
→ See `docs/2 - Planning/RISK_REGISTER.md`

**Understand detailed requirements**
→ See `docs/2 - Planning/PROJECT_PLAN.md` (when complete, see `docs/3 - Design/REQUIREMENTS.md`)

**Understand the architecture**
→ See `docs/3 - Design/ARCHITECTURE.md` (planned)

**Set up development environment**
→ See `CONTRIBUTING.md` and `.env.example`

**Deploy or operate the agent**
→ See `docs/4 - Implementation/DEPLOYMENT_GUIDE.md` and `OPERATIONS_MANUAL.md` (planned)

**Check project status**
→ See `docs/5 - Monitoring & Controlling/STATUS_REPORTS.md` (updated weekly; local only, not in git)

---

## 📈 Project Governance

### Document Approval Chain

Each phase's documents follow this approval process:

```
Document Created (Draft)
         ↓
Self-Review (owner, against checklist)
         ↓
Approved ✅ / Rejected ❌
         ↓
Version Controlled in Git
```

### Version Control

All documents include:
- Document version (e.g., 1.0)
- Date created & updated
- Owner/author
- Approval signatures
- Status (Draft / Approved / Archived)

### Document Status Indicators

- ✅ **Complete** - Document is finished & approved
- 🟡 **In Progress** - Document being created/updated
- 📝 **Pending** - Document to be created
- 🔴 **Archived** - Old version, no longer active

---

## 🔄 Phase Transitions

Each phase must complete before moving to the next:

```
Phase 1 ✅ COMPLETE (charter approved Nov 15, 2026)
    ↓ Gate Review
Phase 2 🟡 IN PROGRESS
    ↓ Gate Review
Phase 3 📝 UPCOMING (overlaps with Phase 2)
    ↓ Gate Review
Phase 4 📝 UPCOMING
    ↓ Gate Review
Phase 5 📝 UPCOMING
    ↓ Gate Review
PROJECT CLOSURE ✅
```

**Gate Review Checklist** before proceeding:
- All documents complete?
- All deliverables completed?
- Risks mitigated?
- Hours on track vs. plan?
- Owner approval recorded?

---

## 📋 Document Checklist

### Initiation Phase (Complete ✅)
- [x] PROJECT_CHARTER.md - Business case & authorization

### Planning Phase (In Progress 🟡 – drafts done, baseline pending)
- [x] PROJECT_SCHEDULE.md - Timeline & milestones
- [x] RISK_REGISTER.md - Risk identification
- [x] PROJECT_PLAN.md - Consolidated plan

### Design Phase (Pending 📝)
- [ ] ARCHITECTURE.md - System design
- [ ] REQUIREMENTS.md - Detailed specs (draft written, review pending)
- [ ] TECHNICAL_SPECIFICATIONS.md - API & database

### Implementation Phase (Pending 📝)
- [ ] DEPLOYMENT_GUIDE.md - Setup procedures
- [ ] OPERATIONS_MANUAL.md - Day-to-day operations
- [ ] BUILD_LOG.md - Progress tracking

### Monitoring & Controlling (Pending 📝)
- [ ] PERFORMANCE_METRICS.md - KPIs
- [x] STATUS_REPORTS.md - Weekly updates (started; local only, not in git)
- [ ] LESSONS_LEARNED.md - Post-project review

---

## 🎓 PMI Standards Reference

This project follows PMI standards for:

✅ **Project Charter** - Formal authorization document (PMBOK: Initiate Processes)  
✅ **Project Management Plan** - Consolidated planning (PMBOK: Planning Processes)  
✅ **Schedule Baseline** - Integrated schedule (PMBOK: Schedule Management)  
✅ **Risk Register** - Comprehensive risk analysis (PMBOK: Risk Management)  
✅ **Stakeholder Register** - Identified & managed (PMBOK: Stakeholder Management)  
✅ **Change Management** - Formal change control process (PMBOK: Integration Management)  
✅ **Quality Plan** - Quality standards & metrics (PMBOK: Quality Management)  
✅ **Communication Plan** - Stakeholder communication (PMBOK: Communications Management)  

---

**Last Updated**: November 15, 2026  
**Version**: 1.1  
**Status**: Current
