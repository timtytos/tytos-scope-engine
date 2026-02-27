# Blueprint: Rollen & Berechtigungen

## Trigger Keywords
rollen, berechtigungen, permissions, access control, rechte, zugriff, rbac

## Zwei Ansätze
- **Einfach (≤5 Rollen, Standard):** Enum-basiert. Rollen fest im Code. Reicht für 90% der Projekte.
- **Komplex (>5 Rollen):** Permission-basiert mit separaten Tabellen. Nur wenn nötig.

## Einfacher Ansatz (Standard)
Rolle als Enum auf users-Tabelle. Permission-Helper: `canAccess(user.role, 'module', 'action')` als Config-Objekt.

### Permission Matrix (Beispiel)
| Feature | Admin | Team | Partner |
|---------|-------|------|---------|
| Dashboard (all) | ✅ | ❌ | ❌ |
| Dashboard (own) | ✅ | ✅ | ✅ |
| Users CRUD | ✅ | ❌ | ❌ |
| Leads (all) | ✅ R+W | ✅ R / Own W | Own only |
| Settings | ✅ | ❌ | ❌ |

## Komplexer Ansatz (nur wenn nötig)
Tabellen: roles, permissions (module + action + scope), role_permissions (Mapping)
Views: Rollen-Liste, Rollen-Editor mit Checkbox-Grid, User-Rollen-Zuweisung

## Required View
- Admin → Settings → Rollen-Übersicht: Matrix welche Rolle was darf

## Implementation Notes
- JEDE Route + JEDE API-Route prüft Berechtigung
- RLS als letzte Verteidigungslinie
- Navigation zeigt nur erlaubte Menüpunkte
- Buttons versteckt wenn keine Berechtigung

## Edge Cases
- Letzten Admin entrechten: BLOCKIEREN
- Rolle löschen mit Users: BLOCKIEREN, erst reassignen
- Neue Rolle: Startet mit 0 Permissions

## Beziehungen
- Auth (Rolle aus Session), Jedes Modul, Audit Log
