# Cases Management System - Dokumentation

## Übersicht

Das Cases Management System ist das Herzstück der JURA-MODULAR.ORG Plattform und bietet eine vollständige Lösung für die Verwaltung von Rechtsmandaten in Anwaltskanzleien.

## ✅ Implementierte Features

### 🏗️ **Datenmodelle**

#### **Case (Mandat)**
- **UUID-basierte Identifikation** für eindeutige Referenzierung
- **Automatische Aktennummergenerierung** (Format: YYYY-NNNN)
- **Vollständige Mandatsinformationen**: Titel, Beschreibung, Priorität
- **Workflow-Management**: Status-basierte Bearbeitung
- **Finanzielle Aspekte**: Budget, individuelle Stundensätze
- **Terminverwaltung**: Fristen und Deadlines
- **Rollenbasierte Zuordnung**: Mandant, Anwalt, Assistent

```python
# Beispiel: Mandat erstellen
case = Case.objects.create(
    title="Scheidungsverfahren Müller",
    description="Einvernehmliche Scheidung",
    case_type=family_law_type,
    client=client_user,
    lawyer=lawyer_user,
    status=open_status,
    priority="high"
)
```

#### **CaseType (Rechtsgebiet)**
- **Kategorisierung** von Mandaten nach Rechtsgebieten
- **Individuelle Stundensätze** pro Rechtsgebiet
- **Farbkodierung** für UI-Darstellung
- **Aktivitätsstatus** für Verwaltung

#### **CaseStatus (Mandatsstatus)**
- **Workflow-Definition** mit konfigurierbaren Statusen
- **Final-Status Logik** für automatisches Schließen
- **Reihenfolge-Management** für Statusübergänge
- **Slug-basierte URL-Referenzierung**

#### **CaseDocument (Mandatsdokumente)**
- **Dateiverwaltung** mit Upload-Funktionalität
- **Versionierung** für Dokumentenhistorie
- **Vertraulichkeitsstufen** für sensible Dokumente
- **Typisierung** (Vertrag, Schriftwechsel, Beweisstück, etc.)
- **Metadaten-Tracking** (Uploader, Datum, Größe)

#### **CaseNote (Aktennotizen)**
- **Strukturierte Notizen** zu Mandaten
- **Zeiterfassung** für abrechenbare Tätigkeiten
- **Wichtigkeits-Markierungen** für prioritäre Informationen
- **Volltext-Suchfunktionalität**

#### **CaseTask (Aufgaben)**
- **Aufgabenverwaltung** mit Zuweisungen
- **Deadline-Management** mit Erinnerungen
- **Prioritätsstufen** für Aufgabenorganisation
- **Completion-Tracking** für Fortschrittsverfolgung

#### **CaseTimeEntry (Zeiterfassung)**
- **Präzise Zeiterfassung** auf Minutenbasis
- **Stundensatz-Management** pro Eintrag
- **Automatische Kostenberechnung**
- **Abrechnungsstatus** für Fakturierung

### 🔐 **Berechtigungssystem**

#### **Rollenbasierte Zugriffskontrolle**
- **Admin**: Vollzugriff auf alle Mandate und Funktionen
- **Lawyer**: Zugriff auf zugewiesene Mandate
- **Assistant**: Zugriff auf assistierte Mandate
- **Client**: Zugriff nur auf eigene Mandate

#### **Objektebenen-Berechtigungen**
```python
# Beispiel: Berechtigung prüfen
permission = CanViewOrAssignCases()
has_access = permission.has_object_permission(request, view, case)
```

### 🌐 **REST API**

#### **Vollständige CRUD-Operationen**
- **GET /api/cases/** - Liste aller Mandate (gefiltert nach Berechtigung)
- **POST /api/cases/** - Neues Mandat erstellen
- **GET /api/cases/{id}/** - Mandatsdetails abrufen
- **PUT/PATCH /api/cases/{id}/** - Mandat aktualisieren
- **DELETE /api/cases/{id}/** - Mandat löschen

#### **Custom Actions**
- **POST /api/cases/{id}/close/** - Mandat schließen
- **POST /api/cases/{id}/reopen/** - Mandat wiedereröffnen
- **GET /api/cases/statistics/** - Mandatsstatistiken

#### **Filterung und Suche**
```bash
# Nach Mandant filtern
GET /api/cases/?client=123

# Nach Status filtern  
GET /api/cases/?status=open

# Dokumente zu einem Mandat
GET /api/cases/documents/?case=456

# Zeiteinträge filtern
GET /api/cases/time-entries/?case=456&is_billable=true
```

### 🎯 **Business Logic**

#### **Automatische Prozesse**
- **Aktennummer-Generierung**: Eindeutige, sequenzielle Nummern
- **Effektiver Stundensatz**: Fallback auf Rechtsgebiet-Standard
- **Auto-Closing**: Automatisches Schließen bei Final-Status
- **Kostenberechnung**: Stunden × Stundensatz

#### **Validierung und Constraints**
- **Eindeutige Aktennummern** pro Jahr
- **Pflichtfelder** für vollständige Datenintegrität
- **Referenzielle Integrität** zwischen verwandten Objekten

## 🧪 **Tests**

### **Model Tests**
```python
# Beispiel: Case Model Test
def test_case_creation(self):
    case = Case.objects.create(
        title="Test Case",
        case_type=case_type,
        client=client,
        lawyer=lawyer,
        status=status
    )
    assert case.case_number  # Auto-generated
    assert case.effective_hourly_rate == Decimal('200.00')
```

### **Test Coverage**
- ✅ **Model Creation & Validation**
- ✅ **Business Logic (Properties & Methods)**
- ✅ **Unique Constraints**
- ✅ **Automatic Field Generation**
- ✅ **String Representations**

### **Test Execution**
```bash
# Alle Tests ausführen
cd backend && python -m pytest ../tests/cases/ -v

# Spezifische Tests
python -m pytest ../tests/cases/test_simple.py::TestCaseModelsSimple::test_case_creation -v
```

## 🗃️ **Datenbankschema**

### **Tabellen-Übersicht**
- `cases_casetype` - Rechtsgebiete
- `cases_casestatus` - Mandate-Status
- `cases_case` - Haupttabelle für Mandate
- `cases_casedocument` - Dokumente
- `cases_casenote` - Notizen
- `cases_casetask` - Aufgaben
- `cases_casetimeentry` - Zeiterfassung

### **Indizes für Performance**
```sql
-- Wichtige Indizes
CREATE INDEX cases_case_case_number_idx ON cases_case(case_number);
CREATE INDEX cases_case_client_status_idx ON cases_case(client_id, status_id);
CREATE INDEX cases_case_lawyer_priority_idx ON cases_case(lawyer_id, priority);
CREATE INDEX cases_case_deadline_idx ON cases_case(deadline);
```

## 🚀 **Deployment**

### **Migrationen**
```bash
# Migrationen erstellen
python manage.py makemigrations cases

# Migrationen anwenden
python manage.py migrate
```

### **Admin Interface**
- **Vollständige Admin-Integration** für alle Models
- **Optimierte List-Views** mit Filtern und Suche
- **Readonly-Felder** für System-generierte Daten

## 📊 **Performance Optimierungen**

### **Datenbankoptimierungen**
- **Indizierung** kritischer Felder
- **Select Related** für Foreign Keys
- **Pagination** für große Datensätze

### **API Optimierungen**
- **Serializer Optimierung** mit computed fields
- **Query-Parameter Filtering** statt URL-nesting
- **Cached Properties** für Performance

## 🔧 **Konfiguration**

### **Settings**
```python
# In settings.py
INSTALLED_APPS = [
    # ...
    'kanzlei_apps.cases',
    # ...
]
```

### **URLs**
```python
# In urls.py
urlpatterns = [
    path('api/cases/', include('kanzlei_apps.cases.urls')),
]
```

## 📈 **Statistiken & Monitoring**

### **Verfügbare Metriken**
- **Gesamtanzahl Mandate**
- **Offene vs. geschlossene Mandate**
- **Mandate pro Anwalt**
- **Durchschnittliche Bearbeitungszeit**
- **Umsatz nach Rechtsgebiet**

### **API Endpoint**
```bash
GET /api/cases/statistics/
{
    "total_cases": 156,
    "open_cases": 89,
    "closed_cases": 67,
    "cases_by_type": {...},
    "revenue_by_month": {...}
}
```

## 🎯 **Next Steps**

### **Geplante Erweiterungen**
1. **Advanced Filtering** mit django-filter
2. **Full-Text Search** mit PostgreSQL
3. **Document Preview** für PDF/DOC Files
4. **Email Integration** für Kommunikation
5. **Calendar Integration** für Termine
6. **Billing Module** für Rechnungsstellung

### **Performance Improvements**
1. **Database Query Optimization**
2. **Caching Strategy** mit Redis
3. **API Response Compression**
4. **Background Task Processing**

---

*Letzte Aktualisierung: 2. August 2025*
*Version: 2.0.0 - Phase 2 Complete*
