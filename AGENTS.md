# Fragments Skills

This repository contains agent skills for the Fragments design governance platform. Skills are located in the `skills/` directory, each with a `SKILL.md` entrypoint.

## Skill format

Each skill follows the [TanStack Intent](https://tanstack.com/intent) standard with YAML frontmatter:
- `name`: `fragments/<skill-name>` (matches the directory path under `skills/`)
- `description`: Agent routing key -- describes when an AI agent should load this skill
- `type`: `core` for standalone skills
- `library`: The npm package this skill covers
- `library_version`: Compatible version range
- `sources`: Source file references

Skills also retain Agent Skills open standard fields (`argument-hint`, `disable-model-invocation`, `user-invocable`) for backward compatibility.

## Skill directory

| Skill | Library | Purpose |
|-------|---------|---------|
| [policy](skills/policy/) | `@fragments-sdk/govern` | Create custom governance policies |
| [govern](skills/govern/) | `@fragments-sdk/govern` | Run checks, review, fix violations |
| [cloud-setup](skills/cloud-setup/) | `@fragments-sdk/govern` | Set up governance from scratch |
| [ui-setup](skills/ui-setup/) | `@fragments-sdk/ui` | Set up the component library |
| [ui](skills/ui/) | `@fragments-sdk/ui` | Best practices and patterns |
| [mcp](skills/mcp/) | `@fragments-sdk/mcp` | MCP server tool reference |
