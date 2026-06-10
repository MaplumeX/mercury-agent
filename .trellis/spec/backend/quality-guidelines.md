# Quality Guidelines

> Code quality standards for backend development.

---

## Overview

- **Test runner**: Vitest v3 (no config file — uses defaults)
- **Linting**: No ESLint or Prettier configured
- **Type checking**: TypeScript strict mode enabled in `tsconfig.json`
- **Build**: tsup (ESM, node20 target)

---

## Forbidden Patterns

- **No `any` without justification** — if `any` is needed (e.g., AI SDK tool call types), add a short comment explaining why
- **No `enum`** — use string literal unions instead (`type AgentState = 'idle' | 'thinking' | ...`)
- **No CommonJS** — the project is pure ESM; use `import`/`export`, never `require()`
- **No importing without `.js` extension** — ESM requires: `import { x } from './foo.js'` even in `.ts` files
- **No throwing in channel send** — always `.catch()` channel sends to prevent cascading failures

---

## Required Patterns

- **Prepared statements** for all DB queries — never interpolate values into SQL strings
- **Structured logging** — pass context as Pino's first-arg object, not string concatenation
- **`.js` extension on all local imports** — ESM requirement enforced by tsconfig
- **Register new tools in registry** — `src/capabilities/registry.ts`
- **Register new providers in registry** — `src/providers/registry.ts`
- **Register new API routes in server** — `src/web/server.ts` via `app.route()`
- **Register new types in barrel** — `src/types/index.ts`

---

## Testing Requirements

- Test files are co-located with source: `foo.ts` → `foo.test.ts`
- Framework: Vitest (`describe` / `it` / `expect`)
- Pattern: Pure unit tests, no mocking framework
- **Current coverage is minimal** (6 test files) — new code should include tests for non-trivial logic
- Run: `npm run test` (single), `npm run test:watch` (watch mode)

---

## Code Review Checklist

- [ ] All local imports use `.js` extension
- [ ] No `any` without justification
- [ ] DB queries use prepared statements (no string interpolation)
- [ ] Channel sends are caught (`.catch()`)
- [ ] Errors are logged, not swallowed
- [ ] New types are re-exported from `src/types/index.ts`
- [ ] New capabilities/providers/routes are registered in their respective registries
