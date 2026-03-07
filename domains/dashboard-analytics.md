# Blueprint: Dashboard & Analytics

## Trigger Keywords
dashboard, übersicht, analytics, statistiken, kpi, reporting, auswertung, kennzahlen

## Prinzip
Ein Dashboard ist NICHT eine Seite mit Zahlen. Ein Dashboard beantwortet: "Was muss ich JETZT tun?" Jede Metrik muss zu einer Aktion führen oder einen Trend zeigen.

## Required Components

### KPI Cards (Top Row)
- 4-6 Haupt-KPIs als Cards
- Jede Card: Wert, Label, Trend (↑↓→), Vergleich zu Vorperiode (%)
- Click auf Card → Drill-Down zur relevanten Seite
- Farbe: Grün (gut), Rot (schlecht), Grau (neutral)

### Charts (Mittlerer Bereich)
- Mindestens 2 Charts
- Zeitreihe (Line/Bar) für Trend-Daten
- Verteilung (Pie/Donut) für Kategorien
- Zeitraum-Selector: Heute, 7 Tage, 30 Tage, 90 Tage, Custom
- Responsive: Charts skalieren, auf Mobile stacken

### Activity Feed / Recent Items
- Letzte 5-10 relevante Aktivitäten
- Wer hat was wann gemacht
- Click → zur relevanten Entity
- Echtzeit oder Polling (alle 60s)

### Quick Actions
- Häufigste Aktionen als Buttons/Links
- z.B. "Neuer Lead", "Task erstellen", "Rechnung schreiben"

### Alerts / Pending Items
- Überfällige Tasks, unbeantwortete Anfragen
- Items die Aufmerksamkeit brauchen
- Badge-Count im Sidebar-Link

## Role-Based Dashboards
Jede Rolle sieht ein ANDERES Dashboard:
- **Admin:** Gesamtübersicht, Team-Performance, Revenue, System-Health
- **Team:** Eigene KPIs, eigene Tasks, eigene Leads/Projekte
- **Partner:** Eigene Referrals, eigene Provisionen, eigene Dokumente

## DB Schema
Dashboard braucht KEINE eigene Tabelle. Es aggregiert aus anderen Modulen.

## Performance
- Queries müssen SCHNELL sein (<500ms)
- Aggregationen cachen oder materialized views
- Keine N+1 Queries
- Skeleton Loading für jeden Widget-Bereich

## Edge Cases
- Leeres Dashboard (neuer Account) → Onboarding-Hinweise statt leere Charts
- Keine Daten für Zeitraum → "Keine Daten" Meldung, nicht broken Chart
- Grosse Datenmengen → Aggregation serverseitig
- Mobile → KPI Cards horizontal scrollbar, Charts full-width

## Beziehungen
- Depends on: Auth (Role-Based), und ALLE Daten-Module
- Ist typischerweise /dashboard Route nach Login
