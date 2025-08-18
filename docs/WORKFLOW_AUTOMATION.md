# ⚡ Workflow Automation & Task Management

## Neue Features (August 2025)
- **CaseTask & SubTask Modelle**: Aufgabenverwaltung pro Mandat inkl. Unteraufgaben, Priorität, Zuweisung, Deadlines und Status.
- **API-Endpunkte**: Vollständige CRUD-API für Aufgaben und Subtasks, inkl. Filterung und Status-Tracking.
- **Testabdeckung**: Eigene Test-Suite für alle Kernfunktionen (Modelle, API, Trigger).

**Beispiel:**
```python
from kanzlei_apps.cases.models import CaseTask, SubTask
task = CaseTask.objects.create(title="Frist prüfen", case=case)
SubTask.objects.create(task=task, title="Dokument einscannen")
```

**API:**
- `/api/cases/tasks/` (Tasks)
- `/api/cases/subtasks/` (Subtasks)

**Status:**
- [x] Implementiert & getestet (August 2025)
