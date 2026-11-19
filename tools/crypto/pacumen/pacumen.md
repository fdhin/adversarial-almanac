---
name: "pacumen"
categories:
  - blackarch-crypto
version: "1.92a0884"
homepage: "https://github.com/bniemczyk/pacumen"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "bniemczyk/pacumen"
github-stars: 18
github-pushed: "2014-08-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pacumen

> Trains classifiers that identify which protocol is tunneled inside encrypted traffic from packet captures alone.

## What it's for

Pacumen ("Packet Acumen") is a research prototype for encrypted-traffic analysis: given packet captures of an encrypted tunnel (for example SSH) that carries a known inner protocol, and captures that do not carry it, it trains a one-class decision-tree classifier over flow features such as packet sizes. The trained classifier can then score a new capture with the probability that the target protocol is inside the tunnel — a side-channel analysis that never decrypts anything. It accompanies the author's paper on the technique and is written for Python 2 with SciPy, NumPy and scapy; the upstream has been untouched since 2014, so expect to provide a Python 2 environment yourself. It does not decrypt traffic or attack cryptography, it only classifies observable metadata.

## Typical use cases

### Train a classifier for a tunneled protocol

Feed target (protocol present) and non-target captures, and write a classifier file.

```
python2 pacumen_train.py -T tunnel-target.pcap -N tunnel-other.pcap target.classifier
```

Outcome: trains a one-class decision tree over the supplied pcaps — `-T` may be repeated for additional target captures and `-N` for additional non-target ones, `-B` overrides the positive-class bias — and writes the pickled classifier to `target.classifier`.

### Score a capture for the target protocol

Run a trained classifier against new pcaps.

```
python2 pacumen_classify_pcap.py -C target.classifier capture.pcap
```

Outcome: prints the probability that the capture contains the classified protocol; add `-V` to also produce the per-flow visualization.

### Visualize a trained classifier

Inspect what a classifier learned.

```
python2 pacumen_visualize.py target.classifier
```

Outcome: loads the pickled classifier and renders the visualization of its underlying Gaussian model.

*No corpus evidence — derived from upstream documentation.*
