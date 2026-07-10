# MASTER ROADMAP V1.0 — GO TO PRODUCTION (tracker)

Substitui a execução por decisões isoladas. Uma branch/PR draft por EPIC; testes antes do
PR; **sem merge/deploy/apply**; parar em gate arquitetural; nunca iniciar nova arquitetura
sem Discovery+Architecture+aprovação. Foundation **FROZEN** (só bugs críticos).

## PRODUCT CORE
| # | EPIC | Estado | Branch / PR |
|---|---|---|---|
| 0 | Product Polish | ✅ **DONE** (a11y + audit) | `claude/epic0-product-polish` · PR #59 |
| 1 | Project Memory PM2→PM9 | ⏭️ **NEXT** | (PM1 pronto: PR #55; PM2 = implementação code-only) |
| 2 | Interactive Tattoo Briefing | pendente (Gate) | — |
| 3 | CRM Completion | pendente (Gate) | — |
| 4 | Financial Dashboard | pendente (Gate) | — |
| 5 | Calendar | pendente (Gate) | — |
| 6 | Landing Pages | pendente (Gate) | — |
| 7 | Automation Engine | pendente (Gate) | — |
| 8 | Tattoo AI | pendente (Gate) — **arquitetura primeiro** | — |
| 9 | Body Mockup | pendente (Gate) — **arquitetura primeiro** | — |
| 10 | Billing Completion | pendente (Gate) | (Stripe PR #54 base) |
| 11 | Academy | pendente (Gate) | — |
| 12 | Marketing Platform | pendente (Gate) | — |

## PLATFORM (só após Product Core)
KCC Implementation (arquitetura pronta: PR #57 / eng ADR-0007 #15) · Partner Platform
Implementation (PR #58 / eng ADR-0008 #16) · CAP2 Load Test (plano pronto: #56) · Launch Gate.

## LAUNCH GATE (antes de deploy)
Segurança · Performance · Billing · Stripe · Meta · Dashboard · Backup · Monitoring ·
Alerting · Costs · Capacity · UX · QA.

## Bloqueios externos conhecidos
- PR #54 (WhatsApp Cloud/Stripe): **READY — BLOCKED BY META CREDENTIALS** (bug SMS). Não tocar.
- Infra atual: Supabase **Free** (ver CAP0) — não suporta launch; Pro+upsize no Launch Gate.

## Modo
Ao concluir EPIC: docs → orquestra → PR draft → (parar só em gate arquitetural, senão
próxima EPIC). Resposta por EPIC: concluída/branch/commit/PR/testes/riscos/próximos/NO MERGE-DEPLOY-APPLY.
