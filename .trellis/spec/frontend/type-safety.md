# Type Safety

> Type safety patterns in this project.

---

## Overview

- **TypeScript strict mode** enabled in both root and UI tsconfig
- **No runtime validation library** in active use (Zod is a dependency but used sparsely)
- **Path alias**: `@/*` → `./src/*` in both backend and frontend

---

## Type Organization

### Backend (`src/types/`)

Centralized types with barrel re-export:

```
src/types/
├── index.ts       # Barrel re-exports
├── agent.ts       # AgentState, AgentMode, SubAgent, Board types
├── message.ts     # Message, MessageSummary
├── channel.ts     # ChannelType, ChannelMessage, Telegram types
└── sql-js.d.ts    # Custom type declarations for sql.js
```

- Domain types co-located in source: `MercuryConfig` in `src/utils/config.ts`, board types in `src/types/agent.ts`
- New shared types go in the appropriate `src/types/` file and must be re-exported from `src/types/index.ts`

### Frontend (`ui/src/lib/api.ts`)

All API types and endpoint functions live in a single file (`api.ts`, ~800 lines):

- Request/response types defined inline alongside their API functions
- No separate `types.ts` file — types and their consumers are co-located

---

## Union Types vs Enums

- **No `enum`** — use string literal unions:

  ```typescript
  // Good
  type AgentState = 'idle' | 'thinking' | 'responding' | 'sleeping';
  type ChannelType = 'cli' | 'telegram' | 'web';

  // Bad — don't use enum
  enum AgentState { Idle = 'idle', Thinking = 'thinking' }
  ```

- **Interfaces** for object shapes, **type aliases** for unions and utility types

---

## Validation

- Validation is primarily manual (regex for API keys, type narrowing)
- Zod is available but not actively used for runtime validation
- API boundary validation happens in Hono route handlers

---

## Common Patterns

- **ESM import extension**: All local imports must use `.js` suffix:
  ```typescript
  import { logger } from '../utils/logger.js';  // Correct
  import { logger } from '../utils/logger';      // Wrong — will fail at runtime
  ```
- **`any` usage**: Minimize. Acceptable for AI SDK tool call types where the SDK's own types are insufficient. Add a comment when used.

---

## Forbidden Patterns

- **No `enum`** — use string literal unions
- **No `@ts-ignore`** — fix the type error or use `@ts-expect-error` with a comment explaining why
- **No `as` type assertions** without necessity — prefer type guards or proper typing
- **No non-null assertion `!`** without certainty — handle the `undefined` case explicitly
