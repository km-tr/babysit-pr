# babysit-pr

GitHub PR babysitter skill for AI coding agents. Continuously monitors a pull request's CI checks, review comments, and mergeability state, then takes action automatically — diagnosing failures, retrying flaky jobs, addressing review feedback, and pushing fixes — until the PR is ready to merge or requires human intervention.

## Features

- **CI Monitoring** — Polls check suites / workflow runs and classifies failures as branch-related or flaky/unrelated.
- **Automatic Flaky Retry** — Reruns likely-flaky failed jobs (up to a configurable limit, default 3).
- **Review Comment Handling** — Surfaces trusted human and approved bot review comments for the agent to address.
- **Mergeability Tracking** — Watches merge-conflict and review-approval state alongside CI.
- **Adaptive Polling** — Backs off exponentially when CI is green and nothing changes; snaps back to fast polling on any state change.

## Directory Structure

```
skills/babysit-pr/
├── SKILL.md                        # Full skill specification (for the AI agent)
├── agents/
│   └── openai.yaml                 # OpenAI Codex agent interface config
├── references/
│   ├── heuristics.md               # CI failure classification checklist & decision tree
│   └── github-api-notes.md         # GitHub CLI / API reference used by the watcher
└── scripts/
    └── gh_pr_watch.py              # Watcher script (Python 3, requires `gh` CLI)
```

## Requirements

- Python 3.8+
- [GitHub CLI (`gh`)](https://cli.github.com/) authenticated with access to the target repository

## Quick Start

### One-shot snapshot

```bash
python3 skills/babysit-pr/scripts/gh_pr_watch.py --pr auto --once
```

### Continuous watch (JSONL stream)

```bash
python3 skills/babysit-pr/scripts/gh_pr_watch.py --pr auto --watch
```

### Retry failed checks

```bash
python3 skills/babysit-pr/scripts/gh_pr_watch.py --pr auto --retry-failed-now
```

### Target a specific PR

```bash
python3 skills/babysit-pr/scripts/gh_pr_watch.py --pr 42 --once
python3 skills/babysit-pr/scripts/gh_pr_watch.py --pr https://github.com/owner/repo/pull/42 --watch
```

## CLI Options

| Flag | Description |
|---|---|
| `--pr` | `auto` (infer from current branch), PR number, or PR URL. Default: `auto` |
| `--repo` | Optional `OWNER/REPO` override |
| `--once` | Emit a single JSON snapshot and exit |
| `--watch` | Continuously emit JSONL snapshots until a stop condition |
| `--retry-failed-now` | Rerun failed jobs for current failed workflow runs |
| `--poll-seconds` | Base polling interval in seconds (default: 30) |
| `--max-flaky-retries` | Max rerun cycles per head SHA (default: 3) |
| `--state-file` | Path to state JSON file (auto-generated if omitted) |

## License

Apache-2.0
