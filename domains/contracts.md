# Blueprint: Verträge / Contracts

## Trigger Keywords
verträge, contracts, agreements, laufzeiten, vertragsverwaltung, vereinbarungen

## Minimum DB Schema

### contracts
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| contract_number | text | unique, not null | Auto: CON-2026-001 |
| title | text | not null | |
| contact_id | uuid | FK contacts | Vertragspartner |
| status | enum | not null | draft, active, expiring, expired, cancelled |
| type | enum | not null | service, license, maintenance, other |
| start_date | date | not null | |
| end_date | date | nullable | Nullable für unbefristet |
| auto_renew | boolean | default false | |
| renewal_period_months | integer | nullable | |
| notice_period_days | integer | nullable | Kündigungsfrist |
| value | decimal | nullable | Vertragswert |
| recurring_value | decimal | nullable | Monatlicher Betrag |
| currency | text | default 'CHF' | |
| notes | text | nullable | |
| pdf_url | text | nullable | Vertrags-PDF |
| signed_at | date | nullable | |
| cancelled_at | date | nullable | |
| cancellation_reason | text | nullable | |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

## Status Pipeline
```
Draft → Active → Expiring (auto, 30 Tage vor end_date)
                → Expired (auto wenn end_date < today)
Active → Cancelled
Expired + auto_renew → Active (neues end_date)
```

## Required Views
- **List:** Nr, Titel, Partner, Status (farbig), Laufzeit, Wert, Nächste Aktion
- **Detail:** Vertrags-Infos, PDF-Vorschau, Timeline (Erstellung→Unterschrift→Verlängerung), verknüpfte Rechnungen
- **Create/Edit:** Partner aus Contacts, Laufzeit-Logik, Auto-Renew Toggle, PDF Upload
- **Expiring Soon Widget:** Dashboard-Element mit Verträgen die bald auslaufen

## Required Actions
- CRUD, PDF hochladen/generieren, Vertrag verlängern, Kündigen (mit Grund+Datum), Rechnung aus Vertrag erstellen

## Required Metrics
- Aktive Verträge (Anzahl + Gesamtwert), Auslaufend nächste 30/60/90 Tage, Revenue aus Verträgen (monthly recurring)

## Edge Cases
- Auto-Renew: Notification X Tage vor Verlängerung
- Vertrag ohne End-Date: Status bleibt "active", kein "expiring"
- Partner wechselt: Vertrag an neuen Kontakt übertragen (mit Historie)

## RLS
- Admin: Alles | Team: Lesen, eigene bearbeiten | Partner: Nur eigene Verträge

## Beziehungen
- Contacts, Invoices (Vertrags-Rechnungen), Products, Documents, Notifications (Ablauf-Warnungen), Audit Log
