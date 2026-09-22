# pr-triage

An [agent-afk](https://github.com/griffinwork40/agent-afk) skill that batch-reviews open PRs in parallel, triages them into merge/fix/follow-up buckets, then executes each bucket with human gates on every irreversible operation.

## What it does

1. **Wave 0 -- Preflight:** Fetches open PRs and builds a manifest table.
2. **Wave 1 -- Parallel Review:** Dispatches `/review` per PR via `compose` (capped at 5 concurrent).
3. **Wave 1.5 -- Triage Gate:** Classifies each PR into GREEN (merge), BLOCKED (fixable), FOLLOW-UP (needs human), or SKIP.
4. **Wave 2 -- Execute Buckets:** Sequential merges with CI checks, parallel `/fix-pr` dispatches for blocked PRs, and tracking issue creation for follow-ups. Every irreversible action is human-gated.
5. **Wave 3 -- Re-review:** Optionally re-reviews fixed PRs and offers to merge.

## Install

```bash
afk skill install griffinwork40/pr-triage
```

Or clone directly into your skills directory:

```bash
git clone https://github.com/griffinwork40/pr-triage.git ~/.afk/skills/pr-triage
```

## Usage

```
/pr-triage                              # Triage all open PRs (up to 10)
/pr-triage --prs 101,102,105            # Triage specific PRs
/pr-triage --limit 20                   # Raise the discovery limit
/pr-triage --repo owner/repo            # Target a different repo
/pr-triage --auto-merge                 # Skip human gate for green PR merges
/pr-triage --skip-fix                   # Triage and merge only, skip fix phase
```

## Flags

| Flag | Default | Description |
|------|---------|-------------|
| `--prs N,N,...` | all open | Specific PR numbers to triage |
| `--limit N` | 10 | Max PRs to fetch in discovery |
| `--repo owner/repo` | cwd remote | Target repository |
| `--auto-merge` | off | Batch-merge green PRs (still sequential + CI-gated) |
| `--skip-fix` | off | Skip the fix phase |

## Requirements

- [agent-afk](https://github.com/griffinwork40/agent-afk) installed and configured
- `gh` CLI authenticated (`gh auth status`)
- Sibling skills: `/review`, `/fix-pr`

## How it fits together

`/pr-triage` is the review-and-merge half of a two-skill loop:

- **[/tackle-issues](https://github.com/griffinwork40/tackle-issues)** burns down open issues into PRs (parallel, lightweight, no gates)
- **`/pr-triage`** reviews, triages, merges, and fixes those PRs (sequential gates, human-approved)

## License

MIT
