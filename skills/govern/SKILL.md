---
name: fragments/govern
description: Run Fragments design governance checks, review staged changes, and fix violations inline. Use when the user wants to check UI code for design issues, review changes before committing, run a governance check, fix accessibility or consistency problems, or validate code against design policies.
type: core
library: "@fragments-sdk/govern"
library_version: ">=0.1.0"
sources:
  - "fragments-sdk/skills:skills/govern/SKILL.md"
argument-hint: "[check|fix|review|status] [path]"
---

# Fragments Govern

Run design governance checks, review pre-commit changes, and fix violations.

## Prerequisites

`@fragments-sdk/govern` and `@fragments-sdk/cli` must be installed, and `fragments.config.ts` must exist. If not, run `/fragments-cloud-setup` first.

## Modes

Parse `$ARGUMENTS` to determine the mode:

- **check** (default): Run checks and report violations
- **fix**: Run checks, then automatically fix violations that have auto-fixes
- **review**: Check only staged/uncommitted UI files (pre-commit review)
- **status**: Show current violation count and trends

If no mode is specified, default to `check`.

---

## Check mode

1. Verify `@fragments-sdk/govern` and `@fragments-sdk/cli` are installed. If not, offer to run `/fragments-cloud-setup`.
2. If a file path is provided in `$ARGUMENTS`, scope the check to that path.
3. Run:
   ```bash
   npx fragments govern check --cloud --format json
   ```
4. Parse the JSON output. The response shape:
   ```json
   {
     "summary": { "errors": 3, "warnings": 7, "info": 1, "files": 4 },
     "violations": [
       {
         "rule": "accessibility/color-contrast",
         "severity": "error",
         "file": "src/components/Card.tsx",
         "line": 42,
         "message": "Contrast ratio 2.8:1 is below minimum 4.5:1",
         "fix": { "property": "color", "value": "var(--fui-text-primary)" }
       }
     ]
   }
   ```
5. Present results:
   - Group violations by severity (error, warning, info)
   - Group by file for easier navigation
   - Show the rule name, line number, and a snippet of offending code
6. Summarize: "X errors, Y warnings, Z info across N files"

## Fix mode

1. Run the check to identify violations.
2. For violations with a `fix` field:
   - Show the proposed change
   - Apply it
3. For violations without auto-fixes, explain what needs to change and why.
4. Re-run the check to confirm fixes applied correctly.

## Review mode

Pre-commit design review -- checks only changed files.

1. Determine scope:
   - If `$ARGUMENTS` contains `--staged`, check only git-staged files
   - Otherwise check all uncommitted changes (staged + unstaged)
2. Gather changed UI files:
   ```bash
   git diff --cached --name-only --diff-filter=ACMR -- '*.tsx' '*.jsx' '*.vue' '*.svelte' '*.astro'
   ```
3. Run the governance check scoped to those files:
   ```bash
   npx fragments govern check --cloud --format json --files <file-list>
   ```
   If the `--files` flag is not available, pass each file path as a positional argument.
4. Present findings grouped by file with line numbers.
5. Offer to auto-fix violations that have fixes available.
6. If no issues: confirm with "All clear -- ready to commit."

## Status mode

1. Run:
   ```bash
   npx fragments govern status --cloud --format json
   ```
2. The response shape:
   ```json
   {
     "total": 11,
     "trend": { "previous": 14, "direction": "improving" },
     "topRules": [
       { "rule": "consistency/design-tokens", "count": 5 },
       { "rule": "accessibility/color-contrast", "count": 3 },
       { "rule": "responsive/touch-targets", "count": 2 }
     ],
     "topFiles": [
       { "file": "src/components/Card.tsx", "count": 4 },
       { "file": "src/pages/Settings.tsx", "count": 3 }
     ]
   }
   ```
3. Show: total open violations, trend vs. last check, top 3 most common violation types, files with the most violations.

---

## Check categories reference

When explaining violations, reference these:

- **accessibility**: Color contrast, ARIA labels, keyboard navigation, focus management, alt text
- **consistency**: Design token usage, spacing patterns, component prop consistency, naming conventions
- **responsive**: Breakpoint coverage, flexible layouts, touch targets, viewport handling

Always show the exact rule name so users can look it up or create policy exceptions.

## Common mistakes

### CRITICAL: Missing `--cloud` flag with cloud-synced policies

Running `govern check` without `--cloud` only runs local rules. Cloud-synced policies and team-wide settings are skipped silently.

Wrong:
```bash
npx fragments govern check --format json
```

Correct:
```bash
npx fragments govern check --cloud --format json
```

If the project has `cloud: true` in `fragments.config.ts`, always include `--cloud`.

### HIGH: Committing auto-fixes without review

Fix mode applies changes automatically, but some fixes may be incorrect (e.g., a token substitution that picks the wrong semantic color). Always review the diff before committing.

Wrong workflow:
```bash
npx fragments govern fix --cloud
git add . && git commit -m "fix violations"
```

Correct workflow:
```bash
npx fragments govern fix --cloud
git diff                    # review what changed
npx fragments govern check --cloud  # confirm no new violations
git add -p                  # stage selectively
```

### MEDIUM: Parsing human-readable output programmatically

Without `--format json`, the output is human-readable text that changes between versions.

Wrong:
```bash
npx fragments govern check --cloud | grep "error"
```

Correct:
```bash
npx fragments govern check --cloud --format json
```

Always use `--format json` when parsing output programmatically. Use `--format github` in GitHub Actions to get PR annotations.
