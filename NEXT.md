# NEXT

Status: ACTIVE
Date: 2026-07-09
Mission: **Finish line — comercialização.** Levar o produto a estado vendável:
WhatsApp Cloud API (oficial) ponta-a-ponta + billing Stripe. Victor investiu 1 ano;
foco em terminar, sem baixar a fasquia de segurança (é AGORA que ela mais importa).

**Modo operacional (decidido por Victor 2026-07-09):** (1) BATCH APPROVALS — o auto-mode
do Claude Code gateia CADA mudança de prod (migração/deploy/dados); Claude agrupa e Victor
dá poucos "aprovado". Não é alterável na orquestra (é permissão do Claude Code). (2) CLAUDE
assume TAMBÉM o frontend (React) desta fase — onboarding do canal WhatsApp, gestão de
templates, UI de billing. Antenor liberto para outras frentes.

**Estado sequência (2026-07-09):** C1 ✅ C2 ✅ C3 ✅ (deployed+validado) · C4 em curso · C5 · EPIC D (design).
Bloqueios de input de Victor: App Secret Meta (+subscrever `messages`); decisões+chaves Stripe.
Bloqueio externo: token produção Meta (bug FB SMS).

## UPDATE 2026-07-17 (Claude — cutover para número de produção)
Estado verificado factualmente via Graph API + logs + DB (não via checklist do dashboard):
- **Número de PRODUÇÃO ligado:** +41 78 261 98 19 "rtz tattoo studio" está VERIFIED e em
  CLOUD_API; canal criado e ligado no CRM (whatsapp_channels, token no Vault) em 2026-07-17.
  O bloqueio antigo do token/SMS foi ultrapassado. Checklist "Register number" do dashboard
  Meta está por marcar mas é cosmético — a API confirma o registo.
- **App Secret + verify token no Vault ✅; campo `messages` subscrito ✅; WABA do número real
  subscrita à app ✅; callback URL correto e ativo ✅** (tudo confirmado via API).
- Dossier App Review: páginas legais EN (Privacy/Data Deletion/ToS) publicadas + ícone 1024 ✅.
- **BLOQUEIO ATIVO: a app Meta continua em Development mode (Unpublished)** → a Meta NÃO
  entrega webhooks de números de produção (só do número de teste). Teste real de inbound
  (2026-07-17) confirmou: mensagem enviada, zero entregas no webhook. **Ação de Victor:
  passar a app para Live mode** (pré-requisitos legais já cumpridos) → retestar inbound.
- Sequência após Live: CHECKPOINT enviar+receber real pelo CRM → Business Verification →
  App Review (`whatsapp_business_messaging` + `whatsapp_business_management`) → merge PR #70
  (limpeza Cloud-only) → Stripe (EPIC D, aguarda decisões).

## Estado atual (factos)
- EPIC C (WhatsApp Cloud) — **C1/C2/C3a DONE + DEPLOYED + VALIDADO** (branch `claude/whatsapp-cloud-api-c1`, PR #54):
  - `whatsapp_channels` + `whatsapp_templates` (RLS por org), token no Vault.
  - `whatsapp-connect-channel` (BYON manual, verify na Graph, token no Vault). deployed.
  - `whatsapp-cloud-webhook` (handshake + X-Hub-Signature-256 + logging). deployed, **verificado na Meta (✓ verde)**.
  - Envio validado com número de teste (`hello_world` entregue). Vault round-trip provado.
- **Bloqueio externo:** Facebook não envia SMS p/ gerar o **token permanente de produção**
  → número real não liga já. NÃO bloqueia o backend: tudo se valida com o número de teste;
  o número real liga depois pela função já pronta. App ainda **Unpublished** (só test webhooks).

## Regra de execução (ambos os agentes)
IMPLEMENTATION FIRST. Uma tarefa não está completa se só produziu auditoria/plano.
Ou muda código + commit + PR + checks + report, ou marca BLOCKED com o gate exato.
Uma mudança de cada vez (protocolo de mudanças isoladas). deno check / build antes de report.

## Gates (só Victor)
- Merge, deploy de edge function, apply de migração, mutação de dados de produção.
- **Stripe / billing / chaves** — gate reforçado (decisões de pricing + chaves).
- Publicar a app Meta / App Review.

## Claude (backend/plataforma/segurança) — sequência até à linha de chegada
1. **C3b — persistência inbound:** o `whatsapp-cloud-webhook` passa a criar/atualizar
   contacto + `whatsapp_chats` + `whatsapp_messages` (dedup por telefone/wa_id) e a
   disparar automação `whatsapp_message_received`. Provider = 'cloud_api'. Nunca cria deal.
2. **C4 — outbound Cloud + janela 24h:** rotear o envio pelo provider layer. Se a org tem
   canal Cloud ativo → `cloudApiProvider` (texto dentro de 24h; fora → template). Evolution
   permanece fallback. Não partir o caminho Evolution existente. Gate de deploy.
3. **C5 — registry de templates:** sync dos templates aprovados (Graph) para
   `whatsapp_templates`; automações fora da janela usam template + mapeamento de variáveis.
4. **BILLING backend (novo EPIC D) — DESIGN primeiro (ADR-0006):** modelo de planos/
   entitlements + `stripe-webhook` (assinado) + checkout/session edge functions. NADA
   implementado sem decisões de Victor (planos, preços, chaves de teste). Gate reforçado.
5. Unificar `whatsapp-send` sob o provider (descontinuar caminho hardcoded Evolution).

## Antenor (frontend/produto) — em paralelo
1. **C6 — Onboarding de canal WhatsApp:** UI em Settings para ligar número Cloud (colar
   phone_number_id/waba_id/token → chama `whatsapp-connect-channel`), estado do canal,
   nunca mostrar/guardar token no cliente.
2. Conversas/Mensagens: garantir que a UI de chat funciona com mensagens `provider='cloud_api'`.
3. Gestão de templates (lista + estado aprovado/pendente) sobre `whatsapp_templates`.
4. **Billing UI (EPIC D):** planos, seats, estado de subscrição, checkout — UI-only até o
   backend Stripe existir; sem chaves no frontend.

## Bloqueios/decisões pendentes de Victor
- Token de produção Meta (bug FB SMS) — externo; retomar quando destravar.
- Decisões Stripe (ADR-0006): tabela de planos + preços, modo (test/prod), chaves.
- App Review Meta: só permissões `whatsapp_business_*` (as 10 de Facebook Login foram engano).

Output: status curto — branch, commit, PR, report path, blockers, next step.
