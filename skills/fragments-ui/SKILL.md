---
name: fragments-ui
description: Best practices and usage guide for building with @fragments-sdk/ui components. Use when the user is building UI with Fragments components, asks about component usage, theming, patterns, accessibility, or needs help choosing the right component for a use case.
---

# Fragments UI Best Practices

Reference guide for building with `@fragments-sdk/ui`. This skill loads automatically when working with Fragments UI components.

## Core principles

1. **Use the design system first.** Always check if a Fragments component exists before building custom UI. Use `fragments_discover` with a `useCase` query to find the right component.
2. **Use design tokens, not raw values.** Never hardcode colors, spacing, font sizes, or radii. Use CSS custom properties from the token system (`--fui-*`).
3. **Compose, don't customize.** Build complex UI by composing smaller components. Don't override internal component styles -- use the provided props and slots.
4. **Accessibility is not optional.** Every interactive element needs keyboard support, focus management, and ARIA attributes. Fragments components handle this -- don't break it with custom wrappers.

## Component discovery workflow

When building new UI, follow this order:

1. **Check for an existing component:**
   Use `fragments_discover` with `useCase` to find matching components.
   Use `fragments_discover` with `compact: true` for a quick overview of everything available.

2. **Check for composition blocks:**
   Use `fragments_blocks` with `search` to find pre-built patterns (e.g., "login form", "settings page", "data table").

3. **Get implementation details:**
   Use `fragments_inspect` on the chosen component to see full props, guidelines, and code examples.
   Use `fragments_implement` for a one-shot bundle of components + tokens + blocks for a use case.

4. **Only build custom** if no component or block covers the use case.

## Token usage

Use `fragments_tokens` to find the right token. Key categories:

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

## Common patterns

### Layout
- Use `Stack` for vertical layouts with consistent `gap`
- Use flexbox with `gap` for horizontal layouts
- Use `Card` with `variant="outlined"` or `variant="filled"` for content containers
- Use `Skeleton` for loading states -- match the shape of the content it replaces

### Forms
- Always use `Input` with a `label` prop -- never a detached label
- Use `Select` for enumerated choices, not custom dropdowns
- Use `Button` variants intentionally: `default` for primary actions, `outline` for secondary, `ghost` for tertiary
- Show validation errors inline using `Text` with `color="danger"`

### Data display
- Use `CodeBlock` for code snippets with `language` and `title` props
- Use `Avatar` with `name` fallback for user/org images
- Use `Badge` for status indicators
- Use `Text` with semantic `color` props (`tertiary`, `muted`) -- not inline color styles

### Icons
- Import from `@phosphor-icons/react`
- Use `weight="bold"` for interactive elements (buttons, links)
- Use `weight="duotone"` for decorative/illustrative contexts
- Size icons to match text: 12px for `xs`, 14px for `sm`, 16px for `md`

## Accessibility checklist

- Interactive elements: use `Button`, not `<div onClick>`
- Images: always pass `alt` to `Avatar` via the `name` prop
- Dynamic content: use `aria-live` regions for toast/notification areas
- Focus: never set `tabIndex` on non-interactive elements
- Color: never rely on color alone to convey meaning -- pair with text or icons

## Performance

Use `fragments_perf` to check component bundle sizes before adding heavy components. Prefer:
- `Text` over custom styled spans
- `Stack` over nested divs with margin
- `Icon` wrapper over raw SVG imports (enables tree-shaking)

Use `fragments_graph` with `mode: "impact"` to understand the blast radius before modifying shared components.

## Anti-patterns -- avoid these

- Wrapping Fragments components in custom styled-components -- use props and tokens instead
- Using `!important` to override component styles
- Hardcoding `px` values for spacing (use tokens)
- Creating one-off button/input components when `Button`/`Input` variants exist
- Using `<a>` tags for actions that don't navigate -- use `Button` with `variant="ghost"`
- Nesting `Card` inside `Card` -- flatten the hierarchy
