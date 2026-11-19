---
name: "kwprocessor"
categories:
  - cracker
version: "1.00"
homepage: "https://github.com/hashcat/kwprocessor"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur]
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "hashcat/kwprocessor"
github-stars: 611
github-pushed: "2026-10-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# kwprocessor

> Keyboard-walk candidate generator with configurable basechars, keymaps and routes.

## What it's for

kwp (kwprocessor) generates password candidates that follow human keyboard patterns — the `1qazxsw2` / `q2w3e4r` family — for feeding hashcat/John or building wordlists. Instead of hardcoding pattern names, it models a walk as three inputs: a base-character file (where walks start), a keymap file (the physical layout, ~70 layouts ship, including non-Latin), and a route file (segmented direction-change counts that bound pattern complexity and candidate length). Shifted and alt-gr keys, diagonals and key-repeats are opt-in from the command line. It emits candidates on stdout (or appends to a file with `-o`); it never talks to a cracker itself, so pair it with a pipe into hashcat/John.

## Typical use cases

### Generate an exhaustive set of keyboard walks

```
kwp basechars/full.base keymaps/en-us.keymap routes/2-to-12-direction-change-every-step.route
```

Outcome: streams every walk matching the route file's direction-change patterns from every base character to stdout — pipe into `hashcat --stdin` or redirect to build a wordlist. Use `-o walks.txt` instead of redirection to append candidates to a file across multiple runs.

### Generate physically-realistic walks with diagonals

Default directions are the four straight ones only; enable the keys a finger actually reaches.

```
kwp -c basechars/full.base keymaps/en-us.keymap routes/2-to-12-direction-change-every-step.route
```

Outcome: `-c` turns on exactly the staggered-keyboard-adjacent set (south-west and north-east diagonals included, the across-a-key diagonals excluded), producing candidates like `q2w3e4r` sawtooths that straight-only walks miss. For full control, `-1` through `-9` toggle individual numpad-ordered directions (`-0` enables all nine) and `-n`/`-x` set the minimum/maximum step distance.

### Generate walks for a non-English keyboard layout

```
kwp -0 basechars/ru.base keymaps/ru.keymap routes/4-to-4-exhaustive.route
```

Outcome: walks the Cyrillic keymap with all nine directions enabled, emitting candidates like `4уыя` that only exist for users typing on that layout — each shipped keymap has a matching `.base` file so every layout character can start a walk. Shift/alt-gr maps are enabled per-walk with `-s`/`-a` (or `-z` for all three maps together).

### Feed hashcat directly

```
kwp basechars/full.base keymaps/en-us.keymap routes/4-to-4-exhaustive.route | hashcat -m 1000 nt.hashes --stdin
```

Outcome: crack attempts run while kwp streams, so huge walk spaces never hit disk — recovered plaintexts appear in hashcat's normal potfile/output. Swap the hash-mode and hash file to match your target format (e.g. `-m 1000` NT, `-m 1800` sha512crypt).

*No corpus evidence — derived from upstream documentation.*
