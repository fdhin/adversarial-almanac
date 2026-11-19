---
name: "cleverhans"
categories:
  - blackarch-ai
version: "v4.0.0.r7.g574efc1"
homepage: "https://github.com/cleverhans-lab/cleverhans"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "cleverhans-lab/cleverhans"
github-stars: 6459
github-pushed: "2024-04-10"
github-archived: false
github-issues: 46
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cleverhans

> The classic reference library for adversarial examples — attack implementations (FGSM, PGD and more) benchmarked against each other, born from the original research line that named the field.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Crafting and studying adversarial perturbations against machine learning models. Cleverhans is the library the original adversarial-example papers were demonstrated with; its value today is its carefully maintained, framework-multiplexed attack implementations — the same attack expressed consistently for JAX, TensorFlow 2 and PyTorch — and its tutorials, which are kept runnable. Use it to reproduce canonical attacks (FGSM, PGD) on standard datasets, or as the reference semantics when implementing attacks elsewhere. Development has slowed (last push 2024); for active, broader coverage across evasion/poisoning/extraction pair it with [[adversarial-robustness-toolbox]].

## Typical use cases

### Train a model and attack it with FGSM and PGD

```
python tutorials/tf2/mnist_tutorial.py
```

Outcome: the tutorial trains an MNIST classifier, then crafts adversarial examples with the fast gradient sign method and projected gradient descent, reporting accuracy on clean vs. adversarial inputs. Equivalent tutorials ship per framework — `tutorials/torch/cifar10_tutorial.py` (PyTorch), `tutorials/jax/mnist_tutorial.py` (JAX), `tutorials/tf2/cifar10_tutorial.py` — so you can compare how the identical attack behaves across stacks on the same dataset.
