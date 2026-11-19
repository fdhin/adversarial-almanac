---
name: "omen"
categories:
  - blackarch-cracker
version: "19.10aa99e"
homepage: "https://github.com/RUB-SysSec/OMEN"
packaged-by: [blackarch]
blackarch-builddate: "2019-09-15"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "RUB-SysSec/OMEN"
github-stars: 336
github-pushed: "2019-07-11"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# omen

> Ordered Markov ENumerator — trains an n-gram model on passwords and enumerates new guesses most-likely-first.

## What it's for

OMEN (Ruhr-Universität Bochum systems-security group) is a Markov-model password guesser in two stages: `omen-createng` learns n-gram and length statistics from a training password list, and `omen-enumng` then enumerates new candidate passwords in descending probability order, so early guesses cover far more real passwords than unordered generation. The intended workflow pipes the ordered stream into a hash evaluator such as John the Ripper or hashcat — most valuable against slow hashes (bcrypt, PBKDF2, scrypt, Argon2) where the guess budget is limited. OMEN+ adds John-style "single crack" behaviour by biasing generation with per-user hint material (names, dates, hints). The experimental `omen-evalpw` and `omen-alphabetcreator` helpers score a single password and build a frequency-ordered alphabet from a training list.

## Typical use cases

### Train a model on a password list

Learn the statistics before generating anything.

```
omen-createng --iPwdList training-passwords.txt
```

Outcome: reads one password per line and writes the IP.level, CP.level, EP.level and LN.level probability files plus a `createConfig` to the working directory; these files define the model that every subsequent `omen-enumng` run uses.

### Stream ordered guesses into a cracker

Generate and crack in one pipeline.

```
omen-enumng -p -m 1000000 | john --stdin hashes.txt
```

Outcome: one million password guesses ordered by model probability are printed to stdout and consumed by John as its candidate source for `hashes.txt`; cracked accounts appear in John's session output. The same stream can feed hashcat's stdin the same way.

### Measure hit-rate against a plaintext test set

Tune training data before a real run.

```
omen-enumng -s=passwords-testset.txt -m 10000000
```

Outcome: runs the simulated plaintext attack with live feedback length-scheduling and writes an evaluation report to the `results/` folder showing how much of the test set each guess number covers — use it to compare training corpora.

### Bias guessing with per-user hints (OMEN+)

Targeted attack using known personal details.

```
omen-enumng -H hints.txt -a alphas.txt -p -m 1000000
```

Outcome: generation is weighted by each user's tab-separated hints in `hints.txt` with per-hint weights from the matching alpha file, so candidates combining hint material appear earlier in the ordered stream.

*No corpus evidence — derived from upstream documentation.*
