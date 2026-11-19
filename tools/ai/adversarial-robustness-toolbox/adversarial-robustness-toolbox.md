---
name: "adversarial-robustness-toolbox"
categories:
  - blackarch-ai
version: "1.20.1.r183.g4da50bd"
homepage: "https://github.com/Trusted-AI/adversarial-robustness-toolbox"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "Trusted-AI/adversarial-robustness-toolbox"
github-stars: 6303
github-pushed: "2026-11-05"
github-archived: false
github-issues: 27
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 129
gh-activity-checked: "2026-11-09"
---

# adversarial-robustness-toolbox

> IBM's Python library for machine learning security — attack and defense building blocks for adversarial examples, poisoning, extraction and evasion, usable from both red and blue team code.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Stress-testing ML models the way you'd stress-test software. ART gives you a common interface — wrap your model in an ART classifier, then apply attacks from a catalog that spans evasion (FGSM, PGD, DeepFool, C&W, many more), poisoning, model extraction and membership inference, plus defenses and robustness metrics. It wraps models from TensorFlow, Keras, PyTorch, scikit-learn and XGBoost, so the same attack code runs against whichever framework the target model lives in. Use it to measure how far a classifier degrades under adversarial perturbation, or to adversarially train a model before an attacker does it for you.

## Typical use cases

### Craft adversarial examples against your own model

```
python examples/get_started_pytorch.py
```

Outcome: the upstream get-started script trains a small MNIST model through an ART classifier and generates adversarial examples with the Fast Gradient Sign Method, printing clean vs. adversarial accuracy so you can see the gap. Per-framework variants exist (`get_started_tensorflow_v2.py`, `get_started_keras.py`, `get_started_scikit_learn.py`, `get_started_xgboost.py`) — pick the one matching your stack and read it as a working template; parameters are chosen for low compute, not accuracy.

### Benchmark a classifier's robustness before shipping

```
from art.estimators.classification import PyTorchClassifier
from art.attacks.evasion import ProjectedGradientDescent

classifier = PyTorchClassifier(model=model, clip_values=(0, 1),
                               loss=criterion, nb_classes=10)
attack = ProjectedGradientDescent(classifier, eps=0.1)
x_test_adv = attack.generate(x=x_test)
```

Outcome: a perturbed copy of your test set, tuned by `eps`, on which you re-run the model's own accuracy metric. The drop from clean to adversarial accuracy is the robustness number to track across releases.
