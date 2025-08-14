# Benachrichtigungs-API und Service

## Übersicht
Das Benachrichtigungssystem ermöglicht es, automatisierte und manuelle Benachrichtigungen an Nutzer zu senden. Die API bietet Endpunkte zum Listen, Erstellen und als gelesen markieren von Benachrichtigungen.

## Endpunkte
- `GET /notifications/` – Listet alle Benachrichtigungen des angemeldeten Nutzers.
- `POST /notifications/create/` – Erstellt eine neue Benachrichtigung für den angemeldeten Nutzer.
- `PATCH /notifications/<id>/read/` – Markiert eine Benachrichtigung als gelesen.

## Modell
- Typen: email, deadline, status, custom
- Felder: type, recipient, content, is_read, sent_at

## Service
Der NotificationService bietet Methoden zum Erstellen und als gelesen markieren von Benachrichtigungen.

## Testabdeckung
Alle API-Endpunkte und Service-Methoden sind durch Unit-Tests abgedeckt.
