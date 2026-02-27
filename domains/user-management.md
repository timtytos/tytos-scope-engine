# Blueprint: User Management (Admin)

## Trigger Keywords
benutzerverwaltung, user management, mitarbeiter, team verwalten, nutzer, accounts

## Prinzip
Auth (Login/Logout) ≠ User Management (CRUD auf Users). Auth ist für jeden. User Management ist ADMIN-only.

## Minimum DB Schema
Nutzt users-Tabelle aus auth-login. Zusätzlich:

### user_invitations
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| email | text | not null | |
| role | enum | not null | Vorgesehene Rolle |
| invited_by | uuid | FK users | |
| token | text | unique | |
| expires_at | timestamptz | not null | 7 Tage default |
| accepted_at | timestamptz | nullable | |
| created_at | timestamptz | default now() | |

## Required Views
- **User List (Admin):** Avatar, Name, Email, Rolle (Badge), Status, Letzter Login + Filter/Search
- **User Detail/Edit (Admin):** Profil bearbeiten, Rolle ändern, Deaktivieren/Reaktivieren, Password Reset, Activity Log
- **User Invite Flow:** Email+Rolle → Einladungs-Email → Registrierung mit vorgegebener Rolle
- **Own Profile (alle Rollen):** Name, Avatar, Telefon, Passwort ändern, Notification Prefs — NICHT eigene Rolle ändern

## Required Actions
- Einladen, Bearbeiten, Deaktivieren (Soft), Reaktivieren, Password Reset erzwingen, Einladung erneut senden/widerrufen

## Edge Cases
- Letzten Admin deaktivieren: BLOCKIEREN
- User deaktivieren mit offenen Tasks/Leads: Warnung + Reassignment
- Einladung abgelaufen: "Erneut einladen" Button
- Gleiche Email nochmal einladen: Fehler wenn User existiert

## RLS
- Admin: Alle Users | Team/Partner: Nur eigenes Profil bearbeiten, andere nur Name+Avatar sehen

## Beziehungen
- Auth, Alle Module (created_by, assigned_to), Audit Log, Roles & Permissions
