# Blueprint: E-Mail Integration

## Trigger Keywords
email, e-mail, smtp, inbox, templates, email versand, mailing, email tracking

## Zwei Ebenen:

### 1. Transactional Emails (System-Emails)
Automatische Emails die das System verschickt: Einladungen, Password Reset, Notifications, Rechnungen.

### 2. User-Emails (wenn im Brief)
User kann aus der App heraus Emails an Kontakte/Leads senden, mit Tracking.

---

## Transactional Emails (immer dabei)

### Implementation
- Supabase Edge Functions + Resend/Postmark/SMTP
- HTML Email Templates (responsive, dark-mode ready)
- Absender: noreply@kundenname.ch oder konfigurierbar

### Required Templates
- **Einladung:** "Du wurdest eingeladen zu [App]"
- **Password Reset:** Reset-Link
- **Notification Digest:** Zusammenfassung ungelesener Notifications (optional, täglich)
- **Rechnung:** PDF als Attachment

### DB: email_log
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| to_email | text | not null | |
| template | text | not null | Template-Name |
| subject | text | not null | |
| status | enum | not null | queued, sent, delivered, bounced, failed |
| error | text | nullable | |
| entity_type | text | nullable | |
| entity_id | uuid | nullable | |
| sent_at | timestamptz | nullable | |
| created_at | timestamptz | default now() | |

---

## User-Emails (optional, wenn Brief es erwähnt)

### DB: email_templates (User-erstellte Templates)
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| name | text | not null | |
| subject | text | not null | |
| body | text | not null | HTML mit Platzhaltern |
| placeholders | text[] | default {} | z.B. {contact_name}, {company} |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |

### Required Views
- **Email Compose (Modal):** An (aus Kontakt/Lead), Betreff, Body (Rich Text), Template-Auswahl, Attachment
- **Email History (auf Entity):** Gesendete Emails chronologisch
- **Template Management (Settings):** CRUD auf Email-Templates, Platzhalter-Vorschau
- **Email Log (Admin):** Alle gesendeten Emails, Status, Bounce-Rate

### Required Actions
- Email senden (an Kontakt/Lead), Template auswählen + personalisieren, Attachment hinzufügen, Bulk-Email (an gefilterte Liste, mit Throttling)

## Edge Cases
- Bounce: Email-Adresse als "bounced" markieren, Warnung bei erneutem Senden
- Rate Limiting: Max X Emails/Stunde (Provider-abhängig)
- Grosse Attachments: Max 10MB, Warnung bei >5MB
- Unsubscribe: Link in jeder Marketing-Email (DSGVO/DSG)

## CH/EU Compliance
- Opt-in für Marketing-Emails
- Unsubscribe in jedem Mailing
- Impressum/Absender-Info in Footer
- Email-Log Aufbewahrung

## RLS
- Admin: Alle Logs, Template-Management | Team: Eigene Emails senden/sehen | Partner: Kein Zugriff

## Beziehungen
- Contacts/Leads (Empfänger), Invoices (PDF versenden), Notifications (Email-Channel), Settings (SMTP Config), Audit Log
