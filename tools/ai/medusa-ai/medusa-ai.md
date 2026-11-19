---
name: "medusa-ai"
categories:
  - blackarch-ai
version: "2026.7.0"
homepage: "https://github.com/Pantheon-Security/medusa"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
meta-checked: "2026-11-09"
github-repo: "Pantheon-Security/medusa"
github-stars: 1015
github-pushed: "2026-08-10"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 347
gh-activity-checked: "2026-11-09"
---

# medusa-ai

> AI-first security scanner with 40,000+ detection patterns for AI/ML stacks, agents, MCP servers and RAG pipelines — also catches classic CVEs, works out of the box with no other tools installed.

*No corpus evidence — derived from upstream documentation. (PyPI package: `medusa-security`.)*

## What it's for

Scanning the new attack surface that regular SAST tools don't model: LLM applications, agent frameworks, MCP server configurations and RAG pipelines — supply-chain poisoning of AI dependencies, prompt injection planted in repos or MCP tool descriptions, leaked API keys in agent configs — alongside conventional CVE detection (Log4Shell, Spring4Shell, XZ Utils and ~200 more). It is deliberately self-contained: no external scanner installs required, cross-platform. Run it on AI-heavy codebases before commit and on third-party agent tooling before you wire it into anything privileged.

## Typical use cases

### Scan a local project

```
medusa scan .
```

Outcome: the working tree is analyzed against the full pattern set; findings print with severity and file/line — the everyday invocation for your own repo before pushing.

### Audit a third-party repository before adopting it

```
medusa scan --git https://github.com/someorg/some-agent-toolkit
```

Outcome: the repo is fetched and checked for AI supply-chain attacks — repo poisoning, planted prompt injection, MCP tool poisoning — so you know whether an agent toolkit or MCP server is safe to connect before it ever touches your environment.

### Hunt leaked AI provider keys

```
medusa secrets scan
```

Outcome: the configured scope is searched for exposed API keys (Claude, Cursor and other AI tool credentials), reporting exact locations — the fastest cleanup pass after an engagement or before open-sourcing a directory.

### Install the guardrails into your AI IDE tooling

```
medusa init --ide claude-code
```

Outcome: MEDUSA's checks are wired into the named IDE/agent tool so its generated code and edits are scanned as you work, rather than only at scan time.
