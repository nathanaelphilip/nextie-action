# Nextie Action

Scan your Next.js repo for issues, auto-fix them, and create PRs. Static analysis works out of the box — no API keys required.

## Quick Setup

The easiest way to get started:

```bash
npx @functionform/nextie init --workflow
```

This creates a workflow file in your repo. Push and trigger from **Actions > Nextie > Run workflow**.

## Repository Permissions

The action needs permission to create branches and PRs. In your repo, go to **Settings > Actions > General**, scroll to **Workflow permissions**, and select:

- **Read and write permissions**
- **Allow GitHub Actions to create and approve pull requests**

## Manual Setup

```yaml
name: Nextie

on:
  schedule:
    - cron: '0 9 * * 1'  # Weekly on Monday
  workflow_dispatch:

permissions:
  contents: write
  pull-requests: write

jobs:
  nextie:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: nathanaelphilip/nextie-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          review-mode: 'true'
```

## Review Mode

With `review-mode: 'true'`, nextie creates **one PR per fix** (max 5). Each PR contains the actual code change.

- **Merge** a PR to accept the fix
- **Close** a PR to skip it permanently
- Add the **`nextie-retry`** label before closing to re-suggest on the next run

Stale branches from closed/merged PRs are cleaned up automatically on the next run.

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `github-token` | GitHub token for creating PRs | **required** |
| `analyzers` | Comma-separated analyzer categories | all |
| `exclude` | Glob patterns to exclude | |
| `threshold` | Confidence threshold (0-1) | `0.8` |
| `test-command` | Override auto-detected test command | |
| `base-branch` | Base branch for PRs | `main` |
| `node-version` | Node.js version | `20` |
| `review-mode` | One PR per fix (max 5) | `false` |
| `max-fixes` | Max fixes to apply (0 = unlimited) | `0` |
| `incremental` | Process one fix at a time with test validation | `true` |
| `skip-on-fail` | Continue when a fix fails | `true` |
| `separate-prs` | Separate PRs per analyzer category | `false` |
| `anthropic-api-key` | Enable AI-enhanced analysis (optional) | |
| `provider` | AI provider: `anthropic` or `ollama` | |
| `model` | AI model to use | |
| `debug` | Enable debug output | `false` |

### Analyzer Categories

`typos`, `security`, `type-safety`, `error-handling`, `performance`, `code-quality`, `testing`, `nextjs`

## Outputs

| Output | Description |
|--------|-------------|
| `pr-url` | URL of the first created PR |
| `pr-urls` | All review PR URLs (newline-separated) |
| `prs-created` | Number of review PRs created |
| `fixes-committed` | Fixes committed (commit mode) |
| `fixes-reverted` | Fixes reverted (failed tests) |

## Examples

### Run specific analyzers

```yaml
- uses: nathanaelphilip/nextie-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    analyzers: security,performance
```

### Exclude directories

```yaml
- uses: nathanaelphilip/nextie-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    exclude: "src/legacy/**,tests/**"
```

### Commit mode (auto-apply)

```yaml
- uses: nathanaelphilip/nextie-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

Without `review-mode`, all passing fixes are committed to a single PR automatically.

### With AI enhancement

```yaml
- uses: nathanaelphilip/nextie-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    anthropic-api-key: ${{ secrets.GITHUB_TOKEN }}
```

## Cache Action

A separate action for Next.js 16 caching analysis:

```yaml
- uses: nathanaelphilip/nextie-action/action-cache.yml@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

| Input | Description | Default |
|-------|-------------|---------|
| `github-token` | GitHub token for creating PRs | **required** |
| `base-branch` | Base branch for PRs | `main` |
| `profile` | Cache profile: `default`, `minutes`, `hours`, `days`, `weeks`, `max` | `hours` |
| `debug` | Enable debug output | `false` |

## CLI

Install the CLI for local usage:

```bash
npx @functionform/nextie fix .
```

See [@functionform/nextie on npm](https://www.npmjs.com/package/@functionform/nextie) for full CLI documentation.

## License

MIT
