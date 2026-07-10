# Security Remediation — PR #54 (easytattoo-crm)

Data: 2026-07-10 · Autor: Claude / Fable
Branch: `claude/whatsapp-cloud-api-c1` · Commit: `ea65078`
Escopo: **exclusivamente** os 10 bloqueios de segurança. Sem features novas.
**NO DEPLOY · NO MIGRATION APPLY · NO MERGE.**

## Arquivos alterados + risco corrigido

| Arquivo | Risco corrigido |
|---|---|
| `supabase/migrations/20260716000000_whatsapp_channels_rls_hardening.sql` (novo) | **B1/B2** — helpers `get_user_org_role`/`is_org_admin`; RLS por operação; revoga `access_token_secret_id` do cliente |
| `supabase/migrations/20260715000000_billing_stripe.sql` (editado, não aplicado) | **B4/B5** — tabela `stripe_webhook_events` (idempotência); estados de subscrição completos |
| `supabase/functions/_shared/stripeWebhook.ts` (novo) | **B3/B5** — verificação de assinatura+timestamp; mapeamento de estados; puro/testável |
| `supabase/functions/_shared/stripeWebhook.test.ts` (novo) | **B10** — 10 testes unitários |
| `supabase/functions/stripe-webhook/index.ts` | **B3/B4/B5** — anti-replay (300s), idempotência, máquina de estados, fail-closed |
| `supabase/functions/create-checkout-session/index.ts` | **B8** — role owner/admin; allowlist de plano + de URLs; erros genéricos |
| `supabase/functions/whatsapp-connect-channel/index.ts` | **B1/B6** — role owner/admin; correlation_id; HTTP correto; sem vazamento; sem 200-em-erro |
| `supabase/functions/whatsapp-sync-templates/index.ts` | **B1/B6** — idem |
| `supabase/tests/security_pr54_verification.sql` (novo) | **B10** — script de RLS/role (pós-apply) |

## Migrations adicionadas/alteradas (NÃO aplicadas)
1. `20260716000000_whatsapp_channels_rls_hardening.sql` — NOVA.
2. `20260715000000_billing_stripe.sql` — editada (ainda nunca aplicada em prod):
   `+stripe_webhook_events`, estados de subscrição expandidos.

## Policies finais (whatsapp_channels / whatsapp_templates)
- `*_select` — `FOR SELECT USING (organization_id = get_user_org_id())`.
- `*_insert` — `FOR INSERT WITH CHECK (organization_id = get_user_org_id() AND is_org_admin())`.
- `*_update` — `FOR UPDATE USING (...admin) WITH CHECK (...admin)`.
- `*_delete` — `FOR DELETE USING (...admin)`.
- `whatsapp_channels`: `REVOKE SELECT ... FROM anon, authenticated` + `GRANT SELECT (colunas não-sensíveis) TO authenticated` → `access_token_secret_id` inacessível ao cliente; `anon` sem acesso.
- Papéis privilegiados: `owner`, `admin`. `staff`/outros = leitura apenas.

## Matriz verify_jwt (a confirmar no deploy)
| Função | verify_jwt | Proteção própria |
|---|---|---|
| whatsapp-connect-channel | **true** | JWT + role owner/admin |
| whatsapp-sync-templates | **true** | JWT + role owner/admin |
| create-checkout-session | **true** | JWT + role owner/admin + allowlists |
| whatsapp-cloud-webhook | **false** | X-Hub-Signature-256 (HMAC), fail-closed sem App Secret |
| stripe-webhook | **false** | Stripe-Signature + timestamp 300s, fail-closed sem secret |

Nenhuma função pública aceita `organization_id` do body como fonte de autorização.

## Testes executados
- `deno test _shared/stripeWebhook.test.ts` → **10/10 OK**:
  - Stripe: assinatura válida (F), inválida (F), timestamp antigo→rejeitado (G),
    sem secret (N), sem header, malformado, múltiplos v1, mapeamento de estados (I/J),
    isPaidAccess só active/trialing.
  - Meta: X-Hub-Signature-256 válida/inválida (E).
- `deno check` nas 5 funções → OK.
- `npx tsc --noEmit` (frontend) → **0 erros**.

### Testes NÃO executados agora (motivo documentado)
- RLS/role isolamento (A, B, C, D, L): script `supabase/tests/security_pr54_verification.sql`
  pronto, mas **exige as migrações aplicadas** (proibido aplicar em prod nesta ronda)
  + fixtures de `auth.users`. Correr em **staging/pós-gate** (transação com ROLLBACK).
- Idempotência (H), estados end-to-end (I/J via webhook real), price allowlist (K),
  verify_jwt (M), fail-closed público (N em runtime): exigem funções **deployed** +
  migrações aplicadas. A lógica pura está coberta por deno tests; o resto valida-se
  no smoke pós-deploy.

## Itens pendentes / observações
- **Pré-existente (fora do escopo):** `whatsapp-send:418` (caminho Evolution) regista o
  body cru da resposta Evolution. Não introduzido pelas features do PR #54; deixado
  intacto para não fazer refactor amplo nem mexer na compatibilidade Evolution.
  Recomendo follow-up separado para truncar/remover esse log.
- Deploy de `stripe-webhook` deve incluir `_shared/stripeWebhook.ts` no bundle.
- UX: o hook frontend mostra mensagem genérica em erro (códigos estáveis do backend);
  polir a UI é follow-up, não segurança.

## Confirmação
**NADA foi deployed, aplicado em produção, ou merged.** Só código na branch + PR #54.
Aguarda nova revisão do ChatGPT/Victor.
