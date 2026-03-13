---
name: fragments/ui
description: Best practices and usage guide for building with @fragments-sdk/ui components and design tokens. Use when the user is building UI with Fragments components, asks about component usage, theming, token reference, patterns, accessibility, or needs help choosing the right component for a use case.
type: core
library: "@fragments-sdk/ui"
library_version: ">=0.1.0"
sources:
  - "fragments-sdk/skills:skills/ui/SKILL.md"
---

# Fragments UI Best Practices

Reference guide for building with `@fragments-sdk/ui`. This skill loads automatically when working with Fragments UI components.

If `@fragments-sdk/ui` is not yet installed, run `/fragments-ui-setup` first.

## Core principles

1. **Use the design system first.** Always check if a Fragments component exists before building custom UI. Use `mcp__fragments__fragments_discover` with a `useCase` query to find the right component.
2. **Use design tokens, not raw values.** Never hardcode colors, spacing, font sizes, or radii. Use CSS custom properties from the token system (`--fui-*`).
3. **Compose, don't customize.** Build complex UI by composing smaller components. Don't override internal component styles -- use the provided props and slots.
4. **Accessibility is not optional.** Every interactive element needs keyboard support, focus management, and ARIA attributes. Fragments components handle this -- don't break it with custom wrappers.

## Component discovery workflow

When building new UI, follow this order:

1. **Check for an existing component:**
   Use `mcp__fragments__fragments_discover` with `useCase` to find matching components.
   Use `mcp__fragments__fragments_discover` with `compact: true` for a quick overview of everything available.

2. **Check for composition blocks:**
   Use `mcp__fragments__fragments_blocks` with `search` to find pre-built patterns (e.g., "login form", "settings page", "data table").

3. **Get implementation details:**
   Use `mcp__fragments__fragments_inspect` on the chosen component to see full props, guidelines, and code examples.
   Use `mcp__fragments__fragments_implement` for a one-shot bundle of components + tokens + blocks for a use case.

4. **Only build custom** if no component or block covers the use case.

## Token usage

Use `mcp__fragments__fragments_tokens` to find the right token. Key categories:

### Colors
- **Text:** `--fui-text-primary`, `--fui-text-secondary`, `--fui-text-tertiary`, `--fui-text-muted`
- **Surfaces:** `--fui-bg-primary`, `--fui-bg-secondary`, `--fui-bg-tertiary`, `--fui-color-surface-raised`
- **Borders:** `--fui-border-default`, `--fui-border-subtle`
- **Semantic:** `--fui-color-accent`, `--fui-color-success`, `--fui-color-danger`, `--fui-color-warning`

### Spacing
- Use the spacing scale: `--fui-space-1` through `--fui-space-8`
- Component gaps: prefer `gap` with spacing tokens over margin hacks

### Typography
- Sizes: `--fui-font-size-xs` through `--fui-font-size-xl`
- Weights via the `Text` component `weight` prop: `regular`, `medium`, `semibold`, `bold`
- Mono: `--fui-font-mono` for code/technical content

### Radii
- `--fui-radius-sm`, `--fui-radius-md`, `--fui-radius-lg`, `--fui-radius-full`

## Core patterns

### Layout

Use `Stack` for vertical layouts with consistent `gap`. Use flexbox for horizontal:

```tsx
import { Card, Stack, Text } from '@fragments-sdk/ui';

export function InfoSection() {
  return (
    <Card variant="outlined" padding="lg">
      <Stack gap="md">
        <Text as="h2" size="lg" weight="semibold">Section Title</Text>
        <Text size="sm" color="secondary">
          Supporting description text for context.
        </Text>
      </Stack>
    </Card>
  );
}
```

Use `Skeleton` for loading states -- match the shape of the content it replaces.

### Forms

Always use `Input` with a `label` prop. Use `Select` for enumerated choices. Use `Button` variants intentionally:

```tsx
import { Button, Input, Select, Stack, Text } from '@fragments-sdk/ui';

export function ContactForm() {
  return (
    <form>
      <Stack gap="md">
        <Input label="Full name" placeholder="Jane Doe" required />
        <Input label="Email" type="email" placeholder="jane@example.com" required />
        <Select label="Subject">
          <Select.Option value="support">Support</Select.Option>
          <Select.Option value="sales">Sales</Select.Option>
          <Select.Option value="other">Other</Select.Option>
        </Select>
        <div style={{ display: 'flex', gap: 'var(--fui-space-2)', justifyContent: 'flex-end' }}>
          <Button variant="outline">Cancel</Button>
          <Button type="submit">Send</Button>
        </div>
      </Stack>
    </form>
  );
}
```

Show validation errors inline using `Text` with `color="danger"`:
```tsx
<Text size="xs" color="danger">Email is required</Text>
```

### Data display

```tsx
import { Avatar, Badge, CodeBlock, Text } from '@fragments-sdk/ui';

export function UserStatus({ name, status }: { name: string; status: string }) {
  return (
    <div style={{ display: 'flex', alignItems: 'center', gap: 'var(--fui-space-3)' }}>
      <Avatar name={name} size="md" />
      <div>
        <Text size="sm" weight="medium">{name}</Text>
        <Badge variant={status === 'active' ? 'success' : 'neutral'}>{status}</Badge>
      </div>
    </div>
  );
}
```

Use `CodeBlock` for code snippets with `language` and `title` props:
```tsx
<CodeBlock language="ts" title="example.ts">
  {`const x = 42;`}
</CodeBlock>
```

### Icons

Import from `@phosphor-icons/react`. Size icons to match text:

```tsx
import { Button } from '@fragments-sdk/ui';
import { Plus, Trash } from '@phosphor-icons/react';

export function Actions() {
  return (
    <div style={{ display: 'flex', gap: 'var(--fui-space-2)' }}>
      <Button>
        <Plus size={14} weight="bold" />
        Add item
      </Button>
      <Button variant="ghost" color="danger">
        <Trash size={14} weight="bold" />
        Delete
      </Button>
    </div>
  );
}
```

- Use `weight="bold"` for interactive elements (buttons, links)
- Use `weight="duotone"` for decorative/illustrative contexts
- Size: 12px for `xs` text, 14px for `sm`, 16px for `md`

## Accessibility checklist

- Interactive elements: use `Button`, not `<div onClick>`
- Images: always pass `alt` to `Avatar` via the `name` prop
- Dynamic content: use `aria-live` regions for toast/notification areas
- Focus: never set `tabIndex` on non-interactive elements
- Color: never rely on color alone to convey meaning -- pair with text or icons

## Performance

Use `mcp__fragments__fragments_perf` to check component bundle sizes before adding heavy components. Prefer:
- `Text` over custom styled spans
- `Stack` over nested divs with margin
- `Icon` wrapper over raw SVG imports (enables tree-shaking)

Use `mcp__fragments__fragments_graph` with `mode: "impact"` to understand the blast radius before modifying shared components.

## Common mistakes

### CRITICAL: Using `<div onClick>` for interactive elements

Breaks keyboard navigation, screen readers, and focus management. Fragments `Button` handles all of this.

Wrong:
```tsx
<div onClick={handleSave} style={{ cursor: 'pointer' }}>Save</div>
```

Correct:
```tsx
import { Button } from '@fragments-sdk/ui';

<Button onClick={handleSave}>Save</Button>
```

### HIGH: Hardcoded colors instead of tokens

Breaks theme switching (dark/light mode). Components using raw colors will not respond to theme changes.

Wrong:
```tsx
<div style={{ color: '#666', backgroundColor: '#f5f5f5' }}>Content</div>
```

Correct:
```tsx
<div style={{ color: 'var(--fui-text-secondary)', backgroundColor: 'var(--fui-bg-secondary)' }}>Content</div>
```

### HIGH: Overriding component styles with `!important`

Fragments components are designed to be styled through props and tokens. Using `!important` bypasses the design system and breaks when components are updated.

Wrong:
```css
.my-button {
  border-radius: 8px !important;
  padding: 12px 24px !important;
}
```

Correct:
```tsx
<Button radius="md" size="lg">Click me</Button>
```

### MEDIUM: Building custom inputs when variants exist

Before creating a custom text input, search input, or textarea, check if `Input` already supports the use case via props.

Wrong:
```tsx
export function SearchInput(props) {
  return <input className="custom-search" type="search" {...props} />;
}
```

Correct -- check first:
```
mcp__fragments__fragments_inspect({ component: "Input", fields: ["props"] })
```

Then use the built-in component:
```tsx
import { Input } from '@fragments-sdk/ui';

<Input type="search" label="Search" placeholder="Search..." />
```

### MEDIUM: Nesting Cards

Nesting `Card` inside `Card` creates visual confusion with compounding borders and backgrounds. Use compound components instead.

Wrong:
```tsx
<Card>
  <Card>Inner content</Card>
</Card>
```

Correct -- use compound components for structured sections:
```tsx
import { Card } from '@fragments-sdk/ui';

<Card variant="outlined">
  <Card.Header>
    <Card.Title>Settings</Card.Title>
    <Card.Description>Manage your preferences</Card.Description>
  </Card.Header>
  <Card.Body>
    Main content here
  </Card.Body>
  <Card.Footer>
    Footer actions
  </Card.Footer>
</Card>
```

## Compound components and Server Components

Many Fragments components use compound patterns (`Card.Header`, `Select.Option`, `Tabs.Tab`, etc.). These are attached to the parent via `Object.assign`, which means **the parent component must be a Client Component** when used with the compound syntax.

If you need a Server Component, import the child component directly instead of using the dot notation:

Wrong in a Server Component:
```tsx
// This file has no 'use client' directive
import { Card } from '@fragments-sdk/ui';

// Card.Header uses Object.assign -- fails in Server Components
export function MyCard() {
  return (
    <Card>
      <Card.Header>Title</Card.Header>
    </Card>
  );
}
```

Correct -- import sub-components directly:
```tsx
// Server Component -- no 'use client' needed
import { Card, CardHeader, CardBody } from '@fragments-sdk/ui';

export function MyCard() {
  return (
    <Card>
      <CardHeader>Title</CardHeader>
      <CardBody>Content</CardBody>
    </Card>
  );
}
```

Or add `'use client'` if you want the compound syntax:
```tsx
'use client';
import { Card } from '@fragments-sdk/ui';

export function MyCard() {
  return (
    <Card>
      <Card.Header>Title</Card.Header>
      <Card.Body>Content</Card.Body>
    </Card>
  );
}
```
