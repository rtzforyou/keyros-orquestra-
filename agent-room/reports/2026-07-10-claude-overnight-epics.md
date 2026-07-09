# Report — Claude overnight (2026-07-09 → 07-10): código de todas as epics

Modo: autónomo (Victor a dormir). Pedido: terminar o código de todas as epics;
"ignorar hard-gates". **Decisão de integridade:** não fiz bypass do guardrail do
Claude Code — NÃO deployei nem apliquei migrações em produção sem supervisão (um
bug no envio ao vivo partiria o WhatsApp de orgs reais). Entreguei TUDO em código
no PR #54, com build verde, + checklist de aprovação para de manhã.

## Entregue (branch claude/whatsapp-cloud-api-c1, PR #54)
- **C4** — outbound WhatsApp Cloud + janela 24h no `whatsapp-send` (branch Cloud;
  Evolution intacto). commit b7cfef8. deno check OK.
- **C5** — `whatsapp-sync-templates` + envio de template. commit 2c542b2. deno check OK.
- **EPIC D** — billing/Stripe: migração draft + `stripe-webhook` + `create-checkout-session`.
  commit ab21fd5. ADR-0006 (engine, commit 50161ce). deno check OK.
- **Frontend** — hooks `useWhatsAppChannels`/`useWhatsAppTemplates`, dialog de
  onboarding Cloud (token→Vault), wiring em IntegrationsSettings. commit b1c798c.
  **tsc 0 erros + `npm run build` OK.**

## NÃO feito (propositadamente — gates de prod, batch de manhã)
1. Aplicar migração `20260715000000_billing_stripe.sql`.
2. Deploys: `whatsapp-send`, `whatsapp-sync-templates`, `stripe-webhook`
   (verify_jwt=false), `create-checkout-session`.
3. Meta App Secret → Vault + subscrever campo `messages`.
4. Stripe: 4 decisões (planos/preços/ciclo/seat) + chaves de teste + Products/Prices
   + seed `billing_plans`.
5. Teste de segurança do Victor.
6. Merge PR #54.

## Follow-ups menores conhecidos
- i18n: strings inline nos componentes novos (mover para useTranslation).
- Billing UI: ligar `BillingSettings` ao `create-checkout-session`.
- Cloud outbound não escreve nas tabelas legadas `conversations/messages` (só nova
  arquitetura) — reavaliar se algum ecrã legado precisa.

## Risco pendente
Drift repo↔prod fecha-se com o merge do PR #54 + deploys de manhã. Enquanto não
deployado, produção continua a usar Evolution (inalterado) — sem regressão.
