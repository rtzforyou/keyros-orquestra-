# EPIC 0 — Product Polish (audit + fixes)

Estado: **executado** (Master Roadmap V1.0). Branch `claude/epic0-product-polish`.
Sem merge/deploy. Foundation FROZEN (só bugs críticos). 2026-07-10.

## Baseline (confirmado)
O Product Core está **limpo**: `npm run build` ✅ (3.1s), **sem** `console.log` em
componentes/hooks, **sem** TODO/FIXME/XXX, **sem** diálogos nativos (`alert/confirm/prompt`),
todos os `<img>` têm `alt`, `@ts-ignore` inexistente (só 2 `eslint-disable` de
`exhaustive-deps` **intencionais** em CreateAppointmentDialog e ContactMultiSelect).

## Fixes aplicados nesta EPIC (seguros, aditivos — só atributos, zero lógica)
Acessibilidade: `aria-label` em **botões-ícone** que tinham `title` (tooltip) mas eram
invisíveis a leitores de ecrã. Mirror do `title` existente:
- `messages/ChatHeader.tsx` — botão "voltar" (mobile).
- `messages/ConversationList.tsx` — toggle do terminal de diagnóstico.
- `settings/IntegrationsSettings.tsx` — botão de desligar integração (Power).
- `settings/LandingIntegrationsSettings.tsx` — toggle ativar/desativar (Power).
- `settings/StudioSettings.tsx` — botão copiar Location ID.

Verificado: `Button` reencaminha `{...props}` → `aria-label` chega ao DOM. `tsc` 0 erros,
`npm run build` ✅.

## Backlog de polish (NÃO feito agora — cada um merece um pass próprio, evita refactor amplo)
| Item | Porquê | Risco/scope |
|---|---|---|
| **A11y — rollout completo de `aria-label`** | 0 uso de aria-label no resto da app; icon-only buttons sem label. | Médio (muitos ficheiros) — fazer por área (pipeline, contacts, dashboard). |
| **Bundle code-splitting** | JS único 1.26 MB (>500 kB warning); afeta LCP. | Médio — `React.lazy` por rota em App.tsx + `manualChunks`. Pass de performance dedicado. |
| **i18n de strings PT hardcoded** | ex.: `'Desactivar'/'Activar'` (LandingIntegrationsSettings), `'Dá acesso…'` (TeamContent). | Baixo mas espalhado — mover para `useTranslation` (en/pt/fr). |
| **Focus states / navegação por teclado** | rever tab order e `:focus-visible` em modais/drawers. | Baixo/Médio. |
| **Consistência de empty/error/loading** | já bom (LoadingSpinner/EmptyState); auditar cobertura por ecrã. | Baixo. |

## Recomendação
Tratar o backlog em passes pequenos e verificáveis (um por área), sempre com `tsc`+`build`
antes do PR. O **code-splitting** é o item de maior impacto de UX/performance para o launch
(ver CAP0/CAP1) — candidato a EPIC de performance própria.

## Gate
Sem gate arquitetural nesta EPIC. **NO MERGE / NO DEPLOY / NO APPLY.**
