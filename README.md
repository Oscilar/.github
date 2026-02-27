# oscilar/.github

Centralized GitHub Actions assets for the `oscilar` organization.

## Included

- Reusable workflow: `.github/workflows/codex-pr-review.yml`
- Workflow template: `workflow-templates/codex-pr-review.yml`

## What this workflow does

When a caller repo triggers on `pull_request` (`opened`, `synchronize`, `reopened`), it:

1. Checks out the PR merge ref.
2. Runs `openai/codex-action@v1` with a review-focused prompt.
3. Upserts a single PR comment with Codex feedback.

## Setup

1. Add `CI_OPENAI_API_KEY` as an organization or repository secret.
2. Choose visibility based on how you want to distribute workflows:
   - Reusable workflows can be hosted in a private `oscilar/.github` repo, but you must allow access from org repositories in Actions settings.
   - Workflow templates are only supported when the `oscilar/.github` repository is public.
3. In each consuming repo, either:
   - Use the workflow template from Actions -> New workflow, or
   - Add this workflow file manually:

```yaml
name: Codex PR Review

on:
  pull_request:
    types:
      - opened
      - synchronize
      - reopened

permissions:
  contents: read
  issues: write
  pull-requests: write

jobs:
  codex_pr_review:
    uses: oscilar/.github/.github/workflows/codex-pr-review.yml@main
    secrets:
      CI_OPENAI_API_KEY: ${{ secrets.CI_OPENAI_API_KEY }}
```

## Optional inputs in caller repos

The reusable workflow accepts optional `with:` inputs:

- `model`
- `effort`
- `sandbox` (defaults to `read-only`)
- `working_directory`
- `codex_args`
- `additional_instructions`

Example:

```yaml
jobs:
  codex_pr_review:
    uses: oscilar/.github/.github/workflows/codex-pr-review.yml@main
    with:
      model: codex-mini-latest
      effort: medium
      additional_instructions: "Prioritize auth, schema migrations, and breaking API changes."
    secrets:
      CI_OPENAI_API_KEY: ${{ secrets.CI_OPENAI_API_KEY }}
```
