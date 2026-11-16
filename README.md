# Azure DevOps Content Polishing Agent

**Status**: In Planning Phase  
**Version**: 0.1.0 (Development)  
**Last Updated**: November 15, 2026

---

## 📋 Project Overview

An automated, containerized agent that reviews Azure DevOps work items every 5 minutes and proposes (or, when enabled, applies) improvements. v0.1 is suggest-only by default:
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
- **[Archive](./docs/archive/README.md)** - Superseded documents (illustrative business case), not maintained

### Technical Documentation (In Progress)
- **Requirements** (Due Nov 29, 2026) - Detailed v0.1 requirements
- **Architecture Design** (Due Dec 6, 2026) - System design & component diagrams
- **Setup Guide** (Due Feb 21, 2027) - Installation & configuration
- **Operations Manual** (Due Feb 21, 2027) - Deployment & troubleshooting
- **API Reference** (Due Feb 28, 2027) - Dashboard API endpoints

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
docker-compose up
```

### First Run
The agent will:
1. Connect to your Azure DevOps organization
2. Fetch work items matching your configured filters
3. Apply polishing rules
4. Display results on the dashboard at `http://localhost:5000`

---

## 🏛️ Project Phases

Solo project at ~10 hours per week. See the [Project Schedule](./docs/2%20-%20Planning/PROJECT_SCHEDULE.md) for task-level dates.

| Phase | Dates | Effort | Status | Deliverables |
|-------|-------|--------|--------|--------------|
| **Initiation** | Nov 15, 2026 | – | 🟢 Complete | Approved charter |
| **Planning** | Nov 16 – Nov 22, 2026 | 4 h | 🟡 In Progress | Schedule, risks, plan baseline |
| **Design** | Nov 16 – Dec 6, 2026 | 24 h | 🟠 Upcoming | Requirements, architecture, API spike |
| **Implementation** | Nov 30, 2026 – Jan 31, 2027 | 72 h | 🟠 Upcoming | Source code + unit tests, Docker image |
| **Testing** | Feb 1 – Feb 14, 2027 | 20 h | 🟠 Upcoming | Integration & end-to-end tests |
| **Release** | Feb 15 – Feb 28, 2027 | 16 h | 🟠 Upcoming | Documentation, v0.1.0 tag |
| **Buffer** | Mar 1 – Mar 21, 2027 | 27 h | 🟠 Reserve | Target completion Mar 21, 2027 |

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

## 🎯 Success Metrics

- ✅ Agent runs 99% uptime
- ✅ Dashboard response <500ms
- ✅ 100% of changes audited
- ✅ Zero data loss on restart
- ✅ Complete documentation

---

**Last Updated**: November 15, 2026  
**Next Milestone**: Planning Baseline Set (Nov 22, 2026)
