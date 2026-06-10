# Database Guidelines

> Database patterns and conventions for this project.

---

## Overview

- **Primary DB**: SQLite via `better-sqlite3` (native binding)
- **Fallback**: `sql.js` (WASM-based) — used only when better-sqlite3 is unavailable
- **No ORM**: All access is raw SQL through better-sqlite3's prepared statement API
- **Schema location**: Defined in-code in `src/memory/second-brain-db.ts` via `this.db.exec(CREATE TABLE IF NOT EXISTS ...)`
- **Other persistence**: File-based JSON/JSONL for short-term memory (`src/memory/store.ts`), YAML for config (`src/utils/config.ts`)

---

## Query Patterns

- **Prepared statements with named parameters**:
  ```typescript
  const stmt = this.db.prepare('SELECT * FROM memories WHERE id = :id');
  const row = stmt.get({ id });
  ```
- **Batch operations**: Use `this.db.transaction()` for multi-statement writes:
  ```typescript
  const insertMany = this.db.transaction((items) => {
    for (const item of items) stmt.run(item);
  });
  insertMany(items);
  ```
- **Read queries**: `stmt.get()` for single row, `stmt.all()` for multiple rows
- **FTS5 full-text search**: Query via ` Memories_fts` virtual table with `MATCH` operator

---

## Migrations

There are no separate migration files. Schema is managed inline in `SecondBrainDB.init()`:

```typescript
async init() {
  this.db.exec('CREATE TABLE IF NOT EXISTS memories (...)');
  this.db.exec('CREATE INDEX IF NOT EXISTS idx_memories_user_key ON memories(user_key)');
  // ... more tables
}
```

- Schema changes are additive: `CREATE TABLE IF NOT EXISTS` / `CREATE INDEX IF NOT EXISTS`
- No rollback mechanism — schema only grows forward
- Foreign keys are enabled via `PRAGMA foreign_keys = ON`

---

## Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Tables | snake_case, plural | `memories`, `persons`, `memory_persons` |
| Columns | snake_case | `user_key`, `created_at`, `dismissed` |
| Indexes | `idx_` prefix + table + columns | `idx_memories_user_key` |
| FTS tables | `{table}_fts` | `memories_fts` |
| Junction tables | `{table1}_{table2}` | `memory_persons`, `person_aliases` |
| Meta tables | `{table}_meta` | `second_brain_meta` |
| Boolean columns | adjective/past participle | `dismissed` (0/1), not `is_dismissed` |

---

## PRAGMA Settings

```typescript
this.db.exec('PRAGMA journal_mode = WAL');      // Write-Ahead Logging for concurrent reads
this.db.exec('PRAGMA synchronous = NORMAL');     // Balance safety vs performance
this.db.exec('PRAGMA foreign_keys = ON');         // Enforce referential integrity
```

---

## Soft Delete

Use a `dismissed` column (INTEGER, 0/1) rather than hard deletion. Filter with `WHERE dismissed = 0` in queries.

---

## Common Mistakes

- **Forgetting `.js` extension** on imports — ESM requires it even in TypeScript files
- **Not using prepared statements** — raw `db.exec()` with string interpolation is vulnerable and slow
- **Missing PRAGMA settings** — new DB instances must set WAL + foreign_keys
- **Assuming sql.js is equivalent** — better-sqlite3 is preferred; sql.js is a degraded fallback
