---
name: "prompt-injection-in-the-wild"
categories:
  - ai
version: "git (2026-11-01)"
homepage: "https://github.com/cybershujin/prompt-injection-in-the-wild"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "cybershujin/prompt-injection-in-the-wild"
github-stars: 4
github-pushed: "2026-11-01"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 2
---

# prompt-injection-in-the-wild

> A curated tracker of publicly reported prompt-injection techniques — delivery method, encoding, propagation behavior, confirmed models and sources — as a dataset with a live dashboard.

*No corpus evidence — this is a reference dataset, not a program.*

## What it's for

Looking up how prompt injection actually happens in the wild instead of theorizing about it. Every entry is a publicly reported technique decomposed the way a defender needs it: how the payload reached the model (tool-call result, external website, MCP connection, document, email, image), what encoding disguised it (Base64, leetspeak, invisible Unicode tags, homoglyphs), and whether it propagates (poisoned MCP server, agent long-term memory, tool-call chains, self-spreading email). Entries record the models it was confirmed against, the reporting source, and MITRE ATLAS tags where they exist. Use it to build threat models for agent deployments and to pick realistic test cases for red-team exercises; the data ships as `tracker.json` for programmatic use alongside a browsable dashboard.

## Typical use cases

### Filter the dataset for a delivery vector you're worried about

```
jq '[.[] | select(.delivery // "" | test("mcp"; "i"))] | length' tracker.json
```

Outcome: the count of reported techniques whose delivery path involves MCP connections — repeat with other delivery/encoding/propagation values to size a specific threat surface before deciding which defenses to prioritize.

### Browse the live dashboard

Open the dashboard linked from the repository (cybershujin.github.io/Prompt-Injection-in-the-Wild).

Outcome: the full tracker rendered for browsing and filtering by category — the faster route when you're exploring for awareness rather than querying for a specific pipeline decision.
