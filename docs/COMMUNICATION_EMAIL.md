# 📧 Communication Hub – Email Integration

## Überblick
Die Email-Integration ermöglicht den Versand und Empfang von E-Mails direkt aus Jura Modular. Sie ist eng mit dem Messaging-System und den Cases verknüpft.

## Features
 - **OutgoingEmail**: Modell für ausgehende E-Mails (Betreff, Text, Empfänger, Status, Anhang)
 - **IncomingEmail**: Modell für eingehende E-Mails (Betreff, Text, Absender, Anhang)
 - **EmailService**: Versand von E-Mails via SMTP, inkl. Anhänge
 - **EmailTemplate**: Vorlagen für Standardantworten (Name, Betreff, Text, Kategorie, aktiv)
 - **API-Endpunkte**: REST-API für E-Mail-Versand, -Empfang und Vorlagenmanagement

## Status
✅ Backend-Modelle und API-Endpunkte implementiert
✅ Grundlegende Tests für Modelle und Versand
✅ IMAP-Empfangsmodul (Empfang, Management Command, Tests) abgeschlossen
✅ EmailTemplate-Feature (Modell, API, Tests) abgeschlossen (August 2025)
✅ Automatisierung (IMAP-Abruf, Celery-Task) abgeschlossen (August 2025)
✅ Email-to-Case Assignment (automatische Zuordnung, Tests abgeschlossen, August 2025)

## Beispiel-Workflow
1. Anwalt erstellt eine OutgoingEmail im System
2. EmailService versendet die Nachricht via SMTP
3. Eingehende E-Mails werden als IncomingEmail gespeichert
4. Verknüpfung mit Cases und Messaging möglich
5. Standardantworten können als Vorlage ausgewählt und angewendet werden

---
Letztes Update: 14. August 2025
