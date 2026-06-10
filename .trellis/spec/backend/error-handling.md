# Error Handling

> How errors are handled in this project.

---

## Overview

- No custom error hierarchy — only one domain-specific class: `ProviderModelFetchError` in `src/utils/provider-models.ts`
- Errors are typically caught inline with `try/catch` and surfaced via logger + channel notification
- The pattern is **best-effort propagation**: always try to tell the user, but don't cascade failures

---

## Error Handling Patterns

### Agent Core (`src/core/agent.ts`)

Provider failures trigger fallback to the next provider in the chain, with a user notification:
```typescript
try {
  // ... LLM call
} catch (err) {
  logger.error({ err }, 'Provider failed');
  // Try next provider in chain
  channel.send('Switching to fallback provider due to error...').catch(() => {});
}
```

Channel `.send()` calls are always wrapped in `.catch(() => {})` to prevent cascading failures from a broken channel.

Loop detection aborts the agent loop with a user-facing message rather than throwing.

### API Layer (`src/web/middleware.ts`)

Global error handler middleware wraps all requests:
```typescript
app.onError((err, c) => {
  logger.error({ err }, 'Request error');
  if (c.req.path.startsWith('/api/')) {
    return c.json({ error: 'Internal server error' }, 500);
  }
  return c.html('500', 500);
});
```

Auth errors return `{ error: 'Unauthorized' }` with status 401, or redirect to `/login` for HTML requests.

### Crash Recovery (`src/core/crash-flag.ts`)

Uncaught exceptions and unhandled rejections write a crash flag file to disk. On next startup, the agent reports the crash to the user.

---

## API Error Responses

All API errors follow a single format:
```json
{ "error": "Human-readable message" }
```

| Scenario | Status | Body |
|----------|--------|------|
| Auth required | 401 | `{ "error": "Unauthorized" }` |
| Validation error | 400 | `{ "error": "description" }` |
| Not found | 404 | `{ "error": "Not found" }` |
| Internal error | 500 | `{ "error": "Internal server error" }` |

---

## Common Mistakes

- **Cascading channel failures** — always wrap `channel.send()` in `.catch()` to prevent a broken channel from crashing the agent
- **Swallowing errors silently** — always `logger.error()` at minimum, even if recovery is possible
- **Throwing in cleanup code** — catch and log in `finally` blocks, never throw
