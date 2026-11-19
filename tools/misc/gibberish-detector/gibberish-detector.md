---
name: "gibberish-detector"
categories:
  - blackarch-misc
version: "v0.1.1.r2.gecac969"
homepage: "https://github.com/domanchi/gibberish-detector"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "domanchi/gibberish-detector"
github-stars: 69
github-pushed: "2022-02-17"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gibberish-detector

> Trains a character model on real text, then flags gibberish strings with it.

## What it's for

A Python 3 package (domanchi, adapted from rrenaud's Gibberish-Detector) that learns the character-transition statistics of a training corpus and scores new strings against the model: strings unlike real text — keyboard mash, random tokens, generated hostnames — score high and are reported as gibberish. Typical uses: filtering machine-generated names or tokens out of wordlists and enumeration output. The model is only as good as its corpus, and the verdict is purely statistical — it says nothing about what a string means.

## Typical use cases

### Train a model

```
gibberish-detector train big.txt > big.model
```

Outcome: reads the training text, estimates the model and writes it to `big.model` (redirected from stdout) for later detection runs.

### Score a single string

```
gibberish-detector detect --model big.model --string "ertrjiloifdfyyoiu"
```

Outcome: prints `True` when the string looks like gibberish relative to the model, `False` when it resembles real text — usable directly in shell pipelines via the exit path of the printed value.

### Interactively probe a model

```
gibberish-detector detect --model big.model --interactive
```

Outcome: enters an interactive mode that reads one line at a time and answers like `True (4.154)` or `False (2.375)` — the number is the model's score for that input — until Ctrl-D ends the session.

### Use it from Python

```python
from gibberish_detector import detector

Detector = detector.create_from_model("big.model")
print(Detector.is_gibberish("ertrjiloifdfyyoiu"))
```

Outcome: loads the model once and classifies strings in-process (printing `True` here) — suitable for filtering large streams inside a larger script.

*No corpus evidence — derived from upstream documentation.*
