---
name: "evil-opencode"
categories:
  - blackarch-ai
version: "1.19.0"
homepage: "https://github.com/winmin/evil-opencode"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-12"
build-system: other
license: MIT
meta-checked: "2026-11-09"
github-repo: "winmin/evil-opencode"
github-stars: 314
github-pushed: "2026-10-29"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3039
gh-activity-checked: "2026-11-09"
---

# evil-opencode

> Automatically rebuilt releases of the OpenCode coding agent with LLM safety guardrails stripped from the system prompts and outbound telemetry disabled.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Getting an AI coding agent that does not refuse. Upstream OpenCode ships system-prompt instructions that make the model decline certain requests (malware, credential discovery, offense-only security work); this project's CI clones each upstream release tag, removes those guardrail passages, adds an unconditional-assistance prompt, disables telemetry, and publishes fresh binaries for Linux, macOS and Windows — with the built binary scanned for telemetry signatures before release, failing closed. The result is the standard OpenCode agent with its refusals off: relevant when your legitimate work (offensive security research, malware analysis, red-team tooling) trips generic safety filters. Note it removes the model provider's prompt-level refusals only — the underlying model's own alignment still applies.

## Typical use cases

### Run OpenCode without refusals or telemetry

```
evil-opencode
```

Outcome: the familiar OpenCode coding-agent interface starts, but the agent proceeds with requests its guardrails would normally decline, and makes no telemetry callbacks to upstream — local logs and user-facing network features are preserved. Use it wherever you'd use OpenCode, on any codebase, with any configured model.
