---
name: fragments-ui-setup
description: Set up the @fragments-sdk/ui component library in your project. Installs the package, configures theming, sets up the provider, adds the MCP server for AI-assisted development, and creates a sample component. Use when the user wants to add Fragments UI components to their project, set up the design system, or start building with @fragments-sdk/ui.
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

**Next.js App Router** -- `app/layout.tsx`:
```tsx
import { FragmentsProvider } from '@fragments-sdk/ui';

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <FragmentsProvider theme="dark">
          {children}
        </FragmentsProvider>
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

## 8. Configure TypeScript paths (if needed)

If the project uses path aliases, ensure `@fragments-sdk/ui` resolves correctly. Check `tsconfig.json` for `compilerOptions.paths` and add if missing:

```json
{
  "compilerOptions": {
    "moduleResolution": "bundler"
  }
}
```

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
