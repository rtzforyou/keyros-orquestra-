# RC2a — Embedded Signup + Completion (Meta Cloud, Independent Tech Provider)

- **EPIC:** RC2a — completion self-service do WhatsApp Cloud (pré-requisito do App Review / vídeo).
- **Agent:** Claude
- **Repo (privado):** rtzforyou/easytattoo-crm
- **Branch:** `claude/rc2a-embedded-signup` (base `origin/main` `71cf3fe`)
- **Commits:** `49912d7` (discovery), `7c783ce` (código)
- **PR (draft):** rtzforyou/easytattoo-crm#65
- **Regras:** só Meta Cloud · NO PROD DEPLOY · NO APPLY · NO MERGE · Evolution não removido · Stripe/Project Memory/Landing/KCC/Partner intocados.

## Estado global: CODE_READY (E2E real com a Meta NÃO executado)

## Discovery (antes de código)
`docs/rc2/RC2_DISCOVERY.md`: estado remoto lido read-only. FATOS: tabela `whatsapp_channels` sem `business_id/granted_scopes/onboarding_metadata`; `provider` CHECK só `cloud_api/evolution`; `status` CHECK sem `awaiting_payment_method/revoked`; UNIQUE(phone_number_id) já existe; Vault só tem `whatsapp_app_verify_token` (faltam App Secret + PIN secret); `whatsapp-embedded-signup`/`sync-templates` não deployed.

## Implementado
- **Completion** `whatsapp-embedded-signup` (nova fn): auth+org+owner/admin (staff→403); code→token (App ID env + App Secret Vault); debug_token + waba + phone_numbers (revalida hints); register com PIN determinístico (Vault); subscribed_apps; token só no Vault; upsert idempotente (UNIQUE phone; cross-tenant→409); audit. Token/PIN nunca em resposta/log.
- **Helpers** `_shared/metaEmbeddedSignup.ts` + 11 testes.
- **Revoke**: webhook trata `account_update` → status='revoked'.
- **Migração** `20260724`: provider meta_cloud + status awaiting_payment_method/revoked + colunas + RLS owner/admin.
- **Frontend** `WhatsAppMetaConnect.tsx`: botão owner/admin, FB Login for Business (config_id, response_type=code), state machine completa, popup/mobile, awaiting_payment_method com link ao Business Manager.

## Testes executados
tsc PASS · build PASS · `deno test _shared` 51/0 (11 novos) · deno check OK.
12 testes da missão: PASS com evidência = 5 (UNIQUE via SQL), 7/8/9 (grep token/PIN), 11/12 (unit). NOT_VERIFIABLE (requer deploy+secrets+app live+conta Victor) = 1,2,3,4,6,10 — com o código que garante cada um.

## External blockers
Secrets `META_APP_SECRET`+`META_PIN_SECRET` (Vault); envs `WHATSAPP_APP_ID`/`VITE_META_APP_ID`/`VITE_META_CONFIG_ID`; deploy da fn + redeploy webhook + apply migração 20260724; subscrever `messages`+`account_update`. HIPÓTESE a confirmar live: campo exato de método de pagamento e estrutura do account_update.

## Riscos / não verificado
- `whatsapp-send` filtra `provider='cloud_api'`; para canais `meta_cloud` enviarem, esse filtro precisa aceitar ambos (fora do GATE de RC2a = "ver conectado"; recomendado antes de mensagens fluírem).
- Prontidão de envio (payment method) é HIPÓTESE quanto ao campo Graph — degradação honesta (nunca "conectado" sem sinal positivo).

**Parar após RC2a — não iniciar RC2b. NO MERGE / NO PROD DEPLOY / NO APPLY.**
