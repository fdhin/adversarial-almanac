---
name: "bopscrk"
categories:
  - blackarch-automation
version: "v2.4.7.r2.g7eede16"
homepage: "https://github.com/R3nt0n/bopscrk"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "R3nt0n/bopscrk"
github-stars: 1120
github-pushed: "2024-09-07"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bopscrk

> Targeted wordlist generator: combines victim-specific words (and optionally song lyrics) with configurable case and leet transforms, separators, and common password suffixes.

## What it's for

bopscrk builds password wordlists tailored to one person or organization: you supply base words associated with the target (name, dates, pets, employer, hobbies), and it generates all combinations up to `-n` words at a time, then enriches them with common separators (`-`, `_`, `.`), numbers, and special characters, and optionally applies case and leet transformations. The lyricpass module (based on initstring's tool) fetches the lyrics of artists you name and adds each line — and the initials of each line — as candidate words. Both the charsets for leet substitution and the combination/separation behavior are customizable in `bopscrk.cfg` (e.g. `leet_charset=a:4 e:3`, `recursive_leet=true`, `extensive_case=true`). It works interactively or as a one-line command; mind `-n` and `--max`, since aggressive settings explode the output size.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate a wordlist interactively

Answer prompts about the target instead of structuring flags.

```
bopscrk -i
```

Outcome: the script walks you through target fields one by one (fields can be left empty; artist names pull in lyrics) and writes the generated wordlist to the default output file `tmp.txt` or the path you choose during the prompts.

### Build a wordlist non-interactively

Script the generation from known personal details.

```
bopscrk -w John,Doe,1990,70 -c -l -n 2 -o targeted.txt
```

Outcome: combines the comma-separated base words two at a time with case (`-c`) and leet (`-l`) transforms and separator/number suffixes, writing the deduplicated wordlist to `targeted.txt` (default range `--min 4` to `--max 32` applies).

### Add song lyrics to the wordlist

Cover music-based passwords for targets whose favorite artists are known.

```
bopscrk -w John,Doe -a "john doe,john smith" -o lyrics-based.txt
```

Outcome: downloads the lyrics of all songs by the named artists, adds each line, its space-substituted forms, and the initials of each phrase (plus the artist names themselves) to the base words, and writes the combined transforms to `lyrics-based.txt`.

### Tune word length and combination depth

Keep the wordlist a usable size.

```
bopscrk -w John,Doe,1990 -n 3 --min 6 --max 16 -o filtered.txt
```

Outcome: only words 6–16 characters long survive, built from up to 3 base words per combination — smaller than the default run, at the cost of skipping short and long candidates.
