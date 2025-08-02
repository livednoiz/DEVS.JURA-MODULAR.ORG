# 👨‍💻 Development Setup

Complete guide for setting up a development environment for **Jura Modular**.

## 🛠️ Prerequisites

### Required Software

| Tool | Version | Purpose | Installation |
|------|---------|---------|--------------|
| **Python** | 3.12+ | Backend development | [python.org](https://python.org) |
| **Node.js** | 18+ | Frontend tooling | [nodejs.org](https://nodejs.org) |
| **Git** | Latest | Version control | [git-scm.com](https://git-scm.com) |
| **VS Code** | Latest | Recommended IDE | [code.visualstudio.com](https://code.visualstudio.com) |
| **Docker** | 24+ | Containerization (optional) | [docker.com](https://docker.com) |

### VS Code Extensions (Recommended)

```json
{
  "recommendations": [
    "ms-python.python",
    "ms-python.flake8",
    "ms-python.black-formatter",
    "bradlc.vscode-tailwindcss",
    "angular.ng-template",
    "ms-vscode.vscode-typescript-next",
    "esbenp.prettier-vscode",
    "ms-vscode.vscode-json",
    "redhat.vscode-yaml",
    "ms-python.pylint"
  ]
}
```

### Operating System Setup

#### Ubuntu/Debian
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Python and dependencies
sudo apt install python3.12 python3.12-venv python3.12-dev
sudo apt install build-essential libpq-dev

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs

# Install Git
sudo apt install git
```

#### macOS
```bash
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install dependencies
brew install python@3.12 node git postgresql

# Install Python tools
pip3 install --user pipx
pipx install poetry
```

#### Windows (WSL2 Recommended)
```powershell
# Install WSL2
wsl --install

# Then follow Ubuntu instructions inside WSL2
```

## 🚀 Project Setup

### 1. Clone Repository

```bash
# Clone the project
git clone https://github.com/livednoiz/JURA-MODULAR.ORG.git
cd JURA-MODULAR.ORG

# Create development branch
git checkout -b feature/your-feature-name
```

### 2. Backend Development Setup

```bash
# Navigate to backend
cd backend

# Create virtual environment
python3.12 -m venv .venv

# Activate virtual environment
source .venv/bin/activate  # Linux/macOS
.venv\Scripts\activate     # Windows

# Upgrade pip
python -m pip install --upgrade pip

# Install dependencies
pip install -r requirements.txt
pip install -r requirements-dev.txt  # Development dependencies
```

#### Development Dependencies

Create `requirements-dev.txt`:
```txt
# Testing
pytest>=7.4.0
pytest-django>=4.5.0
pytest-cov>=4.1.0
pytest-mock>=3.11.0
factory-boy>=3.3.0

# Code Quality
black>=23.7.0
flake8>=6.0.0
isort>=5.12.0
mypy>=1.5.0
pre-commit>=3.3.0

# Development Tools
django-debug-toolbar>=4.2.0
django-extensions>=3.2.0
ipython>=8.14.0
django-silk>=5.0.0

# Documentation
sphinx>=7.1.0
sphinx-rtd-theme>=1.3.0
```

### 3. Environment Configuration

```bash
# Copy environment template
cp .env.example .env

# Edit .env file
nano .env
```

#### `.env` Configuration

```bash
# Debug Settings
DJANGO_DEBUG=True
DJANGO_SECRET_KEY=your-super-secret-development-key-here

# Database
DJANGO_DATABASE_URL=sqlite:///db.sqlite3
# DJANGO_DATABASE_URL=postgresql://user:pass@localhost:5432/jura_modular

# Redis (optional for development)
REDIS_URL=redis://localhost:6379/0

# Email (for development)
EMAIL_BACKEND=django.core.mail.backends.console.EmailBackend

# Static/Media Files
DJANGO_STATIC_URL=/static/
DJANGO_MEDIA_URL=/media/

# Security (development only)
DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1,0.0.0.0
CORS_ALLOWED_ORIGINS=http://localhost:4200,http://127.0.0.1:4200

# Logging
DJANGO_LOG_LEVEL=DEBUG

# 2FA Settings (development)
OTP_TOTP_ISSUER=JuraModular-Dev
```

### 4. Database Setup

```bash
# Apply migrations
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Load sample data (optional)
python manage.py loaddata fixtures/dev_data.json
```

#### Sample Data Fixture

Create `fixtures/dev_data.json`:
```json
[
  {
    "model": "users.user",
    "pk": 2,
    "fields": {
      "username": "lawyer1",
      "email": "lawyer@example.com",
      "first_name": "Max",
      "last_name": "Mustermann",
      "role": "lawyer",
      "is_active": true,
      "date_joined": "2025-08-01T10:00:00Z"
    }
  },
  {
    "model": "users.user", 
    "pk": 3,
    "fields": {
      "username": "client1",
      "email": "client@example.com",
      "first_name": "Anna",
      "last_name": "Schmidt",
      "role": "client",
      "is_active": true,
      "date_joined": "2025-08-01T10:00:00Z"
    }
  }
]
```

### 5. Frontend Development Setup

```bash
# Navigate to frontend
cd ../frontend

# Install Angular CLI globally
npm install -g @angular/cli

# Install dependencies
npm install

# Start development server
ng serve
```

#### Frontend Environment

Create `src/environments/environment.ts`:
```typescript
export const environment = {
  production: false,
  apiUrl: 'http://127.0.0.1:8000/api',
  websocketUrl: 'ws://127.0.0.1:8000/ws',
  
  // Feature Flags
  features: {
    twoFactorAuth: true,
    documentOcr: false,
    advancedReporting: false
  },
  
  // Debug Settings
  enableDebugInfo: true,
  logLevel: 'DEBUG'
};
```

## 🔧 Development Tools

### Code Quality Setup

#### 1. Pre-commit Hooks

```bash
# Install pre-commit
pip install pre-commit

# Install git hooks
pre-commit install
```

Create `.pre-commit-config.yaml`:
```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: https://github.com/psf/black
    rev: 23.7.0
    hooks:
      - id: black
        language_version: python3.12

  - repo: https://github.com/pycqa/isort
    rev: 5.12.0
    hooks:
      - id: isort

  - repo: https://github.com/pycqa/flake8
    rev: 6.0.0
    hooks:
      - id: flake8
        additional_dependencies: [flake8-docstrings]
```

#### 2. Code Formatting

```bash
# Format Python code
black backend/
isort backend/

# Check code style
flake8 backend/

# Type checking
mypy backend/kanzlei_apps/
```

Create `pyproject.toml`:
```toml
[tool.black]
line-length = 88
target-version = ['py312']
include = '\.pyi?$'
extend-exclude = '''
/(
  migrations
)/
'''

[tool.isort]
profile = "black"
line_length = 88
known_django = "django"
sections = ["FUTURE", "STDLIB", "DJANGO", "THIRDPARTY", "FIRSTPARTY", "LOCALFOLDER"]

[tool.mypy]
python_version = "3.12"
check_untyped_defs = true
ignore_missing_imports = true
warn_unused_ignores = true
warn_redundant_casts = true
warn_unused_configs = true
```

### Database Tools

#### Django Debug Toolbar

Add to `settings.py`:
```python
if DEBUG:
    INSTALLED_APPS += ['debug_toolbar']
    MIDDLEWARE += ['debug_toolbar.middleware.DebugToolbarMiddleware']
    INTERNAL_IPS = ['127.0.0.1']
```

#### Database Inspection

```bash
# Django shell with IPython
python manage.py shell_plus

# Database shell
python manage.py dbshell

# Show SQL for migration
python manage.py sqlmigrate accounts 0001
```

### Testing Setup

#### Pytest Configuration

Create `pytest.ini`:
```ini
[tool:pytest]
DJANGO_SETTINGS_MODULE = kanzlei_backend.settings
python_files = tests.py test_*.py *_tests.py
python_classes = Test* *Tests
python_functions = test_*
addopts = 
    --verbose
    --tb=short
    --strict-markers
    --disable-warnings
    --reuse-db
    --cov=kanzlei_apps
    --cov-report=html:htmlcov
    --cov-report=term-missing:skip-covered
    --cov-fail-under=80
testpaths = tests
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    unit: unit tests
    integration: integration tests
    e2e: end-to-end tests
```

#### Test Coverage

```bash
# Run tests with coverage
pytest tests/ --cov=kanzlei_apps

# Generate HTML coverage report
pytest tests/ --cov=kanzlei_apps --cov-report=html

# View coverage report
open htmlcov/index.html
```

#### Factory Setup

Create `tests/factories.py`:
```python
import factory
from django.contrib.auth import get_user_model
from kanzlei_apps.accounts.models import ClientProfile

User = get_user_model()

class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = User
    
    username = factory.Sequence(lambda n: f"user{n}")
    email = factory.LazyAttribute(lambda obj: f"{obj.username}@example.com")
    first_name = factory.Faker("first_name")
    last_name = factory.Faker("last_name")
    phone_number = factory.Faker("phone_number")
    role = "client"

class ClientProfileFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = ClientProfile
    
    user = factory.SubFactory(UserFactory, role="client")
    user_type = "private"
    phone_number = factory.Faker("phone_number")
    address = factory.Faker("address")
```

## 🐳 Docker Development Setup

### Docker Compose for Development

Create `docker-compose.dev.yml`:
```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: jura_modular
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app
      - ./docs:/docs
    environment:
      - DJANGO_DEBUG=True
      - DJANGO_DATABASE_URL=postgresql://postgres:postgres@db:5432/jura_modular
      - REDIS_URL=redis://redis:6379/0
    depends_on:
      - db
      - redis
    command: python manage.py runserver 0.0.0.0:8000

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    ports:
      - "4200:4200"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    command: ng serve --host 0.0.0.0

volumes:
  postgres_data:
```

### Development Dockerfile

Create `backend/Dockerfile.dev`:
```dockerfile
FROM python:3.12-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt requirements-dev.txt ./
RUN pip install -r requirements-dev.txt

# Copy application code
COPY . .

# Set environment variables
ENV PYTHONPATH=/app
ENV DJANGO_SETTINGS_MODULE=kanzlei_backend.settings

EXPOSE 8000

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

### Docker Commands

```bash
# Start development environment
docker-compose -f docker-compose.dev.yml up

# Run migrations in container
docker-compose -f docker-compose.dev.yml exec backend python manage.py migrate

# Run tests in container
docker-compose -f docker-compose.dev.yml exec backend pytest

# Access backend shell
docker-compose -f docker-compose.dev.yml exec backend python manage.py shell

# View logs
docker-compose -f docker-compose.dev.yml logs -f backend
```

## 📊 Development Workflow

### Daily Development Routine

1. **Start Development Environment**
   ```bash
   # Activate virtual environment
   source backend/.venv/bin/activate
   
   # Start services
   python backend/manage.py runserver &
   cd frontend && ng serve &
   ```

2. **Before Coding**
   ```bash
   # Pull latest changes
   git pull origin main
   
   # Run tests
   pytest tests/
   
   # Check code quality
   pre-commit run --all-files
   ```

3. **During Development**
   ```bash
   # Create new migration
   python manage.py makemigrations
   
   # Run tests for changed code
   pytest tests/accounts/ -v
   
   # Check specific code
   flake8 kanzlei_apps/accounts/
   ```

4. **Before Committing**
   ```bash
   # Format code
   black .
   isort .
   
   # Run full test suite
   pytest
   
   # Check coverage
   pytest --cov=kanzlei_apps --cov-report=term-missing
   ```

### Git Workflow

```bash
# Create feature branch
git checkout -b feature/new-feature

# Make changes and commit
git add .
git commit -m "feat: add new feature"

# Push branch
git push origin feature/new-feature

# Create pull request via GitHub UI
```

### Debugging Tips

#### Django Debug Toolbar
- Access at `/?debug=1` when enabled
- Shows SQL queries, cache hits, signals, etc.

#### IPython Debugging
```python
# Add breakpoint in code
import ipdb; ipdb.set_trace()

# Or use Python 3.7+
breakpoint()
```

#### Logging Configuration
```python
# Add to settings.py for verbose logging
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
        },
    },
    'loggers': {
        'kanzlei_apps': {
            'handlers': ['console'],
            'level': 'DEBUG',
        },
    },
}
```

## 🔧 IDE Configuration

### VS Code Settings

Create `.vscode/settings.json`:
```json
{
  "python.defaultInterpreterPath": "./backend/.venv/bin/python",
  "python.linting.enabled": true,
  "python.linting.flake8Enabled": true,
  "python.formatting.provider": "black",
  "python.sortImports.args": ["--profile", "black"],
  
  "files.exclude": {
    "**/__pycache__": true,
    "**/node_modules": true,
    "**/.git": true,
    "**/dist": true
  },
  
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.organizeImports": true
  }
}
```

### VS Code Tasks

Create `.vscode/tasks.json`:
```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Django: Run Server",
      "type": "shell",
      "command": "${workspaceFolder}/backend/.venv/bin/python",
      "args": ["${workspaceFolder}/backend/manage.py", "runserver"],
      "group": "build",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "new"
      }
    },
    {
      "label": "Django: Run Tests",
      "type": "shell",
      "command": "${workspaceFolder}/backend/.venv/bin/python",
      "args": ["-m", "pytest", "tests/"],
      "group": "test",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "new"
      }
    }
  ]
}
```

---

Mit dieser Entwicklungsumgebung sind Sie bereit, effizient am **Jura Modular** Projekt zu arbeiten! 🚀
