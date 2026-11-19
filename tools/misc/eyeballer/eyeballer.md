---
name: "eyeballer"
categories:
  - blackarch-misc
version: "143.5299227"
homepage: "https://github.com/BishopFox/eyeballer"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "BishopFox/eyeballer"
github-stars: 1302
github-pushed: "2026-03-08"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eyeballer

> Convolutional neural network that triages piles of website screenshots into pentest-useful labels.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Eyeballer (Bishop Fox) classifies website screenshots — the hundreds produced on a large-scope external test by tools like EyeWitness or GoWitness — into labels that drive triage: old-looking sites (frequently vulnerable legacy web apps), login pages (credential-attack surface), full webapps (broad attack surface), custom 404 pages, and parked domains (not real attack surface). It exists because manually eyeballing thousands of screenshots doesn't scale. You supply screenshots (ideally ~1.6:1 aspect ratio, e.g. 1440x900) and a pretrained weights file from the project's releases; it outputs human-readable and CSV results. It can also retrain on your own labeled screenshots and evaluate model precision/recall. It is a classifier, not a screenshot taker — feed it images from another tool — and it needs the Python/TensorFlow stack.

## Typical use cases

### Classify a directory of screenshots

Triage a whole engagement's screenshot dump at once.

```
eyeballer --weights bishop-fox-pretrained-v3.h5 predict screenshots/
```

Outcome: scores every image in the directory against the label set and writes `results.html` (browsable, image per label) plus `results.csv` (machine-readable) — work the login pages and old-looking sites first, drop the parked domains from scope.

### Predict a single screenshot

Check one suspicious target quickly.

```
eyeballer --weights bishop-fox-pretrained-v2.h5 predict target.png
```

Outcome: prints the label predictions for that one image and adds it to the same results files — a fast spot-check without batch overhead.

### Retrain on your own labeled screenshots

Adapt the model to your environment's applications.

```
eyeballer train
```

Outcome: trains a new model from the `images/` folder and `labels.csv` in the working tree (the training set from the project's Kaggle dataset) and writes `weights.h5` — GPU strongly recommended for reasonable training time.

### Evaluate a trained model

Measure precision and recall before trusting the labels.

```
eyeballer --weights weights.h5 evaluate
```

Outcome: runs the model against held-out evaluation images and reports overall binary accuracy plus per-label precision/recall (including a "none of the above" pseudo-label) — decide per label whether it is reliable enough to triage on.
