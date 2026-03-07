# Blueprint: Nachrichten & Benachrichtigungen

## Trigger Keywords
nachrichten, messaging, benachrichtigungen, notifications, chat, inbox, mitteilungen

## Zwei separate Systeme:
1. **Notifications** (System → User): Automatische Benachrichtigungen über App-Events
2. **Messaging** (User → User): Direkte Kommunikation zwischen Usern

---

## Notifications

### DB: notifications
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| user_id | uuid | FK users | Empfänger |
| type | enum | not null | assignment, mention, due_date, status_change, system |
| title | text | not null | |
| body | text | nullable | |
| entity_type | text | nullable | |
| entity_id | uuid | nullable | Link zur Entity |
| is_read | boolean | default false | |
| read_at | timestamptz | nullable | |
| created_at | timestamptz | default now() | |

### Views
- **Bell Icon (Header):** Badge Unread-Count, Dropdown letzte 10
- **Notification Center (/notifications):** Alle, Filter by Type/Read, "Alle gelesen" Button
- Click → Entity + als gelesen markieren

### Delivery: In-App (immer), Email (konfigurierbar pro Type)

---

## Messaging (wenn im Brief)

### DB: conversations, conversation_members, messages
- conversations: id, type (direct/group), name, created_by, timestamps
- conversation_members: conversation_id, user_id, last_read_at
- messages: id, conversation_id, sender_id, content, type (text/file/system), file_url, timestamps

### Views
- Conversation List, Chat View (chronologisch, Input unten, Auto-Scroll), New Conversation (User-Picker)
- Echtzeit via Supabase Realtime, Typing Indicator optional, File Attachments, @mention

## Edge Cases
- Notification für gelöschte Entity: "Inhalt entfernt" statt Error
- User deaktiviert: Notifications stoppen, Messages lesbar
- Auto-Cleanup nach 90 Tage
- Realtime-Fallback: Polling alle 10s

## RLS
- Notifications: Nur eigene | Messages: Nur eigene Conversations | Admin: KEIN Zugriff auf private Messages

## Beziehungen
- Alle Module (generieren Notifications), User Settings, Dashboard (Unread Count)
