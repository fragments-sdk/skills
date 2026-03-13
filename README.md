# Fragments Skills

Agent skills for [Fragments](https://fragments.dev) -- the design governance platform and UI component library. Works with Claude Code, Cursor, Copilot, and any agent supporting the [Agent Skills](https://agentskills.io) standard. Listed on the [TanStack Intent registry](https://tanstack.com/intent).

## Install

Via TanStack Intent (auto-discovered from npm):

```bash
npm install @fragments-sdk/skills
npx @tanstack/intent list
```

Via Agent Skills:

```bash
npx skills add fragments-sdk/skills
```

Install a specific skill:

```bash
npx skills add fragments-sdk/skills --skill ui-setup
```

## Skills

### Setup

Two setup paths depending on what you need:

| Skill | Description | Invocation |
|-------|-------------|------------|
| [ui-setup](skills/ui-setup/) | Set up `@fragments-sdk/ui` -- install, theming, provider, MCP server, sample component | `/ui-setup` |
| [cloud-setup](skills/cloud-setup/) | Set up Fragments Cloud governance -- install `@fragments-sdk/govern`, API key, config, first check, CI/CD | `/cloud-setup` |

### Governance

| Skill | Description | Invocation |
|-------|-------------|------------|
| [govern](skills/govern/) | Run governance checks, review staged changes pre-commit, auto-fix violations | `/govern check`, `/govern review --staged` |
| [policy](skills/policy/) | Create custom design policies from natural language | `/policy create "buttons must be 44px min"` |

### Reference (auto-loaded)

These skills load automatically when relevant -- no need to invoke them directly:

| Skill | Description |
|-------|-------------|
| [ui](skills/ui/) | Best practices for `@fragments-sdk/ui` -- component selection, tokens, patterns, accessibility |
| [mcp](skills/mcp/) | Reference for all Fragments MCP tools -- discover, generate, validate, inspect |

## What is Fragments?

**UI Components** (`@fragments-sdk/ui`) -- A complete design system with tokens, components, composition blocks, and MCP tools for AI-assisted development.

**Governance** (`@fragments-sdk/govern`) -- Run checks from CLI or CI, track violations, and enforce design policies. View results in the Fragments Cloud dashboard.

## License

MIT
