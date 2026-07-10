# Partner Platform — Discovery + Architecture

Data: 2026-07-10 · Autor: Claude / Fable · EPIC Partner Platform
Estado: **Discovery + Architecture only.** Sem código, schema, migrations, RLS, deploy, merge.

## Entregas
- Produto (docs only): branch `claude/partner-platform-architecture`, **PR draft
  easytattoo-crm#58** — `docs/partner-platform/README.md`, `PP0-discovery.md`, `PP1-architecture.md`.
- Engine: branch `claude/adr-0008-partner`, **PR draft keyros-engine#16** — ADR-0008.

## PP0 — descoberta + resposta central
Confirmado: 1 login = 1 org (RLS `org = get_user_org_id()`); não existe conceito de
parceiro/agrupamento de orgs. **Como adicionar um domínio Partner sem quebrar a
arquitetura?** → **Terceiro plano cross-tenant SCOPED e CONSENTIDO.** Manter a RLS de
tenant **intacta**; `partners` + `partner_users` (papel desacoplado) + `partner_workspace_links`
(link **consentido** partner↔estúdio, com permissões por módulo). O estúdio **aceita** o
link; o parceiro nunca reivindica orgs; revogar corta o acesso. Autz `is_partner_member` +
escopo por links **só no backend**; cross-tenant só via `partner-*` (service_role atrás da
autz), auditado (`partner_audit_logs`). **Nunca reutilizar `organization_admin`.**

## Distinção KCC vs Partner
KCC = plataforma, vê **todos** os tenants, sem consentimento. Partner = cliente, vê
**subconjunto consentido**, com aceitação do estúdio. Planos e auditorias separados.

## PP1 — arquitetura
- **Roles:** partner_owner > partner_admin > partner_manager > partner_viewer.
- **Permissões por módulo** (CRM/Pipeline/Landing/Campanhas/Financeiro/Funcionários/
  Agenda/Project Memory): Nenhum/Leitura/Escrita/Administração — limitadas pelo consentimento
  do estúdio, reduzidas pelo partner_role.
- **Fluxos:** convite→aceitação (consentimento), dashboard consolidado→drill sem logout.
- **Dashboard global:** Leads, Conversão, ROI, Campanhas, Origens, Comparativos (agregado
  do conjunto ligado).
- **Billing (só desenho):** studio paga / partner paga vários / comissão / licenciamento.
- **Data model proposto (NÃO criado):** partners, partner_users, partner_workspace_links,
  partner_invitations, partner_audit_logs, partner_feature_flags.
- **Roadmap:** PP0 Discovery · PP1 Architecture · PP2 Database · PP3 Permissions · PP4
  Dashboard · PP5 Billing · PP6 Analytics · PP7 Support Tools.

## Riscos / dependências
Consentimento/revogação; fuga de escopo (filtrar sempre pelos links); escalada de papel;
ambiguidade de billing por estúdio. Depende do modelo Stripe (PR#54) e reutiliza fontes de
`useDashboard` por org agregadas no partner plane.

## Gate
Implementação (schema/edge/frontend) **aguarda aprovação**. Não alterou Product Core,
Project Memory, PR#54, KCC. **NO IMPL / NO DEPLOY / NO MERGE.**
