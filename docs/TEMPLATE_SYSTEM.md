# Template-System (Dokument-, E-Mail- und Workflow-Vorlagen)

## Überblick
Das Template-System ermöglicht die zentrale Verwaltung und flexible Nutzung von Vorlagen für Dokumente, E-Mails und Workflows. Alle Vorlagen sind über das Admin-Interface und die API zugänglich und können mit benutzerdefinierten Feldern (`custom_fields`) erweitert werden.

---

## Features
- **Dokumentvorlagen**: Verträge, Briefe, Schriftsätze etc. mit Platzhaltern und Versionierung
- **E-Mail-Vorlagen**: Standardtexte für Kommunikation, mit Variablen und HTML/Text-Unterstützung
- **Workflow-Vorlagen**: Standardisierte Abläufe für Falltypen, mit konfigurierbaren Schritten und Feldern
- **Custom Fields**: JSON-Felder für flexible Erweiterungen und dynamische Workflows
- **API**: Vollständige CRUD-Endpunkte für alle Vorlagentypen
- **Admin**: Verwaltung und Suche im Django-Admin
- **Testabdeckung**: pytest-Tests für alle API-Funktionen

---

## API-Endpunkte
- `/api/cases/document-templates/`
- `/api/cases/email-templates/`
- `/api/cases/workflow-templates/`

---

## Beispiel: Dokumentvorlage
```json
{
  "name": "Standardvertrag",
  "template_type": "contract",
  "description": "Vorlage für Standardverträge",
  "content": "<h1>Vertrag</h1>",
  "variables": ["mandant_name", "datum"],
  "custom_fields": {"zusatz": "optional"},
  "version": 1,
  "is_active": true
}
```

---

## Weiterführende Dokumentation
- [Workflow Automation](WORKFLOW_AUTOMATION.md)
- [Cases Management](CASES_MANAGEMENT.md)
- [API Übersicht](../README.md)

---

> Für Fragen oder Erweiterungswünsche bitte Issue im Repository eröffnen.
