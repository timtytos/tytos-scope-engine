# Blueprint: Dokumente / Document Management

## Trigger Keywords
dokumente, documents, dateien, files, uploads, archiv, ablage, dms

## Minimum DB Schema

### documents
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| name | text | not null | |
| description | text | nullable | |
| file_url | text | not null | Supabase Storage |
| file_type | text | not null | MIME |
| file_size | integer | not null | Bytes |
| folder_id | uuid | FK folders, nullable | |
| entity_type | text | nullable | 'lead', 'contact', 'invoice' |
| entity_id | uuid | nullable | |
| tags | text[] | default {} | |
| version | integer | default 1 | |
| uploaded_by | uuid | FK users | |
| created_at | timestamptz | default now() | |

### folders
| Field | Type | Constraint | Notes |
|-------|------|-----------|-------|
| id | uuid | PK | |
| name | text | not null | |
| parent_id | uuid | FK folders, nullable | |
| color | text | nullable | |
| created_by | uuid | FK users | |
| created_at | timestamptz | default now() | |

## Storage
- Supabase Storage, Private Bucket (RLS)
- Max 50MB, erlaubt: PDF, DOCX, XLSX, PNG, JPG, SVG, CSV, ZIP

## Required Views
- **File Browser:** Grid (Thumbnails) + List Toggle, Ordner-Navigation, Drag & Drop Upload, Multi-Select
- **Upload:** Drag & Drop, Multiple, Progress Bar, Duplikat-Warnung
- **Preview:** PDF inline, Bilder Lightbox, Office Download
- **Entity Documents:** Tab auf jeder Entity-Detail-Seite

## Required Actions
- Upload (Einzel + Multi), Download (Einzel + ZIP), Soft Delete (Papierkorb 30 Tage), Rename, Move, Copy, Share Link (mit Expiry), Version Upload, Tags

## Edge Cases
- Upload abgebrochen: Cleanup
- Storage Limit: Warnung 80%, Block 100%
- Ordner löschen mit Inhalt: Wahlweise löschen oder verschieben
- Gleicher Dateiname: Auto-Suffix

## RLS
- Admin: Alles | Team: Eigene + geteilte | Partner: Nur explizit geteilte

## Beziehungen
- Alle Module (Entity-Verknüpfung), Supabase Storage, Audit Log
