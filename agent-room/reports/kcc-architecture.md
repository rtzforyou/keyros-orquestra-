# KCC — Keyros Control Center (Discovery + Architecture)

Data: 2026-07-10 · Autor: Claude / Fable · EPIC KCC
Estado: **Discovery + Architecture only.** Sem código, schema, integrações, deploy ou merge.

## Entregas
- Produto (docs only): branch `claude/kcc-architecture`, **PR draft easytattoo-crm#57** —
  `docs/kcc/README.md`, `docs/kcc/KCC0-discovery.md`, `docs/kcc/KCC1-architecture.md`.
- Engine: branch `claude/adr-0007-kcc`, **PR draft keyros-engine#15** — ADR-0007.

## KCC0 — descoberta + resposta central
Confirmado que **não existe** conceito de plataforma (nem role, nem tabela `platform_*`);
o único gate é org-level `users.role='admin'` (frontend). Produto é 100% RLS por org.

**Como adicionar um módulo de plataforma sem quebrar o multi-tenant?** → **Dois planos
ortogonais.** Manter a RLS de tenant **intacta** e criar um **platform plane** separado:
`platform_users` (papel desacoplado), autz `is_platform_member(min_role)` **só no
backend**, acesso cross-tenant **só** via edge functions `platform-*` (`verify_jwt=true`,
`service_role` atrás da autz), **auditado** (`platform_audit_logs`), segredos só como
**estado**. Rejeitada a alternativa de afrouxar a RLS do produto (acopla planos → risco
de vazamento cross-tenant).

## KCC1 — arquitetura
- **Roles:** platform_owner > platform_admin > developer / support (matriz de capacidades).
- **Platform Access Layer:** frontend nunca autoriza; menu deriva da autz da API.
- **Domínios:** Tenants (overview/orgs/users/subscriptions), Billing/Stripe/Meta,
  Costs/MRR/ARR/CAC/LTV/Margins, Infra (Supabase/Storage/Realtime/Edge/Cron/Domain/API
  keys), Usage (AI/Storage/WhatsApp), Health/Incidents, Deploy History, Roadmap, Feature
  Flags, GitHub/PR, Environment/Secrets Status — cada um com fonte de verdade + fase.
- **Secrets status:** Configured / Missing / Expired / Rotation Required (nunca valores).
- **Fases:** V1 read-only · V2 operações (suspender tenant, impersonation segura,
  subscription repair, usage reset, support mode) · V3 live monitoring.
- **Data model proposto (NÃO criado):** platform_users, platform_audit_logs,
  platform_feature_flags, platform_incidents, platform_deploy_log.

## Gaps / dependências
- Health/Usage/Metrics exigem instrumentação nova (hoje sem APM/p95 — ver CAP0).
- Bootstrap seguro do 1º `platform_owner` (seed manual; nunca via convites de org).

## Gate
Implementação (schema/edge/frontend) **aguarda aprovação**. Não alterou Product Core,
PR #54, Project Memory. **NO IMPL / NO DEPLOY / NO MERGE.**
