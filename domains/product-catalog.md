# Blueprint: Produktkatalog

## Trigger Keywords
produkte, catalog, artikel, inventory, sortiment, produktkatalog, waren, dienstleistungen

## Minimum DB Schema

### products
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| name | text | not null | |
| description | text | nullable | |
| sku | text | unique, nullable | Artikelnummer |
| type | enum | not null | product, service, subscription |
| category_id | uuid | FK categories, nullable | |
| unit_price | decimal | not null | |
| currency | text | default 'CHF' | |
| unit | text | default 'Stk' | Stk, Std, Monat, Pausch |
| tax_rate | decimal | default 8.1 | CH MwSt |
| is_active | boolean | default true | |
| image_url | text | nullable | |
| sort_order | integer | default 0 | |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

### categories
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| name | text | not null | |
| parent_id | uuid | FK categories, nullable | Verschachtelung |
| sort_order | integer | default 0 | |

## Required Views
- **List:** Name, SKU, Preis, Kategorie, Status + Filter/Search/Sort
- **Detail/Edit:** Alle Felder, Bild-Upload, Kategorie-Zuweisung
- **Category Management:** Baum-Ansicht, Drag & Drop Sortierung
- **Product Picker (Modal):** Wiederverwendbar in Invoices, Angeboten etc. — Suche + Auswahl

## Required Actions
- CRUD, Kategorien CRUD, Bild Upload, Sortierung ändern, Aktivieren/Deaktivieren, Duplikieren, CSV Import/Export

## Edge Cases
- Produkt löschen mit Rechnungsreferenz: Soft Delete, in Rechnung bleibt Snapshot
- Preisänderung: Bestehende Rechnungen behalten alten Preis
- Kategoriebaum: Max 3 Ebenen

## RLS
- Admin: Alles | Team: Lesen + in Rechnungen verwenden | Partner: Nur aktive Produkte lesen

## Beziehungen
- Invoices (invoice_items), Contracts, Dashboard (meistverkaufte Produkte)
