# SPEC.md · Kleist Events

Quelle: Meeting 1 und 2 (20 Nachrichten) mit der Inhaberin Sabine Kleist. Zu jeder Regel steht die Nachricht, aus der sie stammt.

## Entitäten

| Entität | Attribute |
|---|---|
| Kunde | kunde_nr, name, kontakt, wiederkehrend |
| Event | event_nr, kunde → Kunde, bezeichnung, eventtyp (Tagung, Weihnachtsfeier, Kick-off, Produktlaunch, Hochzeit, Stadtfest), eventdatum, gaestezahl, gesamtbudget, nachgespraech_gefuehrt, status (Anfrage, Planung, Bestätigt, Live, Abgeschlossen) |
| Dienstleister | dienstleister_nr, name, kontakt, gewerke, bewertung, notiz, zahlungsbedingungen, stornobedingungen |
| Buchung | buchung_nr, event → Event, dienstleister → Dienstleister, gewerk, vereinbarte_leistung, vereinbarte_menge, preis, status (angefragt, zugesagt, schriftlich bestätigt, geleistet, bezahlt) |
| Zahlung | zahlung_nr, event → Event, buchung → Buchung, richtung (eingehend, ausgehend), zahlungsart (Anzahlung, Restzahlung, Schlussrechnung, Dienstleisterrechnung), betrag, faelligkeit, status (offen, bezahlt), bezahlt_am |
| Aufgabe | aufgabe_nr, event → Event, titel, art (Aufgabe, Termin), faelligkeit, status (offen, erledigt), verantwortlich → Nutzer |
| Dokument | dokument_nr, event → Event, buchung → Buchung, dokumentart (Vertrag, Angebot, Bestätigung, Rechnung), ablageart (digital, physisch), ablageort |
| Nutzer | nutzer_nr, name, rolle (Inhaberin, Buchhaltung, Helferin) |

## Beziehungen

- Kunde 1:n Event
- Event n:m Dienstleister über Buchung
- Event 1:n Aufgabe
- Event 1:n Zahlung
- Buchung 1:0..n Zahlung
- Event 1:n Dokument
- Nutzer n:m Event
- Nutzer 1:n Aufgabe

## Business Rules

- **BR1** Anzahlung 30 % nach Bestätigung, ohne Anzahlung keine verbindliche Buchung von Dienstleistern, Restzahlung 14 Tage vor dem Event, interne Terminreservierung vor der Anzahlung erlaubt (Nachricht 9, 17)
- **BR2** Alle wesentlichen Dienstleister schriftlich bestätigt spätestens 21 Tage vor dem Event, mündliche Zusage zählt nur als „zugesagt“ (Nachricht 6, 16, 17)
- **BR3** Budget: Warnung ab 85 % Summe aller Buchungen, ab 100 % Sperre bis zur Freigabe durch die Inhaberin, Zusatzleistungen bis 500 € im Konzept entscheidet sie selbst, darüber Zustimmung des Kunden (Nachricht 10, 14)
- **BR4** Storno und Verschiebung: Anzahlung wird nie erstattet, bis 3 Monate vorher meist ohne Gebühren, unter 3 Wochen gelten die Stornobedingungen der Dienstleister und werden an den Kunden weitergereicht (Nachricht 15)
- **BR5** Bei Datumsänderung werden nur rechnerische Fristen automatisch angepasst (Restzahlung, Cutoff-Datum, „X Tage vor Event“), alles andere nur als Hinweis, keine Nachricht nach außen, Inhaberin kontaktiert die Dienstleister (Nachricht 4, 16)

## Widersprüche

- **W1** „Einzelunternehmerin, macht alles allein“ (Nachr. 7) vs. Mann und Tochter arbeiten mit (Nachr. 6, 7, 12) → aufgelöst über `rolle`
- **W2** „Buchhaltung läuft gut“ (Nachr. 7, 8) vs. „kein Überblick über offene Zahlungen“ (Nachr. 5, 7, 8) → aufgelöst über `zahlung.status` je Event

## Prioritäten

1. Eventübersicht aller laufenden Events mit Status, Datum und Kunde (Nachricht 14, 11)
2. Dienstleister je Event mit Status: angefragt, zugesagt, schriftlich bestätigt, geleistet, bezahlt (Nachricht 14, 6)
3. Budget je Event: geplant, verplant, ausgegeben, offen, mit Warnung bei 85 % und Sperre bei 100 % (Nachricht 14, 5, 10)
4. Aufgaben mit Deadlines und Zuständigkeiten sowie Termine (Begehung, Briefing, Eventtag), Hinweis bei Datumsänderung (Nachricht 14, 4, 16)
5. Verträge und Rechnungen am Event, offene Zahlungen, Hinweis auf physische Originale (Nachricht 14, 8, 9, 20)
6. Stammdaten Dienstleister (Kontakt, Gewerk, Bewertung, Notizen) und Kunden (Name, Kontakt, wiederkehrend), vor Übernahme in ein neues Event prüfen: Kontakt, Preise, Verfügbarkeit (Nachricht 14, 6, 19)
7. Rollen mit unterschiedlichen Zugriffsrechten (Nachricht 15, 7, 12)
8. Lesender Zugriff ohne Netz am Eventtag (Nachricht 18)
9. Später: Event-Vorlagen (Nachricht 13), Altdaten aus dem bisherigen System (Nachricht 19)

## Offene Fragen

- Event-Vorlagen für Stammkunden mit vorausgewählten Standarddienstleistern und Basis-Aufgabenliste: Soll das kommen und wie sieht der Ablauf aus (Nachricht 13, Meeting 2)
- Stornofristen: Was gilt zwischen 3 Monaten und 3 Wochen vor dem Event (Nachr. 15)
- Gilt die 500-€-Grenze für Zusatzleistungen je einzelner Leistung oder summiert über das Event, und ab wann „verschiebt“ etwas das Budget „spürbar“ (Nachr. 5, 10)
- Darf der Mann im Notfall Dienstleister-Infos sehen (Nachr. 12), obwohl er sonst nur Rechnungen und Zahlungsstatus sieht (Nachr. 7, 15)
