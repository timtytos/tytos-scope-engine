# Blueprint: Auth & Login

## Trigger Keywords
login, auth, authentifizierung, anmeldung, registrierung, passwort, session, zugang

## Minimum DB Schema

### users (extends Supabase auth.users)
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK, FK auth.users | |
| email | text | unique, not null | Von auth.users |
| full_name | text | not null | |
| avatar_url | text | nullable | |
| role | enum | not null | Projekt-spezifisch |
| is_active | boolean | default true | Deaktivierung statt Löschung |
| phone | text | nullable | |
| last_login_at | timestamptz | nullable | |
| settings | jsonb | default {} | User-Preferences |
| created_at | timestamptz | default now() | |
| updated_at | timestamptz | default now() | |

## Required Flows

### Login
1. Email + Password Form
2. Client-Validierung (Email-Format, Password nicht leer)
3. supabase.auth.signInWithPassword()
4. Erfolg → Redirect zu role-based Dashboard
5. Fehler → Spezifische Meldung (falsche Credentials, Account deaktiviert, Rate Limit)
6. Session persistiert über Browser-Refresh

### Password Reset
1. "Passwort vergessen" Link auf Login-Page
2. Email-Eingabe → supabase.auth.resetPasswordForEmail()
3. Success-Message (auch wenn Email nicht existiert — Security)
4. Email mit Reset-Link
5. Reset-Page: Neues Passwort + Bestätigung
6. Validierung: Min 8 Zeichen, nicht gleich wie altes
7. Erfolg → Redirect zu Login mit Success-Message

### Logout
1. Button in Sidebar/Header (immer sichtbar)
2. supabase.auth.signOut()
3. Session löschen, Redirect zu /login
4. Back-Button führt nicht zurück in App

### Session Management
- Auth State Change Listener
- Automatischer Redirect wenn nicht eingeloggt
- Token Refresh automatisch via Supabase
- Inaktivitäts-Timeout (optional, konfigurierbar)

## Required Views

### Login Page (/login)
- Zentriertes Card-Layout mit Logo/Branding
- Email + Password Inputs
- "Angemeldet bleiben" Checkbox
- Submit Button mit Loading State
- "Passwort vergessen" Link
- Error Display (Toast oder Inline)
- Kein Zugang zu App-Routes ohne Auth

### Password Reset Page (/reset-password)
- Email-Eingabe Schritt
- Bestätigungs-Schritt (Email gesendet)
- Neues Passwort Schritt (via Token)

## Auth Guard
- Middleware oder Layout-Level Check
- Unauthenticated → /login
- Authenticated aber is_active=false → /login mit Meldung
- Authenticated aber falsche Rolle → /unauthorized oder role-based redirect

## Edge Cases
- Browser-Tab lange offen → Session abgelaufen → Graceful Redirect
- Mehrere Tabs → Auth State Sync
- Deaktivierter User versucht Login → Klare Fehlermeldung
- Brute Force Protection → Rate Limiting via Supabase
- Email case-insensitive behandeln

## RLS Basics
- Jede Tabelle braucht RLS Policy
- auth.uid() als Basis für alle Policies
- Role-Check via users.role Join
- Service Role Key NIEMALS im Client

## Beziehungen
- Basis für ALLE anderen Module (jedes Modul depends_on Auth)
- User Profile → Eigene Daten bearbeiten
- User Management (Admin) → Users CRUD
- Audit Log → Login/Logout Events loggen
