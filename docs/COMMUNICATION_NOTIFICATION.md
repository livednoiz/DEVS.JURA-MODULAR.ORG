
# Benachrichtigungs-API, Trigger & Echtzeit-Service

## Übersicht
Das Benachrichtigungssystem ermöglicht automatisierte und manuelle Benachrichtigungen an Nutzer. Neben klassischen API-Endpunkten werden Benachrichtigungen jetzt auch in Echtzeit per WebSocket ausgeliefert.

## Endpunkte
- `GET /notifications/` – Listet alle Benachrichtigungen des angemeldeten Nutzers.
- `POST /notifications/create/` – Erstellt eine neue Benachrichtigung für den angemeldeten Nutzer.
- `PATCH /notifications/<id>/read/` – Markiert eine Benachrichtigung als gelesen.

## Echtzeit-Benachrichtigungen (WebSocket)
- URL: `ws://<host>/ws/notifications/`
- Pro Nutzer wird eine eigene WebSocket-Gruppe verwendet (`notifications_<user_id>`)
- Neue Benachrichtigungen werden sofort an den Client gepusht

## Trigger
- Statuswechsel eines Falls (CaseStatus)
- Neue Nachricht (Message)
- Deadline nähert sich (CaseTask)
- Automatisierte E-Mail-Zuordnung (IMAP, Celery)

## Modell
- Typen: email, deadline, status, custom
- Felder: type, recipient, content, is_read, sent_at

## Service
Der NotificationService bietet Methoden zum Erstellen, als gelesen markieren und Echtzeit-Ausliefern von Benachrichtigungen (Channels, Redis).

## Testabdeckung
- Alle API-Endpunkte, Trigger und Echtzeit-Features sind durch Unit- und Integrationstests abgedeckt.

## Beispiel WebSocket-Nachricht
```json
{
	"id": 42,
	"type": "deadline",
	"content": "Deadline für Aufgabe 'Vertrag prüfen' am 15.08.2025!",
	"sent_at": "2025-08-14T10:00:00Z",
	"is_read": false
}
```

---
Letztes Update: 14. August 2025
