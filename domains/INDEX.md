# Domain Blueprints Index

## Verfügbare Blueprints

| Blueprint | File | Trigger Keywords |
|-----------|------|-----------------|
| Auth & Login | auth-login.md | login, auth, anmeldung, passwort, session, registrierung |
| Dashboard & Analytics | dashboard-analytics.md | dashboard, übersicht, analytics, statistiken, kpi, reporting |
| CRM Leads & Vertrieb | crm-leads.md | leads, vertrieb, sales, pipeline, funnel, akquise, prospects |
| Task Management | task-management.md | tasks, aufgaben, todos, kanban, workflow, zuweisungen |
| User Management | user-management.md | benutzerverwaltung, mitarbeiter, team verwalten, accounts |
| Rollen & Berechtigungen | roles-permissions.md | rollen, berechtigungen, permissions, access control, rbac |
| Kontakte / Partner | contacts-partners.md | kontakte, partner, kunden, lieferanten, addressbuch, firmen |
| Rechnungen / Invoicing | invoicing.md | rechnungen, invoicing, billing, faktura, angebote, zahlungen |
| Dokumente | document-management.md | dokumente, dateien, uploads, archiv, dms, ablage |
| Benachrichtigungen | notifications-messaging.md | nachrichten, benachrichtigungen, notifications, chat, inbox |
| Settings | settings-page.md | einstellungen, settings, konfiguration, preferences, profil |
| Audit Log | audit-log.md | audit, log, protokoll, änderungshistorie, nachverfolgung |
| Kalender | calendar.md | kalender, calendar, termine, events, scheduling, meetings |
| Zeiterfassung | time-tracking.md | zeiterfassung, time tracking, stunden, arbeitszeit |
| Produktkatalog | product-catalog.md | produkte, catalog, artikel, inventory, sortiment |
| Verträge | contracts.md | verträge, contracts, agreements, laufzeiten, vertragsverwaltung |
| E-Mail Integration | email-integration.md | email, smtp, inbox, templates, email versand, mailing |

## Wie Blueprints verwendet werden

1. `prepare-scope.md` liest den Brief
2. Für jedes Feature im Brief → Keyword-Matching gegen diese Tabelle
3. Passender Blueprint wird geladen
4. Gap Analysis: Was steht im Brief vs. was sagt der Blueprint
5. Fehlende Elemente werden ergänzt (OHNE Brief-Inhalte zu verändern)
6. Output: Enriched + Compressed Scope Contract

## Blueprint-Struktur (jeder Blueprint enthält)

**Pflicht:**
- Trigger Keywords
- Minimum DB Schema (Felder, Typen, Constraints)
- Required Views (Seiten/Komponenten)
- Required Actions (User-Interaktionen)
- Edge Cases (was kann schiefgehen)
- RLS (Rollen-basierte Sichtbarkeit)
- Beziehungen zu anderen Modulen

**Optional:**
- Status Pipeline (wenn Status-Workflows existieren)
- Required Metrics (wenn Dashboard-relevant)
- Performance Notes
- Branchenspezifisches (z.B. CH MwSt bei Invoicing)
