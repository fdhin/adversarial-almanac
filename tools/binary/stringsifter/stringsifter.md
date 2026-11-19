---
name: "stringsifter"
categories:
  - blackarch-binary
version: "39.33c0cd5"
homepage: "https://github.com/fireeye/stringsifter"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "fireeye/stringsifter"
github-stars: 766
github-pushed: "2026-07-24"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stringsifter

> Ranks a binary's extracted strings by relevance for malware analysis using a learning-to-rank model.

## What it's for

StringSifter (Mandiant/FireEye FLARE and data science teams) applies a gradient-boosted learning-to-rank model to reorder the output of a `strings`-style extractor, so that strings relevant to malware analysis — indicators, paths, API names — surface first instead of drowning among thousands of uninteresting lines. It ships two composable commands: `flarestrings`, a drop-in replacement for GNU `strings` with predictable output across platforms, and `rank_strings`, which ranks any line-separated list of strings. It does not decode obfuscated strings itself — pair it with a deobfuscating extractor such as FLOSS for that. The model was trained on weak labels derived from the EMBER dataset; neither labeled data nor training code is published.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Rank a sample's strings by malware relevance

```
flarestrings sample.bin | rank_strings
```

Outcome: prints every extracted string reordered best-first by model score, so the most analysis-relevant strings appear at the top of the list.

### Show the top strings with their scores

```
flarestrings sample.bin | rank_strings --scores --limit 20
```

Outcome: prints only the 20 highest-ranked strings with the rank score included on each line — a compact triage summary of an unknown binary.

### Filter ranked output by score threshold

```
flarestrings -n 8 sample.bin | rank_strings --min-score 0.9
```

Outcome: extracts strings of at least 8 characters (`-n 8`) and keeps only those the model scores at 0.9 or above, leaving a short high-confidence list.

### Rank strings collected by other tools

```
rank_strings strs.txt
```

Outcome: ranks an existing file of strings (one per line, e.g. combined `strings` and `strings -el` output) given as the positional argument, without re-running extraction.

### Batch-rank a folder of string dumps

```
rank_strings --batch /tmp/strings-dumps/
```

Outcome: instead of stdout, writes one `<input_file>.ranked_strings` file per input in the folder, each containing that file's ranked output.
