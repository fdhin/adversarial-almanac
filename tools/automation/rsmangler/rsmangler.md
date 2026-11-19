---
name: "rsmangler"
categories:
  - blackarch-automation
version: "1.4"
homepage: "http://www.randomstorm.com/rsmangler-security-tool.php"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2015-11-10"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "digininja/RSMangler"
github-stars: 236
github-pushed: "2019-07-24"
github-archived: false
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rsmangler

> Robin Wood's wordlist mangler: feed it a handful of seed words and it explodes them into every permutation, case, leet, year, number and prefix/suffix variant John-the-Ripper-style — plus a few extras.

## What it's for

Turning a tiny, high-signal seed list (a company name, a project, a pet's name) into a full candidate wordlist for password attacks. Its signature feature over plain mangling rules is permutations mode: it combines the input words into every ordered concatenation (so `proactive security management` also yields `proactivesecuritymanagement`, `securityproactive`, ...) and even derives an acronym (`psm`) that joins the mangling. Every word is then passed through the whole mangle stack — numbers 1–123 and 01–09 at both ends, years 1990–now at both ends, capital/upper/lower/swap-case, leet and full-leet, reversal, `-ed`/`-ing` suffixes, doubling, and the prefixes/suffixes `admin`, `sys`, `pw`, `pwd`. Output is deduplicated by default. Because permutations mode multiplies fast (3 words ≈ 5,345 results, 5 words ≈ 108,557, and a few hundred words will eat gigabytes of RAM), it is built for very small seed lists; lists over 5 words trigger an abort warning unless forced. It is a generator only — it does not hash, crack or spray by itself. Written in Ruby (`rsmangler.rb`); the packaged `rsmangler` command wraps it.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Explode a small seed list with all default mangles

```
printf 'proactive\nsecurity\nmanagement\n' > seeds.txt
rsmangler --file seeds.txt > mangled.txt
```

Outcome: prints every mangled variant to STDOUT — redirect it to a file as shown. With defaults, all mangles run, including permutations: expect the seed words, their pair/triple concatenations in every order, the `psm` acronym, and each of those with number/year/case/leet/prefix variants attached, deduplicated. Ten lines of output per second of eyeballing tells you quickly whether the pool fits your attack budget.

### Keep the volume sane by disabling permutations

The mangle flags turn mangles OFF, not on — pass them to trim the explosion:

```
rsmangler --file seeds.txt --perms > mangled.txt
```

Outcome: permutations are skipped, so only the individual seed words get the number/year/case/leet/suffix treatment — hundreds instead of tens of thousands of candidates. Combine several disable-flags (e.g. also `--years --common`) to shrink further; every mangle has a matching off-switch in the help.

### Mangle a stream from another generator

The input file can be `-` for STDIN, so rsmangler can sit at the end of a pipeline:

```
cat seeds.txt | rsmangler --file - --force > mangled.txt
```

Outcome: reads the seed words from the pipe, `--force` suppresses the interactive warning about long input lists, and the mangled output goes to STDOUT for the next stage (hashcat/John input, or another filter). `--output <file>` is the built-in alternative to shell redirection, and `--allow-duplicates` trades dedup for speed and lower memory.
