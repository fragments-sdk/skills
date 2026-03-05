# Fragments Skills

Agent skills for [Fragments](https://fragments.dev) -- the design governance platform and UI component library. Works with Claude Code, Cursor, Copilot, and any agent supporting the [Agent Skills](https://agentskills.io) standard.

## Install

```bash
npx skills add fragments-sdk/skills
```

Install a specific skill:

```bash
npx skills add fragments-sdk/skills --skill fragments-ui-setup
```

## Skills

### Setup

Two setup paths depending on what you need:

| Skill | Description | Invocation |
|-------|-------------|------------|
| [fragments-ui-setup](skills/fragments-ui-setup/) | Set up `@fragments-sdk/ui` -- install, theming, provider, MCP server, sample component | `/fragments-ui-setup` |
| [fragments-cloud-setup](skills/fragments-cloud-setup/) | Set up Fragments Cloud governance -- install `@fragments-sdk/govern`, API key, config, first check, CI/CD | `/fragments-cloud-setup` or `/fragments-cloud-setup <api-key>` |

### Governance

| Skill | Description | Invocation |
|-------|-------------|------------|
| [fragments-govern](skills/fragments-govern/) | Run governance checks, review staged changes pre-commit, auto-fix violations | `/fragments-govern check`, `/fragments-govern review --staged` |
| [fragments-policy](skills/fragments-policy/) | Create custom design policies from natural language | `/fragments-policy create "buttons must be 44px min"` |

### Reference (auto-loaded)

These skills load automatically when relevant -- no need to invoke them directly:

| Skill | Description |
|-------|-------------|
| [fragments-ui](skills/fragments-ui/) | Best practices for `@fragments-sdk/ui` -- component selection, tokens, patterns, accessibility |
| [fragments-mcp](skills/fragments-mcp/) | Reference for all Fragments MCP tools -- discover, generate, validate, inspect |

## What is Fragments?

**UI Components** (`@fragments-sdk/ui`) -- A complete design system with tokens, components, composition blocks, and MCP tools for AI-assisted development.

**Governance** (`@fragments-sdk/govern`) -- Run checks from CLI or CI, track violations, and enforce design policies. View results in the Fragments Cloud dashboard.

## License

MIT
