---
name: "openattack"
categories:
  - scanner
version: "git (2023-07-20)"
homepage: "https://github.com/thunlp/openattack"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "thunlp/openattack"
github-stars: 781
github-pushed: "2023-07-20"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 28
---

# openattack

> An open-source Python toolkit for textual adversarial attacks against NLP models.

## What it's for

OpenAttack (THUNLP, Tsinghua University's NLP lab) is a Python library for generating textual adversarial examples: it perturbs input texts — character and word substitutions, synonym swaps, syntax-level edits — so that a victim classification model flips its prediction while a human still reads the text as equivalent. It is an NLP robustness-testing toolkit, not a network or security scanner: "attack" here means fooling a machine-learning model. It bundles classic attack models (PWWS, Genetic, SEA, HotFlip, BERT-Attack and more), built-in victim models fine-tuned on standard datasets such as SST-2, and an `AttackEval` evaluator with effectiveness, quality and efficiency metrics. It has no command-line interface — the documented surface is the Python API.

## Typical use cases

### Attack a built-in victim model

Run a classic adversarial attack against a bundled BERT model.

```
python3 - <<'PY'
import OpenAttack as oa
import datasets

def dataset_mapping(x):
    return {"x": x["sentence"], "y": 1 if x["label"] > 0.5 else 0}

victim = oa.DataManager.loadVictim("BERT.SST")
dataset = datasets.load_dataset("sst", split="train[:20]").map(function=dataset_mapping)
attacker = oa.attackers.PWWSAttacker()
attack_eval = oa.AttackEval(attacker, victim)
attack_eval.eval(dataset, visualize=True)
PY
```

Outcome: loads the SST-2-fine-tuned BERT victim (first run downloads model and data via Hugging Face `datasets`), runs the PWWS attacker over the 20 samples and prints each adversarial example with its success status and word-level modifications.

### Score an attack with quality metrics

Compare attackers on more than success rate.

```
python3 - <<'PY'
import OpenAttack as oa
import datasets

def dataset_mapping(x):
    return {"x": x["sentence"], "y": 1 if x["label"] > 0.5 else 0}

victim = oa.DataManager.loadVictim("BERT.SST")
dataset = datasets.load_dataset("sst", split="train[:20]").map(function=dataset_mapping)
attacker = oa.attackers.PWWSAttacker()
attack_eval = oa.AttackEval(attacker, victim,
    metrics=[oa.metric.EditDistance(), oa.metric.ModificationRate()])
attack_eval.eval(dataset)
PY
```

Outcome: besides the attack success rate, AttackEval reports the selected adversarial-example quality metrics (edit distance, modification rate), giving a comparable scorecard across attack models.

No corpus evidence — derived from upstream documentation (thunlp/OpenAttack README; API-only, no console entry point in setup.py).
