# Project Memory — PM2→PM9 (EPIC 1) — Implementação versionada (NÃO aplicada)

Branch `claude/project-memory-pm2-pm9`. **NO MERGE / NO DEPLOY / NO APPLY.**
Depende de **PM1** (PR #55) como contrato de domínio (aplicar PM1 antes de PM2).
Feature atrás do module registry (`project_memory` = `reserved`) até ao gate final.

## Mapa de fases → entregas
| Fase | Entrega |
|---|---|
| **PM2** Uploads/Storage | bucket privado `project-memory` (desenho; **não criado**), paths por tenant, validação server-side, signed URLs **curtas** (120s), sem service-role no frontend. Funções `project-memory-upload-url`, `-finalize-upload`, `-reference-url`. |
| **PM3** Quotas + usage | `project_quota_limits`, `project_usage_counters`, helpers `project_within_quota` / `project_bump_usage`. Quota verificada **antes** de emitir upload URL / gerar AI. |
| **PM4** Idempotência | finalização de upload idempotente (`project_upload_sessions.idempotency_key` unique); AI reports versionados (nunca sobrescreve). |
| **PM5** AI Reports | `project-memory-ai-report` — versionado, **opcional e degradável** (sem IA config/erro → não bloqueia, não grava, não consome versão/quota). |
| **PM6** Isolamento | prompts/relatórios/URLs só com dados da própria org: `assertOwnOrgOnly`, `pathBelongsToOrg`, verificação de propriedade em todas as funções. |
| **PM7** Feature flag | `project_memory` no module registry como `reserved` (OFF até gate). |
| **PM8** Testes | SQL `project_memory_pm2_verification.sql` + deno `projectMemory.test.ts` + (PM1) `project_memory_pm1_verification.sql`. |
| **PM9** Consolidação | este doc + custos + gate. |

## Inventário
**Migrations (não aplicadas):**
- `20260721000000_project_memory_pm2_uploads_quota.sql` — upload_sessions, quota_limits,
  usage_counters + RLS (SELECT por org; escrita só service_role; anon revogado) + helpers
  `project_within_quota`/`project_bump_usage` (SECURITY DEFINER, EXECUTE só service_role).
- (dependência) `20260720000000_project_memory_pm1.sql` (PR #55).

**Edge Functions (verify_jwt=true; NÃO deployed):**
- `project-memory-upload-url` — valida propriedade+mime+tamanho+quota → signed UPLOAD URL curta + sessão idempotente.
- `project-memory-finalize-upload` — idempotente; cria `project_references`, bumps usage, marca sessão, evento timeline.
- `project-memory-reference-url` — signed DOWNLOAD URL curta; isolamento duplo (linha da org + prefixo do path).
- `project-memory-ai-report` — AI opcional/degradável/versionada; input só da própria org.

**Shared:** `_shared/projectMemory.ts` (path builders, validação mime/size, TTL, versionamento, guardas de isolamento) + testes.

**Frontend:** `hooks/useProjectMemory.ts` (`useProjectFiles`, `useProjectMemory`: read RLS + upload/URL/AI via edge), `components/project-memory/ProjectMemoryPanel.tsx` (painel mínimo/scaffold), `lib/moduleRegistry.ts` (+`project_memory` reserved).

**Policies (novas, na migração PM2):** `project_upload_sessions_select`, `project_quota_limits_select`, `project_usage_counters_select` (todas `= get_user_org_id()`); escrita só service_role; `anon` REVOKE ALL.

**Testes:** deno `projectMemory.test.ts` (6 ✅: validação, paths, isolamento, versionamento). SQL PM2 (quota dentro/fora, usage acumula, AI mensal, upload idempotente, anon negado, grants). Correr SQL **pós-apply em staging**.

## Segurança (checklist do briefing)
- ✅ bucket privado + paths por tenant + validação server-side + signed URLs curtas + sem service-role no frontend.
- ✅ quotas + usage counters antes de uploads ilimitados.
- ✅ idempotência (upload finalize + AI versionado).
- ✅ AI reports versionados, nunca sobrescreve.
- ✅ AI opcional e degradável, não bloqueia o Project Memory.
- ✅ nenhum conteúdo de outro tenant em prompts/relatórios/URLs.
- ✅ feature atrás do module registry/flag (`reserved`).

## Custos (estimativa)
- **Storage:** Supabase Free 1 GB (para launch → Pro + upsize; ver CAP0). Quota por org
  (default 5 GB) evita abuso; contabilizado em `project_usage_counters.storage_bytes`.
- **Edge invocations:** upload = 2 chamadas (url+finalize) + N download-url; Free 500K/mês.
- **AI:** provider externo pay-per-use (opcional; `PROJECT_AI_API_*`). Quota
  `max_ai_reports_per_month` (default 200/org) limita custo. Degrada a custo zero se off.
- **Signed URLs / DB:** desprezável.

## Gate (parar aqui)
Aplicar PM1+PM2, **criar o bucket privado `project-memory`**, definir `PROJECT_AI_API_*`
(opcional), flipar `project_memory` para `active`, deploy das 4 funções, correr os testes
SQL em staging — **tudo no gate final da EPIC 1**. NÃO iniciar Interactive Tattoo Briefing
antes da aprovação completa. **NO MERGE / NO DEPLOY / NO APPLY.**
