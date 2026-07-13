# KEYROS GATES — FRAMEWORK V1

O agente para e aguarda Victor antes de qualquer ação abaixo.

## Gate 1 — Merge

- merge de PR;
- fechamento de branch com integração na main.

## Gate 2 — Deploy

- deploy de frontend;
- deploy ou redeploy de Edge Function;
- alteração de configuração remota que mude comportamento operacional.

## Gate 3 — Banco e Storage

- aplicar migration;
- alterar RLS, grants, policies, funções SQL ou triggers remotamente;
- criar, remover ou alterar bucket;
- executar backfill, limpeza ou alteração de dados reais.

## Gate 4 — Produção

- qualquer escrita em produção;
- testes destrutivos ou de carga contra produção;
- mudança de domínio, DNS, OAuth, webhook ou callback de produção.

## Gate 5 — Segredos e contas externas

- criar, trocar, revogar ou expor segredo;
- configurar credenciais Meta, Stripe, Supabase ou outro fornecedor;
- submeter App Review, Business Verification ou ação externa irreversível.

## Gate 6 — Arquitetura e escopo

- nova arquitetura não decidida em ADR;
- alteração de contrato multi-tenant;
- alteração de modelo de autorização;
- nova EPIC, fase ou feature não descrita em `CURRENT.md`.

## Gate 7 — Segurança crítica

Ao encontrar risco Critical ou High comprovado, o agente:

1. não publica detalhes exploráveis em repositório público;
2. registra evidência no repositório privado apropriado;
3. interrompe somente a parte afetada;
4. informa Victor com impacto e ação mínima necessária.

## Regra de saída

Ao atingir um gate, o agente entrega:

- estado atual;
- evidência;
- ação bloqueada;
- decisão exata necessária de Victor.

Não inicia a etapa seguinte.
