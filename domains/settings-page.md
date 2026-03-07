# Blueprint: Settings / Einstellungen

## Trigger Keywords
einstellungen, settings, konfiguration, preferences, profil, account settings

## Prinzip
Settings ist KEIN einzelnes Feature. Es ist ein Container für 5-10 Unterseiten.

## Standard Unterseiten
1. **Profil (alle):** Name, Avatar, Email (read-only), Telefon, Passwort ändern
2. **Benachrichtigungen (alle):** Email/In-App an/aus pro Typ
3. **Erscheinungsbild (alle):** Dark/Light Mode, Kompakte Ansicht
4. **Team (Admin):** User einladen, Rollen verwalten
5. **Firma/Organisation (Admin):** Name, Logo, Adresse, Währung, Branding
6. **Integrationen (Admin):** SMTP, Calendar Sync, API Keys, Webhooks
7. **Daten & Export (Admin):** Export CSV/JSON, Import, Account-Löschung
8. **Security (Admin):** 2FA, Login-Historie, Active Sessions
9. **Audit Log (Admin):** Link + Retention Settings

## DB Schema
### organization_settings (meistens 1 Row)
id, name, logo_url, address (jsonb), currency (default 'CHF'), locale (default 'de-CH'), branding (jsonb), features (jsonb), updated_at

### user_preferences (auf users oder separat)
user_id, theme (light/dark/system), notifications (jsonb), sidebar_collapsed, locale

## Required Views
- Settings Layout: Sidebar-Nav links, Content rechts
- Jede Unterseite eigene Route: /settings/profile, /settings/team, etc.
- Save Button pro Sektion (nicht global)
- Responsive: Sidebar → Dropdown auf Mobile

## Edge Cases
- Dark Mode → Alle Komponenten müssen es supporten (von Anfang an!)
- Admin ändert Logo → überall sofort sichtbar

## Beziehungen
- Auth, User Management, Roles, Notifications, Alle Module (Feature Flags)
