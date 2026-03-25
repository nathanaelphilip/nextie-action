# Nextie Action

Automatically scan your Next.js repo for issues, fix them, and create PRs.

## Features

- **Static analysis** — typos, security, performance, type safety, error handling, Next.js best practices
- **Auto-fix with tests** — only commits fixes that pass your test suite
- **Smart PR summaries** — AI-generated titles and descriptions (powered by GitHub Models)
- **Zero config** — works out of the box, no API keys required

## Usage

```yaml
name: Nextie

on:
  schedule:
    - cron: '0 9 * * 1'  # Weekly on Monday
  workflow_dispatch:

jobs:
  nextie:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: nathanaelphilip/nextie-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `github-token` | GitHub token for creating PRs | **required** |
| `analyzers` | Comma-separated categories to run | all |
| `exclude` | Glob patterns to exclude | |
| `threshold` | Minimum confidence (0-1) | `0.8` |
| `test-command` | Override test command | auto-detect |
| `base-branch` | Base branch for PR | `main` |
| `node-version` | Node.js version | `20` |
| `review-mode` | Create one PR per fix for review (max 5) | `false` |

### Analyzer Categories

`typos`, `security`, `type-safety`, `error-handling`, `performance`, `code-quality`, `testing`, `nextjs`

## Outputs

| Output | Description |
|--------|-------------|
| `pr-url` | URL of created PR (empty if no fixes) |
| `fixes-committed` | Number of fixes committed |
| `fixes-reverted` | Number of fixes reverted (failed tests) |

## License

MIT
