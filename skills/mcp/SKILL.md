---
name: fragments/mcp
description: Reference for all Fragments MCP server tools -- component discovery, UI generation, governance validation, design token lookup, performance data, and component graph queries. Use when the user asks how to use Fragments MCP tools, wants to generate UI with AI, validate specs, explore the component graph, or integrate Fragments tools into their agent workflow.
type: core
library: "@fragments-sdk/mcp"
library_version: ">=0.1.0"
sources:
  - "fragments-sdk/skills:skills/mcp/SKILL.md"
user-invocable: false
---

# Fragments MCP Tools

Reference for using the Fragments MCP server tools. These tools let AI agents discover components, generate UI, validate governance, and query the design system -- all without leaving the conversation.

## Prerequisites

The Fragments MCP server must be configured. In `.claude/settings.json`:

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

If not configured, run `/fragments-ui-setup --with-mcp` to set it up.

## Tool naming

All tools use the MCP prefix format: `mcp__fragments__fragments_<tool>`. For example, the discover tool is `mcp__fragments__fragments_discover`. This reference uses short names after the prefix for readability, but always use the full name when calling tools.

## Available tools

### Discovery and exploration

**`mcp__fragments__fragments_discover`** -- Find components in the design system.
- No params: list all components
- `useCase: "file upload with drag and drop"`: AI-powered suggestions ranked by relevance
- `component: "Button"`: find alternatives to a specific component
- `compact: true`: token-efficient overview (just names + categories)
- `category: "forms"`: filter by category
- `search: "date"`: text search across names, descriptions, tags

**`mcp__fragments__fragments_inspect`** -- Deep dive into a single component.
- `component: "Input"`: get full props, guidelines, code examples, accessibility info
- `fields: ["props", "examples"]`: request only specific data to save tokens
- `verbosity: "compact"`: just meta + prop names
- `variant: "Primary"`: filter examples to a specific variant

**`mcp__fragments__fragments_blocks`** -- Find composition patterns.
- `search: "login"`: find pre-built patterns like "Login Form", "Settings Page"
- `category: "dashboard"`: browse by category
- `component: "DataTable"`: find blocks that use a specific component
- `verbosity: "full"`: include complete code

### Implementation

**`mcp__fragments__fragments_implement`** -- One-shot implementation helper.
- `useCase: "user profile card with avatar and stats"`: returns matched components, props, code examples, relevant blocks, and CSS tokens in a single call
- Best for: starting a new feature -- saves multiple round-trips

**`mcp__fragments__fragments_generate_ui`** -- Generate a complete UI element tree from a description.
- `prompt: "a settings page with profile section and notification preferences"`: returns a structured element tree renderable with Fragments components
- `currentTree: <existing tree>`: refine an existing UI instead of generating from scratch. Pass the full tree object returned from a previous `generate_ui` call.
- Best for: rapid prototyping and AI-driven UI generation

### Governance and validation

**`mcp__fragments__fragments_govern`** -- Validate a UI spec against policies.
- `spec`: the UI spec to validate, structured as:
  ```json
  {
    "nodes": [
      {
        "type": "Button",
        "props": { "variant": "default" },
        "styles": { "minHeight": "32px" },
        "children": [{ "type": "text", "value": "Submit" }]
      }
    ]
  }
  ```
- Returns a verdict with score (0-100), violations, and fix suggestions
- Checks: safety (blocked props), component allowlists, design token compliance, brand conformance, WCAG accessibility
- `format: "summary"`: compact text output

### Design system intelligence

**`mcp__fragments__fragments_tokens`** -- Query CSS design tokens.
- `category: "colors"`: browse tokens by category (colors, spacing, typography, surfaces, shadows, radius, borders)
- `search: "accent"`: find tokens by keyword

**`mcp__fragments__fragments_graph`** -- Query the component relationship graph.
- `mode: "dependencies"`, `component: "Card"`: what does Card depend on?
- `mode: "dependents"`, `component: "Button"`: what uses Button?
- `mode: "impact"`, `component: "Text"`: blast radius of changing Text
- `mode: "composition"`, `component: "Select"`: compound component tree
- `mode: "alternatives"`, `component: "Input"`: similar components
- `mode: "health"`: overall design system health metrics

**`mcp__fragments__fragments_perf`** -- Component performance data.
- No params: all component bundle sizes
- `component: "DataTable"`: specific component
- `filter: "over-budget"`: find components exceeding size budgets
- `sort: "budget"`: sort by most over-budget

## Recommended workflows

### Building a new page

1. `mcp__fragments__fragments_implement` with the use case description -- get matched components + tokens
2. `mcp__fragments__fragments_blocks` to find a composition pattern close to what you need
3. `mcp__fragments__fragments_inspect` on specific components for detailed prop reference
4. `mcp__fragments__fragments_govern` to validate the result before shipping

### Reviewing existing code

1. `mcp__fragments__fragments_discover` with `compact: true` to know what's available
2. `mcp__fragments__fragments_tokens` to verify token usage in custom styles
3. `mcp__fragments__fragments_graph` with `mode: "alternatives"` to suggest better component choices

### Prototyping with AI

1. `mcp__fragments__fragments_generate_ui` with a natural language description
2. Iterate by passing the result back as `currentTree` with refinement prompts
3. `mcp__fragments__fragments_govern` to validate the generated UI

### Understanding the design system

1. `mcp__fragments__fragments_graph` with `mode: "health"` for an overview
2. `mcp__fragments__fragments_discover` with categories to explore what's available
3. `mcp__fragments__fragments_perf` to understand bundle size implications

## Common mistakes

### CRITICAL: Context window bloat from unfiltered discovery

Calling `mcp__fragments__fragments_discover` without `compact: true` returns full component details for every component, which can consume significant context window space.

Wrong:
```
mcp__fragments__fragments_discover({})
```

Correct -- use compact mode, then inspect only what you need:
```
mcp__fragments__fragments_discover({ compact: true })
mcp__fragments__fragments_inspect({ component: "DataTable", fields: ["props", "examples"] })
```

### HIGH: Multiple round-trips instead of using implement

Making 4+ separate tool calls (discover, inspect, tokens, blocks) when `mcp__fragments__fragments_implement` returns all of that in one call.

Wrong:
```
mcp__fragments__fragments_discover({ useCase: "user profile" })
mcp__fragments__fragments_inspect({ component: "Avatar" })
mcp__fragments__fragments_tokens({ category: "spacing" })
mcp__fragments__fragments_blocks({ search: "profile" })
```

Correct -- one call:
```
mcp__fragments__fragments_implement({ useCase: "user profile card with avatar and stats" })
```

### MEDIUM: Inspecting without field filters

Calling `mcp__fragments__fragments_inspect` without `fields` returns everything (props, examples, guidelines, accessibility). If you only need props, filter to save tokens.

Wrong:
```
mcp__fragments__fragments_inspect({ component: "Select" })
```

Correct:
```
mcp__fragments__fragments_inspect({ component: "Select", fields: ["props"] })
```

### MEDIUM: Skipping governance validation

Building UI without running `mcp__fragments__fragments_govern` before shipping. The govern tool catches accessibility violations, token compliance issues, and policy failures that are easy to miss in code review.

Always run governance as the final step in any UI implementation workflow.
