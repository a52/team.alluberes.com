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
   git clone https://github.com/team.alluberes/azdo-polisher.git
   cd azdo-polisher
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
   # Edit .env with your Azure DevOps credentials
   export $(cat .env | xargs)
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
- Use **pylint** for linting

### Formatting
```bash
# Format code with black
black src/

# Check style with pylint
pylint src/

# Auto-fix with autopep8
autopep8 --in-place --aggressive src/**/*.py
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
        filters: Dictionary of filter criteria (e.g., {'status': 'Active'})
    
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
# tests/unit/test_example.py
import pytest
from src.agent.azure_client import AzureClient

class TestAzureClient:
    @pytest.fixture
    def client(self):
        """Create a test client."""
        return AzureClient(org_url="https://dev.azure.com/test", pat="test_token")
    
    def test_connection_success(self, client):
        """Test successful connection to Azure DevOps."""
        result = client.ping()
        assert result is True
    
    def test_invalid_credentials(self):
        """Test connection with invalid credentials."""
        with pytest.raises(ConnectionError):
            client = AzureClient(org_url="https://dev.azure.com/test", pat="invalid")
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
   git add .
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

5. **Code Review**
   - Minimum 2 approvals required
   - Address review comments
   - Re-request review after changes

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
- [ ] No linting errors (`pylint`)
- [ ] Docstrings added/updated
- [ ] Documentation updated if needed
- [ ] Commit messages follow convention
- [ ] No secrets/credentials in code or logs
- [ ] No debug print statements
- [ ] New dependencies documented

---

## 📚 Documentation

### When to Update Docs
- Adding a new feature → Update API.md or feature guide
- Changing configuration → Update SETUP.md
- Architecture change → Update ARCHITECTURE.md
- Bug fix with workaround → Update TROUBLESHOOTING.md

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
pylint src/

# 6. Start agent locally
python -m src.agent.main

# 7. Access dashboard at http://localhost:5000
```

---

## 🐳 Docker Development

```bash
# Build Docker image locally
docker build -f build/docker/Dockerfile -t azdo-polisher:latest .

# Run container
docker run -e AZURE_DEVOPS_ORG=your_org \
           -e AZURE_DEVOPS_PAT=your_pat \
           -p 5000:5000 \
           azdo-polisher:latest

# Or use docker-compose
docker-compose up
```

---

## 📞 Need Help?

- **Questions?** Ask in team chat or open a discussion
- **Stuck?** Check TROUBLESHOOTING.md or existing issues
- **Documentation unclear?** Let us know!

---

## 🙏 Thank You!

Your contributions make this project better. We appreciate your effort and time!

**Review Time**: Typically 24-48 hours  
**Release Cadence**: Weekly on Mondays (if applicable)

Happy coding! 🎉
