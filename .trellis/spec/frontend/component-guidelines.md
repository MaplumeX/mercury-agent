# Component Guidelines

> How components are built in this project.

---

## Overview

- **UI primitives**: Radix UI via shadcn/ui pattern (`src/components/ui/`) — do not rebuild these from scratch
- **Domain components**: Organized by feature area under `src/components/` (e.g., `chat/`, `layout/`)
- **Styling**: Tailwind CSS with custom design tokens via CSS variables
- **Compositon**: Prefer composition over props-driven branching

---

## Component Structure

```tsx
// 1. Imports
import { useState } from 'react';
import { cn } from '@/lib/utils';
import { Button } from '@/components/ui/button';

// 2. Types (inline or co-located)
interface MessageBubbleProps {
  content: string;
  isUser: boolean;
}

// 3. Component
export function MessageBubble({ content, isUser }: MessageBubbleProps) {
  return (
    <div className={cn(
      'rounded-lg p-3',
      isUser ? 'bg-primary text-primary-foreground' : 'bg-muted'
    )}>
      {content}
    </div>
  );
}
```

- Functional components only — no class components
- Named exports (not default) for better refactoring and reusability
- One component per file; small helper components can share a file if tightly coupled

---

## Props Conventions

- Use `interface` for props (not `type` alias)
- Destructure props in the function signature
- Optional props use `?` with no default — handle `undefined` in the component body
- Pass `className` as the last prop for composability with `cn()`

---

## Styling Patterns

- **Tailwind utility classes** directly in JSX — no CSS modules, no styled-components
- **Conditional classes** via `cn()` (clsx + tailwind-merge):
  ```tsx
  <div className={cn('base-classes', condition && 'conditional-classes')} />
  ```
- **Design tokens**: Colors use Tailwind semantic tokens (`bg-primary`, `text-muted-foreground`) — not raw Tailwind colors
- **shadcn/ui components**: Use `src/components/ui/` primitives for buttons, dialogs, etc. — customize via `className` prop

---

## Accessibility

- Radix UI primitives handle a11y (focus management, ARIA) automatically
- For custom interactive elements: ensure keyboard navigation, proper ARIA roles, focus visibility
- Use semantic HTML elements (`<button>`, `<nav>`, `<main>`)

---

## Common Mistakes

- **Don't import from `@/components/ui/` and override all styles** — these are meant to be customized at the component level, not stripped
- **Don't create global CSS** — use Tailwind utilities and CSS variables
- **Don't use inline `style={{}}`** — use Tailwind classes
- **Don't double-merge classes** — use `cn()` once, not `className={clsx()` + `twMerge()`
