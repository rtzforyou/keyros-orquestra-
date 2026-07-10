# PM0 — Discovery (Project Memory / Tattoo Case File)

Data: 2026-07-10 · Autor: Claude / Fable · Orquestra #3
Fonte: introspeção real do schema de produção (read-only) + CLAUDE.md + código.

## Confirmações (não assumir nomes da issue)
- **Utilizadores:** `public.users(id uuid, organization_id uuid NOT NULL, role varchar['admin'|'staff'], permissions jsonb, email, full_name, avatar_url, created_at)`. Sem `updated_at`.
- **Org FK:** `organizations(id)`. Praticamente todas as tabelas `organization_id ... ON DELETE CASCADE`.
- **contacts:** `id, organization_id NOT NULL, name NOT NULL, phone, email, source, avatar_url, remote_jid, instance_id, tags[], metadata jsonb NOT NULL, created_at`. **Sem `updated_at`, sem soft-delete** (delete físico).
- **deals:** `id, organization_id NOT NULL, contact_id (nullable), title, value, stage, stage_id, currency, financial_status, closed_at, updated_at, ...`. **Sem soft-delete.**
- **Regra de destruição real (FKs):**
  - `deals.contact_id` → **SET NULL** on contact delete.
  - Filhos artísticos do contact: `tattoo_sessions` → **CASCADE**; `appointments`/`conversations` → CASCADE; a maioria (deals, messages, payments) → SET NULL.
  - Filhos do deal: quase todos **SET NULL** (`payments` = RESTRICT, `financial_events` = CASCADE).
- **audit_logs:** `organization_id NOT NULL, user_id, action, target_id, details jsonb, created_at`. Existe e é reutilizável para auditoria de segurança (via edge functions/service_role).
- **Helpers RLS:** `get_user_org_id()` (`SELECT organization_id FROM users WHERE id=auth.uid()`), `auto_fill_organization_id()`. **Não existe** helper de role → criar `is_org_admin()`.
- **Roles/permissões reais:** `users.role` (`admin`/`staff`) + `users.permissions` (jsonb array de module ids) + `lib/moduleRegistry.ts` + `lib/entitlements.ts` (`resolveEffectiveModules`: admin→todos os módulos do plano; não-admin→permissions ∩ assignable ∩ plano). Não há módulo `project_memory` ainda.
- **Padrão updated_at:** inconsistente (contacts sem, deals com). `moddatetime` não instalado → trigger explícito (padrão já usado em `whatsapp_channels`). PM1 usa `project_touch_updated_at()`.
- **Padrão soft-delete/archive:** inconsistente no core (contacts/deals sem). `landing_page_integrations` usa `deleted_at`. PM1 adota `archived_at`.
- **Storage:** buckets `avatars` (público) e **`studio-files` (privado)**. **Não há** bucket `project-memory`, nem adapter de storage, nem tabelas de usage/quota.
- **Colisões:** nenhuma tabela `project_*` existente.
- **Padrão de migrations:** `YYYYMMDDHHMMSS_nome.sql`, RLS via `get_user_org_id()`, `apply_migration` (MCP).

## Implicações para PM1
- `project_files.contact_id` NOT NULL → seguir precedente artístico (`tattoo_sessions` CASCADE) — decisão a confirmar (CASCADE vs RESTRICT).
- `project_files.deal_id` opcional `SET NULL` (regra do EPIC: deal não destrói memory).
- Auditoria: `project_events` (timeline de domínio) + `audit_logs` existente (segurança, fase futura com edge functions).
- Permissão de escrita: sem módulo dedicado ainda → PM1 usa org-member + ownership; decisão a confirmar.
- Storage: reutilizar `studio-files` com prefixo vs bucket dedicado — a confirmar. Nada criado em PM1.
