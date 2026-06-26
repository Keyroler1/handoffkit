# AI Repo Readiness

AI Repo Readiness audits whether a repository, tool, or MCP server is ready for AI agents to use safely and repeatably.

It starts as a CLI and GitHub Action. There is no hosted dashboard, billing system, or required API key in v1.

## What It Checks

AI Repo Readiness scores five categories:

| Category | Weight |
|---|---:|
| Agent-facing setup/docs | 25% |
| Machine interfaces: CLI/API/MCP/OpenAPI | 25% |
| Repo context quality | 20% |
| Eval/test reproducibility | 15% |
| MCP/security boundaries | 15% |

## Install

```bash
npm install -g github:Keyroler1/ai-repo-readiness
```

The `ai-repo-readiness` npm package name is available, but this machine is not authenticated to npm yet. After publishing, the install command will become `npm install -g ai-repo-readiness`.

For local development:

```bash
npm install
npm run build
npm link
```

## CLI

```bash
ai-repo-readiness audit <path>
ai-repo-readiness audit <path> --json
ai-repo-readiness generate <path> --out ai-repo-readiness-pack
ai-repo-readiness check-mcp <command-or-config>
ai-repo-readiness ci <path> --out ai-repo-readiness-artifacts
```

`audit` is read-only and prints to stdout. `generate` writes only to the selected output folder.

## Generated Pack

`ai-repo-readiness generate . --out ai-repo-readiness-pack` creates:

- `AGENTS.md`
- `repo-map.md`
- `skills/agent-setup.md`
- `skills/repo-audit.md`
- `skills/mcp-safety.md`
- `promptfoo.yaml`
- `mcp-review.md`
- `ai-repo-readiness-report.md`
- `ai-repo-readiness-report.json`

## Optional LLM Synthesis

AI Repo Readiness works deterministically without API keys. If you want a short LLM-written synthesis, pass `--llm` and set `OPENAI_API_KEY`.

```bash
OPENAI_API_KEY=... ai-repo-readiness generate . --out ai-repo-readiness-pack --llm
```

If `OPENAI_API_KEY` is missing, the deterministic report still works.

## GitHub Action

```yaml
name: AI Repo Readiness

on:
  pull_request:

jobs:
  ai-repo-readiness:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
      - uses: your-org/ai-repo-readiness@v0
        with:
          path: .
          out: ai-repo-readiness-artifacts
          comment: "true"
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

The Action uploads `ai-repo-readiness-report.md` and `ai-repo-readiness-report.json` as artifacts and writes the Markdown report to the GitHub step summary.

## Validation Loop

Before treating this as production-ready:

1. Audit 20 public repos.
2. Generate 5 useful PRs or issue comments.
3. Get at least 3 maintainers or builders to say they would use it again.
