---
name: fragments-policy
description: Create and manage Fragments design policies from natural language descriptions. Use when the user wants to create a design rule, add a policy, enforce a convention, or customize governance checks.
argument-hint: "[create|list|edit|delete] [description]"
---

# Fragments Policy

Create and manage custom design policies that Fragments enforces during governance checks.

## Modes

Parse `$ARGUMENTS` to determine the mode:

- **create** (default): Create a new policy from a natural language description
- **list**: Show all active policies
- **edit**: Modify an existing policy
- **delete**: Remove a policy

## Create mode

1. Take the user's natural language description. Examples:
   - "All buttons must have a minimum height of 44px for touch targets"
   - "Never use hex colors directly -- always use design tokens"
   - "Every page must have exactly one h1 element"

2. Generate a policy file in `.fragments/policies/`:

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

3. Register the policy in `fragments.config.ts` under the `policies` array.
4. Run a check to validate: `npx fragments govern check --cloud`
5. Show results and ask if the policy needs adjustment.

## List mode

Read all files in `.fragments/policies/` and display: policy name, description, severity, status (active/disabled).

## Edit mode

1. Show the list if not specified.
2. Let the user describe the change in natural language.
3. Update the policy file and re-run checks to validate.

## Delete mode

1. Show the list if not specified.
2. Remove the policy file and its reference in `fragments.config.ts`.

## Guidelines

- Default severity to `warning` unless explicitly requested as `error`
- Always include a human-readable `description`
- Include an auto-fix in the `fix` field when possible
- Use kebab-case for policy file names
- One concern per policy -- keep logic focused
