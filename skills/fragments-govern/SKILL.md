---
name: fragments-govern
description: Run Fragments design governance checks, review staged changes, and fix violations inline. Use when the user wants to check UI code for design issues, review changes before committing, run a governance check, fix accessibility or consistency problems, or validate code against design policies.
argument-hint: "[check|fix|review|status] [path]"
---

# Fragments Govern

Run design governance checks, review pre-commit changes, and fix violations.

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
4. Parse the JSON output and present results:
   - Group violations by severity (error, warning, info)
   - Group by file for easier navigation
   - Show the rule name, line number, and a snippet of offending code
5. Summarize: "X errors, Y warnings, Z info across N files"

## Fix mode

1. Run the check to identify violations.
2. For violations with auto-fixes:
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
   git diff --cached --name-only --diff-filter=ACMR -- '*.tsx' '*.jsx' '*.vue' '*.svelte'
   ```
3. For each file, analyze for:
   - **Design token compliance** -- hardcoded colors, spacing, font sizes that should use tokens
   - **Accessibility** -- missing alt text, ARIA labels, keyboard handlers
   - **Component consistency** -- inline styles duplicating utility classes, divergent prop patterns
   - **Responsive design** -- fixed widths on containers, touch targets under 44px
4. Present findings grouped by file with line numbers.
5. Offer to auto-fix token replacements and missing attributes.
6. If no issues: confirm with a short "All clear -- ready to commit."

## Status mode

1. Run:
   ```bash
   npx fragments govern status --cloud --format json
   ```
2. Show: total open violations, trend vs. last check, top 3 most common violation types, files with the most violations.

---

## Check categories reference

When explaining violations, reference these:

- **accessibility**: Color contrast, ARIA labels, keyboard navigation, focus management, alt text
- **consistency**: Design token usage, spacing patterns, component prop consistency, naming conventions
- **responsive**: Breakpoint coverage, flexible layouts, touch targets, viewport handling

Always show the exact rule name so users can look it up or create policy exceptions.
