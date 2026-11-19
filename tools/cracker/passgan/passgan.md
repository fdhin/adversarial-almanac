---
name: "passgan"
categories:
  - blackarch-cracker
version: "38.5927158"
homepage: "https://github.com/D3vil0p3r/PassGAN"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "D3vil0p3r/PassGAN"
github-stars: 25
github-pushed: "2023-06-01"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# passgan

> Deep-learning password guess generator: train a GAN on a password leak, or sample the bundled RockYou-trained model.

*No corpus evidence — derived from upstream documentation.*

## What it's for

PassGAN implements the paper "PassGAN: A Deep Learning Approach for Password Guessing" (arXiv 1709.00440): a Wasserstein GAN with gradient penalty learns the distribution of a plaintext-password training set and then samples new candidates from it, so generated guesses resemble the leak's population without simply replaying it. This fork (D3vil0p3r), which BlackArch packages, adds a `passgan` command-line interface with `train` and `sample` subcommands plus a pretrained model trained on RockYou. It is a wordlist generator for offline cracking, not a cracker; training and sampling are GPU workloads, and the code targets the TensorFlow 1.x / CUDA 8 era, so expect legacy-environment friction on current stacks.

## Typical use cases

### Generate guesses from the pretrained model

Produce a large candidate wordlist without training anything.

```
passgan sample --input-dir /usr/share/passgan/pretrained --checkpoint /usr/share/passgan/pretrained/checkpoints/checkpoint_5000.ckpt --output gen_passwords.txt --batch-size 1024 --num-samples 1000000
```

Outcome: samples 1,000,000 passwords from the RockYou-trained model in batches of 1024 and writes them to gen_passwords.txt — dedupe before feeding the file to a cracker as a wordlist, since sampling produces repeats.

### Train a model on your own leak

Learn the password habits of a specific population.

```
passgan train --output-dir mymodel --training-data leaked-train.txt
```

Outcome: trains a fresh model on leaked-train.txt (one password per line) using the paper's default hyperparameters, writing checkpoints under mymodel/checkpoints as iterations progress — a 100 MB+ dataset takes hours on a GTX 1080-class GPU. Once checkpoints exist, run `passgan sample` with `--input-dir mymodel` and the matching `--checkpoint` to generate guesses in that leak's style.
