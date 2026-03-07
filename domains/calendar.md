# Blueprint: Kalender

## Trigger Keywords
kalender, calendar, termine, events, scheduling, meetings, appointments

## Minimum DB Schema

### events
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| title | text | not null | |
| description | text | nullable | |
| type | enum | not null | meeting, call, deadline, reminder, other |
| start_at | timestamptz | not null | |
| end_at | timestamptz | not null | |
| all_day | boolean | default false | |
| location | text | nullable | Ort oder Meeting-Link |
| color | text | nullable | Farb-Kategorie |
| recurring | jsonb | nullable | Wiederholungs-Pattern |
| entity_type | text | nullable | 'lead', 'task', 'contact' |
| entity_id | uuid | nullable | Verknüpfung |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

### event_attendees
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| event_id | uuid | FK events | |
| user_id | uuid | FK users | |
| status | enum | default pending | pending, accepted, declined |
| responded_at | timestamptz | nullable | |

## Required Views
- **Month View:** Kalender-Grid, Events als farbige Blöcke, Click → Detail/Edit
- **Week View:** Zeitraster, Events positioniert, Drag to resize/move
- **Day View:** Detailliertes Zeitraster
- **Agenda View:** Chronologische Liste der nächsten Events
- **Mini Calendar (Sidebar):** Monats-Navigation, Tage mit Events markiert
- **Create/Edit Modal:** Titel, Datum/Zeit Picker, Typ, Teilnehmer, Verknüpfung

## Required Actions
- CRUD, Teilnehmer einladen (mit Accept/Decline), Drag & Drop (verschieben/verlängern), Recurring Events erstellen, Entity-Verknüpfung

## Edge Cases
- Überlappende Events: Visuell nebeneinander darstellen
- Recurring Event bearbeiten: "Nur dieses" vs "Alle zukünftigen"
- Zeitzonen: Server speichert UTC, Client zeigt lokal
- Event in der Vergangenheit: Bearbeiten erlaubt, visuell anders

## RLS
- Admin: Alle Events | Team: Eigene + eingeladene | Partner: Nur eigene

## Beziehungen
- Tasks (due_date als Event), Leads (Meetings), Contacts, Notifications (Erinnerungen)
