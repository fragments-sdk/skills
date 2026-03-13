# Fragments Skills

This repo contains agent skills for the Fragments design governance platform. Each skill is in `skills/<name>/SKILL.md`.

When using these skills, always check for an existing `fragments.config.ts` and respect its configuration. Detect the user's package manager from lock files before running install commands.

Skills follow the TanStack Intent standard. Run `npx @tanstack/intent@latest validate` before publishing to npm.
