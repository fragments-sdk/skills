---
name: fragments/ui-setup
description: Set up the @fragments-sdk/ui component library in your project. Installs the package, configures theming, sets up the provider, adds the MCP server for AI-assisted development, and creates a sample component. Use when the user wants to add Fragments UI components to their project, set up the design system, or start building with @fragments-sdk/ui.
type: core
library: "@fragments-sdk/ui"
library_version: ">=0.1.0"
sources:
  - "fragments-sdk/skills:skills/ui-setup/SKILL.md"
disable-model-invocation: true
argument-hint: "[--with-mcp]"
---

# Fragments UI Setup

Set up the `@fragments-sdk/ui` component library -- from install to rendering your first component.

## 1. Detect project environment

- Confirm the project has a `package.json`. If not, ask if the user wants to initialize one.
- Detect the package manager from lock files:
  - `bun.lockb` or `bun.lock` -> bun
  - `pnpm-lock.yaml` -> pnpm
  - `yarn.lock` -> yarn
  - Otherwise -> npm
- Detect the framework and its version:
  - React / Next.js / Remix / Vite+React
  - Vue / Nuxt (note: `@fragments-sdk/ui` requires React -- suggest the Vue adapter or confirm the user wants to add React)
  - Check the TypeScript version -- `@fragments-sdk/ui` requires TS 5.0+

## 2. Install the UI package

First check if `@fragments-sdk/ui` is already available:
- In a **monorepo** with workspace dependencies (look for `"workspace:*"` or `"workspace:^"` in `package.json`), the package may already be linked. If so, **skip the install step**.
- Also check parent directories for `pnpm-workspace.yaml` or a `workspaces` field.

If not already installed:

```bash
<package-manager> add @fragments-sdk/ui
```

Also install the icon library if not present:

```bash
<package-manager> add @phosphor-icons/react
```

## 3. Set up the CSS import

Add the Fragments UI stylesheet to the app's entry point. Detect the right file:

**Next.js App Router** -- `app/layout.tsx`:
```ts
import '@fragments-sdk/ui/styles.css';
```

**Next.js Pages** -- `pages/_app.tsx`:
```ts
import '@fragments-sdk/ui/styles.css';
```

**Vite + React** -- `src/main.tsx`:
```ts
import '@fragments-sdk/ui/styles.css';
```

**Remix** -- `app/root.tsx`:
```ts
import '@fragments-sdk/ui/styles.css';
```

The stylesheet must be imported **before** any app-level CSS so tokens are available for overrides.

## 4. Configure the FragmentsProvider

Wrap the app root with the provider. Detect the right location:

**Next.js App Router** -- `app/layout.tsx` (or a dedicated `app/providers.tsx`):

In Next.js App Router, the provider must be in a Client Component because it uses React context.

```tsx
'use client';

import { FragmentsProvider } from '@fragments-sdk/ui';

export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <FragmentsProvider theme="dark">
      {children}
    </FragmentsProvider>
  );
}
```

Then use it in the root layout:

```tsx
import '@fragments-sdk/ui/styles.css';
import { Providers } from './providers';

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}
```

**Vite + React** -- `src/main.tsx` or `src/App.tsx`:
```tsx
import { FragmentsProvider } from '@fragments-sdk/ui';

function App() {
  return (
    <FragmentsProvider theme="dark">
      {/* app content */}
    </FragmentsProvider>
  );
}
```

Ask the user which theme to default to: `"dark"`, `"light"`, or `"system"`.

## 5. Create a sample component

Create a simple component to verify the setup works. Place it based on the project structure:

```tsx
import { Button, Card, Stack, Text } from '@fragments-sdk/ui';
import { Rocket } from '@phosphor-icons/react';

export function Welcome() {
  return (
    <Card variant="outlined" padding="lg">
      <Stack gap="md">
        <Text as="h2" size="lg" weight="semibold">
          Fragments UI is ready
        </Text>
        <Text size="sm" color="tertiary">
          You can now use any component from the design system.
        </Text>
        <Button>
          <Rocket size={14} weight="bold" />
          Get started
        </Button>
      </Stack>
    </Card>
  );
}
```

Render it somewhere visible and confirm it displays correctly.

## 6. Set up the MCP server (optional)

If `$ARGUMENTS` contains `--with-mcp`, or ask the user: "Want to add the Fragments MCP server for AI-assisted development?"

If yes:

Read `.claude/settings.json` if it exists. Merge the Fragments MCP server into the existing `mcpServers` object -- do NOT overwrite any existing servers. If the file doesn't exist, create it.

The resulting `mcpServers` should include:
```json
{
  "mcpServers": {
    "fragments": {
      "command": "npx",
      "args": ["@fragments-sdk/mcp"]
    }
  }
}
```

This enables the AI agent to discover components, generate UI, inspect props, check performance, and validate governance -- all from within the conversation.

Explain the key tools now available:
- `mcp__fragments__fragments_discover` -- find the right component for any use case
- `mcp__fragments__fragments_implement` -- get everything needed to build a feature in one call
- `mcp__fragments__fragments_inspect` -- deep dive into any component's props and examples
- `mcp__fragments__fragments_blocks` -- find pre-built composition patterns

## 7. Update .gitignore

Add `.agents/` to `.gitignore` if not already present (this is where installed skills live). Create `.gitignore` if needed.

## 8. Configure module resolution (if needed)

If the project uses TypeScript with an older `moduleResolution` setting, ensure it can resolve `@fragments-sdk/ui` subpath exports:

```json
{
  "compilerOptions": {
    "moduleResolution": "bundler"
  }
}
```

This is required for imports like `@fragments-sdk/ui/styles.css` to resolve correctly. Projects using `"moduleResolution": "node"` may fail to find subpath exports.

## 9. Confirm completion

Summarize what was done:
- `@fragments-sdk/ui` and `@phosphor-icons/react` installed
- CSS imported in the app entry point
- `FragmentsProvider` wrapping the app with chosen theme
- Sample component created and rendering
- MCP server configured (if chosen)

Mention related skills:
- The `fragments-ui` skill loads automatically with best practices, token reference, and patterns
- `/fragments-cloud-setup` to add design governance and cloud dashboard

## Common mistakes

### CRITICAL: CSS import after app styles

Importing `@fragments-sdk/ui/styles.css` after app-level CSS causes tokens to be overridden. Components may appear unstyled or use wrong colors.

Wrong -- app CSS loaded first:
```ts
import './globals.css';
import '@fragments-sdk/ui/styles.css';
```

Correct -- Fragments CSS loaded first:
```ts
import '@fragments-sdk/ui/styles.css';
import './globals.css';
```

### HIGH: FragmentsProvider in a Server Component (Next.js App Router)

`FragmentsProvider` uses React context, which requires a Client Component. Placing it directly in `layout.tsx` without `'use client'` causes a runtime error.

Wrong:
```tsx
// app/layout.tsx -- Server Component by default
import { FragmentsProvider } from '@fragments-sdk/ui';

export default function RootLayout({ children }) {
  return (
    <html><body>
      <FragmentsProvider>{children}</FragmentsProvider>
    </body></html>
  );
}
```

Correct -- extract to a Client Component:
```tsx
// app/providers.tsx
'use client';
import { FragmentsProvider } from '@fragments-sdk/ui';

export function Providers({ children }: { children: React.ReactNode }) {
  return <FragmentsProvider theme="dark">{children}</FragmentsProvider>;
}
```

### HIGH: Missing FragmentsProvider entirely

All Fragments components require the provider for theming and context. Without it, components render without tokens and may appear broken or unstyled.

Wrong:
```tsx
import { Button } from '@fragments-sdk/ui';

export default function App() {
  return <Button>Click me</Button>; // no provider ancestor
}
```

Correct:
```tsx
import { FragmentsProvider, Button } from '@fragments-sdk/ui';

export default function App() {
  return (
    <FragmentsProvider theme="dark">
      <Button>Click me</Button>
    </FragmentsProvider>
  );
}
```

### MEDIUM: Monorepo double-install

Installing `@fragments-sdk/ui` in a sub-package when the workspace root already provides it causes duplicate React instances and broken hooks.

If `pnpm-workspace.yaml`, `yarn workspaces`, or `npm workspaces` exist, check the root `node_modules` first before installing.
