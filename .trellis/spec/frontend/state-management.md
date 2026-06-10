# State Management

> How state is managed in this project.

---

## Overview

- **Global state**: Zustand v5 (stores in `src/stores/`)
- **Server state**: Direct fetch via `src/lib/api.ts` — no React Query/SWR
- **Real-time**: SSE events → Zustand chat store
- **URL state**: React Router v7

---

## State Categories

| Category | Where | Example |
|----------|-------|---------|
| Global app state | Zustand stores | Chat messages, theme |
| Server state | Fetched on demand via `api.ts` | Threads, providers, settings |
| Real-time updates | SSE → Zustand | Streaming messages, tool steps |
| Local UI state | `useState` in components | Modals, form inputs, toggles |
| URL state | React Router params | Active page, thread ID |

---

## When to Use Global State

Use Zustand when:
- Multiple components need the same data (e.g., chat messages)
- Data persists across page navigation (e.g., theme)
- Server-pushed events update state (e.g., SSE → chat store)

Keep local with `useState` when:
- State is only used in one component
- State resets on unmount (e.g., form inputs, modal open/close)

---

## Store Pattern

```typescript
// src/stores/chat.ts
import { create } from 'zustand';

interface ChatState {
  messages: Message[];
  isStreaming: boolean;
  // ... state fields
  addMessage: (msg: Message) => void;
  setStreaming: (v: boolean) => void;
  // ... actions
}

export const useChatStore = create<ChatState>((set) => ({
  messages: [],
  isStreaming: false,

  addMessage: (msg) => set((s) => ({ messages: [...s.messages, msg] })),
  setStreaming: (v) => set({ isStreaming: v }),
}));
```

- One store per domain: `chat.ts`, `theme.ts`
- Actions are individual setter functions, not a single `dispatch`
- Use `localStorage` for persistence of specific fields (e.g., active thread, theme)

---

## Server State

No caching or invalidation layer. Fetch fresh data directly from the API:

```typescript
// In a component or hook
const loadThreads = async () => {
  const threads = await api.getThreads();
  // Use in local state or push to Zustand
};
```

For real-time chat, SSE pushes updates into the Zustand chat store automatically via `useSSE`.

---

## Common Mistakes

- **Don't put everything in Zustand** — local UI state (modal open, form input) stays in `useState`
- **Don't duplicate server state** — if data comes from an API, fetch it; don't mirror it in Zustand unless SSE updates it
- **Don't mutate store state directly** — always use `set()` callback pattern
