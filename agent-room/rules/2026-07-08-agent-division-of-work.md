# Keyros Agent Division of Work

Date: 2026-07-08
Owner: Victor / ChatGPT

## Claude

Claude handles larger and more structural work:

- backend architecture;
- security;
- RLS;
- migrations;
- Module Registry;
- Billing foundation;
- entitlements;
- rate limit;
- circuit breaker;
- retry/backoff;
- audit logging;
- event bus;
- automation queues;
- implementation reports.

Recommended loop size: 15 to 20 tasks.

Claude may prepare migrations and test plans, but must not apply migrations, merge PRs, or change production data without explicit Victor/ChatGPT gate approval.

## Antenor

Antenor handles focused frontend and UX implementation:

- frontend components;
- UI states;
- dashboard UI;
- messages UI;
- settings UI;
- permission-aware screens;
- visual consistency;
- safe loading, empty, and error states.

Recommended loop size: exactly 10 tasks.

Every Antenor loop must start with IMPLEMENTATION FIRST.

Antenor must not finish with audit only. A valid loop needs code changes, remote commit SHA, branch, PR or issue reference, changed files, typecheck result, build result, blockers, and next recommended loop.

## ChatGPT Gate Role

ChatGPT reviews before merge:

1. inspect the PR or commit;
2. validate architecture;
3. check security impact;
4. confirm no unauthorized migrations or production changes;
5. approve, request changes, or block;
6. only then release merge/apply gates if Victor explicitly authorizes.

## Permanent gates

- No merge without Victor/ChatGPT approval.
- No migration apply without Victor/ChatGPT approval.
- No production data changes without Victor/ChatGPT approval.
- No Stripe or Billing provider connection outside Billing Engine.
- No local-only delivery accepted unless declared clearly.
