# Hook Guidelines

> How hooks are used in this project.

---

## Custom Hook Patterns

- **SSE connection**: `useSSE` — manages EventSource lifecycle, dispatches into Zustand store
- **Thread management**: `useThreads` — thread list CRUD, wraps API calls + local state
- **Auto-scroll**: `useAutoScroll` — keeps container scrolled to bottom during streaming
- **Keyboard shortcuts**: `useKeyboardShortcuts` — global key bindings

Hooks are co-located in `src/hooks/`. Each hook handles a single concern.

---

## Data Fetching

No React Query or SWR. Data fetching is done directly via the typed API client in `src/lib/api.ts`:

```tsx
// In a component or hook
useEffect(() => {
  api.getThreads().then(setThreads);
}, []);
```

- **Real-time data**: SSE via `useSSE` hook → Zustand store
- **On-demand data**: Direct `api.*()` calls in effects or event handlers
- **No caching layer** — re-fetch when needed

---

## Naming Conventions

- All custom hooks use `use` prefix: `useSSE`, `useThreads`, `useAutoScroll`
- Hook files: camelCase matching the hook name (`useSSE.ts`, `useThreads.ts`)

---

## Common Mistakes

- **Don't put business logic in components** — extract to hooks when stateful or reused
- **Don't call hooks conditionally** — hooks must be called at the top level of the component
- **Don't forget cleanup** — SSE connections, event listeners, and intervals need cleanup in the hook's cleanup function
