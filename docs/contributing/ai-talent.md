# AI Talent — Contributing Guide

This guide applies to all AI Talent projects:
[ai-talent-search-mcp](https://github.com/rakettitiede/mcp-agileday) · [ai-talent-search-pyry](https://github.com/rakettitiede/ai-talent-search-pyry) · [ai-talent-network-mcp](https://github.com/rakettitiede/ai-talent-network-mcp) · [ai-talent-network-minna](https://github.com/rakettitiede/ai-talent-network-minna)

## Getting Started

1. Clone the repository
2. Install dependencies: `npm install`
3. Set up the correct Node.js version: `nvm use`
4. Run tests: `npm test`

See each repo's `docs/deployment/local.md` for detailed setup instructions.

## Development Workflow

1. Create a branch from `main`
2. Make your changes
3. Run tests: `npm test`
4. Commit with a conventional commit message
5. Push and open a PR

## Commit Messages

We use [Conventional Commits](https://www.conventionalcommits.org/). Since we squash-merge all PRs, the convention applies to the **PR title** (which becomes the squashed commit message). Individual commits on a feature branch can be informal.

```
feat: add new search filter
fix: correct embedding dimension
docs: update deployment guide
refactor: simplify ResultsMap logic
infra: add smoke test workflow
chore: update dependencies
```

## Pull Requests

Each ai-talent repo has a default PR template at `.github/pull_request_template.md` that auto-loads on `gh pr create` and in the GitHub web UI. When opening a PR, fill in the `## What` section and the triage block:

```
## What
Your description here.

<!--triage-start-->
## Triage
<!-- P0 = critical/blocking · P1 = important · P2 = nice to have -->
<!-- XS = <30min · S = ~1h · M = ~half day · L = ~day · XL = multi-day -->
<!-- bug · enhancement · refactor · docs · infra · chore -->

| Field    | Value  |
|----------|--------|
| Priority |   P1   |
| Size     |   S    |
| Label    | chore  |
<!--triage-end-->
```

### Auto-triage

The `<!--triage-start-->` / `<!--triage-end-->` delimiters are parsed by the reusable [ai-talent-auto-triage workflow](https://github.com/rakettitiede/.github/blob/main/.github/workflows/ai-talent-auto-triage.yml) which automatically:

- Assigns the PR/issue author
- Applies the GitHub label from the `Label` field
- Adds the item to the [AI Talent Search project board](https://github.com/orgs/rakettitiede/projects/4)
- Sets Estimate = 1, Start date = today, Target date = today, Sprint = current sprint
- Sets Priority and Size on the project board if filled in

Priority and Size are silently skipped if left as HTML comments — no enforcement.

## Architecture Decision Records (ADRs)

For significant technical decisions, create an ADR in `docs/architecture/adr/`:

1. Check the next available number in `docs/architecture/adr/`
2. Create `NNNN-short-title.md`
3. Add an entry to `docs/architecture/adr/README.md`

## Code Style

- ES modules (`.mjs`) throughout
- No comments — use clear, self-documenting naming instead
- No TypeScript
- Optimistic programming — we control the environment end-to-end

## Testing

Smoke tests only for Pyry and Minna. Full integration tests for MCP servers.
Never add LLM behaviour tests — non-deterministic responses make them brittle and low value.

## Releases

All 4 ai-talent services use [release-please](https://github.com/googleapis/release-please) to manage releases. release-please watches commits on `main` and keeps an always-open release PR with the next version bump and changelog.

### How a release happens

1. PRs are merged to `main` with conventional-commit titles (`feat:`, `fix:`, `chore:` etc.)
2. release-please updates the open release PR (e.g. `chore(main): release v1.2.3`) with the calculated version and changelog
3. When ready to ship, merge the release PR
4. release-please creates the git tag and the GitHub Release
5. The tag triggers `google-cloudrun-docker.yml` which deploys to Cloud Run

No laptop `git push --tags` required.

### Version bumps

| Commit prefix | Bump (1.x+) | Bump (0.x) |
|---|---|---|
| `fix:` | patch | patch |
| `feat:` | minor | patch |
| `feat!:` or `BREAKING CHANGE:` | major | minor |
| `docs:`, `chore:`, `refactor:`, `test:`, `ci:`, `build:`, `perf:`, `style:` | none | none |

`ai-talent-network-mcp` is currently on `0.x` so its bumps follow the right column. The other 3 repos are on `1.x+` and follow the left column.

### Release cadence

Daily releases at most. The always-open release PR is the batching mechanism: changes accumulate during the day, you merge once when ready to ship.

### If main is red

The release workflow does **not** pre-check CI status. The safety net lives inside `google-cloudrun-docker.yml`: the `quality-assurance` job runs before deploy. If QA fails, Cloud Run is never touched — production is safe.

If you cut a release on a red main:

1. release-please creates tag `vX.Y.Z` pointing at the broken commit
2. The tag-triggered deploy runs QA, QA fails, deploy is skipped
3. Cleanup: delete the bad tag locally and on origin, fix main via PR, merge the next release PR

```bash
git fetch --tags
git tag -d vX.Y.Z
git push --delete origin vX.Y.Z
```
