# Quality Guidelines

> Code quality standards for frontend development.

---

## Overview

- **Framework**: React 18 + TypeScript (strict mode)
- **Build**: Vite 5
- **Styling**: Tailwind CSS 3
- **Linting**: No ESLint or Prettier configured
- **Testing**: No frontend test framework configured

---

## Forbidden Patterns

- **No inline styles** (`style={{}}`) — use Tailwind classes
- **No CSS modules or styled-components** — Tailwind only
- **No `any` without justification** — type it properly
- **No `enum`** — use string literal unions
- **No default exports** for components — use named exports
- **No direct DOM manipulation** — use React state and refs
- **No prop drilling beyond 2 levels** — use Zustand or context

---

## Required Patterns

- **`cn()` for conditional classes** — from `src/lib/utils.ts` (clsx + tailwind-merge):
  ```tsx
  <div className={cn('base', condition && 'conditional')} />
  ```
- **Radix UI primitives** from `src/components/ui/` — use these for buttons, dialogs, tabs, etc.
- **Lazy-loaded pages** — React Router with `React.lazy()` + `Suspense`
- **Framer Motion** for page transitions — wrap route components in `<motion.div>`

---

## Testing Requirements

No frontend testing infrastructure is in place. When adding tests:
- Use Vitest (already configured for backend, can extend to frontend)
- Co-locate test files: `Component.tsx` → `Component.test.tsx`
- Prefer testing user behavior over implementation details

---

## Code Review Checklist

- [ ] Uses `cn()` for conditional Tailwind classes
- [ ] Components use named exports (not default)
- [ ] Uses Radix UI primitives from `src/components/ui/` where applicable
- [ ] No inline styles, CSS modules, or styled-components
- [ ] New pages are lazy-loaded in the router
- [ ] API types defined alongside endpoint functions in `src/lib/api.ts`
- [ ] State is local (`useState`) unless truly global (then Zustand)
- [ ] SSE updates go through `useSSE` → Zustand store, not direct component state
