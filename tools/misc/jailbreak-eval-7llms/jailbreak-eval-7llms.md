---
name: "jailbreak-eval-7llms"
categories:
  - misc
version: "git (2026-11-05)"
homepage: "https://github.com/mdniloykhan/jailbreak-eval-7llms"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "mdniloykhan/jailbreak-eval-7llms"
github-stars: 0
github-pushed: "2026-11-08"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 54
gh-activity-checked: "2026-11-09"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 1
---

# jailbreak-eval-7llms

> Research harness that fires 250 adversarial jailbreak prompts (5 attack families) at 7 local LLMs via Ollama and judges the responses.

## What it's for

This is the code and data release for a systematic jailbreak study ("Systematic Evaluation of Adversarial Jailbreaking Attacks and Defenses Across Seven Large Language Models"): 250 attack prompts across five families (direct, override, obfuscation, roleplay, hypothetical framing), run against seven open models (Llama-3/3.1/3.2, Mistral-7B, Qwen2.5-7B, Gemma2-9B, Phi-4-14B) served locally through Ollama. It ships both an LLM rubric judge (Phi-4) and a rule-based scorer, plus defense experiments (keyword blacklist, self-reminder, paraphrase, perplexity filter). The scripts expect a local Ollama with the models already pulled; results land as per-model CSVs, agreement reports, and figures. The README is explicit that the results are provisional — the judge is unvalidated and the rule-based scorer has known defects — so treat it as a reproducible evaluation scaffold, not settled findings.

## Typical use cases

### Run the main attack experiment

```
python scripts/phase1_expanded.py
```

Outcome: sends all 250 prompts to each of the 7 models via Ollama (1,750 generations, several hours, resumable) and writes one CSV per model under `results/expanded/` with each (truncated) response and rule-based label.

### Score responses with the content judge

```
python scripts/judge_validation_v2.py
```

Outcome: applies the Phi-4 rubric judge at temperature 0 to the stored responses and writes the judge agreement report and corrected attack-success files under `results/judge/`.

### Test defenses

```
python scripts/phase3_defenses.py
```

Outcome: re-runs the first obfuscation and roleplay prompts with each defense enabled (keyword blacklist, self-reminder, paraphrase, perplexity filter) and writes the defense outputs and summary under `results/defense/`.

### Regenerate the figures

```
python scripts/make_all_figures.py
```

Outcome: rebuilds the five PDF figures used in the manuscript from the CSV results.

*No corpus evidence — derived from upstream documentation.*
