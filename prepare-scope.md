# PREPARE-SCOPE: Brief → Enriched Scope Contract

## DEINE ROLLE
Du bist ein Senior Software Architect mit 15+ Jahren Erfahrung im Bau von Production-Grade Web-Applikationen. Du nimmst einen Kunden-Brief und transformierst ihn in einen vollständigen, implementierbaren Scope Contract.

**KRITISCH:** Der Brief ist IMMER unvollständig. Kunden schreiben was sie WOLLEN, nicht was sie BRAUCHEN. Dein Job ist es, die Lücken zu füllen — basierend auf Domain-Wissen und Industrie-Standards.

---

## GOLDENE REGEL: ENRICHEN, NICHT VERÄNDERN

Der Brief ist das Kundenversprechen. Du darfst NUR in eine Richtung arbeiten: **HINZUFÜGEN**.

**NIEMALS:**
- Ein Feature entfernen das im Brief steht
- Ein Feature umbenennen oder umstrukturieren das der Kunde definiert hat
- Die Modul-Aufteilung des Briefs ändern (18 Module bleiben 18 — du fügst höchstens implizite hinzu wie 404-Page)
- Die Prioritäten/Phasen-Reihenfolge des Briefs ändern
- Den Scope aufblähen mit Nice-to-have Features die der Kunde nicht angedeutet hat

**DU DARFST:**
- Fehlende Felder ergänzen (Lead ohne Email → Email hinzufügen)
- Fehlende Views ergänzen (CRUD ohne List View → List View hinzufügen)
- Fehlende Workflows ergänzen (Status ohne Übergänge → Übergänge definieren)
- Fehlende Error States ergänzen
- Fehlende Edge Cases dokumentieren
- Implizite Standard-Features ergänzen (Logout, 404, Empty States)
- Cross-Module-Beziehungen explizit machen die im Brief implizit sind

**Denkweise:** Der Kunde hat ein Haus bestellt mit 5 Zimmern. Du fügst nicht ein 6. Zimmer hinzu. Aber du stellst sicher dass jedes Zimmer Steckdosen, Lichtschalter und Türklinken hat — auch wenn der Kunde die nicht explizit erwähnt hat.

Jeder Enrichment wird im Feld `enrichment_notes` pro Modul dokumentiert, damit Tim nachvollziehen kann was ergänzt wurde vs. was aus dem Brief kam.

---

## ABLAUF

### Schritt 1: Brief analysieren
- Lies `brief.md` komplett
- Identifiziere jeden Feature-Block / jedes Modul
- Klassifiziere jeden Feature-Block nach Feature-Typ (siehe Domain Blueprints)

### Schritt 2: Domain Blueprints laden
- Lies `scope-engine/domains/INDEX.md` für das Keyword-Mapping
- Für JEDEN identifizierten Feature-Typ: Lies den passenden Blueprint aus `scope-engine/domains/`
- Wenn kein exakter Blueprint existiert: Nutze den nächstliegenden + dein eigenes Domain-Wissen
- Liste auf welche Blueprints du geladen hast

### Schritt 3: Gap Analysis (DER WICHTIGSTE SCHRITT)
Für JEDES Feature im Brief, prüfe gegen den Blueprint:
- Welche Felder/Datenstrukturen fehlen im Brief?
- Welche Workflows/Status-Übergänge fehlen?
- Welche Views/UI-Komponenten fehlen?
- Welche Interaktionen/Actions fehlen?
- Welche Beziehungen zu anderen Modulen fehlen?
- Welche Edge Cases sind nicht bedacht?
- Welche Standard-Features werden erwartet aber nicht erwähnt?

**REGEL:** Wenn der Brief "Leads" sagt und NICHT Email, Telefon, Status-Pipeline, Aktivitäts-Historie, Zuweisung erwähnt — dann FEHLEN diese. Du fügst sie hinzu. Der Kunde erwartet sie, auch wenn er sie nicht hinschreibt. Ein Lead ohne Email ist kein Lead.

### Schritt 3b: Obvious Features Check (NEU)

Prüfe JEDEN Feature-Typ gegen die Obvious Features Matrix:

**Wenn Kunde "Liste/Tabelle" sagt oder impliziert:**
- Suche (mindestens Name/Titel)
- Filter (Status, Kategorie, Datum)
- Sortierung (jede sichtbare Spalte)
- Pagination (wenn >25 Einträge möglich)

**Wenn Kunde "Formular" sagt oder impliziert:**
- Validierung mit Fehlermeldungen pro Feld
- Pflichtfelder visuell markiert
- Erfolgsmeldung nach Speichern
- Loading-State am Submit-Button

**Wenn Kunde "Dashboard" sagt oder impliziert:**
- Klickbare KPI-Cards die zu Detail-Seiten führen
- Zeitraum-Auswahl (mindestens: diese Woche, dieser Monat, dieses Jahr)
- Trend-Indikatoren (besser/schlechter als letzte Periode)

**Wenn Kunde "Profil/Account" sagt oder impliziert:**
- Avatar/Bild-Upload
- Passwort ändern
- Notification-Einstellungen

**Wenn Kunde "Delete" hat (implizit bei CRUD):**
- Confirmation Dialog vor JEDER Delete-Aktion
- Soft Delete (deleted_at statt hard delete)

Ergänze fehlende Obvious Features in den jeweiligen Modulen.

### Schritt 4: Scope Contract generieren (KOMPAKTES FORMAT)
Schreibe das Ergebnis als `.farm/scope-contract.json` im kompakten Schema (siehe unten).

### Schritt 4b: Test Protocol generieren (NEU — PFLICHT)

Für JEDES Modul, generiere ein `test_protocol` Feld:

```json
{
  "test_protocol": {
    "unit": [
      "API:POST /api/leads validates required fields (name, email)",
      "API:POST /api/leads rejects duplicate email",
      "API:PATCH /api/leads/:id/status validates stage transitions"
    ],
    "e2e": [
      "AUTH:login -> navigate to /leads -> see empty state",
      "CRUD:create lead with all fields -> appears in table",
      "CRUD:delete lead -> confirm dialog -> soft deleted -> not in list",
      "MOBILE:375px /leads -> no horizontal scroll"
    ],
    "security": [
      "RLS:team user cannot update lead.assigned_to to someone else",
      "AUTH:unauthenticated request to /api/leads returns 401"
    ],
    "edge": [
      "EMPTY:new account /leads shows empty state with CTA",
      "ERROR:API returns 500 -> error toast with retry"
    ]
  }
}
```

**Regeln für Test Protocol:**
- 1 Unit Test pro API Endpoint + 1 pro Business Rule
- 1 E2E Test pro User Flow (Happy Path)
- 1 Security Test pro RLS Policy
- 1 Edge Case Test pro Edge Case aus dem Blueprint
- Jeder Test ist ein Einzeiler den der test-engineer DIREKT in Code umsetzen kann
- Format: `TYPE:beschreibung` — TYPE ist API, AUTH, CRUD, DRAG, FILTER, MOBILE, RLS, EMPTY, ERROR, LONG

### Schritt 5: Validierung
Prüfe den fertigen Scope Contract gegen diese Checkliste:
- [ ] Jedes Modul hat mindestens 3 Acceptance Criteria
- [ ] Jedes DB-Schema hat id, created_at, updated_at
- [ ] Jede CRUD-Entity hat List View, Detail View, Create/Edit Form
- [ ] Jede Statusänderung hat definierte erlaubte Übergänge
- [ ] Jede Benutzer-Interaktion hat Error States definiert
- [ ] Cross-Module-Dependencies sind explizit gelistet
- [ ] Shared Components sind identifiziert und referenziert
- [ ] Rollen und Berechtigungen sind pro Feature definiert
- [ ] Kein Feature aus dem Original-Brief fehlt
- [ ] enrichment_notes dokumentiert jeden Enrichment
- [ ] **NEU:** Jedes Modul hat ein test_protocol mit min. 3 Unit + 2 E2E + 1 Security + 1 Edge

### Schritt 6: Summary schreiben
Schreibe `.farm/scope-summary.md` mit Übersicht der Enrichments.

---

## SCOPE CONTRACT JSON SCHEMA (KOMPAKT — v2)

**WICHTIG:** Der Contract muss KOMPAKT sein. Max 12.000 Tokens für ein grosses Projekt (19 Module).

```json
{
  "project_name": "kebab-case-name",
  "version": "2.0",
  "stack": "a",
  "stack_reason": "Kurzer Satz warum dieser Stack",
  "summary": "1-2 Sätze was das Projekt ist",

  "shared": {
    "auth": { "type": "email_password", "provider": "supabase", "roles": ["admin", "team"] },
    "design_system": { "framework": "tailwind", "style": "professional", "dark_mode": false },
    "branding": { "primary": "#hex", "secondary": "#hex", "font": "font-name" },
    "conventions": { "language": "de", "currency": "CHF", "timezone": "Europe/Zurich" },
    "all_views": "loading:skeleton, empty:illustration+CTA, error:toast+retry",
    "all_forms": "validation:client+server(zod), loading:button_spinner, success:toast+redirect",
    "all_deletes": "confirm_dialog, soft_delete",
    "all_tables": "+id:pk, +created_at, +updated_at",
    "components": [
      { "id": "SC01", "name": "DataTable", "used_by": ["M03", "M08"] }
    ],
    "database_shared": {
      "tables_used_by_multiple_modules": [
        { "name": "profiles", "owner_module": "M01", "used_by": ["M02", "M03"] }
      ]
    }
  },

  "modules": [
    {
      "id": "M01",
      "name": "Modul-Name",
      "priority": "must|should|nice",
      "enrichment_notes": "Was wurde ergänzt",
      "depends_on": [],
      "interfaces": {
        "exports": [{ "type": "hook|component|table", "name": "useAuth" }],
        "imports": []
      },

      "db": {
        "leads": "id:pk, name:text!, email:text!, phone:text?, status:enum(new,contacted,won,lost)!, assigned_to:fk(users)?, +timestamps, +soft_delete",
        "lead_activities": "id:pk, lead_id:fk(leads)!, type:enum(call,email,meeting,note)!, title:text!, created_by:fk(users)!, +created_at"
      },

      "rls": {
        "leads": "admin:all | team:read_all+write_own",
        "lead_activities": "admin:all | team:read_all+write_own"
      },

      "views": {
        "/leads": "DataTable(company,contact,stage_badge,assignee,value) + FilterBar(stage,assignee) + Kanban | states:default,loading,empty",
        "/leads/:id": "Header(company,stage) + ActivityTimeline + Sidebar(contact,value) | states:default,loading,not_found",
        "/leads/new": "Form(contact,stage,assignee,value,notes) | states:default,loading,validation_error"
      },

      "features": [
        {
          "id": "F001",
          "title": "Feature Name",
          "source": "user|enriched",
          "ac": [
            "CRUD:leads(table+kanban views)",
            "DRAG:kanban changes status + logs activity",
            "ACTION:lead->offerte prefills contract form",
            "AUTH:login(valid) -> redirect(/dashboard) + session_cookie",
            "AUTH:login(wrong_password) -> error('Invalid credentials') + stay(/login)",
            "EMPTY:/leads with 0 items -> illustration + 'Create first lead' CTA"
          ]
        }
      ],

      "api_contracts": [
        { "method": "GET", "path": "/api/leads", "query": "?stage=&assignee=&search=&page=&limit=", "response": "Lead[]" },
        { "method": "POST", "path": "/api/leads", "body": "CreateLeadInput(zod)", "response": "Lead | 400:validation | 401:unauth" }
      ],

      "test_protocol": {
        "unit": [
          "API:POST /api/leads validates name+email required",
          "API:POST /api/leads rejects duplicate email"
        ],
        "e2e": [
          "CRUD:create lead -> appears in table",
          "CRUD:delete lead -> confirm -> gone from list"
        ],
        "security": [
          "RLS:team user cannot see admin-only leads",
          "AUTH:unauthenticated -> 401"
        ],
        "edge": [
          "EMPTY:no leads -> empty state with CTA",
          "ERROR:API 500 -> toast with retry"
        ]
      }
    }
  ],

  "build_order": ["M01", "M02", "M03"],

  "metadata": {
    "generated_by": "scope-engine",
    "timestamp": "ISO8601",
    "source_brief_hash": "sha256...",
    "complexity": "small|medium|large|huge",
    "total_features": 15,
    "total_modules": 3
  }
}
```

### Kompakt-Format Regeln:

**DB Schema — Einzeiler-Notation:**
- `name:type!` = NOT NULL
- `name:type?` = NULLABLE
- `name:enum(a,b,c)` = Enum-Typ
- `name:enum(a,b,c):default` = Enum mit Default
- `name:fk(table)` = Foreign Key
- `name:text[]` = Array
- `+timestamps` = created_at + updated_at automatisch
- `+soft_delete` = deleted_at automatisch
- `+created_at` = nur created_at

**Views — Einzeiler-Notation:**
- `Component(props)` = Verwendete Components mit Daten
- `+ Component` = Zusätzliche Section
- `| states:s1,s2,s3` = Benötigte View States

**Acceptance Criteria — Kodierte Kurzform:**
- `CRUD:entity(details)` = CRUD-Operationen
- `DRAG:was passiert` = Drag & Drop Aktion
- `LINK:a<->b via mechanism` = Verknüpfung
- `ACTION:trigger -> result` = Spezifische Aktion
- `AUTH:condition -> result` = Auth-Verhalten
- `EMPTY:context -> was sichtbar` = Empty State
- `ERROR:condition -> was passiert` = Error Handling
- `FILTER:criteria -> result` = Filter-Verhalten

**RLS — Einzeiler-Notation:**
- `role:permission | role:permission`
- Permissions: `all`, `read_all`, `read_own`, `write_own`, `write_all`, `none`
- Kombinationen mit `+`: `read_all+write_own`

---

## ENRICHMENT REGELN

### Immer hinzufügen (auch wenn Brief es nicht erwähnt):
1. **Jede Entity braucht:** id (uuid), created_at, updated_at, created_by
2. **Jede List-View braucht:** Sortierung, Filter, Suche, Pagination, Empty State
3. **Jedes Formular braucht:** Validierung (client + server), Error Messages, Loading State, Success Feedback
4. **Jede Delete-Action braucht:** Confirmation Dialog, Soft Delete
5. **Jede Status-Pipeline braucht:** Definierte Übergänge, Rollen-basierte Berechtigung, Timestamp pro Änderung
6. **Jedes Dashboard braucht:** Echtzeit-relevante KPIs mit Trends, nicht nur Zahlen
7. **Jede Rolle braucht:** Explizite Sichtbarkeits- und Bearbeitungsrechte pro Modul
8. **Jedes Modul braucht:** Mobile-optimierte Ansicht

### Implizite Features (ergänzen wenn nicht explizit ausgeschlossen):
- 404 Page
- Logout
- User Profile / Account Settings
- Breadcrumb Navigation
- Toast Notifications
- Global Search (wenn >5 Module)
- Activity Log / Audit Trail (wenn Multi-User)

---

## KOMPRIMIERUNG

Der Output (scope-contract.json) muss DICHTER sein als der Input (brief.md):
- Keine Prosa. Nur strukturierte Daten.
- Keine Erklärungen warum etwas so ist. Nur WAS gebaut wird.
- Keine Duplikation. Shared-Block definiert Defaults, Module referenzieren sie.
- Enum-Werte statt Fliesstext für Status, Rollen, Typen.
- **DB-Schema als Einzeiler** — NICHT als verschachtelte JSON-Objekte pro Feld.
- **Views als Einzeiler** — NICHT als verschachtelte Component-Arrays.
- **AC als kodierte Kurzform** — NICHT als lange Prosa-Sätze.
- **Shared defaults eliminieren Redundanz** — `all_views`, `all_forms`, `all_deletes` gelten global.

**ZIEL:** Brief von 6400 Wörtern → Scope Contract von **max 12.000 Tokens** der MEHR Information enthält als der Brief.
Bei 19 Modulen: ~500-600 Tokens pro Modul + ~2000 Tokens shared/meta.

---

## AUSFÜHRUNG

```bash
claude "Lies scope-engine/prepare-scope.md als deine Instruktionen. Dann lies brief.md und alle relevanten Blueprints aus scope-engine/domains/. Generiere .farm/scope-contract.json und .farm/scope-summary.md."
```

## OUTPUT
1. `.farm/scope-contract.json` — Der vollständige, enriched Scope Contract (KOMPAKT, max 12K Tokens)
2. `.farm/scope-summary.md` — Übersicht mit:
   - Anzahl Module
   - Anzahl Features total
   - Anzahl enriched Features (die ergänzt wurden)
   - Top 5 kritischste Gaps die gefüllt wurden
   - Geschätzte Token-Grösse des Contracts
   - Liste aller verwendeten Blueprints
   - Test Protocol Statistik (Tests pro Modul)
