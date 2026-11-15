# Azure DevOps Content Polishing Agent

**Status**: In Planning Phase  
**Version**: 0.1.0 (Development)  
**Last Updated**: November 15, 2026

---

## 📋 Project Overview

An automated, containerized agent that improves Azure DevOps work item documentation every 5 minutes by:
- ✨ Formatting descriptions with markdown standards
- 📝 Filling missing required fields (acceptance criteria, story points)
- 🏷️ Standardizing labels and fixing grammar/spelling
- 🔗 Creating intelligent links between related items
- 📊 Providing a local web dashboard for monitoring
- 🎯 Enabling manual triggers for immediate processing
- 📋 Maintaining a complete audit trail of all changes

---

## 🏗️ Project Structure

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
- **Architecture Design** (Coming Dec 5) - System design & component diagrams
- **API Reference** (Coming Dec 20) - Dashboard API endpoints
- **Setup Guide** (Coming Jan 1) - Installation & configuration
- **Operations Manual** (Coming Jan 1) - Deployment & troubleshooting

---

## 🚀 Quick Start

### Prerequisites
- Docker & Docker Compose
- Python 3.11+ (for local development)
- Azure DevOps Personal Access Token (PAT)

### Installation (Coming Soon)
```bash
# Clone the repository
git clone https://github.com/team.alluberes/azdo-polisher.git
cd azdo-polisher

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

| Phase | Duration | Status | Deliverables |
|-------|----------|--------|--------------|
| **Initiation** | Week 1 (Nov 15-21) | 🟡 In Progress | Charter, Stakeholder alignment |
| **Planning** | Weeks 2-3 (Nov 22-Dec 5) | 🟡 Planning | Schedule, Risks, Detailed plan |
| **Design** | Week 2-3 (Nov 22-Dec 5) | 🟠 Upcoming | Architecture, Technical specs |
| **Implementation** | Weeks 4-6 (Dec 6-20) | 🟠 Upcoming | Source code, Docker image |
| **Testing** | Week 7 (Dec 21-27) | 🟠 Upcoming | Tests, QA report |
| **Release** | Week 8 (Dec 28-Jan 3) | 🟠 Upcoming | Documentation, Deploy |

**Status Legend**: 🟢 Complete | 🟡 In Progress | 🟠 Upcoming | 🔴 On Hold

---

## 👥 Team

| Role | Name | Contact | GitHub |
|------|------|---------|--------|
| Project Manager | [Your Name] | [Email] | [@username] |
| Tech Lead | [Name] | [Email] | [@username] |
| Developer | [Name] | [Email] | [@username] |
| QA Lead | [Name] | [Email] | [@username] |
| DevOps | [Name] | [Email] | [@username] |

---

## 🤝 Contributing

This project is currently in active development. See [CONTRIBUTING.md](./CONTRIBUTING.md) for:
- Development setup
- Code style guidelines
- Pull request process
- Testing requirements
- Documentation standards

---

## 📞 Support

- **Questions?** Open an issue in GitHub
- **Bug Report?** [Report here](https://github.com/team.alluberes/azdo-polisher/issues)
- **Documentation?** See `/docs` folder or [wiki](https://github.com/team.alluberes/azdo-polisher/wiki)

---

## 📜 License

[Add your license here]

---

## 🎯 Success Metrics

- ✅ Agent runs 99% uptime
- ✅ Dashboard response <500ms
- ✅ 100% of changes audited
- ✅ Zero data loss on restart
- ✅ Complete documentation

---

**Last Updated**: November 15, 2026  
**Next Milestone**: Planning Complete (Nov 28, 2026)
