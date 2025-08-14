# 📬 Communication Hub (Milestone 2.2)

## Überblick
Das Communication Hub Modul ermöglicht die interne Kommunikation zwischen Anwälten, Klienten und Kanzlei-Mitarbeitern. Es bildet die Grundlage für Messaging, Team-Chat und Benachrichtigungen.

## Features
- **MessageThread**: Gruppiert Nachrichten nach Thema oder Fall
- **Message**: Einzelne Nachricht mit Sender, Empfänger, Text und optionalem Anhang
- **File Attachments**: Upload von Dokumenten direkt in Nachrichten
- **Threading & Search**: Nachrichten werden thematisch gruppiert und sind durchsuchbar

## API-Endpunkte
- `GET /api/communication/threads/` – Liste aller Threads
- `POST /api/communication/threads/` – Neuen Thread anlegen
- `GET /api/communication/messages/` – Nachrichten abrufen
- `POST /api/communication/messages/` – Neue Nachricht senden

### Beispiel-JSON für Nachricht
```json
{
  "thread": 1,
  "sender": 2,
  "recipient": 3,
  "body": "Hallo, bitte prüfen Sie das Dokument im Anhang.",
  "attachment": "<file>"
}
```


## Status
- ✅ Backend-Modelle und API-Endpunkte implementiert
- ✅ Erweiterte Trigger für Benachrichtigungen (Statuswechsel, Deadlines, neue Nachrichten)
- ✅ Echtzeit-Benachrichtigungen via WebSocket (Django Channels, Redis)
- ✅ Vollständige Testabdeckung für Messaging, Email und Notification
- 🚧 Team-Chat und UI-Integration in Planung
- 🚧 Mobile Push Notifications folgen

## Weitere Schritte
- Frontend-Anbindung für Messaging und Echtzeit-Benachrichtigungen
- Integration mit Case- und User-Modulen

---
Letztes Update: 14. August 2025
