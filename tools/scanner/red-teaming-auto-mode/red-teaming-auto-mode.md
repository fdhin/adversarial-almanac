---
name: "red-teaming-auto-mode"
categories:
  - scanner
version: "git (2026-09-25)"
homepage: "https://github.com/safety-research/red-teaming-auto-mode"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "safety-research/red-teaming-auto-mode"
github-stars: 8
github-pushed: "2026-09-25"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 1
---

# red-teaming-auto-mode

> Research code for red-teaming the safety monitors of autonomous coding agents, published with the paper "Red-teaming Claude Code's auto-mode monitor".

## What it's for

This is a research artifact, not an installable scanner. Production coding
agents run real-time monitors (Claude Code's Auto Mode, Codex's Guardian) that
review each proposed action and block unsafe ones; this repository red-teams
those monitors under a persistent adversarial agent and studies design changes
that make them more robust. It ships two evaluation trees: `arena/`, an
"on-policy" harness that drives a real Claude Code agent inside a Docker
sandbox plus an offline transcript-and-injection optimizer, and `simulation/`,
which holds LLM-simulation capability and investigator (fuzzing) evals together
with the monitor implementations they study.

There is no practitioner-facing CLI and no packaged release: working with it
means cloning the repository and running the evaluation harnesses against your
own agent-monitor setup, and the repo carries benchmark canary GUIDs marking it
as evaluation data. Treat it as a methodology and dataset reference for
adversarial-robustness research on agent safety monitors rather than as a tool
you point at a target.

*No corpus evidence — derived from upstream documentation (the repository's
own README); no runnable CLI is documented, so this note is description-only.*
