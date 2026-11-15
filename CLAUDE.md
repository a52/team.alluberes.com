# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## 🎯 Project Overview

**Azure DevOps Content Polishing Agent** - A containerized Python agent that automatically improves Azure DevOps work item documentation every 5 minutes by:
- Formatting descriptions with markdown standards
- Filling missing required fields (acceptance criteria, story points)
- Standardizing labels and fixing grammar/spelling
- Creating intelligent links between related items
- Providing a local Flask web dashboard for monitoring
- Maintaining complete audit logs of all changes

**Status**: Planning Phase (Dec 5, 2026 - Design phase starts)  
**Target Completion**: January 3, 2027  
**Tech Stack**: Python 3.11, Flask, APScheduler, azure-devops SDK, Docker

---

## 🏗️ Architecture Overview

### Core Components

The project is organized into 4 main source modules, coordinated by an APScheduler-based task runner:

**1. Agent Service** (`src/agent/`)
- `main.py` - Entry point, scheduler initialization (5-minute intervals)
- `azure_client.py` - Azure DevOps REST API wrapper using azure-devops SDK
- `work_item_processor.py` - Core polishing logic (formatting, field filling, linking)
- `filters.py` - Work item filtering engine (status, type, area path, custom)
- `audit_logger.py` - JSON-based audit trail of all changes

**2. Web Dashboard** (`src/dashboard/`)
- `app.py` - Flask application entry point
- `routes.py` - API endpoints (last-run summary, processed items, manual trigger, status)
- `static/` - CSS/JavaScript for UI (localhost-only, no auth required)

**3. Configuration** (`src/config/`)
- `config.py` - Centralized config loading from environment variables (.env)

**4. Shared Utilities** (`src/shared/`)
- Common utilities for logging, error handling, retry logic

### Data Flow

```
Every 5 minutes:
  Scheduler triggers agent
    ↓
  Load config from env vars
    ↓
  Connect to Azure DevOps via PAT
    ↓
  Fetch work items matching filters
    ↓
  Process each item through polishing rules
    ↓
  Log all changes to audit_logs/ (JSON)
    ↓
  Dashboard reads latest results
    ↓
  Display at http://localhost:5000
```

### Key Architectural Decisions

- **Scheduled Execution**: APScheduler in-process (no external scheduler)
- **Configuration**: Environment variables only (secure, Docker-friendly)
- **Logging**: File-based JSON logs (no database)
- **Dashboard**: Flask + vanilla HTML/CSS (lightweight, no build step)
- **Containerization**: Single Dockerfile, docker-compose for local dev
- **Audit Trail**: Before/after JSON snapshots for every change

---

## 🛠️ Development Environment Setup

### Prerequisites

- Python 3.11+ (check with `python3 --version`)
- Docker & Docker Compose (check with `docker --version`)
- Git
- Azure DevOps organization with a Personal Access Token (PAT)

### One-Time Setup

```bash
# 1. Create virtual environment
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 2. Install dependencies
pip install -r requirements.txt        # Production
pip install -r requirements-dev.txt    # Dev tools (testing, linting, formatting)

# 3. Set up environment
cp .env.example .env
# Edit .env with your Azure DevOps credentials:
# - AZURE_DEVOPS_ORG: your org URL
# - AZURE_DEVOPS_PAT: your personal access token
# - AZURE_DEVOPS_PROJECT: your project name

# 4. Verify setup
pytest tests/unit --collect-only  # Should show 0 tests (not implemented yet)
```

### Activate Venv (Every Session)

```bash
source venv/bin/activate  # Linux/Mac
# or
venv\Scripts\activate     # Windows
```

---

## 🔨 Common Development Commands

### Running Code

```bash
# Run the agent directly (5-minute polling loop)
python -m src.agent.main

# Start dashboard only (for UI development)
python -m src.dashboard.app

# Run with Docker Compose (recommended for full testing)
docker-compose up --build
```

### Testing

```bash
# Run all tests
pytest

# Run specific test file
pytest tests/unit/test_azure_client.py -v

# Run with coverage report
pytest --cov=src --cov-report=html
pytest --cov=src --cov-fail-under=80  # Must pass: 80%+ coverage

# Run integration tests (requires Azure credentials)
pytest tests/integration/ -v

# Run single test
pytest tests/unit/test_azure_client.py::TestAzureClient::test_connection -v

# Watch mode (requires pytest-watch)
ptw
```

### Code Quality

```bash
# Format code with black (modifies files in-place)
black src/ tests/

# Check PEP 8 compliance with pylint
pylint src/

# Check for security issues
bandit -r src/

# Type checking
mypy src/

# All-in-one: format, lint, type check
black src/ && pylint src/ && mypy src/ && pytest --cov=src
```

### Building & Deployment

```bash
# Build Docker image locally
docker build -f build/docker/Dockerfile -t azdo-polisher:latest .

# Run Docker image locally
docker run -e AZURE_DEVOPS_ORG=https://dev.azure.com/myorg \
           -e AZURE_DEVOPS_PAT=mytoken \
           -p 5000:5000 \
           azdo-polisher:latest

# Use docker-compose (simpler)
docker-compose up              # Run locally
docker-compose down            # Stop containers
docker-compose logs -f agent   # View logs
```

---

## 📝 Code Style & Conventions

### Python Standards

- **Style Guide**: PEP 8 (enforced by black)
- **Naming**: `snake_case` for functions/vars, `PascalCase` for classes, `UPPER_CASE` for constants
- **Docstrings**: Google-style format with Args, Returns, Raises sections
- **Type Hints**: Required on all public functions
- **Line Length**: 88 characters (black default)

### Example Function Template

```python
def process_work_items(org_id: str, filters: dict) -> List[AuditEntry]:
    """Process work items and return audit trail of changes.
    
    Fetches work items matching filters, applies polishing rules,
    and logs all modifications to the audit trail.
    
    Args:
        org_id: Azure DevOps organization identifier
        filters: Dict with keys: status, work_item_types, area_path
    
    Returns:
        List of AuditEntry objects documenting all changes
    
    Raises:
        ConnectionError: If Azure DevOps API is unreachable
        ValueError: If filters are invalid
    """
```

### Error Handling

- Use specific exceptions (not bare `except:`)
- Log exceptions with full context before re-raising
- Implement exponential backoff for API retries (see requirements in PROJECT_PLAN.md)

---

## 🧪 Testing Requirements

### Minimum Coverage: 80%

- Unit tests in `tests/unit/` (fast, mocked dependencies)
- Integration tests in `tests/integration/` (slower, use real Azure SDK)
- Test files mirror source structure: `src/agent/foo.py` → `tests/unit/test_foo.py`

### Test Structure

```python
import pytest
from unittest.mock import Mock, patch
from src.agent.azure_client import AzureClient

class TestAzureClient:
    @pytest.fixture
    def client(self):
        """Set up test fixture."""
        return AzureClient(org="https://dev.azure.com/test", pat="test_token")
    
    def test_connection_success(self, client):
        """Test successful connection."""
        assert client.ping() is True
    
    def test_connection_failure_retries(self, client):
        """Test retry logic on connection failure."""
        with pytest.raises(ConnectionError):
            client.ping()
```

### Before Committing

Run this to verify all quality gates pass:

```bash
pytest --cov=src --cov-fail-under=80 && \
black src/ --check && \
pylint src/ && \
mypy src/
```

---

## 🔀 Git Workflow

### Branch Naming

- `feature/description` - New features (e.g., `feature/azure-integration`)
- `bugfix/description` - Bug fixes (e.g., `bugfix/rate-limit-handling`)
- `chore/description` - Dependencies, tooling (e.g., `chore/upgrade-flask`)
- `docs/description` - Documentation only (e.g., `docs/api-reference`)

### Commit Message Format

Follow conventional commits:

```
type(scope): short description

Longer explanation of what and why (not how).
Wrap at 72 characters.

Fixes #123
```

**Types**: `feat`, `fix`, `docs`, `test`, `chore`, `refactor`, `perf`, `style`

### Pull Request Process

1. Create branch from `main`
2. Make changes & commit with conventional messages
3. Push: `git push origin feature/my-feature`
4. Open PR with clear title & description
5. Ensure CI passes (tests, linting, coverage >80%)
6. Request reviews (minimum 2 approvals required)
7. Address feedback, re-request review
8. Squash commits if many small ones, then merge

---

## 📂 Key Project Files & Documentation

### Essential Reading

1. **README.md** - Project overview & quick start
2. **docs/PMI_PHASES_GUIDE.md** - PMI phase structure & phase navigation
3. **docs/1 - Initiation/PROJECT_CHARTER.md** - Business case & goals
4. **docs/2 - Planning/PROJECT_PLAN.md** - 22 requirements, scope, risks
5. **docs/2 - Planning/PROJECT_SCHEDULE.md** - Timeline & critical path
6. **CONTRIBUTING.md** - Detailed development guide

### Configuration Files

- **.env.example** - Environment template (60+ documented variables)
- **requirements.txt** - Production dependencies (15 packages)
- **requirements-dev.txt** - Development dependencies (25 packages)
- **.gitignore** - Python, Docker, IDE ignores

### Source Structure

```
src/
├── agent/              # 5 core agent modules
├── dashboard/          # Flask app + UI
├── config/             # Configuration loading
└── shared/             # Shared utilities

tests/
├── unit/               # Fast, isolated tests
└── integration/        # Tests with real Azure SDK

build/
├── docker/             # Dockerfile & docker-compose
└── ci-cd/              # GitHub Actions workflows
```

---

## 🎯 Upcoming Milestones

- **Dec 5, 2026**: Design phase starts (ARCHITECTURE.md, REQUIREMENTS.md)
- **Dec 6, 2026**: Implementation phase (core agent code)
- **Dec 18, 2026**: Feature complete (Docker image ready)
- **Dec 27, 2026**: Testing phase complete
- **Jan 3, 2027**: Production release

Current implementation status: Planning phase → Design phase next

---

## 🚀 Quick Start for Implementation Phase

When implementation begins (Dec 6):

1. **Create project structure**
   ```bash
   touch src/agent/__init__.py src/dashboard/__init__.py src/config/__init__.py
   ```

2. **Start with Azure client module** (`src/agent/azure_client.py`)
   - Wrap azure-devops SDK
   - Handle authentication & retries
   - Cover with unit tests

3. **Then work item processor** (`src/agent/work_item_processor.py`)
   - Implement polishing rules (see FR-2 in PROJECT_PLAN.md)
   - Comprehensive test coverage

4. **Dashboard next** (`src/dashboard/app.py`)
   - Simple Flask routes for API endpoints
   - Vanilla HTML/CSS for UI

5. **Finally integration & Docker**
   - docker-compose.yml for local dev
   - Dockerfile for containerization

---

## ⚠️ Important Notes

- **Never commit secrets**: No PATs, passwords, or API keys in code
- **Configuration via .env**: All Azure credentials come from environment
- **No print() statements**: Use logging module instead
- **Performance targets**: Dashboard <500ms, processing cycle <5 min
- **Audit logging**: Every change must be logged before/after
- **Error handling**: Implement exponential backoff for Azure API calls

---

## 📞 References

- **Azure DevOps Python SDK**: https://docs.microsoft.com/en-us/azure/devops/integrate/
- **APScheduler Docs**: https://apscheduler.readthedocs.io/
- **Flask Documentation**: https://flask.palletsprojects.com/
- **pytest Docs**: https://docs.pytest.org/
- **PEP 8 Style Guide**: https://pep8.org/

---

**Last Updated**: November 15, 2026  
**Project Phase**: Planning → Design (Dec 5)  
**Lead**: [Project Manager Name]
