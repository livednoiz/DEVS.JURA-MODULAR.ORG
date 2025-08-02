# 📚 Jura Modular - Comprehensive Documentation

Willkommen zur umfassenden Dokumentation des **Jura Modular** Kanzlei-Management-Systems.

## 📖 Table of Contents

### 🚀 [Getting Started](getting-started.md)
- [Installation Guide](getting-started.md#installation)
- [Quick Start](getting-started.md#quick-start)
- [First Steps](getting-started.md#first-steps)

### 🏗️ [Architecture](architecture.md)
- [System Overview](architecture.md#system-overview)
- [Technology Stack](architecture.md#technology-stack)
- [Database Design](architecture.md#database-design)
- [Security Architecture](architecture.md#security)

### 📋 [API Documentation](api/)
- [Authentication API](api/authentication.md)
- [Accounts API](api/accounts.md)
- [Cases API](api/cases.md)
- [Appointments API](api/appointments.md)
- [Property Management API](api/property.md)

### 👨‍💻 [Development](development/)
- [Development Setup](development/setup.md)
- [Coding Standards](development/standards.md)
- [Testing Guide](development/testing.md)
- [Contributing](development/contributing.md)

### 🚀 [Deployment](deployment/)
- [Production Setup](deployment/production.md)
- [Docker Guide](deployment/docker.md)
- [Environment Configuration](deployment/environment.md)
- [Monitoring & Logging](deployment/monitoring.md)

### 📱 [User Guides](user-guides/)
- [Admin Manual](user-guides/admin.md)
- [Lawyer Guide](user-guides/lawyer.md)
- [Client Portal](user-guides/client.md)
- [Property Manager Guide](user-guides/property-manager.md)

### 🔧 [Configuration](configuration/)
- [Settings Reference](configuration/settings.md)
- [Environment Variables](configuration/environment.md)
- [Security Configuration](configuration/security.md)
- [Performance Tuning](configuration/performance.md)

### 🐛 [Troubleshooting](troubleshooting.md)
- [Common Issues](troubleshooting.md#common-issues)
- [FAQ](troubleshooting.md#faq)
- [Error Codes](troubleshooting.md#error-codes)

---

## 🎯 Project Overview

**Jura Modular** ist ein modulares, cloudbasiertes Verwaltungssystem für Rechtsanwaltskanzleien, Hausverwaltungen und juristische Dienstleister. Es bietet:

### 🏢 **Zielgruppen**
- **Rechtsanwaltskanzleien** - Mandantenverwaltung, Fallbearbeitung, Terminplanung
- **Hausverwaltungen** - Eigentümerverwaltung, Betriebskosten, Wartung
- **Juristische Dienstleister** - Dokumentenmanagement, Workflow-Automation
- **Mandanten/Klienten** - Selbstservice-Portal, Dokumenten-Upload

### 🚀 **Core Features**
- **Benutzer- & Rollenverwaltung** mit 2FA-Sicherheit
- **Mandanten-/Fallverwaltung** mit Zeiterfassung
- **Terminplanung & Kalender-Integration**
- **Dokumentenmanagement** mit Versionierung
- **Hausverwaltung** (Eigentümer, Betriebskosten, Wartung)
- **RESTful API** für Frontend & Integrationen
- **Responsive Angular Frontend**

### 🛠️ **Technology Stack**

#### Backend
- **Framework**: Django 5.2+ (Python 3.12)
- **API**: Django REST Framework
- **Authentication**: JWT mit 2FA (TOTP)
- **Database**: SQLite (dev), PostgreSQL/MySQL (prod)
- **Security**: CORS, CSRF Protection, Rate Limiting

#### Frontend
- **Framework**: Angular 17+
- **UI Components**: Angular Material / Bootstrap
- **State Management**: NgRx (optional)
- **HTTP Client**: Angular HttpClient

#### DevOps & Deployment
- **Containerization**: Docker & Docker Compose
- **Web Server**: Nginx (reverse proxy)
- **Process Manager**: Gunicorn (production)
- **CI/CD**: GitHub Actions
- **Monitoring**: Prometheus, Grafana

### 📊 **System Statistics** (Current Status)

| Component | Status | Features | Tests | Coverage |
|-----------|---------|----------|--------|----------|
| **Accounts App** | ✅ Complete | 15+ Endpoints | 15+ Tests | 90%+ |
| **Cases App** | 🚧 Planned | - | - | - |
| **Appointments App** | 🚧 Planned | - | - | - |
| **Property App** | 🚧 Planned | - | - | - |
| **Frontend** | 🚧 Planned | - | - | - |

### 🔒 **Security Features**
- **Multi-Factor Authentication (2FA)** mit TOTP
- **JWT Token Rotation** für sichere Sessions
- **Role-Based Access Control (RBAC)**
- **Input Validation & Sanitization**
- **SQL Injection Prevention**
- **HTTPS Enforcement** (Production)
- **CORS & CSRF Protection**
- **Rate Limiting** gegen Brute-Force

### 📈 **Scalability & Performance**
- **Modular Architecture** für easy scaling
- **Database Optimization** mit Indexing
- **Caching Layer** (Redis/Memcached)
- **Background Tasks** (Celery)
- **Load Balancing** ready
- **Horizontal Scaling** support

---

## 🚀 Quick Navigation

### For Developers
- **[Development Setup](development/setup.md)** - Get started in 5 minutes
- **[API Reference](api/)** - Complete API documentation
- **[Testing Guide](development/testing.md)** - Write & run tests

### For Administrators
- **[Installation Guide](getting-started.md)** - Production deployment
- **[Configuration](configuration/)** - System configuration
- **[Monitoring](deployment/monitoring.md)** - System health & metrics

### For End Users
- **[User Guides](user-guides/)** - Feature walkthroughs
- **[FAQ](troubleshooting.md#faq)** - Common questions
- **[Support](support.md)** - Get help

---

## 📞 Support & Contact

- **Documentation Issues**: [GitHub Issues](https://github.com/livednoiz/JURA-MODULAR.ORG/issues)
- **Feature Requests**: [GitHub Discussions](https://github.com/livednoiz/JURA-MODULAR.ORG/discussions)
- **Security Issues**: security@jura-modular.org
- **Commercial Support**: [COMMERCIAL-OFFER.md](../COMMERCIAL-OFFER.md)

---

## 📄 License

Dieses Projekt verwendet eine **Dual License**:
- **Open Source**: AGPLv3 für nicht-kommerzielle Nutzung
- **Commercial**: Kommerzielle Lizenz für Business-Nutzung

Siehe [LICENSE](../LICENSE) und [LICENSE-commercial.txt](../LICENSE-commercial.txt) für Details.

---

*Last Updated: August 1, 2025*  
*Version: 1.0.0-alpha*
