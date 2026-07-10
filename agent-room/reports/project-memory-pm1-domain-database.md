# PM1 — Domain + Database Design (Project Memory / Tattoo Case File)

Data: 2026-07-10 · Autor: Claude / Fable · Orquestra #3
Branch: `claude/project-memory-pm1` · PR draft: easytattoo-crm#55
Migração: `20260720000000_project_memory_pm1.sql` — **NÃO aplicada**.

## Princípio
Domínio **separado** do pipeline comercial. Nada de dados artísticos/técnicos em
`deals`. Fluxo: `Contact → (Deal opcional) → Project File → References/Notes/AI/Events`.

## Tabelas
- **project_files** — dossiê por contacto. `contact_id` NOT NULL (CASCADE), `deal_id`
  opcional (SET NULL), `assigned_user_id`, `created_by`, título, `body_zone`,
  `body_side` (check), `style`, `width_cm`/`height_cm`, `budget`/`currency`, `status`
  (check, 9 estados), `client_intent`, `artist_summary`, `technical_plan` jsonb,
  `metadata` jsonb, `created_at`/`updated_at`, `archived_at`.
- **project_references** — só metadata (`storage_path`, `thumbnail_path`, `mime_type`,
  `file_size`, `width/height`, `source` check, `client_note`, `aspect_to_keep`,
  `ai_description`, `sort_order`). Nunca bytea/base64.
- **project_notes** — `type` (8 tipos, check), `content`, `author_id`, timestamps.
- **project_ai_reports** — versionado append-only. `UNIQUE(project_file_id, report_type, version)`.
- **project_events** — timeline append-only (`event_type` check).

## Decisões
1. **contact delete → CASCADE** (precedente `tattoo_sessions`). *Confirmar vs RESTRICT.*
2. **deal delete → SET NULL** (memory sobrevive). Sem cascade do deal. ✔ regra do EPIC.
3. **1 project file ativo por deal**: `UNIQUE(deal_id) WHERE deal_id IS NOT NULL AND archived_at IS NULL`. *Confirmar (test G).*
4. **Soft-delete** via `archived_at`; **DELETE físico não exposto** (sem policy DELETE).
5. **updated_at** via trigger `project_touch_updated_at()`.
6. **AI reports/events append-only** (nunca sobrescreve).

## RLS
- `anon`: `REVOKE ALL`.
- SELECT: `organization_id = get_user_org_id()`.
- INSERT: org + pai (contact/project_file) na própria org (bloqueia cross-tenant, test B/K).
- UPDATE project_files: `is_org_admin() OR assigned_user_id = auth.uid() OR created_by = auth.uid()`.
- UPDATE notes: autor ou admin. references: membro da org (pai na org).
- ai_reports/events: SELECT+INSERT (append-only).
- `is_org_admin()` (owner/admin) criado (CREATE OR REPLACE — idêntico ao do PR #54).
- `organization_id` **nunca** vem do frontend.

## Testes
`supabase/tests/project_memory_pm1_verification.sql` — A–N. **Correr pós-apply em
staging** (transação ROLLBACK). Não corrido nesta ronda (proibido apply em prod +
requer fixtures de auth.users). Lógica de constraint/RLS revista estaticamente.

## Storage (só desenho)
Reutilizar bucket privado `studio-files` com prefixo
`project-memory/{org}/{project_file_id}/references/{file_id}/original.ext` (+thumb.webp).
Bucket dedicado é alternativa. Futuro: signed URLs curtas, upload validado no backend,
thumbnail/compressão, quota/tenant, migração para S3/R2. **Nada criado.**

## Riscos / dúvidas p/ aprovação
- CASCADE vs RESTRICT no contact (decisão 1).
- Regra "1 file por deal" (decisão 3).
- Granularidade de escrita (org-member+ownership vs módulo/permissão `project_memory`).
- Storage: `studio-files`+prefixo vs bucket dedicado.

## GATE
PM1 termina aqui. **Não** iniciar PM2/upload/frontend/IA/bucket sem revisão.
**Nada deployed, applied ou merged.**
