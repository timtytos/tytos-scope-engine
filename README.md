# CRUCIBLE v1.0 — Der Schmelztiegel

Transformiert rohe Kunden-Briefs in strukturierte, enriched, komprimierte Scope Contracts.

## Der Flow
```
brief.md → [CRUCIBLE] → scope-contract.json → [VULCAN] → fertiges Produkt
```

CRUCIBLE laeuft in einer **eigenen Session** vor VULCAN. Das haelt den Context sauber.

## Dateien
```
~/.crucible/
├── README.md                         ← Du bist hier
├── prepare-scope.md                  ← Haupt-Instruktionen
└── domains/                          ← 18 Domain Blueprints
    ├── INDEX.md                      ← Verzeichnis mit Keyword-Mapping
    ├── auth-login.md
    ├── dashboard-analytics.md
    ├── crm-leads.md
    ├── task-management.md
    ├── user-management.md
    ├── roles-permissions.md
    ├── contacts-partners.md
    ├── invoicing.md
    ├── document-management.md
    ├── notifications-messaging.md
    ├── settings-page.md
    ├── audit-log.md
    ├── calendar.md
    ├── time-tracking.md
    ├── product-catalog.md
    ├── contracts.md
    └── email-integration.md
```

## Benutzung

```bash
# 1. Brief als brief.md ins Projekt-Root legen
# 2. CRUCIBLE starten (eigene Claude Session):
claude "Lies ~/.crucible/prepare-scope.md als deine Instruktionen. Dann lies brief.md und alle relevanten Blueprints aus ~/.crucible/domains/. Generiere .vulcan/scope-contract.json."

# 3. scope-contract.json pruefen
# 4. Neue Session starten, VULCAN ausfuehren:
claude "/vulcan-auto"
```

## Goldene Regel

CRUCIBLE ENRICHED den Brief, sie VERAENDERT ihn nicht:
- Fehlende Felder ergaenzen (Lead ohne Email → Email hinzu)
- Fehlende Views ergaenzen (CRUD ohne List → List hinzu)
- Fehlende Workflows ergaenzen (Status ohne Uebergaenge → definieren)
- NIEMALS Features entfernen die der Kunde will
- NIEMALS Module umbenennen oder umstrukturieren
- NIEMALS Scope aufblaehen mit nicht angedeuteten Features
