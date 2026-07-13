# RC2 — Meta Cloud API: Embedded Signup (implementação)

- **EPIC:** RC2 — Meta Cloud API self-service para beta fechado.
- **Agent:** Claude (backend/plataforma/frontend desta fase)
- **Repo (privado):** rtzforyou/easytattoo-crm
- **Branch:** `claude/rc2-meta-embedded-signup` (base `origin/main` `71cf3fe`)
- **Commit:** `cfa87ed`
- **PR (draft):** rtzforyou/easytattoo-crm#64
- **Regras:** só Meta Cloud (Stripe/Project Memory/Landing/KCC/Partner intocados) · NO DEPLOY · NO APPLY · NO MERGE · sem refactor fora de escopo.

## Implementado (código)
- **Embedded Signup ponta-a-ponta** (fecha M5): frontend FB JS SDK + `FB.login(config_id, response_type=code)` → envia só o `code`; backend `whatsapp-connect-channel` (modo `code`) troca `code`→business token na Graph com App ID (env) + App Secret (Vault), subscreve app à WABA, regista número, guarda token no Vault, associa à org da sessão. Modo manual (BYON) inalterado.
- **Business ID**: migração `20260723` adiciona coluna `business_id`; connect-channel captura-o (sessionInfo ou `owner_business_info`).
- **Helpers puros** `_shared/embeddedSignup.ts` + 12 testes.
- **Frontend**: `WhatsAppEmbeddedSignup.tsx`, `useWhatsAppChannels.connectViaEmbeddedSignup`, wire no `WhatsAppCloudConfigDialog`. Config pública `VITE_META_APP_ID`/`VITE_META_CONFIG_ID`.

## Validado (não reimplementado)
Webhook + HMAC + inbound + status(sent/delivered/read) + idempotência + janela 24h + templates + multi-tenant + owner/admin (já existiam, deployed exceto sync-templates).

## Testes executados
- build PASS · tsc PASS · `deno test _shared` **52/0** (12 novos) · `deno check` OK (connect-channel/cloud-webhook/send).
- **E2E real com a Meta NÃO executado** — bloqueado (app Live + Config ID + App Secret no Vault + número real). Não se afirma validação E2E.

## Deploys necessários (gate)
Redeploy `whatsapp-connect-channel` (v2), deploy `whatsapp-sync-templates` (404), confirmar `whatsapp-send` (branch Cloud), build frontend com envs Meta.

## Secrets/config necessários
`WHATSAPP_APP_ID` (env), `whatsapp_app_secret` (Vault), `VITE_META_APP_ID`+`VITE_META_CONFIG_ID` (frontend público). Meta: app Live + Advanced Access `whatsapp_business_*` + ES Configuration (Config ID) + subscrever `messages`.

## Blockers restantes
M1 (App Secret no Vault) · M2 (subscrever `messages`) · M3 (deploy sync-templates) · RC2-A (app Live + App Review + Config ID — externo Meta) · RC2-B (aplicar migração business_id) · M6 (token produção — externo).

## Riscos / não verificado
- Deploy do outbound Cloud (C4) por confirmar.
- Registo do número (`/register`) é best-effort; se a WABA tiver 2FA PIN, pode exigir PIN — a UI aceita PIN opcional.
- `whatsapp-send` deployado usa envio Cloud inline (não o `_shared/cloudApiProvider`); duplicação pré-existente não tocada (fora de escopo).

**Parar após RC2 — não iniciar RC3. NO MERGE / NO DEPLOY / NO APPLY.**
