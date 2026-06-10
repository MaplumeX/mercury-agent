# Directory Structure

> How backend code is organized in this project.

---

## Directory Layout

```
src/
├── index.ts              # CLI entry point (Commander-based, shebang #!/usr/bin/env node)
├── core/                 # Agent runtime — lifecycle, message loop, scheduling
│   ├── agent.ts          # Main Agent class (LLM calls, loop detection)
│   ├── lifecycle.ts       # State machine (unborn → birthing → ... → sleeping)
│   ├── scheduler.ts       # Cron-based task scheduler (node-cron)
│   ├── supervisor.ts      # Multi-agent orchestration
│   ├── sub-agent.ts       # Sub-agent runner
│   ├── background-tasks.ts # Background shell/agent task manager
│   ├── task-board.ts      # Task board tracking
│   ├── board-manager.ts   # Multi-board kanban manager
│   ├── board-db.ts        # Board persistence
│   ├── programming-mode.ts # Plan/execute mode toggle
│   ├── saver-mode.ts      # Token saver mode
│   ├── crash-flag.ts      # Crash detection/reporting
│   ├── file-lock.ts       # Inter-agent file lock
│   └── resource-manager.ts # CPU/memory resource tracking
├── capabilities/          # Tool definitions (AI SDK tools)
│   ├── registry.ts        # Central tool registry
│   ├── permissions.ts     # Permission management
│   ├── filesystem/        # read_file, write_file, edit_file, etc.
│   ├── shell/             # run_command, cd, approve_command
│   ├── git/               # git_status, git_diff, git_log, etc.
│   ├── github/            # create_pr, review_pr, create_issue, etc.
│   ├── scheduler/         # schedule_task, list_tasks, cancel_task
│   ├── skills/            # install_skill, list_skills, use_skill
│   ├── messaging/         # send_message
│   ├── interaction/       # ask_user
│   ├── spotify/           # Spotify control tools
│   ├── subagents/         # delegate_task, list_agents, stop_agent
│   ├── system/            # budget_status, save_memory, search_memory
│   └── web/               # fetch_url
├── channels/             # Communication channels
│   ├── base.ts            # Abstract BaseChannel
│   ├── registry.ts        # ChannelRegistry
│   ├── cli.ts             # CLI channel (Ink-based TUI)
│   ├── telegram.ts        # Telegram bot channel (grammy)
│   └── web.ts             # Web SSE channel
├── providers/            # LLM provider adapters
│   ├── base.ts            # Abstract BaseProvider
│   ├── registry.ts        # ProviderRegistry (lazy-load, fallback chain)
│   ├── anthropic.ts       # Anthropic (@ai-sdk/anthropic)
│   ├── deepseek.ts        # DeepSeek (@ai-sdk/deepseek)
│   ├── openai-compat.ts   # OpenAI-compatible (@ai-sdk/openai)
│   ├── chatgpt-web.ts     # ChatGPT OAuth provider
│   ├── github-copilot.ts   # GitHub Copilot OAuth provider
│   ├── mimo.ts            # MiMo/Xiaomi provider
│   └── ollama.ts          # Ollama provider
├── memory/               # Persistence layers
│   ├── store.ts           # ShortTermMemory, LongTermMemory, EpisodicMemory (JSON/JSONL)
│   ├── user-memory.ts     # UserMemoryStore (wraps SecondBrainDB)
│   └── second-brain-db.ts # SQLite schema + CRUD (better-sqlite3 + FTS5)
├── auth/                 # OAuth flows
│   ├── chatgpt-auth.ts    # ChatGPT OAuth
│   ├── chatgpt-session.ts # ChatGPT session persistence
│   ├── chatgpt-models.ts  # ChatGPT model listing
│   ├── github-auth.ts     # GitHub OAuth
│   ├── github-session.ts  # GitHub session persistence
│   └── github-models.ts   # GitHub model listing
├── web/                  # Web server + API
│   ├── server.ts          # Hono app setup, route registration, static serving
│   ├── middleware.ts       # Auth guard + error handler middleware
│   ├── auth.ts             # Session auth (bcryptjs, cookie-based)
│   └── api/               # API route modules (each is a Hono sub-app)
│       ├── auth.ts         # Login/logout/password
│       ├── chat.ts         # Chat SSE + send + threads + model switching
│       ├── chat-history.ts # Thread persistence
│       ├── brain.ts        # Brain/memory API
│       ├── providers.ts    # Provider config/testing
│       ├── config.ts       # App configuration API
│       ├── status.ts       # Status API
│       ├── system.ts       # System API (scheduler, permissions)
│       ├── agents.ts       # Sub-agent management API
│       ├── spotify.ts      # Spotify control API
│       ├── kanban.ts       # Board/card management API
│       └── workspace-ide.ts # IDE integration API
├── soul/                 # Agent identity & system prompt generation
│   └── identity.ts
├── skills/               # Skill system
│   ├── loader.ts          # Skill discovery from filesystem
│   ├── registry.ts        # Skill registry
│   ├── intent-router.ts   # Intent-based skill routing
│   ├── batcher.ts         # Batch skill execution
│   ├── store.ts           # Skill marketplace store
│   ├── cli.ts             # Skill CLI commands
│   └── types.ts           # Skill type definitions
├── cli/                  # CLI infrastructure
│   ├── daemon.ts          # Daemon background process management
│   ├── service.ts         # System service install/uninstall
│   └── watchdog.ts        # Watchdog restart on crash
├── types/                # Shared type definitions
│   ├── index.ts           # Barrel re-exports
│   ├── agent.ts           # AgentState, AgentMode, SubAgent, Board types
│   ├── message.ts         # Message, MessageSummary
│   ├── channel.ts         # ChannelType, ChannelMessage, Telegram types
│   └── sql-js.d.ts        # Custom type declarations for sql.js
├── utils/                # Shared utilities
│   ├── logger.ts          # Pino logger
│   ├── config.ts          # YAML config management (mercury.yaml)
│   ├── tokens.ts          # Token budget tracking
│   ├── github.ts          # GitHub API utilities
│   ├── markdown.ts        # Markdown utilities
│   ├── manual.ts          # Help/manual text
│   ├── tool-label.ts      # Tool call display formatting
│   ├── provider-models.ts  # Provider model catalog fetching
│   └── arrow-select.ts    # Arrow key selection UI
└── ui/                   # Legacy Ink-based TUI (App.tsx)
    ├── App.tsx
    ├── index.ts
    └── types.ts
```

---

## Module Organization

New features follow the established domain-driven layout:

- **New capability/tool**: Add a directory under `src/capabilities/` with the tool definition file(s) and register in `src/capabilities/registry.ts`
- **New provider**: Add a file under `src/providers/` extending `BaseProvider` and register in `src/providers/registry.ts`
- **New channel**: Add a file under `src/channels/` extending `BaseChannel` and register in `src/channels/registry.ts`
- **New API routes**: Add a file under `src/web/api/` exporting a Hono sub-app and register in `src/web/server.ts`
- **New types**: Add to the appropriate file in `src/types/` and re-export from `src/types/index.ts`

---

## Naming Conventions

- **Files**: kebab-case (e.g., `background-tasks.ts`, `sub-agent.ts`, `openai-compat.ts`)
- **Directories**: kebab-case (e.g., `capabilities/`, `file-system/` → `filesystem/`)
- **Classes**: PascalCase (e.g., `Agent`, `SecondBrainDB`, `ProviderRegistry`)
- **Functions/variables**: camelCase (e.g., `handleMessage`, `fallbackChain`)
- **Constants**: UPPER_SNAKE_CASE for true constants, camelCase for config objects
- **Type imports**: Use `.js` extension (ESM requirement): `import { logger } from '../utils/logger.js'`

---

## Examples

- Well-organized capability: `src/capabilities/filesystem/` — cohesive tool group with clear single-responsibility
- Well-structured API: `src/web/api/chat.ts` — Hono sub-app with SSE streaming
- Registry pattern: `src/providers/registry.ts` — lazy-load with fallback chain
