# ANLEITUNG FÜR DEN NÄCHSTEN CHAT

## Kontext
Die CRUCIBLE ist fertig und im Farm-v3-Repo installiert. Sie nimmt einen Kunden-Brief (brief.md) und generiert einen enriched, komprimierten Scope Contract (.vulcan/scope-contract.json). 

Was FEHLT: Die Farm v3 muss angepasst werden, damit sie den scope-contract.json als Input verwendet statt den rohen Brief.

## Was du im nächsten Chat tun musst

### 1. TYTOS-MASTER.md als erste Nachricht posten (wie immer)

### 2. Dann diese Nachricht senden:

---

**Aufgabe: Farm v3 anpassen für Scope Contract Input**

Die CRUCIBLE ist installiert unter `crucible/`. Sie generiert `.vulcan/scope-contract.json` — einen enriched, komprimierten Scope Contract aus dem Kunden-Brief.

Jetzt muss die Farm v3 angepasst werden:

**Was sich ändern muss:**
1. `/vulcan-auto` (und `/vulcan-plan`, `/vulcan-auto --yolo`, `/vulcan-auto --guided`) liest NICHT mehr `brief.md` direkt. Stattdessen erwartet er `.vulcan/scope-contract.json` als Input.
2. `vulcan-scope` wird ersetzt/angepasst: Er parsed den scope-contract.json statt Fliesstext.
3. `vulcan-scope` kann übersprungen werden — der Scope ist already enriched und komprimiert.
4. `vulcan-execute` bekommt pro Modul NUR den relevanten JSON-Block aus dem Scope Contract (das jeweilige Modul + shared components + dependency interfaces). NICHT den ganzen Contract.
5. `vulcan-integrate` nutzt den `shared` Block aus dem Contract für Cross-Module-Konsistenz.

**Was NICHT geändert wird:**
- Die restlichen Skills (vulcan-verify, vulcan-release, vulcan-learn, scoring, etc.)
- Die Subagents
- Die Quality Gate Hooks
- Die Knowledge Base
- Die Config

**Hier sind meine aktuellen Farm v3 Skills:**

[HIER FÜGST DU DIE RELEVANTEN SKILL-FILES EIN — mindestens:]
- `.claude/skills/vulcan-auto.md`
- `.claude/skills/vulcan-scope.md`  
- `.claude/skills/vulcan-scope.md`
- `.claude/skills/vulcan-execute.md`
- `.claude/skills/vulcan-integrate.md`
- `.claude/skills/vulcan-plan.md`

[OPTIONAL aber hilfreich:]
- `.claude/skills/vulcan-plan/SKILL.md`
- `config.json`
- `CLAUDE.md`

---

### 3. Claude wird die Skills anpassen

Das Ergebnis sollte sein:
- Angepasste vulcan-scope (liest JSON statt Brief)
- vulcan-scope wird zu einem Validierungs-Schritt (prüft ob scope-contract.json valide ist)
- vulcan-execute bekommt Module-Slicing-Logik (pro Modul nur den relevanten Ausschnitt)
- vulcan-integrate nutzt shared-Block

### 4. Testen mit Joraliah

Danach:
```bash
# Schritt 1: CRUCIBLE
claude "Lies crucible/prepare-scope.md als deine Instruktionen. Dann lies brief.md und alle relevanten Blueprints aus crucible/domains/. Generiere .vulcan/scope-contract.json und .vulcan/scope-summary.md."

# Schritt 2: Review
cat .vulcan/scope-summary.md

# Schritt 3: Farm
claude "/vulcan-auto"
```

## Session Update für MASTER.md

```
Session 4 – 2026-02-16
Thema: Lösung für Problem A + B: CRUCIBLE
Ergebnisse:
- Entscheidung: Pre-Farm CRUCIBLE statt Farm-internes Enrichment
- Flow: brief.md → [Claude Code: prepare-scope] → scope-contract.json → [Farm v3]
- CRUCIBLE gebaut: prepare-scope.md + 17 Domain Blueprints + INDEX
- Blueprints: auth, dashboard, crm-leads, tasks, users, roles, contacts, invoicing, documents, notifications, settings, audit-log, calendar, time-tracking, products, contracts, email
- Goldene Regel: Enrichen nicht verändern — Brief-Inhalte werden nie geändert, nur ergänzt
- Klarstellung: 1M Context nur für API pay-as-you-go, nicht Pro/Max — Kompression essentiell
Entscheidungen: Intelligenz VOR die Farm, nicht in die Farm. Kompression statt grösserem Context.
Nächste Session: Farm v3 Skills anpassen um scope-contract.json als Input zu verwenden. Dann Joraliah Test-Run.
Blocker: vulcan-scope, vulcan-scope, vulcan-execute müssen angepasst werden.
```

## Aktualisierter STATUS für MASTER.md

```
Phase: CRUCIBLE gebaut → Farm v3 muss angepasst werden für scope-contract.json Input
Nächste Aktion: Farm v3 Skills (intake, scope, execute, integrate) anpassen
Blocker: Farm liest noch brief.md statt scope-contract.json
```
