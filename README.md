# TYTOS Scope Engine v2.0

Verwandelt vage Kunden-Briefs in strukturierte, implementierbare Scope Contracts.

**Pipeline-agnostisch** — funktioniert mit FARM, Gigafactory, oder jeder anderen Build-Pipeline.

## Was ist das?

Eine Wissens-Bibliothek aus 17 Domain Blueprints + einem Transformations-Prompt. Kein Code, kein Framework. Einfach Markdown-Dateien die Claude Code als Instruktionen bekommt.

Loest zwei Probleme:
- **Domain-Wissen:** Blueprints liefern Branchen-Standards die Claude sonst nicht kennt
- **Context Overload:** Komprimiertes JSON statt 6400-Woerter-Fliesstext

## Der Flow

```
brief.md --> [TYTOS Scope Engine] --> scope-contract.json --> [beliebige Pipeline] --> fertiges Produkt
```

## Dateien

```
tytos-scope-engine/
├── README.md                         <- Du bist hier
├── prepare-scope.md                  <- Haupt-Prompt fuer Claude Code
└── domains/                          <- 17 Domain Blueprints
    ├── INDEX.md                      <- Verzeichnis mit Keyword-Mapping
    ├── auth-login.md                 <- Auth & Login
    ├── dashboard-analytics.md        <- Dashboard & KPIs
    ├── crm-leads.md                  <- CRM Leads & Vertrieb
    ├── task-management.md            <- Task Management
    ├── user-management.md            <- User Management (Admin)
    ├── roles-permissions.md          <- Rollen & Berechtigungen
    ├── contacts-partners.md          <- Kontakte / Partner
    ├── invoicing.md                  <- Rechnungen (CH QR-Rechnung)
    ├── document-management.md        <- Dokument-Management
    ├── notifications-messaging.md    <- Notifications & Chat
    ├── settings-page.md              <- Settings (Multi-Page)
    ├── audit-log.md                  <- Audit Log
    ├── calendar.md                   <- Kalender & Events
    ├── time-tracking.md              <- Zeiterfassung
    ├── product-catalog.md            <- Produktkatalog
    ├── contracts.md                  <- Vertraege
    └── email-integration.md          <- E-Mail System
```

## Benutzung

### 1. Klonen

```bash
git clone git@github.com:timtytos/tytos-scope-engine.git ~/.tytos/scope-engine
```

### 2. Brief schreiben

Lege `brief.md` ins Projekt-Root. Das ist der Kunden-Brief — egal wie vage.

### 3. Scope Engine starten

```bash
claude "Lies ~/.tytos/scope-engine/prepare-scope.md als deine Instruktionen. \
       Dann lies brief.md und alle relevanten Blueprints aus \
       ~/.tytos/scope-engine/domains/. \
       Generiere scope-contract.json und scope-summary.md."
```

### 4. Review

- `scope-summary.md` lesen — zeigt was enriched wurde
- `scope-contract.json` pruefen — alles drin?

### 5. Pipeline starten

```bash
# FARM:
claude "/farm-auto"

# Gigafactory:
claude "/gf-auto"
```

## Was die Scope Engine macht (6 Schritte)

1. **Brief lesen** — `brief.md` komplett analysieren
2. **Blueprints matchen** — Keywords erkennen, passende Domain Blueprints laden
3. **Gap Analysis** — Brief vs Blueprint: Was fehlt?
4. **Enrichen** — Fehlende Felder, Views, Workflows, Error States ergaenzen
5. **Komprimieren** — Einzeiler-DB-Schema, kodierte ACs, max 12K Tokens
6. **Validieren** — Checkliste: Min. 3 AC pro Modul, RLS, Dependencies, Test Protocols

## Goldene Regel

Die Scope Engine ENRICHED den Brief, sie VERAENDERT ihn nicht:
- Fehlende Felder ergaenzen (Lead ohne Email -> Email hinzu)
- Fehlende Views ergaenzen (CRUD ohne List -> List hinzu)
- Fehlende Workflows ergaenzen (Status ohne Uebergaenge -> definieren)
- NIEMALS Features entfernen die der Kunde will
- NIEMALS Module umbenennen oder umstrukturieren
- NIEMALS Scope aufblaahen mit nicht angedeuteten Features

**Denkweise:** Der Kunde bestellt ein Haus mit 5 Zimmern. Du baust 5 Zimmer — aber mit Steckdosen, Lichtschaltern und Tuerklinken.

## Kompakt-Format (v2)

- DB-Schema als Einzeiler: `name:text!, email:text?, status:enum(new,active,closed)!`
- Views als Einzeiler: `DataTable(name,email,status) + FilterBar | states:loading,empty`
- ACs als kodierte Kurzform: `CRUD:leads(table+kanban)`, `AUTH:login(valid) -> redirect(/dashboard)`
- RLS als Einzeiler: `admin:all | team:read_all+write_own`

## Neue Blueprints erstellen

Wenn ein Feature-Typ fehlt:
1. Aehnliches Blueprint als Template kopieren
2. Ausfuellen: Keywords, DB Schema, Views, Actions, Edge Cases, RLS, Beziehungen
3. In `domains/INDEX.md` eintragen

## TYTOS GmbH

Arsenalstrasse 40, 6010 Kriens, Schweiz
