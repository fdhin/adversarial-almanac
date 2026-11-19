---
name: "garak"
categories:
  - blackarch-ai
version: "0.17.1.r26.gd6a8be1"
homepage: "https://github.com/NVIDIA/garak"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "NVIDIA/garak"
github-stars: 9916
github-pushed: "2026-11-06"
github-archived: false
github-issues: 548
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1602
gh-activity-checked: "2026-11-09"
---

# garak

> NVIDIA's LLM vulnerability scanner — nmap for language models: runs probe modules against a model or agent and reports which weaknesses hit.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding the failure modes of an LLM before someone else does. Garak ships a library of probes — prompt-injection payloads, encoding tricks, hallucination bait, data-leakage and dan/jailbreak families, and more — and a set of target connectors (OpenAI-compatible endpoints, local models, HF models, agents). A scan fires selected probes at a target, detectors judge the responses, and you get a report of hits per probe. Run it when onboarding a model, after a system-prompt or guardrail change, or in CI against a staging endpoint. It diagnoses weaknesses; fixing them (or accepting them) stays with you.

## Typical use cases

### Scan an OpenAI-compatible model with one probe family

```
export OPENAI_API_KEY="sk-..."
python3 -m garak --target_type openai --target_name gpt-5-nano --spec probes.encoding
```

Outcome: the encoding probe family (payloads hidden in base64, unicode tricks and the like) is fired at the named model; the terminal and the generated report show per-probe pass/fail — a quick smoke test that your model or gateway doesn't decode-and-obey hidden instructions.

### Scan a self-hosted or agent endpoint

Point the same command at any OpenAI-compatible endpoint via its target options (base URL plus API key), choosing broader probe sets than a single family for an onboarding scan.

Outcome: a full vulnerability report across injection, jailbreak, leakage and hallucination probes, giving you a ranked list of which probe classes the deployment actually succumbs to — the input for deciding guardrails, prompt hardening, or model swaps.
