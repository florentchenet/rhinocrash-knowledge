---
tags: [setup, gemini, tooling, requirements]
date: 2025-12-09
source: Gemini 2.0 Flash (delegated query)
status: SPECIFICATION
priority: HIGH
---

# Gemini's Tooling Requirements

**Context**: Asked Gemini for detailed, specific tooling needs for full workspace.

**His response**: Production-grade, professional setup ✅

---

## Development Tools

### IDEs/Editors

**VS Code** with extensions:
- Python (Microsoft)
- JavaScript (ES6) code snippets
- Prettier - Code formatter
- ESLint
- Docker
- Remote - SSH
- GitHub Pull Requests and Issues
- vscode-pets

**Cursor**: For pair programming + AI assistance

---

### Linters & Formatters

**Python**:
```bash
pip install \
  pylint==2.17.* \
  black==23.* \
  ruff  # latest
```

**JavaScript/TypeScript**:
```bash
npm install -D \
  eslint@8 \
  prettier@2
```

---

### Debuggers

**Python**:
- `ipdb` (latest) - Interactive debugging

**JavaScript**:
- Chrome DevTools (VS Code integrated)
- Node.js debugging via VS Code

---

### Build Tools

**Python**:
```bash
pip install setuptools wheel
# GNU Make for automation
```

**JavaScript**:
```bash
npm install -D \
  pnpm \
  vite
```

**Docker**:
- docker (latest)
- docker-compose (latest)
- buildx

---

## Testing Frameworks

### Unit Testing

**Python**:
```bash
pip install \
  pytest==7.* \
  pytest-cov==4.*
```

**JavaScript**:
```bash
npm install -D \
  jest@29 \
  # OR
  vitest  # latest, faster
```

### Integration Testing

```bash
npm install -D playwright
```

### Coverage

**Python**: `coverage.py`
**JavaScript**: Istanbul (built into Jest/Vitest)

### Mocking

**Python**: `unittest.mock` (stdlib), `pytest-mock`
**JavaScript**: `jest.fn()` (built-in)

---

## Code Analysis

### Static Analysis

**Python**:
```bash
pip install \
  mypy==1.* \
  flake8
```

**TypeScript**:
```bash
npm install -D \
  eslint \
  typescript
```

### Security

**Python**:
```bash
pip install \
  bandit \
  safety
```

**JavaScript**:
```bash
npm install -D snyk
```

### Performance

**Python**:
```bash
pip install py-spy
# Also: cProfile (stdlib)
```

**JavaScript**:
- Chrome DevTools performance profiler
- `clinic.js`

### Complexity

**Python**:
```bash
pip install radon
```

---

## Version Control Plugins

### Git Tools

```bash
# Better diff output
cargo install git-delta

# Terminal UI for Git
cargo install lazygit
```

### Diff Viewers

```bash
npm install -g diff-so-fancy
```

### Commit Helpers

```bash
npm install -g \
  commitizen \
  cz-conventional-changelog
```

---

## Python-Specific

### Environment & Package Management

**Poetry** (recommended):
```bash
pip install poetry
```

**Config** (`pyproject.toml`):
```toml
[tool.poetry]
name = "my-project"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]

[tool.poetry.dependencies]
python = "^3.9"
requests = "^2.28"
fastapi = "^0.85"
uvicorn = {extras = ["standard"], version = "^0.18"}

[tool.poetry.group.dev.dependencies]
pytest = "^7.1"
pytest-cov = "^4.0"
mypy = "^0.971"
black = "^22.8"
ruff = "^0.0.272"
ipdb = "^0.13"
```

### Type Checking

```bash
pip install \
  mypy \
  pyright  # VS Code language server
```

### Jupyter Integration

VS Code Jupyter extension

---

## JavaScript/Node-Specific

### Package Manager

**pnpm** (recommended):
```bash
npm install -g pnpm
```

### Bundler

**Vite** (recommended):
```bash
pnpm add -D vite
```

### TypeScript

```bash
pnpm add -D \
  typescript \
  ts-node
```

---

## Infrastructure Tools

### Docker

```bash
# Already installed
docker --version
docker-compose --version
```

### CI/CD

**GitHub Actions** config in `.github/workflows/`:
- Automated builds
- Tests
- Deployments

### Monitoring

**Metrics to track**:
- CPU usage
- Memory usage
- Network I/O
- Disk I/O
- Request latency
- Error rates
- Custom application metrics

**Tools**:
- Prometheus
- Grafana
- (Deployed via Docker Compose)

---

## MCP Servers

### Language Server Protocol (LSP)

**Python**: `pyright`
**TypeScript**: `typescript-language-server`

### Debug Adapter Protocol (DAP)

- Python debugger (VS Code integrated)
- Node.js debugger (VS Code integrated)

### Custom MCP Servers

None immediately needed (can build as needs arise)

---

## APIs & Services

### External APIs

- **GitHub API**: For repo management, PRs, issues
- **REST APIs**: For data retrieval (project-dependent)

### Database Access

**PostgreSQL**:
- Python: `psycopg2`
- JavaScript: `pg`

### Cloud Services

**Options** (choose one):

**AWS**:
- S3, EC2, Lambda, RDS

**Google Cloud Platform**:
- Cloud Storage, Compute Engine, Cloud Functions, Cloud SQL

**Azure**:
- Blob Storage, Virtual Machines, Functions, Azure SQL Database

---

## Gemini's Ideal Workspace

### Hardware

- **RAM**: 32GB minimum
- **Storage**: Fast SSD
- **Display**: Dual monitors
- **Peripherals**: Comfortable keyboard + mouse
- **Network**: High-speed internet

### Configuration Files

**`.pylintrc`**:
```ini
[MESSAGES CONTROL]
disable=
    C0114,  # Missing module docstring
    C0115,  # Missing class docstring
    C0116,  # Missing function docstring
    W0703,  # Catching too general exception
    R0903,  # Too few public methods
    R0913,  # Too many arguments
    R0914,  # Too many local variables
```

**`.eslintrc.js`**:
```javascript
module.exports = {
    env: {
        browser: true,
        es2021: true,
        node: true
    },
    extends: [
        "eslint:recommended",
        "plugin:@typescript-eslint/recommended",
        "airbnb-base",
        "prettier"
    ],
    parser: "@typescript-eslint/parser",
    parserOptions: {
        ecmaVersion: "latest",
        sourceType: "module"
    },
    plugins: [
        "@typescript-eslint",
        "prettier"
    ],
    rules: {
        "prettier/prettier": "error",
        "no-console": "warn",
        "import/extensions": [
            "error",
            "ignorePackages",
            {
                "js": "never",
                "ts": "never"
            }
        ]
    }
};
```

**`.prettierrc.js`**:
```javascript
module.exports = {
    semi: true,
    trailingComma: 'all',
    singleQuote: true,
    printWidth: 120,
    tabWidth: 4,
};
```

### Environment Variables

```bash
# Python
PYTHONPATH=/workspace  # Set by Poetry

# Database
DATABASE_URL=postgresql://user:pass@host:5432/db

# AWS (if using)
AWS_ACCESS_KEY_ID=xxx
AWS_SECRET_ACCESS_KEY=xxx
AWS_REGION=us-east-1

# GCP (if using)
GOOGLE_APPLICATION_CREDENTIALS=/path/to/credentials.json

# Azure (if using)
AZURE_CLIENT_ID=xxx
AZURE_CLIENT_SECRET=xxx
AZURE_TENANT_ID=xxx

# GitHub
GITHUB_TOKEN=ghp_xxx

# Node
NODE_ENV=development  # or production, test
```

### Persistent Data Storage

**Database**: PostgreSQL
- Via Docker Compose (local)
- Or managed service (cloud)

**Object Storage**:
- AWS S3
- Google Cloud Storage
- Azure Blob Storage

---

## Installation Script

### Python Setup

```bash
#!/bin/bash
# setup-gemini-python.sh

# Install Poetry
curl -sSL https://install.python-poetry.org | python3 -

# Install dependencies
poetry install

# Install dev tools globally
pip install \
  ipdb \
  black \
  ruff \
  mypy \
  pylint \
  bandit \
  safety
```

### JavaScript Setup

```bash
#!/bin/bash
# setup-gemini-js.sh

# Install pnpm
npm install -g pnpm

# Install global tools
pnpm add -g \
  commitizen \
  diff-so-fancy

# Install project dependencies
pnpm install
```

### Docker Setup

```bash
#!/bin/bash
# setup-gemini-docker.sh

# Install Docker (if not present)
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

# Install docker-compose
pip install docker-compose

# Start services
docker-compose up -d
```

---

## Usage for Implementation

### Phase 1: Minimal Setup (Immediate)

```bash
# Essential only
pip install poetry pytest black
npm install -g pnpm
```

### Phase 2: Full Development (Week 1)

```bash
# Run all setup scripts
./setup-gemini-python.sh
./setup-gemini-js.sh
```

### Phase 3: Production Ready (Week 2)

```bash
# Add monitoring, CI/CD
./setup-gemini-docker.sh
# Configure GitHub Actions
# Setup Prometheus + Grafana
```

---

## Integration with rhinocrash

### Gemini's Workspace Structure

```
/opt/rhinocrash/workspace/gemini/
├── .venv/              # Poetry virtual env
├── src/                # Source code
├── tests/              # Test files
├── .git/               # Git repository
├── pyproject.toml      # Poetry config
├── .pylintrc           # Linting config
├── .eslintrc.js        # JS linting
└── docker-compose.yml  # Services
```

### Environment Setup

```bash
# Create workspace
mkdir -p /opt/rhinocrash/workspace/gemini
cd /opt/rhinocrash/workspace/gemini

# Initialize
poetry init
git init

# Install tooling
pip install -r requirements-dev.txt
```

---

**Status**: Specifications complete. Ready for workspace provisioning.

**Next Step**: Create setup scripts and provision Gemini's workspace.
