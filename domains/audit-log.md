# Blueprint: Audit Log

## Trigger Keywords
audit, log, protokoll, historie, änderungsprotokoll, nachverfolgung, compliance

## Prinzip
Passives System. Zeichnet auf was in allen Modulen passiert. Append-only — NIE ändern oder löschen.

## DB Schema

### audit_log
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| entity_type | text | not null | 'lead', 'task', 'user', etc. |
| entity_id | uuid | not null | |
| action | enum | not null | create, update, delete, status_change, login, logout, export |
| changes | jsonb | nullable | {field: {old: x, new: y}} |
| metadata | jsonb | nullable | Zusätzlicher Kontext |
| performed_by | uuid | FK users | |
| ip_address | text | nullable | |
| created_at | timestamptz | default now() | IMMUTABLE |

## Was wird geloggt
Jede Erstellung, Änderung (mit altem+neuem Wert), Löschung, Status-Änderung, Login/Logout, Daten-Export, Rollen-Änderung, fehlgeschlagene Aktionen (Permission Denied)

## Implementation
- Application-Level Middleware + Supabase DB Trigger als Fallback
- System-Actions: performed_by = 'system' UUID
- Bulk: Ein Eintrag PRO betroffene Entity

## Required Views (Admin only)
- **Audit Log List:** Zeitpunkt, User, Aktion, Entity-Typ, Entity (Link), Details + Filter/Pagination — KEIN Edit, KEIN Delete
- **Entity-Level:** Tab auf jeder Detail-Seite mit Änderungshistorie + Diff-View (alt→neu farbig)

## Performance
- Index auf: entity_type+entity_id, performed_by, created_at
- Partitionierung nach Monat wenn >100K Einträge/Monat
- Archivierung nach 1 Jahr

## RLS
- Admin: Alles | Team: Nur eigene Aktionen | Partner: Kein Zugriff

## Beziehungen
- Wird von ALLEN Modulen befüllt, Settings (Retention), Dashboard (Activity Feed)
