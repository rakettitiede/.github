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
5. Push and open a PR using the AI Talent template

## Commit Messages

We use [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add new search filter
fix: correct embedding dimension
docs: update deployment guide
refactor: simplify ResultsMap logic
infra: add smoke test workflow
chore: update dependencies
```

## Pull Requests

Always use the AI Talent PR template when opening PRs. Append `?template=ai-talent.md` to the GitHub PR URL, or use it directly in `gh pr create`:

```bash
gh pr create \
  --title "fix: your title here" \
  --body "## What
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
<!--triage-end-->"
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
