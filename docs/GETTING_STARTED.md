# 🚀 Getting Started

Willkommen bei **Jura Modular**! Diese Anleitung führt Sie durch die Installation und den ersten Start des Systems.

## 📋 Voraussetzungen

### System Requirements
- **Python**: 3.12+ 
- **Node.js**: 18+ (für Frontend)
- **npm/yarn**: Latest version
- **Git**: Für Version Control
- **SQLite**: Für Development (automatisch installiert)
- **PostgreSQL/MySQL**: Für Production (optional)

### Betriebssystem Support
- ✅ **Linux** (Ubuntu 20.04+, Debian 11+, CentOS 8+)
- ✅ **macOS** (10.15+)
- ✅ **Windows** (WSL2 empfohlen)

## ⚡ Quick Start (5 Minuten)

### 1. Repository klonen
```bash
git clone https://github.com/livednoiz/JURA-MODULAR.ORG.git
cd JURA-MODULAR.ORG
```

### 2. Backend Setup
```bash
# Zum Backend-Verzeichnis wechseln
cd backend

# Virtual Environment erstellen
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
# oder
.venv\Scripts\activate     # Windows

# Dependencies installieren
pip install -r requirements.txt

# Datenbank migrieren
python manage.py migrate

# Superuser erstellen
python manage.py createsuperuser

# Development Server starten
python manage.py runserver
```

### 3. Frontend Setup (Optional)
```bash
# Zum Frontend-Verzeichnis wechseln
cd ../frontend

# Node Dependencies installieren
npm install

# Development Server starten
ng serve
```

### 4. Erste Schritte
- **Backend**: http://127.0.0.1:8000/
- **Admin Interface**: http://127.0.0.1:8000/admin/
- **API Docs**: http://127.0.0.1:8000/api/docs/ (geplant)
- **Frontend**: http://localhost:4200/ (wenn installiert)

## 🔧 Detaillierte Installation

### Backend Installation

#### 1. Python Environment Setup
```bash
# Python Version prüfen
python --version  # Sollte 3.12+ sein

# Virtual Environment erstellen
cd backend
python -m venv .venv

# Environment aktivieren
source .venv/bin/activate  # Linux/macOS
.venv\Scripts\activate     # Windows

# Upgrade pip
python -m pip install --upgrade pip
```

#### 2. Dependencies installieren
```bash
# Core Dependencies
pip install -r requirements.txt

# Für Development (optional)
pip install -r requirements-dev.txt  # wenn vorhanden
```

#### 3. Environment-Variablen konfigurieren
```bash
# .env-Datei erstellen (optional)
cp .env.example .env  # wenn vorhanden

# Wichtige Settings für Development:
export DJANGO_DEBUG=True
export DJANGO_SECRET_KEY="your-secret-key-here"
export DJANGO_ALLOWED_HOSTS="localhost,127.0.0.1"
```

#### 4. Datenbank Setup
```bash
# Migrations erstellen (falls nötig)
python manage.py makemigrations

# Datenbank migrieren
python manage.py migrate

# Static Files sammeln (für Production)
python manage.py collectstatic --noinput
```

#### 5. Superuser & Testdaten erstellen
```bash
# Superuser erstellen
python manage.py createsuperuser

# Testdaten laden (optional)
python manage.py loaddata fixtures/sample_data.json  # falls vorhanden
```

#### 6. Server starten
```bash
# Development Server
python manage.py runserver

# Mit spezifischem Port
python manage.py runserver 8080

# Für externe Zugriffe
python manage.py runserver 0.0.0.0:8000
```

### Frontend Installation (Angular)

#### 1. Node.js Setup
```bash
# Node.js Version prüfen
node --version   # Sollte 18+ sein
npm --version

# Angular CLI installieren (global)
npm install -g @angular/cli
```

#### 2. Frontend Dependencies
```bash
cd frontend

# Node Modules installieren
npm install

# Oder mit Yarn
yarn install
```

#### 3. Environment Configuration
```typescript
// src/environments/environment.ts
export const environment = {
  production: false,
  apiUrl: 'http://127.0.0.1:8000/api',
  websocketUrl: 'ws://127.0.0.1:8000/ws'
};
```

#### 4. Development Server starten
```bash
# Standard Port (4200)
ng serve

# Custom Port
ng serve --port 3000

# Mit Auto-Reload
ng serve --live-reload
```

## 🧪 Installation verifizieren

### 1. Backend Tests
```bash
cd backend

# Alle Tests ausführen
python manage.py test

# Spezifische App testen
python manage.py test accounts

# Mit pytest
pytest tests/

# Mit Coverage
pytest tests/ --cov=kanzlei_apps
```

### 2. API-Endpoints testen
```bash
# Health Check (wenn implementiert)
curl http://127.0.0.1:8000/api/health/

# Login testen
curl -X POST http://127.0.0.1:8000/api/accounts/login/ \
  -H "Content-Type: application/json" \
  -d '{"username": "admin", "password": "your_password"}'
```

### 3. Admin Interface testen
1. Browser öffnen: http://127.0.0.1:8000/admin/
2. Mit Superuser-Credentials einloggen
3. Benutzer und Klientenprofile anzeigen

## 🎯 Erste Schritte

### 1. System konfigurieren
1. **Admin Interface**: http://127.0.0.1:8000/admin/
2. **Benutzer erstellen**: Users → Add user
3. **Klientenprofile**: Client Profiles → Add client profile
4. **Permissions testen**: Verschiedene Rollen ausprobieren

### 2. API erkunden
```bash
# Postman Collection importieren (wenn vorhanden)
# Oder HTTP-Client Ihrer Wahl verwenden

# Token erhalten
POST /api/accounts/login/
{
  "username": "admin",
  "password": "password"
}

# Authentifizierte Requests
GET /api/accounts/profile/
Authorization: Bearer <your_access_token>
```

### 3. Frontend entwickeln (wenn installiert)
```bash
# Neue Component erstellen
ng generate component my-component

# Service erstellen
ng generate service services/my-service

# Build für Production
ng build --prod
```

## 🔧 Troubleshooting

### Häufige Probleme

#### 1. Python/Django Issues
```bash
# Django nicht gefunden
pip install Django

# Migration Errors
python manage.py migrate --fake-initial

# Static Files
python manage.py collectstatic --clear

# Permission Denied
chmod +x manage.py
```

#### 2. Database Issues
```bash
# SQLite locked
rm db.sqlite3
python manage.py migrate

# PostgreSQL Connection
pip install psycopg2-binary

# MySQL Connection  
pip install mysqlclient
```

#### 3. Frontend Issues
```bash
# Node Modules Issues
rm -rf node_modules package-lock.json
npm install

# Angular CLI Issues
npm uninstall -g @angular/cli
npm install -g @angular/cli@latest

# Port bereits in Verwendung
ng serve --port 4201
```

#### 4. Virtual Environment Issues
```bash
# Environment nicht aktiviert
source .venv/bin/activate

# Packages nicht gefunden
pip install -r requirements.txt

# Environment korrupt
rm -rf .venv
python -m venv .venv
```

## 📚 Nächste Schritte

Nach erfolgreicher Installation:

1. **[API Documentation](api/)** - API-Endpoints verstehen
2. **[Development Guide](development/setup.md)** - Code-Standards lernen
3. **[User Guides](user-guides/)** - Features erkunden
4. **[Configuration](configuration/)** - System anpassen

## 🆘 Hilfe bekommen

- **Dokumentation**: [Troubleshooting](troubleshooting.md)
- **GitHub Issues**: [Problem melden](https://github.com/livednoiz/JURA-MODULAR.ORG/issues)
- **Discussions**: [Community](https://github.com/livednoiz/JURA-MODULAR.ORG/discussions)

---

*Happy Coding! 🚀*
