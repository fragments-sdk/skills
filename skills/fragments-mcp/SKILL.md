---
name: fragments-mcp
description: Guide for using the Fragments MCP server tools in AI agents. Use when the user asks how to use Fragments MCP tools, wants to generate UI with AI, validate specs against governance, explore the component graph, or integrate Fragments into their agent workflow.
user-invocable: false
---

# Fragments MCP Tools

Reference for using the Fragments MCP server tools. These tools let AI agents discover components, generate UI, validate governance, and query the design system -- all without leaving the conversation.

## Available tools

### Discovery and exploration

**`fragments_discover`** -- Find components in the design system.
- No params: list all components
- `useCase: "file upload with drag and drop"`: AI-powered suggestions ranked by relevance
- `component: "Button"`: find alternatives to a specific component
- `compact: true`: token-efficient overview (just names + categories)
- `category: "forms"`: filter by category
- `search: "date"`: text search across names, descriptions, tags

**`fragments_inspect`** -- Deep dive into a single component.
- `component: "Input"`: get full props, guidelines, code examples, accessibility info
- `fields: ["props", "examples"]`: request only specific data to save tokens
- `verbosity: "compact"`: just meta + prop names
- `variant: "Primary"`: filter examples to a specific variant

**`fragments_blocks`** -- Find composition patterns.
- `search: "login"`: find pre-built patterns like "Login Form", "Settings Page"
- `category: "dashboard"`: browse by category
- `component: "DataTable"`: find blocks that use a specific component
- `verbosity: "full"`: include complete code

### Implementation

**`fragments_implement`** -- One-shot implementation helper.
- `useCase: "user profile card with avatar and stats"`: returns matched components, props, code examples, relevant blocks, and CSS tokens in a single call
- Best for: starting a new feature -- saves multiple round-trips

**`fragments_generate_ui`** -- Generate a complete UI element tree from a description.
- `prompt: "a settings page with profile section and notification preferences"`: returns a structured element tree renderable with Fragments components
- `currentTree: <existing tree>`: refine an existing UI instead of generating from scratch
- Best for: rapid prototyping and AI-driven UI generation

### Governance and validation

**`fragments_govern`** -- Validate a UI spec against policies.
- `spec: { nodes: [...] }`: the UI spec to validate
- Returns a verdict with score (0-100), violations, and fix suggestions
- Checks: safety (blocked props), component allowlists, design token compliance, brand conformance, WCAG accessibility
- `format: "summary"`: compact text output

### Design system intelligence

**`fragments_tokens`** -- Query CSS design tokens.
- `category: "colors"`: browse tokens by category (colors, spacing, typography, surfaces, shadows, radius, borders)
- `search: "accent"`: find tokens by keyword

**`fragments_graph`** -- Query the component relationship graph.
- `mode: "dependencies"`, `component: "Card"`: what does Card depend on?
- `mode: "dependents"`, `component: "Button"`: what uses Button?
- `mode: "impact"`, `component: "Text"`: blast radius of changing Text
- `mode: "composition"`, `component: "Select"`: compound component tree
- `mode: "alternatives"`, `component: "Input"`: similar components
- `mode: "health"`: overall design system health metrics

**`fragments_perf`** -- Component performance data.
- No params: all component bundle sizes
- `component: "DataTable"`: specific component
- `filter: "over-budget"`: find components exceeding size budgets
- `sort: "budget"`: sort by most over-budget

## Recommended workflows

### Building a new page

1. `fragments_implement` with the use case description -- get matched components + tokens
2. `fragments_blocks` to find a composition pattern close to what you need
3. `fragments_inspect` on specific components for detailed prop reference
4. `fragments_govern` to validate the result before shipping

### Reviewing existing code

1. `fragments_discover` with `compact: true` to know what's available
2. `fragments_tokens` to verify token usage in custom styles
3. `fragments_graph` with `mode: "alternatives"` to suggest better component choices

### Prototyping with AI

1. `fragments_generate_ui` with a natural language description
2. Iterate by passing the result back as `currentTree` with refinement prompts
3. `fragments_govern` to validate the generated UI

### Understanding the design system

1. `fragments_graph` with `mode: "health"` for an overview
2. `fragments_discover` with categories to explore what's available
3. `fragments_perf` to understand bundle size implications
