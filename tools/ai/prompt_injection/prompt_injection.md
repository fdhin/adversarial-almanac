---
name: "prompt_injection"
categories:
  - ai
version: "git (2026-10-13)"
homepage: "https://github.com/kevinchunye/prompt_injection"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "kevinchunye/prompt_injection"
github-stars: 2
github-pushed: "2026-10-13"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# prompt_injection

> Cross-Surface Prompt Injection Benchmark — measures at which pipeline stage a prompt-injection defense fires (or doesn't) using canary tokens tracked through exposed → persisted → relayed → executed.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Localizing defense failures, not just detecting them. Most benchmarks say "the injection worked" or "it was blocked"; this one embeds a unique canary token (`SECRET-[A-F0-9]{8}`) in each payload and traces it through four stages of an agent's execution pipeline — whether the model was exposed to it, whether it got persisted, whether it was relayed onward, and whether it was ultimately executed. That separation tells you where in your pipeline a defense actually stops the attack versus where it leaks through. Scenarios cover propagation, memory poisoning, tool poisoning and permission escalation; defenses testable include write filters and spotlighting; models are specified by name (an API key for the target models is required).

## Typical use cases

### Verify the experiment wiring with a dry run

```
python -m agent_bench.runner \
  --scenario propagation \
  --attack-variants direct \
  --defenses none \
  --models gpt-4o-mini \
  --n-runs 1 --dry-run --log-dir runs/test
```

Outcome: no API calls are made; the runner validates that the scenario/variant/defense/model combination is wired correctly and reports what a full run would execute — the cheap pre-flight before spending tokens.

### Run a full factorial experiment and analyze it

```
python -m agent_bench.runner \
  --scenario propagation memory_poison tool_poison permission_esc \
  --attack-variants none direct encoded \
  --defenses none write_filter spotlighting \
  --models gpt-4o-mini claude-haiku-4-5-20251001 \
  --n-runs 4 --log-dir runs/experiment

python scripts/analyze_scenario_compare_v1.py --run-dir runs/experiment
```

Outcome: every combination of scenarios × attack variants × defenses × models runs 4 times, with canary leakage logged per pipeline stage; the analysis script then compares defenses across scenarios — output answers questions like "does my write filter stop persistence but not execution" with stage-level evidence.
