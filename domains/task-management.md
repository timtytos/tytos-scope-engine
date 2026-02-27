# Blueprint: Task Management

## Trigger Keywords
tasks, aufgaben, todos, ticketing, workflow, zuweisungen, kanban, backlog

## Minimum DB Schema

### tasks
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| title | text | not null | |
| description | text | nullable | Markdown |
| status | enum | not null | backlog, todo, in_progress, review, done, cancelled |
| priority | enum | default medium | low, medium, high, urgent |
| assigned_to | uuid | FK users, nullable | |
| created_by | uuid | FK users | |
| due_date | date | nullable | |
| estimated_hours | decimal | nullable | |
| actual_hours | decimal | nullable | |
| parent_task_id | uuid | FK tasks, nullable | Subtasks (max 1 Ebene) |
| related_type | text | nullable | 'lead', 'contact', 'project' |
| related_id | uuid | nullable | FK zur verknüpften Entity |
| tags | text[] | default {} | |
| completed_at | timestamptz | nullable | Auto bei status=done |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

### task_comments
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| task_id | uuid | FK tasks | |
| content | text | not null | |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |

## Status Pipeline
```
Backlog → Todo → In Progress → Review → Done
                                       → Cancelled
```

## Required Views
- **List View:** Tabelle mit Gruppierung by Status/Zuständig/Priorität, Quick-Edit inline
- **Kanban View:** Drag & Drop, Swimlanes toggle
- **Detail/Modal:** Inline-Edit Titel, Markdown Description, Sidebar mit Metadaten, Subtasks als Checkliste, Kommentare, Activity Log
- **My Tasks:** Nur eigene, gruppiert nach Überfällig/Heute/Diese Woche/Später

## Required Actions
- CRUD, Status ändern (Einzel + Bulk + Drag), Zuweisen, Subtask hinzufügen/abhaken, Kommentieren, Duplikieren, Entity-Verknüpfung

## Required Metrics
- Tasks nach Status, Überfällige Tasks, Completion Rate, Durchschnittliche Bearbeitungszeit, Tasks pro Mitarbeiter

## Edge Cases
- Überfällige Tasks: Rot markiert, eigener Filter
- Task mit offenen Subtasks auf Done: Warnung
- Gelöschter Zuständiger: Tasks auf unassigned
- Bulk max 50, mit Progress

## RLS
- Admin: Alle | Team: Alle lesen, eigene bearbeiten | Partner: Nur related Tasks

## Beziehungen
- Users, Leads, Calendar, Notifications, Zeiterfassung, Audit Log
