# Azure DevOps Content Polishing Agent

**Status**: In Planning Phase  
**Version**: 0.1.0 (Development)  
**Last Updated**: November 15, 2026

---

## 📋 Project Overview

An automated, containerized agent that reviews Azure DevOps work items every 5 minutes and proposes improvements. v0.1 is suggest-only by default: no work item field is changed, suggestions appear on the dashboard and as one comment per item, and applying changes is opt-in per rule.
- ✨ Formatting descriptions
- 📝 Flagging missing required fields (acceptance criteria, story points)
- 🏷️ Standardizing tags
- 📊 Providing a local web dashboard for monitoring
- 🎯 Enabling manual triggers for immediate processing
- 📋 Maintaining a complete audit trail of all changes

Planned for v0.2: grammar/spelling fixes, automatic linking of related items, Slack alerts.

---

## 🏗️ Project Structure

Planned layout. Today only `docs/` exists; the other folders are created during implementation.

```
project-root/
├── docs/                          # Project documentation (PMI-aligned)
│   ├── 1 - Initiation/           # Project charter & business case
│   ├── 2 - Planning/             # Schedule, risks, project plan
│   ├── 3 - Design/               # Architecture & technical specs
│   ├── 4 - Implementation/       # Deployment & operational guides
│   └── 5 - Monitoring & Controlling/  # KPIs & performance metrics
│
├── src/                          # Source code
│   ├── agent/                    # Core agent & processing logic
│   ├── dashboard/                # Flask web UI
│   ├── config/                   # Configuration management
│   └── shared/                   # Shared utilities
│
├── build/                        # Build & deployment
│   ├── docker/                   # Dockerfile & docker-compose
│   └── ci-cd/                    # GitHub Actions workflows
│
├── releases/                     # Release artifacts
│   └── v0.1.0/                   # Version-specific files
│
├── assets/                       # Design assets
│   ├── diagrams/                 # Architecture & flow diagrams
│   ├── mockups/                  # UI mockups
│   └── images/                   # Screenshots
│
├── tests/                        # Test suites
│   ├── unit/                     # Unit tests
│   └── integration/              # Integration tests
│
├── .github/
│   └── workflows/                # CI/CD pipelines
│
├── requirements.txt              # Python dependencies
├── .env.example                  # Environment template
└── README.md                     # This file
```

---

## 📚 Documentation

### Project Management Documents
- **[Project Charter](./docs/1%20-%20Initiation/PROJECT_CHARTER.md)** - Authorization & business justification
- **[Project Schedule](./docs/2%20-%20Planning/PROJECT_SCHEDULE.md)** - Timeline, milestones, Gantt data
- **[Risk Register](./docs/2%20-%20Planning/RISK_REGISTER.md)** - Risk identification & mitigation strategies
- **[Project Plan](./docs/2%20-%20Planning/PROJECT_PLAN.md)** - Consolidated planning document

### Technical Documentation (In Progress)
Due dates are in the [Project Schedule](./docs/2%20-%20Planning/PROJECT_SCHEDULE.md).

- **[Requirements](./docs/3%20-%20Design/REQUIREMENTS.md)** (draft) - Detailed v0.1 requirements
- **Architecture Design** (planned) - System design & component diagrams
- **Setup Guide** (planned) - Installation & configuration
- **Operations Manual** (planned) - Deployment & troubleshooting
- **API Reference** (planned) - Dashboard API endpoints

---

## 🚀 Quick Start

### Prerequisites
- Docker & Docker Compose
- Python 3.11+ (for local development)
- Azure DevOps Personal Access Token (PAT)

### Installation (Coming Soon)
```bash
# Clone the repository
git clone https://github.com/a52/team.alluberes.com.git
cd team.alluberes.com

# Set up environment variables
cp .env.example .env
# Edit .env and add your Azure DevOps credentials

# Start the agent with Docker
docker compose up
```

### First Run
The agent will:
1. Connect to your Azure DevOps organization
2. Fetch work items matching your configured filters
3. Evaluate the polishing rules and record suggestions (no fields are changed in the default suggest-only mode)
4. Display results on the dashboard at `http://localhost:8080`

---

## 🏛️ Project Phases

Solo, part-time project with no fixed deadline. Forecast dates and effort are kept only in the [Project Schedule](./docs/2%20-%20Planning/PROJECT_SCHEDULE.md); weekly progress is tracked in a local status report that is not published.

| Phase | Status | Deliverables |
|-------|--------|--------------|
| **Initiation** | 🟢 Complete | Approved charter |
| **Planning** | 🟡 In Progress | Schedule, risks, plan baseline |
| **Design** | 🟠 Upcoming | Requirements, architecture, API spike |
| **Implementation** | 🟠 Upcoming | Source code + unit tests, Docker image |
| **Testing** | 🟠 Upcoming | Integration & end-to-end tests |
| **Release** | 🟠 Upcoming | Documentation, v0.1.0 tag |
| **Buffer** | 🟠 Reserve | Contingency included in the forecast |

**Status Legend**: 🟢 Complete | 🟡 In Progress | 🟠 Upcoming | 🔴 On Hold

---

## 👥 Team

This is a solo project.

| Role | Name | Contact | GitHub |
|------|------|---------|--------|
| Owner & Project Manager (all roles) | Abnel Lluberes | abnel@alluberes.com | [@a52](https://github.com/a52) |

---

## 🤝 Contributing

This project is in its planning phase. See [CONTRIBUTING.md](./CONTRIBUTING.md) for:
- Development setup
- Code style guidelines
- Pull request process
- Testing requirements
- Documentation standards

---

## 📞 Support

- **Questions?** Open an issue in GitHub
- **Bug Report?** [Report here](https://github.com/a52/team.alluberes.com/issues)
- **Documentation?** See `/docs` folder or [wiki](https://github.com/a52/team.alluberes.com/wiki)

---

## 📜 License

This project is licensed under the **Apache License 2.0** - see the [LICENSE](./LICENSE) file for details.

**Summary**: Free for personal and commercial use, modification, and distribution. Contributors grant users a license to any of their patents that cover their contributions, and anyone who sues claiming the project infringes a patent loses that license.

---

## 🎯 Success Targets

Targets for v0.1 (not yet measured):
- Agent runs 7 days unattended without a crash
- Dashboard response <500ms
- 100% of changes audited
- Zero data loss on restart
- Complete documentation

---

**Last Updated**: November 15, 2026  
**Next Milestone**: Planning baseline (see the [Project Schedule](./docs/2%20-%20Planning/PROJECT_SCHEDULE.md))
