# Communication Email API

## Endpunkte

### OutgoingEmail
- `GET /api/communication/outgoing-emails/` – Alle ausgehenden E-Mails abrufen
- `POST /api/communication/outgoing-emails/` – Neue E-Mail versenden
- `GET /api/communication/outgoing-emails/<id>/` – Einzelne E-Mail abrufen
- `PUT /api/communication/outgoing-emails/<id>/` – E-Mail aktualisieren
- `DELETE /api/communication/outgoing-emails/<id>/` – E-Mail löschen

### IncomingEmail
- `GET /api/communication/incoming-emails/` – Alle eingehenden E-Mails abrufen
- `POST /api/communication/incoming-emails/` – Neue eingehende E-Mail speichern
- `GET /api/communication/incoming-emails/<id>/` – Einzelne E-Mail abrufen
- `PUT /api/communication/incoming-emails/<id>/` – E-Mail aktualisieren
- `DELETE /api/communication/incoming-emails/<id>/` – E-Mail löschen

## Felder
**OutgoingEmail**
- `id`, `subject`, `body`, `to`, `sent_at`, `status`, `user`, `attachment`

**IncomingEmail**
- `id`, `subject`, `body`, `from_email`, `received_at`, `user`, `attachment`

## Hinweise
- Versand erfolgt über Django SMTP-Backend
- Attachments werden als Datei-Upload unterstützt
- Verknüpfung mit Cases und Messaging möglich

---
Letztes Update: 14. August 2025
