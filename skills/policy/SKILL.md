---
name: fragments/policy
description: Create and manage Fragments design policies from natural language descriptions. Use when the user wants to create a design rule, add a policy, enforce a convention, or customize governance checks.
type: core
library: "@fragments-sdk/govern"
library_version: ">=0.1.0"
sources:
  - "fragments-sdk/skills:skills/policy/SKILL.md"
argument-hint: "[create|list|edit|delete] [description]"
---

# Fragments Policy

Create and manage custom design policies that Fragments enforces during governance checks.

## Prerequisites

- `@fragments-sdk/govern` and `@fragments-sdk/cli` must be installed. If not, run `/fragments-cloud-setup` first.
- A `fragments.config.ts` must exist. Ensure it includes a `policies` glob:

```ts
import { defineConfig } from '@fragments-sdk/govern';

export default defineConfig({
  cloud: true,
  checks: ['accessibility', 'consistency', 'responsive'],
  policies: ['./.fragments/policies/*.ts'],
  input: './src/**/*.{tsx,jsx}',
});
```

## Modes

Parse `$ARGUMENTS` to determine the mode:

- **create** (default): Create a new policy from a natural language description
- **list**: Show all active policies
- **edit**: Modify an existing policy
- **delete**: Remove a policy

## Create mode

1. Take the user's natural language description.

2. Generate a policy file in `.fragments/policies/`. Here are examples covering common categories:

**Touch target enforcement:**

```ts
// .fragments/policies/min-button-height.ts
import { definePolicy } from '@fragments-sdk/govern';

export default definePolicy({
  name: 'min-button-height',
  description: 'All buttons must have a minimum height of 44px for touch targets',
  severity: 'error',
  check({ node, report }) {
    if (node.type === 'button' || node.role === 'button') {
      const height = node.computedStyles?.height;
      if (height && parseFloat(height) < 44) {
        report({
          message: `Button height is ${height} but minimum is 44px`,
          fix: { property: 'minHeight', value: '44px' },
        });
      }
    }
  },
});
```

**Design token enforcement:**

```ts
// .fragments/policies/no-hardcoded-colors.ts
import { definePolicy } from '@fragments-sdk/govern';

export default definePolicy({
  name: 'no-hardcoded-colors',
  description: 'Colors must use design tokens, not raw hex/rgb values',
  severity: 'warning',
  check({ node, report }) {
    const colorProps = ['color', 'backgroundColor', 'borderColor'];
    for (const prop of colorProps) {
      const value = node.styles?.[prop];
      if (value && /^(#|rgb|hsl)/.test(value)) {
        report({
          message: `Hardcoded color "${value}" on "${prop}" -- use a design token instead`,
          fix: { property: prop, value: 'var(--fui-text-primary)' },
        });
      }
    }
  },
});
```

**Structural accessibility:**

```ts
// .fragments/policies/single-h1.ts
import { definePolicy } from '@fragments-sdk/govern';

export default definePolicy({
  name: 'single-h1',
  description: 'Each page must have exactly one h1 element',
  severity: 'error',
  check({ node, context, report }) {
    if (node.tag === 'h1') {
      context.h1Count = (context.h1Count || 0) + 1;
      if (context.h1Count > 1) {
        report({
          message: `Multiple h1 elements found (this is #${context.h1Count}). Use h2-h6 for subheadings.`,
        });
      }
    }
  },
});
```

3. Register the policy -- ensure `fragments.config.ts` has a `policies` glob that matches the new file path.
4. Run a check to validate: `npx fragments govern check --cloud`
5. Show results and ask if the policy needs adjustment.

## List mode

```bash
npx fragments govern policies list
```

If the CLI subcommand is not available, read all files in `.fragments/policies/` directly and display: policy name, description, severity, status (active/disabled).

## Edit mode

1. If no policy name is specified, show the list first.
2. Read the target policy file in `.fragments/policies/`.
3. Let the user describe the change in natural language.
4. Update the policy file and re-run checks to validate:
   ```bash
   npx fragments govern check --cloud
   ```

## Delete mode

1. If no policy name is specified, show the list first.
2. Confirm with the user before deleting.
3. Remove the policy file from `.fragments/policies/`.
4. Remove its reference from `fragments.config.ts` if individually listed (not needed if using a glob pattern).
5. Confirm deletion.

## Common mistakes

### CRITICAL: Policy exists but never runs

Forgetting to add the `policies` glob in `fragments.config.ts`. The file sits on disk but the governance engine never loads it.

Wrong -- no `policies` field:
```ts
import { defineConfig } from '@fragments-sdk/govern';

export default defineConfig({
  cloud: true,
  checks: ['accessibility', 'consistency'],
  input: './src/**/*.{tsx,jsx}',
});
```

Correct -- `policies` glob included:
```ts
import { defineConfig } from '@fragments-sdk/govern';

export default defineConfig({
  cloud: true,
  checks: ['accessibility', 'consistency'],
  policies: ['./.fragments/policies/*.ts'],
  input: './src/**/*.{tsx,jsx}',
});
```

### HIGH: Multi-concern policy

Bundling unrelated checks into one policy makes it impossible to disable a single rule without losing the others.

Wrong -- one policy checking touch targets, colors, and headings:
```ts
export default definePolicy({
  name: 'ui-standards',
  severity: 'error',
  check({ node, report }) {
    if (node.type === 'button' && parseFloat(node.computedStyles?.height) < 44) {
      report({ message: 'Button too small' });
    }
    if (node.styles?.color && /^#/.test(node.styles.color)) {
      report({ message: 'Hardcoded color' });
    }
  },
});
```

Correct -- split into `min-button-height.ts` and `no-hardcoded-colors.ts` with one concern each.

### MEDIUM: `error` severity for style preferences

`error` severity fails CI pipelines. Reserve it for accessibility and functional issues. Use `warning` for stylistic guidance.

Wrong:
```ts
export default definePolicy({
  name: 'prefer-semibold',
  description: 'Headings should use semibold weight',
  severity: 'error',
  // ...
});
```

Correct:
```ts
export default definePolicy({
  name: 'prefer-semibold',
  description: 'Headings should use semibold weight',
  severity: 'warning',
  // ...
});
```

## Guidelines

- Default severity to `warning` unless explicitly requested as `error`
- Always include a human-readable `description`
- Include an auto-fix in the `fix` field when possible
- Use kebab-case for policy file names
- One concern per policy -- keep logic focused
