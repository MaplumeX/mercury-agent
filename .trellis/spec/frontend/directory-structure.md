# Directory Structure

> How frontend code is organized in this project.

---

## Directory Layout

```
ui/
├── index.html             # Vite entry HTML
├── vite.config.ts         # Vite config (dev proxy to :6174, PWA plugin)
├── tailwind.config.ts     # Tailwind config (custom design tokens)
├── tsconfig.json          # TypeScript config (strict, ESNext, bundler)
├── package.json           # Frontend dependencies
├── public/                # Static assets
└── src/
    ├── main.tsx           # React entry point
    ├── App.tsx            # Root component (React Router setup)
    ├── pages/             # Route-level page components (lazy-loaded)
    │   ├── Chat.tsx       # Main chat interface
    │   ├── Dashboard.tsx  # Agent dashboard
    │   ├── Kanban.tsx     # Board/card management
    │   ├── Tasks.tsx      # Sub-agent task list
    │   ├── Settings.tsx   # App settings
    │   ├── Login.tsx      # Auth
    │   ├── Workspace.tsx  # File browser + git
    │   ├── Providers.tsx, Skills.tsx, Permissions.tsx, Schedules.tsx, Usage.tsx
    │   └── brain/         # Brain sub-pages
    │       ├── Memory.tsx, Persons.tsx, PersonDetail.tsx, Goals.tsx, Graph.tsx
    ├── components/        # Reusable components
    │   ├── chat/          # Chat-specific components
    │   │   ├── StreamingMessage.tsx, ToolCallCard.tsx, WorkspacePanel.tsx
    │   │   ├── ThreadList.tsx, PermissionPrompt.tsx, MarkdownRenderer.tsx
    │   │   ├── MessageBubble.tsx, ModelSwitcher.tsx, ChatInput.tsx
    │   │   ├── CodeModeToggle.tsx, CodeBlock.tsx
    │   │   └── ...
    │   ├── layout/        # Layout components
    │   │   ├── AppLayout.tsx, Sidebar.tsx, ThemeToggle.tsx
    │   ├── ui/            # Radix UI primitives (shadcn/ui pattern)
    │   │   ├── button.tsx, card.tsx, dialog.tsx, tabs.tsx
    │   │   ├── select.tsx, switch.tsx, tooltip.tsx
    │   │   └── ...
    │   └── ...
    ├── hooks/             # Custom React hooks
    │   ├── useSSE.ts      # Server-Sent Events connection
    │   ├── useThreads.ts  # Chat thread management
    │   ├── useAutoScroll.ts
    │   └── useKeyboardShortcuts.ts
    ├── stores/            # Zustand state stores
    │   ├── chat.ts       # Chat messages, streaming, threads, provider info
    │   └── theme.ts      # Dark/light/system theme
    ├── lib/               # Shared utilities
    │   ├── api.ts         # Typed fetch API client + all type definitions
    │   └── utils.ts       # cn(), formatDate, formatTokens, etc.
    └── assets/            # Static assets bundled by Vite
```

---

## Module Organization

- **Pages** (`src/pages/`): One file per route. Sub-domains get their own directory (e.g., `brain/`).
- **Components** (`src/components/`): Grouped by domain (`chat/`, `layout/`). Shared primitives live in `ui/`.
- **Hooks** (`src/hooks/`): Cross-cutting stateful logic extracted from components.
- **Stores** (`src/stores/`): Global Zustand stores. One file per store.
- **Lib** (`src/lib/`): Pure utilities and the API client. No React dependencies.

---

## Naming Conventions

- **Files**: PascalCase for components (`StreamingMessage.tsx`), camelCase for utilities (`useSSE.ts`, `api.ts`)
- **Component files**: Named after the default export component
- **Directories**: kebab-case or flat grouping (`chat/`, `brain/`, `layout/`)
- **Path alias**: `@/*` → `./src/*` (configured in vite.config.ts and tsconfig.json)

---

## Examples

- Chat page + components: `src/pages/Chat.tsx` uses components from `src/components/chat/`
- API integration: `src/lib/api.ts` defines types + fetch functions; pages call these directly
- SSE real-time: `src/hooks/useSSE.ts` pushes events into `src/stores/chat.ts`
