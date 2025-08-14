# Communication API

## Endpunkte

### MessageThread
- `GET /api/communication/threads/` – Alle Threads abrufen
- `POST /api/communication/threads/` – Neuen Thread erstellen
- `GET /api/communication/threads/<id>/` – Einzelnen Thread abrufen
- `PUT /api/communication/threads/<id>/` – Thread aktualisieren
- `DELETE /api/communication/threads/<id>/` – Thread löschen

### Message
- `GET /api/communication/messages/` – Alle Nachrichten abrufen
- `POST /api/communication/messages/` – Neue Nachricht senden
- `GET /api/communication/messages/<id>/` – Einzelne Nachricht abrufen
- `PUT /api/communication/messages/<id>/` – Nachricht aktualisieren
- `DELETE /api/communication/messages/<id>/` – Nachricht löschen

## Felder
**MessageThread**
- `id`, `subject`, `created_at`, `updated_at`, `case`

**Message**
- `id`, `thread`, `sender`, `recipient`, `body`, `sent_at`, `is_read`, `attachment`

## Hinweise
- Alle Endpunkte sind mit JWT-Auth geschützt (`IsAuthenticated`)
- Attachments werden als Datei-Upload unterstützt
- Nachrichten sind nach Thread gruppiert

---
Letztes Update: 14. August 2025
