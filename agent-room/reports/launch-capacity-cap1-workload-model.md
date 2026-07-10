# CAP1 — Workload Model (Keyros / easytattoo-crm)

Data: 2026-07-10 · Autor: Claude / Fable · Orquestra #4
Base: CAP0 (plano Free, 60 conn, ~5–7 Realtime/utilizador). **Estimativas modeladas
(INFERRED), a validar por load test** — NÃO são um número de utilizadores garantido.

## Pressupostos de modelação
- 1 utilizador "ativo" com a app aberta mantém **~5–7 canais Realtime** (CAP0 §4).
- Uma sessão de trabalho gera leituras por navegação (dashboard, pipeline, inbox,
  contactos) e escritas por ação (mover deal, responder mensagem, criar nota).
- WhatsApp inbound/outbound passa por edge functions + webhook; rate limit
  whatsapp_send = 20/min/org (CAP0 §6).
- Storage/IA marcados quando o Project Memory / Cloud API estiverem ativos (hoje off).

## Perfis

### SOLO (1–2 utilizadores, 1 canal WhatsApp, CRM+agenda, uso moderado)
| Métrica | Estimativa | Classe |
|---|---|---|
| Logins/hora | 1–3 | INFERRED |
| Reads/min (pico de navegação) | 20–60 | INFERRED |
| Writes/min | 2–10 | INFERRED |
| Conexões Realtime | 5–14 | INFERRED |
| Mensagens WhatsApp/dia | 20–150 | INFERRED |
| Automações disparadas/dia | 5–50 | INFERRED |
| Uploads/dia (Project Memory) | 0–20 | INFERRED |
| Signed URLs/dia | 0–40 | INFERRED (PM2+) |
| Storage/mês | ~50–300 MB | INFERRED |
| Consumo IA | 0 (hoje) | CONFIRMED |

### STUDIO (5–10 utilizadores, inbox+pipeline simultâneos, automações, várias agendas)
| Métrica | Estimativa | Classe |
|---|---|---|
| Logins/hora | 5–20 | INFERRED |
| Reads/min | 150–600 | INFERRED |
| Writes/min | 20–80 | INFERRED |
| Conexões Realtime | 25–70 | INFERRED |
| Mensagens WhatsApp/dia | 200–1500 | INFERRED |
| Automações/dia | 100–1000 | INFERRED |
| Uploads/dia | 20–150 | INFERRED |
| Signed URLs/dia | 100–800 | INFERRED |
| Storage/mês | 1–10 GB | INFERRED |
| Consumo IA | moderado (futuro) | INFERRED |

### COLLECTIVE (10+ utilizadores, múltiplos canais, bursts, relatórios, IA+automações intensivas)
| Métrica | Estimativa | Classe |
|---|---|---|
| Logins/hora | 20–100+ | INFERRED |
| Reads/min | 600–3000+ | INFERRED |
| Writes/min | 80–400+ | INFERRED |
| Conexões Realtime | 70–200+ | INFERRED (aproxima teto Free) |
| Mensagens WhatsApp/dia | 1500–20000+ | INFERRED (tier Meta) |
| Automações/dia | 1000–10000+ | INFERRED |
| Uploads/dia | 150–1000+ | INFERRED |
| Signed URLs/dia | 800–8000+ | INFERRED |
| Storage/mês | 10–100+ GB | INFERRED |
| Consumo IA | intensivo (futuro) | INFERRED |

## Cenários
| Cenário | Descrição | Efeito dominante |
|---|---|---|
| **Normal** | horário comercial, navegação + respostas | Realtime + reads constantes |
| **Peak** | fim de tarde / promoção; vários artistas no inbox | writes + Realtime + whatsapp_send perto do rate limit |
| **Launch burst** | onboarding em massa (marketing) | logins + conexões DB/Realtime em rajada → **pool saturation risk** |
| **Degraded external provider** | Evolution/Meta/Stripe lento ou em erro | circuit breaker abre (5 falhas, 60s), filas/retry acumulam; webhooks re-tentam |

## Constraints binding (do modelo)
1. **Realtime (200 conn Free):** COLLECTIVE e um launch burst atingem o teto com
   ~30–40 utilizadores simultâneos (5–7 canais cada). **Primeiro limite a bater.**
2. **DB connections (60):** navegação + Realtime + edge functions competem; sem
   pooler bem dimensionado, launch burst satura. Supavisor (transaction) mitiga.
3. **Compute Nano partilhado:** p95 de reads/writes degrada cedo sob concorrência.
4. **whatsapp_send 20/min/org:** limita STUDIO/COLLECTIVE em peak de envio → fila.
5. **Edge 500K/mês + egress 2GB (Free):** COLLECTIVE excede facilmente.

## Capacidade
- **Confirmada:** o ambiente **Free** suporta com folga o perfil **SOLO** (uso atual).
- **Desconhecida (a medir):** número exato de utilizadores/tenants simultâneos para
  STUDIO/COLLECTIVE — depende de compute, pooler e Realtime reais, **não modeláveis
  sem load test**. Não é responsável afirmar um número agora.
- **Recomendação:** para lançamento, **Supabase Pro + upsize de compute (Small/Medium)
  + Supavisor dimensionado + Realtime quota Pro**; medir cada perfil em staging
  (ver test plan) antes de anunciar capacidade.
