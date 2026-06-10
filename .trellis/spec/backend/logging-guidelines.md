# Logging Guidelines

> How logging is done in this project.

---

## Overview

- **Library**: Pino (`src/utils/logger.ts`)
- **Default level**: `silent` (no output unless explicitly enabled)
- **Enable logging**: `--verbose` / `-v` CLI flag, or `LOG_LEVEL` env var
- **Output**: stderr only (`pino.destination(2)`)
- **Format**: Pino's default structured JSON
- **Logger name**: `mercury`

```typescript
import { logger } from '../utils/logger.js';

const verbose = process.argv.includes('--verbose') || process.argv.includes('-v');
const level = process.env.LOG_LEVEL || (verbose ? 'info' : 'silent');

export const logger = pino({ level, name: 'mercury' }, pino.destination(2));
```

---

## Log Levels

| Level | When to use | Example |
|-------|------------|---------|
| `debug` | Internal details, prompt processing | `logger.debug({ systemPrompt }, 'System prompt generated')` |
| `info` | Startup, state changes, user actions | `logger.info({ provider, model }, 'Provider initialized')` |
| `warn` | Fallbacks, non-fatal failures, degraded mode | `logger.warn({ err }, 'Falling back to sql.js')` |
| `error` | Provider failures, uncaught exceptions | `logger.error({ err }, 'Provider call failed')` |

---

## Structured Logging

Use Pino's first-argument object for structured data:

```typescript
// Good — structured fields
logger.info({ provider, model, steps }, 'LLM call completed');

// Good — error objects
logger.error({ err }, 'Provider failed');

// Bad — string concatenation
logger.info(`LLM call completed with ${provider}/${model}`);
```

Include relevant context (provider, model, duration, etc.) as structured fields, not in the message string.

---

## What to Log

- Agent state transitions (lifecycle changes)
- Provider initialization, switching, and failures
- LLM call summaries (provider, model, token counts, duration)
- API key validation results (not the keys themselves)
- Background task start/completion/failure
- Crash and recovery events

---

## What NOT to Log

- API keys, tokens, or credentials — log only validation success/failure
- Full message content or system prompts (use `debug` level if needed)
- User personal data (PII)
- File contents read by agent tools

---

## No Log Rotation

Logs go to stderr only. No file-based logging or rotation is configured. In production, capture stderr with your process manager (systemd, PM2, etc.).
