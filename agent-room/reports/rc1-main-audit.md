# RC1 — Main Audit (Meta Cloud + Stripe + Full Security Review)

- **Task:** RC1.0 Release Candidate Audit — auditoria factual da `main` pós-merges (Meta Cloud API only + Stripe + segurança completa).
- **Agent:** Claude (Heavy Implementation / Platform & Security)
- **Repo:** rtzforyou/easytattoo-crm
- **Branch:** `claude/rc1-main-audit`
- **Baseline SHA:** `71cf3fe8ca1dd986b8c3c6e06c0edf5d18aa13e8`
- **Commit:** `d38d92c`
- **PR:** https://github.com/rtzforyou/easytattoo-crm/pull/62 (draft — docs-only)
- **Regras honradas:** NO MERGE · NO DEPLOY · NO APPLY · landing PR #61 intocada · Evolution não removido · KCC/Partner só auditados.

## Ficheiros entregues (docs-only, `docs/rc1/`)
`RC1_MAIN_AUDIT.md`, `RC1_META_GAP_ANALYSIS.md`, `RC1_STRIPE_GAP_ANALYSIS.md`, `RC1_SECURITY_REVIEW.md`, `RC1_STAGING_DEPLOY_PLAN.md`, `RC1_REQUIRED_SECRETS.md`, `RC1_BLOCKERS.md`.

## Verificação realizada
- **Build/Tests:** `tsc --noEmit` PASS · `npm run build` PASS (1.26 MB) · Deno `_shared` 40/40 PASS · `deno check` 3 erros de tipagem pré-existentes (não runtime) · `npm audit` 5 vulns dev-only.
- **Database/RLS (via MCP, read-only):** `list_migrations`, `list_tables`, `list_edge_functions`, `get_advisors`, `pg_policies`, `role_table_grants`, `cron.job`, `vault.secrets`, `storage.buckets`.
- **Probe (GET não destrutivo) aos endpoints públicos** para provar estado deployed real.

## Estado factual
- **Meta Cloud:** `whatsapp-connect-channel` + `whatsapp-cloud-webhook` **deployed**; tabelas/Vault aplicados. Inbound **fail-closed** (falta `whatsapp_app_secret` no Vault). `whatsapp-sync-templates` **não deployed** (404). Outbound C4 no repo, deploy não confirmado. **Sem Embedded Signup** (ligação BYON manual).
- **Stripe:** `create-checkout-session` + `stripe-webhook` bem desenhados e testados (unit), mas **404 (não deployed)**, migration billing **não aplicada**, secrets/planos por configurar, **frontend billing 100% mock** (`localStorage`).
- **Promo/cupões/influencers/trials:** **nada implementado**.

## Findings de segurança — 0 Critical / 3 High / 5 Medium / 6 Low
- **HIGH SEC-01:** `webhook_logs` legível cross-tenant por qualquer autenticado (policy `USING true`; 8680 payloads PII).
- **HIGH SEC-02:** `contacts` aceita INSERT anónimo com `organization_id` arbitrário (policy `Allow public insert contacts`, anon).
- **HIGH SEC-03:** webhook Evolution `whatsapp-webhook` fail-open sem `WHATSAPP_WEBHOOK_SECRET`.
- **MEDIUM:** RLS `whatsapp_channels` FOR ALL (staff gere canais; hardening 20260716 não aplicado); grants largos + policies `always true` em tabelas de serviço; 3 funções deployed sem fonte no repo.
- **Provado seguro:** sem `service_role` no frontend; endpoints de escrita pública neutralizados a **410** (lead-intake, sync-whatsapp-history, debug-api, diagnostic, debug-whatsapp); webhooks Cloud/Stripe com assinatura+replay+idempotência; RLS ativo nas 43 tabelas.

## Drift repo ↔ remoto
- Repo 43 migrations ≠ remoto 58 registos (nomes divergentes; `payments_rebuild`/`whatsapp_saas_v2` remote-only). Cloud migrations aplicadas sob outro timestamp → risco de re-aplicação. **A pasta migrations não é replayável** → staging deve usar baseline por `db dump`.
- Repo-only não aplicadas: `billing_stripe`, `whatsapp_channels_rls_hardening`, `project_memory_pm1/pm2`.

## Secrets necessários (nomes; Victor configura)
- **Vault:** `whatsapp_app_secret` (falta). **Edge:** `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET` (test), confirmar `WHATSAPP_WEBHOOK_SECRET`. **Meta:** subscrever campo `messages`. **Stripe:** produtos/preços + `billing_plans.stripe_price_id`.

## Blockers para beta (resumo)
🔴 Segurança: SEC-01, SEC-02, SEC-03. 🟠 Meta: M1 (App Secret), M2 (subscrever messages), M3 (deploy templates), M4 (confirmar outbound). 🟠 Stripe: S1–S5 (aplicar+deploy+secrets+seed+frontend real) + promo/trials greenfield. 🟣 Infra: B1 (drift migrations), B2 (funções órfãs), B3 (apagar stubs 410). ⚫ Externo: M6 (token produção Meta / app Unpublished).

## Ordem recomendada
Segurança (SEC-01/02/03) → reconciliar migrations (baseline) → Meta E2E (número teste) → Stripe test E2E + frontend real → promo/trials → limpar prod → beta.

## Riscos / não verificado
- Deploy do outbound Cloud (C4) não inspecionável por diff de fonte.
- `WHATSAPP_WEBHOOK_SECRET` e secrets Stripe: presença não inspecionável via MCP — SEC-03 é HIGH até confirmação.
- Funções deployed-only (`setup-org-storage`, `whatsapp-sync`, `appointment-api`) não auditadas (sem fonte).

## Keyros Engine updated
Não (auditoria docs-only no repo de produto; sem mudança estrutural de grafo). Recomendado: registar findings de segurança no backlog do engine numa tarefa isolada.

**NO MERGE / NO DEPLOY / NO APPLY.** Parado após a auditoria, conforme instrução.
