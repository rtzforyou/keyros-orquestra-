# Launch Capacity — Load Test Plan (PREPARADO, NÃO EXECUTADO)

Data: 2026-07-10 · Autor: Claude / Fable · Orquestra #4
Ferramenta: **k6** (scripts em `load-tests/` do produto — PR draft).
**REGRA: nenhum teste corre contra produção.** Só em **staging** (projeto Supabase
separado / branch de DB) e com **gate próprio**. Sem deploy, sem dados massivos em prod.

## Estágios (ramp de VUs concorrentes)
25 → 50 → 100 → 250 → 500 → 1000
- Cada patamar: ramp-up 1–2 min, sustain 3–5 min, observar, avançar só se dentro dos
  critérios de paragem. Abortar no primeiro patamar que viole um critério.

## Critérios de paragem (abortar/registar limite)
| Métrica | Limiar de paragem (proposto — ajustar em staging) |
|---|---|
| Error rate (HTTP) | > 1% |
| p95 read | > 400 ms |
| p95 write | > 800 ms |
| Database CPU | > 80% sustentado |
| Memory | > 85% |
| DB connections | > 80% do pool (Supavisor) |
| Pool saturation | qualquer `no available connection` |
| Edge Function errors | > 1% ou timeouts |
| Queue/scheduler delay | atraso do cron/retry > 60 s |
| Realtime disconnects | qualquer desconexão/queda de subscrição sob carga |
| Webhook ack latency | p95 > 1 s (Meta/Stripe re-tentam) |

## Cenários funcionais a testar (fases posteriores, staging)
- **RLS sob concorrência** — isolamento tenant A/B mantém-se com N VUs.
- **Idempotência** — `stripe_webhook_events` e `idempotency_key` (Project Memory) sob replays paralelos.
- **Rate limiter** — whatsapp_send 20/min/org sob burst (não vaza acima do limite).
- **Circuit breaker** — provider externo degradado → abre a 5 falhas, recupera a 60s.
- **WhatsApp burst** — inbound webhook 120/min/instância; outbound fila.
- **Automation burst** — muitos triggers `whatsapp_message_received` em simultâneo.
- **Stripe webhook replay** — mesmo `event_id` repetido → efeito único.
- **Project Memory writes** — criação de project files + references + notes concorrentes.

## Perfis de carga (mapear aos VUs — ver CAP1)
- Read-heavy (navegação dashboard/pipeline/inbox) — `k6-read-heavy.js`.
- Write-mixed (mover deals, criar notas, responder) — a adicionar em CAP2.
- Realtime-fanout (medir conexões WebSocket vs teto) — a adicionar em CAP2.

## Métricas a capturar
- k6: `http_req_duration` (p50/p95/p99), `http_req_failed`, VUs, iterações.
- Supabase (dashboard/staging): DB CPU/RAM, conexões ativas, pool, Realtime conns,
  Edge invocations/erros, egress. **Requer acesso ao dashboard de staging.**

## Pré-requisitos (bloqueiam a execução — CAP2 gate)
1. Projeto Supabase de **staging** (Pro, para métricas + PITR + sem pausa).
2. Seed de dados representativo por perfil (SOLO/STUDIO/COLLECTIVE) — sem tocar prod.
3. Acesso a métricas (dashboard/observabilidade) para os critérios de paragem.
4. Token de teste + endpoints de staging nos scripts (`BASE_URL`, `ANON_KEY`).

## Confirmação
**NO PROD LOAD TEST · NO DEPLOY · NO MERGE.** Scripts preparados e versionados;
execução aguarda staging + gate separado (CAP2).
