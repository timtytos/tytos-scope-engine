# Blueprint: Zeiterfassung / Time Tracking

## Trigger Keywords
zeiterfassung, time tracking, stunden, arbeitszeit, stundenzettel, timesheet

## Minimum DB Schema

### time_entries
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| user_id | uuid | FK users | Wer |
| task_id | uuid | FK tasks, nullable | Verknüpfter Task |
| project_name | text | nullable | Falls kein Task |
| description | text | nullable | Was wurde gemacht |
| date | date | not null | |
| start_time | time | nullable | Für Stechuhr-Modus |
| end_time | time | nullable | |
| duration_minutes | integer | not null | Dauer in Minuten |
| billable | boolean | default true | Verrechenbar |
| billed | boolean | default false | Bereits verrechnet |
| invoice_id | uuid | FK invoices, nullable | Verknüpfte Rechnung |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

## Zwei Modi
1. **Timer:** Start/Stop Button, läuft live mit, speichert automatisch
2. **Manuell:** Datum + Dauer + Beschreibung eintragen

## Required Views
- **Timesheet (Woche):** Tage als Spalten, Tasks als Zeilen, Stunden in Zellen, Summen pro Tag + Woche
- **Timer Widget:** Persistent in Header/Sidebar, Start/Stop/Pause, aktueller Task angezeigt
- **List View:** Alle Einträge chronologisch, Filter by User/Task/Datum/Billable
- **Reports:** Stunden pro User/Projekt/Woche, Billable vs Non-Billable

## Required Actions
- Timer starten/stoppen/pausieren, Manueller Eintrag, Bearbeiten, Löschen, Als verrechnet markieren (Bulk), Export für Rechnungsstellung

## Required Metrics
- Stunden pro Woche/Monat (pro User), Billable Ratio, Stunden pro Projekt/Kunde, Vergleich Soll/Ist

## Edge Cases
- Timer vergessen zu stoppen: Max-Limit (z.B. 12h), Warnung
- Eintrag über Mitternacht: In zwei Tage splitten
- Nachträgliche Einträge: Erlauben, aber markieren
- Billable ändern nach Verrechnung: BLOCKIEREN

## RLS
- Admin: Alle Einträge | Team: Eigene + Team-Übersicht (read only) | Partner: Kein Zugriff

## Beziehungen
- Tasks (Verknüpfung), Invoices (Abrechnung), Dashboard (Stunden-KPIs), Users
