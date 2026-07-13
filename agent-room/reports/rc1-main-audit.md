# RC1 — Main Audit (Meta Cloud + Stripe + Full Security Review)

> ⚠️ **Este repositório é PÚBLICO.** Os detalhes de segurança (findings específicos,
> tabelas/políticas, caminhos de exploração e secrets) vivem **apenas** no repo de
> produto **privado** — PR #62 / `docs/rc1/`. Este ficheiro é só um índice.

- **Task:** RC1.0 Release Candidate Audit — auditoria factual da `main` pós-merges.
- **Agent:** Claude (Platform & Security)
- **Repo (privado):** rtzforyou/easytattoo-crm
- **Branch:** `claude/rc1-main-audit` · **Commit:** `d38d92c`
- **PR (draft, docs-only):** rtzforyou/easytattoo-crm#62
- **Regras:** NO MERGE · NO DEPLOY · NO APPLY · landing PR #61 intocada · Evolution não removido.

## Entregáveis (no repo privado, `docs/rc1/`)
`RC1_MAIN_AUDIT`, `RC1_META_GAP_ANALYSIS`, `RC1_STRIPE_GAP_ANALYSIS`, `RC1_SECURITY_REVIEW`, `RC1_STAGING_DEPLOY_PLAN`, `RC1_REQUIRED_SECRETS`, `RC1_BLOCKERS`.

## Baseline (não sensível)
- `tsc --noEmit` PASS · `npm run build` PASS · Deno `_shared` 40/40 PASS.
- `deno check`: 3 erros de tipagem pré-existentes (não runtime). `npm audit`: 5 vulns dev/build-only.

## Estado por área (resumo não sensível)
- **Meta Cloud:** connect + webhook **deployed**; inbound ainda não validado E2E; templates/outbound não confirmados em produção; **sem Embedded Signup** (ligação manual).
- **Stripe:** código desenhado e testado ao nível unitário, mas **nada aplicado/deployed/configurado**; **frontend billing ainda em mock**.
- **Promo/trials/influencers:** **não implementado**.

## Segurança (contagem apenas — detalhe no repo privado)
- **0 Critical · 3 High · 5 Medium · 6 Low.**
- Pontos fortes confirmados: sem `service_role` no frontend; endpoints públicos de escrita neutralizados (410); webhooks Cloud/Stripe com assinatura + anti-replay + idempotência; RLS ativo em todas as tabelas.
- Os 3 High e os Medium (com evidência, ficheiro:linha e correção) estão **só** em `docs/rc1/RC1_SECURITY_REVIEW.md` (privado). Não reproduzidos aqui por este repo ser público e os findings visarem produção ainda não corrigida.

## Infra
- Drift migrations repo↔remoto (repo ≠ histórico remoto) → staging deve usar baseline por dump. Detalhe em `RC1_STAGING_DEPLOY_PLAN.md`.

## Blockers (categorias — detalhe/instruções no privado)
🔴 Segurança (3 High) · 🟠 Meta (App Secret + subscrição `messages` + deploy templates + confirmar outbound) · 🟠 Stripe (aplicar + deploy + secrets + seed + frontend real) + promo/trials greenfield · 🟣 Infra (reconciliar migrations; funções órfãs; apagar stubs) · ⚫ Externo (token produção Meta).

**NO MERGE / NO DEPLOY / NO APPLY.** Auditoria termina aqui; próximos passos aguardam gates de Victor.
