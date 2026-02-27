# Blueprint: CRM Leads & Vertrieb

## Trigger Keywords
leads, vertrieb, sales, pipeline, funnel, akquise, prospects, opportunities

## Minimum DB Schema

### leads
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| name | text | not null | Kontaktperson |
| email | text | not null | Pflichtfeld für Follow-ups |
| phone | text | nullable | |
| company | text | nullable | Firmenname |
| position | text | nullable | Jobtitel |
| source | enum | not null | website, referral, cold, event, partner, other |
| status | enum | not null | new, contacted, qualified, proposal, negotiation, won, lost |
| priority | enum | default medium | low, medium, high, urgent |
| assigned_to | uuid | FK users | Zuständiger Mitarbeiter |
| expected_value | decimal | nullable | Geschätzter Auftragswert |
| expected_close | date | nullable | Erwartetes Abschlussdatum |
| lost_reason | text | nullable | Nur wenn status=lost |
| notes | text | nullable | Freitext-Notizen |
| tags | text[] | default {} | Kategorisierung |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

### lead_activities
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| lead_id | uuid | FK leads | |
| type | enum | not null | call, email, meeting, note, status_change, document |
| title | text | not null | Kurzbeschreibung |
| description | text | nullable | Details |
| outcome | text | nullable | Ergebnis des Kontakts |
| scheduled_at | timestamptz | nullable | Für geplante Aktivitäten |
| completed_at | timestamptz | nullable | |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |

### lead_documents
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| lead_id | uuid | FK leads | |
| name | text | not null | |
| url | text | not null | Storage URL |
| type | text | not null | MIME type |
| uploaded_by | uuid | FK users | |
| created_at | timestamptz | default now() | |

## Status Pipeline
```
New → Contacted → Qualified → Proposal → Negotiation → Won
                                                        → Lost
```
- Jeder Übergang schreibt lead_activity (type=status_change)
- Rückwärts-Übergänge erlaubt (ausser von Won/Lost)
- Won/Lost kann nur Admin zurücksetzen
- Status-Änderung erfordert optionale Notiz

## Required Views

### List View (Default)
- Tabelle: Name, Firma, Status (farbig), Priorität, Zuständig, Erwarteter Wert, Letzte Aktivität
- Sort by: any column
- Filter by: Status, Zuständig, Priorität, Source, Datum
- Search: Name, Firma, Email
- Bulk Actions: Status ändern, Zuweisen, Exportieren
- Pagination: 25/50/100 pro Seite

### Kanban View
- Spalten = Status-Stufen
- Cards: Name, Firma, Wert, Zuständig-Avatar, Tage in Phase
- Drag & Drop ändert Status
- Quick Actions auf Card: Anrufen, Email, Notiz

### Detail View
- Header: Name, Firma, Status-Badge, Priorität, Zuständig
- Tabs: Übersicht | Aktivitäten | Dokumente | Verknüpfungen
- Übersicht: Alle Felder editierbar (inline oder Modal)
- Aktivitäten: Timeline chronologisch, neue Aktivität hinzufügen
- Dokumente: Upload, Download, Preview
- Sidebar: Quick Actions (Status ändern, Anrufen, Email, Notiz)

### Create/Edit Form
- Pflichtfelder markiert, Email-Validierung
- Duplikat-Warnung (gleiche Email oder Name+Firma)

## Required Actions
- CRUD (Create, Read, Update, Soft-Delete)
- Status ändern (Einzeln + Bulk)
- Zuweisen (Einzeln + Bulk)
- Aktivität hinzufügen (Call, Email, Meeting, Notiz)
- Dokument hochladen/löschen
- CSV Import (mit Mapping-Dialog) + CSV/Excel Export
- Lead duplizieren
- Lead zu Kontakt konvertieren (wenn Kontakte-Modul existiert)

## Required Metrics (für Dashboard)
- Leads nach Status (Funnel-Visualisierung)
- Conversion Rate pro Phase
- Durchschnittliche Zeit pro Phase
- Leads nach Source
- Pipeline Value (Summe expected_value nach Status)
- Won/Lost Ratio

## Edge Cases
- Lead ohne Email: Erlauben aber Warnung anzeigen
- Duplikat-Erkennung: Gleiche Email = Hard Block, gleicher Name+Firma = Soft Warning
- Massen-Import: Validierung pro Zeile, Fehler-Report am Ende
- Gelöschter Zuständiger: Leads automatisch auf Admin reassignen
- Lead mit offenen Aktivitäten: Warnung beim Archivieren

## RLS
- Admin: Sieht und bearbeitet alle Leads
- Team: Sieht alle, bearbeitet nur eigene (assigned_to = user.id)
- Partner: Sieht nur Leads wo source='partner' AND created_by=user.id

## Beziehungen
- Users → assigned_to, created_by
- Contacts → Konvertierung Lead → Kontakt
- Tasks → Aufgaben verknüpft mit Lead
- Calendar → Meetings/Calls als Events
- Invoices → Won-Lead → Rechnung erstellen
- Audit Log → Jede Änderung loggen
- Notifications → Bei Zuweisung, Status-Änderung, überfällige Follow-ups
