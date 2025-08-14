# 📧 Communication Hub – Email Integration

## Überblick
Die Email-Integration ermöglicht den Versand und Empfang von E-Mails direkt aus Jura Modular. Sie ist eng mit dem Messaging-System und den Cases verknüpft.

## Features
- **OutgoingEmail**: Modell für ausgehende E-Mails (Betreff, Text, Empfänger, Status, Anhang)
- **IncomingEmail**: Modell für eingehende E-Mails (Betreff, Text, Absender, Anhang)
- **EmailService**: Versand von E-Mails via SMTP, inkl. Anhänge
- **API-Endpunkte**: REST-API für E-Mail-Versand und -Empfang

## Status
- ✅ Backend-Modelle und API-Endpunkte implementiert
- ✅ Grundlegende Tests für Modelle und Versand
- 🚧 Erweiterung um IMAP-Empfang und Templates in Planung

## Beispiel-Workflow
1. Anwalt erstellt eine OutgoingEmail im System
2. EmailService versendet die Nachricht via SMTP
3. Eingehende E-Mails werden als IncomingEmail gespeichert
4. Verknüpfung mit Cases und Messaging möglich

---
Letztes Update: 14. August 2025
