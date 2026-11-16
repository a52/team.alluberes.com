# Contributing Guide
## Azure DevOps Content Polishing Agent

Thank you for contributing to this project! This guide explains how to set up your development environment, follow our standards, and submit your work.

---

## 🛠️ Development Setup

### Prerequisites
- Python 3.11+
- Docker & Docker Compose
- Git
- pip (Python package manager)

### Local Environment Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/a52/team.alluberes.com.git
   cd team.alluberes.com
   ```

2. **Create a virtual environment**
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   pip install -r requirements-dev.txt  # For development & testing
   ```

4. **Set up environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your Azure DevOps credentials (loaded automatically via python-dotenv)
   ```

5. **Verify setup**
   ```bash
   python -m pytest tests/unit --collect-only
   ```

---

## 📝 Code Style & Standards

### Python Style Guide
- Follow **PEP 8** standards
- Use **black** for code formatting
- Use **ruff** for linting and import order

### Formatting
```bash
# Format code with black
black src/

# Lint with ruff
ruff check src/ tests/
```

### Naming Conventions
- **Functions/Variables**: `snake_case`
- **Classes**: `PascalCase`
- **Constants**: `UPPER_CASE`
- **Private methods**: `_leading_underscore`

### Docstring Format
```python
def fetch_work_items(project_id: str, filters: dict) -> List[WorkItem]:
    """Fetch work items from Azure DevOps matching specified filters.
    
    Args:
        project_id: Azure DevOps project identifier
        filters: Dictionary of filter criteria (e.g., {'exclude_states': ['Closed']})
    
    Returns:
        List of WorkItem objects matching the filters
    
    Raises:
        ConnectionError: If Azure DevOps API is unreachable
        ValueError: If filters format is invalid
    """
    pass
```

---

## 🧪 Testing

### Running Tests

```bash
# Run all tests
pytest

# Run specific test file
pytest tests/unit/test_azure_client.py

# Run with coverage
pytest --cov=src --cov-report=html

# Run integration tests
pytest tests/integration/ -v

# Run specific test
pytest tests/unit/test_azure_client.py::test_connection -v
```

### Test Coverage Requirements
- **Minimum**: 80% coverage
- **Target**: >85% coverage
- Check before committing:
  ```bash
  pytest --cov=src --cov-fail-under=80
  ```

### Writing Tests

```python
# tests/unit/test_azure_client.py  (illustrative: unit tests never touch the network)
from unittest.mock import Mock

import pytest

from src.agent.azure_client import AzureClient


class TestAzureClient:
    @pytest.fixture
    def http(self):
        """Fake HTTP session injected into the client."""
        return Mock()

    @pytest.fixture
    def client(self, http):
        """Client under test, wired to the fake session."""
        return AzureClient(
            org_url="https://dev.azure.com/test", pat="test_token", session=http
        )

    def test_connection_success(self, client, http):
        """ping() returns True when the API answers 200."""
        http.get.return_value = Mock(status_code=200)
        assert client.ping() is True

    def test_invalid_credentials(self, client, http):
        """ping() raises when the API rejects the PAT."""
        http.get.return_value = Mock(status_code=401)
        with pytest.raises(ConnectionError):
            client.ping()
```

---

## 🔄 Git Workflow

### Branch Naming
- Feature: `feature/short-description` (e.g., `feature/azure-integration`)
- Bug fix: `bugfix/short-description` (e.g., `bugfix/rate-limit-handling`)
- Chore: `chore/short-description` (e.g., `chore/update-dependencies`)

### Commit Messages
Follow conventional commit format:

```
type(scope): description

Body (optional) - explain what and why, not how

Footer (optional) - issue references
```

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`

**Examples**:
```
feat(azure-client): add work item linking functionality

Implement automatic link detection and creation for related work items
based on ID references in descriptions.

Fixes #42
```

```
fix(dashboard): resolve slow response time with large audit logs

Implement log pagination and caching mechanism to improve dashboard
performance when processing >1000 items.
```

### Pull Request Process

1. **Create a branch**
   ```bash
   git checkout -b feature/my-feature
   ```

2. **Make changes** and commit regularly
   ```bash
   git add <files you changed>   # avoid `git add .` so secrets and stray files stay out
   git commit -m "feat(scope): description"
   ```

3. **Push to GitHub**
   ```bash
   git push origin feature/my-feature
   ```

4. **Open a Pull Request**
   - Title: Clear description of changes
   - Description: Why, what, how
   - Checklist: Tests, docs, formatting
   - Link any related issues

5. **Review**
   - Solo project: self-review the full diff against the checklist below
   - CI must pass before merging

6. **Merge**
   - Squash commits if many small ones
   - Delete branch after merge

---

## 📋 Pull Request Checklist

Before submitting a PR, ensure:

- [ ] Code follows PEP 8 style guide
- [ ] All tests pass (`pytest`)
- [ ] Code coverage >80% (`pytest --cov`)
- [ ] Code is formatted with `black`
- [ ] No linting errors (`ruff check`)
- [ ] Docstrings added/updated
- [ ] Documentation updated if needed
- [ ] Commit messages follow convention
- [ ] No secrets/credentials in code or logs
- [ ] No debug print statements
- [ ] New dependencies documented

---

## 📚 Documentation

### When to Update Docs
Design and operations documents are created during the project (see the schedule); update them once they exist:
- Adding a new feature → `docs/3 - Design/REQUIREMENTS.md` and the dashboard API reference
- Changing configuration → `.env.example` and the setup guide
- Architecture change → `docs/3 - Design/ARCHITECTURE.md`
- Bug fix with workaround → the operations manual

### Documentation Standards
- Clear, concise language
- Include examples where helpful
- Link to related docs
- Verify all code examples work

---

## 🐛 Bug Reporting

When reporting a bug:

1. **Check existing issues** - avoid duplicates
2. **Provide context**:
   - Python version
   - Docker version (if applicable)
   - Error message (full traceback)
   - Steps to reproduce
3. **Expected vs. actual behavior**
4. **Minimal reproduction case** (if complex)

---

## 🎯 Project Structure

```
src/
├── agent/
│   ├── main.py              # Entry point & scheduler
│   ├── azure_client.py      # Azure DevOps API wrapper
│   ├── work_item_processor.py  # Polishing logic
│   ├── filters.py           # Work item filtering
│   └── audit_logger.py      # Change tracking
├── dashboard/
│   ├── app.py               # Flask application
│   ├── routes.py            # API endpoints
│   └── static/
└── config/
    └── config.py            # Configuration management

tests/
├── unit/                    # Unit tests (fast, isolated)
└── integration/             # Integration tests (with real services)
```

---

## 🚀 Local Development Workflow

```bash
# 1. Start virtual environment
source venv/bin/activate

# 2. Make changes to code
# Edit files in src/

# 3. Run tests
pytest

# 4. Format code
black src/

# 5. Check linting
ruff check src/ tests/

# 6. Start agent locally
python -m src.agent.main

# 7. Access dashboard at http://localhost:8080
```

---

## 🐳 Docker Development

```bash
# Build Docker image locally
docker build -f build/docker/Dockerfile -t content-polisher:latest .

# Run container
docker run -e AZURE_DEVOPS_ORG=your_org \
           -e AZURE_DEVOPS_PAT=your_pat \
           -p 127.0.0.1:8080:8080 \
           content-polisher:latest

# Or use Docker Compose
docker compose up
```

---

## 📞 Need Help?

- **Questions?** Open an issue
- **Stuck?** Check existing issues
- **Documentation unclear?** Let us know!

---

## 🙏 Thank You!

Your contributions make this project better. We appreciate your effort and time!

**Release Plan**: see the [Project Schedule](./docs/2%20-%20Planning/PROJECT_SCHEDULE.md)

Happy coding! 🎉
