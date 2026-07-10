# CAP0 — Infrastructure Inventory (Keyros / easytattoo-crm)

Data: 2026-07-10 · Autor: Claude / Fable · Orquestra #4
Branch: `claude/launch-capacity-cap0-cap1`
Método: introspeção real (Supabase MCP + SQL read-only + código). Sem alterar infra.
Classificação: **CONFIRMED** · **INFERRED** · **UNKNOWN** · **REQUIRES DASHBOARD ACCESS**.

## 1. Supabase — projeto e plano
| Item | Valor | Classe |
|---|---|---|
| Projeto | `xqifnqrvcnpxkxftycvf` "easytattoo crm" | CONFIRMED |
| Região | `eu-west-1` | CONFIRMED |
| Postgres | 17.6.1.127 (engine 17, GA) | CONFIRMED |
| **Plano da org** | **FREE** (`bugdvpgwxzokthkefkxf`) | **CONFIRMED** |
| Compute size | Free → **Nano** (shared CPU, ~0.5–1 GB RAM) | INFERRED (Free implica Nano) |
| **max_connections** | **60** (3 reservadas → ~57 úteis) | CONFIRMED |
| Pooler | Supavisor (transaction/session) disponível; tamanho do pool | REQUIRES DASHBOARD |
| Tamanho do banco | **104 MB** (limite Free 500 MB) | CONFIRMED |
| Pausa por inatividade | Free pausa após ~7 dias sem uso | CONFIRMED (política Free) |
| Backups / PITR | Free **não** tem PITR nem backups diários geridos | CONFIRMED (política Free) |

## 2. Banco — tabelas maiores / dados atuais (uso ~1 estúdio)
`webhook_logs` 14 MB/7427 · `whatsapp_messages` 2.4 MB/4528 · `contacts` 1 MB/1836 ·
`rate_limits` 792 kB/1340 · `whatsapp_contacts` 728 kB/1833 · `whatsapp_chats` 72 ·
`deals` 11 · `appointments` 10. **Volume real ≈ 1 tenant ativo.** (CONFIRMED)
- `webhook_logs` é a tabela que mais cresce (logs Evolution) — retenção 7 dias por limpeza oportunista. Candidata a pressão de escrita/tamanho sob carga.

## 3. Índices em tabelas críticas
`whatsapp_messages` unique(org,instance,message_id) + índices; `whatsapp_chats`
unique(org,instance,remote_jid); `contacts` (org, phone, remote_jid); `rate_limits`
(key,action,window_start). (CONFIRMED — ver migrations) · Cobertura fina para queries
de alto volume = **REVER sob carga** (advisor de performance recomendado).

## 4. Realtime (postgres_changes) — por ecrã
Subscrições WebSocket abertas por utilizador com a app aberta (CONFIRMED via código):
`useContacts` (contacts), `useDeals` (deals + pipeline_stages), `useAppointments`
(appointments), `useAutomations` (automations), `useLeads` (leads), `useMockData`
(crm-realtime: appointments INSERT/UPDATE/DELETE). **≈ 5–7 canais/utilizador.**
- Free plan: **200 conexões Realtime concorrentes**, 2M mensagens/mês. → ~30 utilizadores
  simultâneos aproximam o teto de conexões Realtime. **Constraint binding provável.** (INFERRED)

## 5. Edge Functions (23 ACTIVE) — verify_jwt
- **verify_jwt=true:** send-whatsapp, twilio-webhook, automation-execute, whatsapp-sync,
  appointment-api, **whatsapp-connect-channel** (PR#54, v1).
- **verify_jwt=false:** whatsapp-proxy, whatsapp-webhook (v43), whatsapp-send (v18),
  automation-scheduler, landing-lead, setup-org-storage, sync-whatsapp-history,
  automation-engine, lead-intake, automation-retry, automation-bulk-send,
  automation-campaign-control, **whatsapp-cloud-webhook** (PR#54, v3).
- **Stubs 410 ainda deployed (ACTIVE):** debug-api (v16), diagnostic (v11),
  debug-whatsapp (v11) — código é stub 410, mas continuam listadas. (CONFIRMED)
- Free plan: **500K invocações/mês**, timeout wall ~150s (CPU limitado). (INFERRED)
- **Nota:** o PR#54 (Cloud/Stripe) **não** está deployed; whatsapp-send deployed = v18 (Evolution).

## 6. Resiliência / limites configurados (código)
- **Rate limits** (`_shared/rateLimiter.ts`): whatsapp_send 20/min/org; webhook_receive
  120/min/instância; landing_lead 5/min; bulk_campaign 10/min; campaign_control 10/min. (CONFIRMED)
- **Circuit breaker** (`_shared/circuitBreaker.ts`): failureThreshold 5, timeout 10s,
  resetTimeout 60s, escopo por org. (CONFIRMED)
- **Retenção oportunista:** rate_limits 7d, system_events 30d, webhook_logs 7d. (CONFIRMED)

## 7. Schedulers / background jobs
- **1 cron** (`cron.job` id 2, `* * * * *`, ativo) → `net.http_post` p/ automation-scheduler.
  Único job. Retry via automation-retry (invocado). (CONFIRMED)
- Não há fila dedicada (pgmq disponível mas não usada). (CONFIRMED)

## 8. Storage
- Buckets: `avatars` (público), `studio-files` (privado). Free: **1 GB** storage, 50 MB/upload. (CONFIRMED)
- Project Memory (PM1) planeia bucket dedicado `project-memory` (ainda não criado). (CONFIRMED)

## 9. Frontend / edge topology
- Cloudflare Pages (`easytattoo.site`), build no push (CLAUDE.md). Bandwidth/builds/
  Workers = REQUIRES DASHBOARD ACCESS. (INFERRED do CLAUDE.md)

## 10. Monitoramento / observabilidade
- Supabase logs (retenção **24h** no Free). `system_events`, `audit_logs`,
  `system_health_logs`, `circuit_breaker_state` existem. (CONFIRMED)
- **Gaps:** sem APM/p95 latency, sem métricas de saturação de pool, sem tracing,
  sem alertas. **Ponto cego de observabilidade sob carga.** (CONFIRMED — gap)

## 11. Quotas externas
| Provider | Limite | Classe |
|---|---|---|
| Supabase Free | 500MB DB / 60 conn / 200 Realtime / 2M msg / 500K edge / 1GB storage / 2GB egress | CONFIRMED (política Free) |
| Meta WhatsApp Cloud | messaging tier (250→1K→10K→100K/dia por número, por qualidade); 80 msg/s | INFERRED (docs Meta); PR#54 bloqueado por credenciais |
| Evolution API (atual) | self-hosted; quota/host/CPU | UNKNOWN / REQUIRES ACCESS |
| Stripe | ~100 req/s (live); webhooks retry | INFERRED (docs) |
| Email | sem provider ainda | CONFIRMED (n/a) |
| IA | sem provider ainda | CONFIRMED (n/a) |

## 12. UNKNOWN / REQUIRES DASHBOARD ACCESS (para fechar CAP0)
1. Compute exata + RAM/CPU (Nano assumido).
2. Tamanho do pool Supavisor + modo (transaction/session).
3. Egress/bandwidth e uso de invocações de Edge Function no mês.
4. Contagem de mensagens Realtime/mês vs teto 2M.
5. Config de backup/PITR (Free = nenhum gerido — confirmar).
6. Cloudflare Pages: bandwidth, builds, limites de Workers/Functions.
7. Evolution API: host, CPU, limites, uptime.
8. Meta WhatsApp: tier de messaging real do(s) número(s) (após desbloqueio).

## Conclusão CAP0
O ambiente atual é **Free tier, single-studio**. Os **constraints binding** para
lançamento são, por ordem provável: **(1) conexões Realtime (200)**, **(2) conexões
DB (60)**, **(3) compute Nano partilhado**, **(4) invocações Edge (500K/mês)**,
**(5) egress 2GB**. **Free não suporta um lançamento multi-tenant** — CAP2+ exigirá
Pro + upsize de compute + pooler dimensionado. Número de utilizadores suportado =
**UNKNOWN até load test em staging** (ver CAP1 + test plan).
