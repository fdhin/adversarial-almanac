---
name: "claude-bug-bounty"
categories:
  - ai
version: "git (2026-10-28)"
homepage: "https://github.com/shuvonsec/claude-bug-bounty"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "shuvonsec/claude-bug-bounty"
github-stars: 5766
github-pushed: "2026-10-28"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 268
gh-activity-checked: "2026-11-09"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 11
---

# claude-bug-bounty

> Agentic Bug Hunter — an AI bug-bounty operator in the terminal that runs reconnaissance and vulnerability discovery against targets you're authorized to test, with or without a paid AI subscription.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Delegating the recon-to-findings loop to an LLM agent. You give it a target; it plans and executes reconnaissance, probes the attack surface it discovered, and reports what it finds — the drudgery half of bug bounty, automated. The free setup mode works with a non-subscription AI provider, and everything runs locally in your terminal. It is scoped for authorized testing (your programs, your labs); findings still need human validation before reporting, and it does not write your HackerOne reports for you.

## Typical use cases

### First-time setup with a free provider

```
bughunter setup
```

Outcome: interactive configuration — pick an AI provider (free options included), set API keys — after which the `bughunter` command is ready. The PyPI package name is `agentic-bug-hunter`.

### Run reconnaissance against a target

```
bughunter recon target.example.com
```

Outcome: the agent enumerates the target's exposed surface (subdomains, live services, web endpoints) and summarizes what it mapped, giving you — and the next stage — a working inventory of the attack surface.

### Hand off to autonomous hunting

```
bughunter hunt target.example.com
```

Outcome: the agent takes the recon results and probes for vulnerabilities autonomously, iterating on what it learns, and surfaces candidate findings with enough context (target, request, reasoning) for you to verify and report them.
