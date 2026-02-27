# Blueprint: Rechnungen / Invoicing

## Trigger Keywords
rechnungen, invoicing, billing, faktura, angebote, quotes, zahlungen

## Minimum DB Schema

### invoices
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| invoice_number | text | unique, not null | Auto: INV-2026-001 |
| contact_id | uuid | FK contacts | Empfänger |
| status | enum | not null | draft, sent, paid, overdue, cancelled |
| issue_date | date | not null | |
| due_date | date | not null | |
| subtotal | decimal | not null | Netto |
| tax_rate | decimal | default 8.1 | CH MwSt |
| tax_amount | decimal | not null | |
| total | decimal | not null | Brutto |
| currency | text | default 'CHF' | |
| notes | text | nullable | Auf Rechnung sichtbar |
| internal_notes | text | nullable | Intern |
| paid_at | date | nullable | |
| payment_method | text | nullable | |
| pdf_url | text | nullable | |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

### invoice_items
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| invoice_id | uuid | FK invoices | |
| description | text | not null | |
| quantity | decimal | not null | |
| unit | text | default 'Stk' | Stk, Std, Pausch |
| unit_price | decimal | not null | |
| total | decimal | not null | |
| sort_order | integer | not null | |

## Status Pipeline
```
Draft → Sent → Paid
              → Overdue (auto wenn due_date < today)
Draft/Sent → Cancelled
```

## Required Views
- **List:** Nr, Empfänger, Status (farbig), Betrag, Datum, Fällig + Summen-Zeile (ausstehend/überfällig)
- **Create/Edit:** Empfänger aus Contacts, Positionen add/remove/sort (Drag), Live-Berechnung, PDF-Vorschau
- **Detail:** PDF eingebettet, Status-Timeline, Actions

### PDF Generation
- Professionelles Layout mit Logo/Adresse
- **Schweizer QR-Rechnung (QR-IBAN)** — Pflicht für CH
- MwSt-Ausweis, Zahlungsbedingungen, Bankdaten

## Required Actions
- CRUD, PDF generieren, Per Email senden, Als bezahlt markieren, Duplikieren, Stornieren, Mahnung senden

## Required Metrics
- Umsatz (Monat/Quartal/Jahr), Ausstehend, Überfällig, Durchschn. Zahlungsdauer, Top Kunden

## CH-Spezifisch
- MwSt: 8.1% (Standard), 2.6% (reduziert), 3.8% (Beherbergung)
- QR-Rechnung Pflicht seit 2022
- UID-Nummer (CHE-xxx.xxx.xxx) auf Rechnung

## Edge Cases
- Rechnung ändern nach Versand: Nur Storno + Neu
- Kontakt gelöscht: Rechnung behält Snapshot der Adressdaten
- Nummernkreis: Lückenlos, konfigurierbar

## RLS
- Admin: Alle | Team: Eigene erstellen + lesen | Partner: Nur eigene Kontakte

## Beziehungen
- Contacts, Products, Leads (Won→Rechnung), Contracts, Dashboard, Documents, Audit Log
