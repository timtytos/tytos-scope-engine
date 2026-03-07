# Blueprint: Kontakte / Partner-Verwaltung

## Trigger Keywords
kontakte, contacts, partner, partnerverwaltung, kunden, lieferanten, addressbuch, firmen

## Minimum DB Schema

### contacts
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| type | enum | not null | partner, customer, supplier, other |
| company_name | text | not null | |
| contact_person | text | nullable | Ansprechpartner |
| email | text | nullable | |
| phone | text | nullable | |
| mobile | text | nullable | |
| website | text | nullable | |
| address_street | text | nullable | |
| address_city | text | nullable | |
| address_zip | text | nullable | |
| address_country | text | default 'CH' | |
| industry | text | nullable | Branche |
| status | enum | default active | active, inactive, archived |
| notes | text | nullable | |
| tags | text[] | default {} | |
| assigned_to | uuid | FK users, nullable | |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

### contact_persons (wenn Firma mehrere Ansprechpartner)
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| contact_id | uuid | FK contacts | |
| name | text | not null | |
| position | text | nullable | |
| email | text | nullable | |
| phone | text | nullable | |
| is_primary | boolean | default false | |
| created_at | timestamptz | default now() | |

## Required Views
- **List:** Firma, Ansprechpartner, Typ (Badge), Email, Telefon, Status + Filter/Search/Export
- **Detail:** Kontaktdaten klickbar (mailto/tel), Tabs: Übersicht | Ansprechpartner | Aktivitäten | Dokumente | Verknüpfungen
- **Create/Edit:** Firmenname Pflicht, Duplikat-Check auf Firmenname+PLZ

## Required Actions
- CRUD, Ansprechpartner CRUD, Status ändern, CSV Import/Export, vCard Export, Duplikate zusammenführen

## Edge Cases
- Kontakt löschen mit verknüpften Leads/Rechnungen: BLOCKIEREN
- Duplikate: Gleicher Firmenname + PLZ = Warnung

## RLS
- Admin: Alles | Team: Alle lesen, eigene bearbeiten | Partner: Nur eigenen Datensatz

## Beziehungen
- Leads, Invoices, Contracts, Documents, Tasks, Audit Log
