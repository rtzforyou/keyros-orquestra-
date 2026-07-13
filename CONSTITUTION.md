# KEYROS CONSTITUTION — FRAMEWORK V1

## 1. Autoridade

Victor é a única autoridade para definir prioridades, aprovar gates, alterar esta Constituição e alterar GATES.md.

## 2. Fonte do trabalho

O agente lê somente:

1. `CONSTITUTION.md`;
2. `GATES.md`;
3. `rtzforyou/keyros-engine/CURRENT.md`;
4. os ADRs explicitamente citados em `CURRENT.md`;
5. os ficheiros de produto necessários para executar a missão ativa.

O agente não usa arquivos arquivados como instrução atual.

## 3. Regra de execução

O agente executa apenas a missão ativa descrita em `CURRENT.md`.

Não cria novas EPICs, fases, roadmaps, requisitos ou funcionalidades.

Não altera o escopo por iniciativa própria.

Só interrompe por:

- gate definido em `GATES.md`;
- risco crítico comprovado;
- informação indispensável ausente;
- conflito técnico que torne a execução insegura.

## 4. Evidência

Todo estado deve ser classificado como:

- VERIFIED;
- FAILED;
- NOT_VERIFIABLE;
- EXTERNAL_BLOCKER.

O agente não declara algo implementado, testado, aplicado, deployed ou operacional sem evidência.

## 5. Mudanças

Cada missão usa branch própria e PR draft, salvo instrução explícita de Victor.

Mudanças devem ser pequenas, isoladas, testáveis e reversíveis.

Nenhum agente escreve neste repositório.

## 6. Relatórios

Ao concluir uma missão, o agente grava um relatório append-only em `rtzforyou/keyros-engine/log/`.

Agentes não leem o diretório `log/` para decidir o próximo trabalho.

## 7. Proibições

Sem autorização explícita de Victor, o agente não:

- faz merge;
- faz deploy;
- aplica migrations;
- altera produção;
- modifica segredos;
- expande o roadmap;
- inicia a missão seguinte.

## 8. Precedência

Em caso de conflito:

1. instrução direta mais recente de Victor;
2. `GATES.md`;
3. `CONSTITUTION.md`;
4. `keyros-engine/CURRENT.md`;
5. ADR decidido;
6. documentação do produto.
