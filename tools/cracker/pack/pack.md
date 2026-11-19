---
name: "pack"
categories:
  - blackarch-cracker
version: "0.0.4"
homepage: "http://thesprawl.org/projects/pack/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "iphelix/pack"
github-stars: 901
github-pushed: "2019-12-10"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pack

> Password Analysis and Cracking Kit: turn analyzed password leaks into optimal hashcat masks, policies and rules.

*No corpus evidence — derived from upstream documentation.*

## What it's for

PACK (by iphelix, thesprawl.org) is a Python toolkit for password-cracking preparation: it cannot crack passwords itself — its job is to analyze large lists of plaintext passwords (a leak, a previous crack, a policy sample) and produce material that makes hashcat-family crackers recover more passwords faster. Four scripts cover the pipeline: statsgen computes length, charset and mask distributions; maskgen turns those statistics into an ordered set of hashcat masks (e.g. a .hcmask file) optimized for a cracking-time budget; policygen generates the mask sets a password policy permits or forbids; rulegen reverse-engineers the mangling rules and source words behind observed passwords using spell-check dictionaries. It expects wordlists of one password per line and dates from the Python 2 era (rulegen needs Aspell and dictionaries installed).

## Typical use cases

### Profile a password leak

See how the passwords in a list are actually constructed.

```
statsgen leaked-passwords.txt
```

Outcome: prints the distribution of password lengths, character-set classes (loweralpha, numeric, mixedalpha, ...), simple masks and the top masks with occurrence counts — the raw material every other PACK stage builds on. `--hiderare` drops rare masks from the analysis and `--simplemask <class>` restricts it to one simple-mask class (e.g. `stringdigit`).

### Export masks for maskgen

Persist the mask statistics computed by statsgen.

```
statsgen leaked-passwords.txt -o leaked.masks
```

Outcome: writes the mask/occurrence table to leaked.masks (the screen summary still prints), ready to feed to maskgen.

### Build an optimal mask attack for a time budget

Order masks so the first minutes of cracking yield the most guesses.

```
maskgen leaked.masks --targettime 600 --optindex -q -o leaked.hcmask
```

Outcome: generates the mask sequence sorted by optimal index (the occurrence-vs-complexity trade-off; `--complexity` and `--occurrence` are the alternative sort modes) for a 600-second cracking budget and writes it to leaked.hcmask, directly consumable by hashcat's mask-file support. `--pps` sets the assumed guesses-per-second rate used in the time math.

### Enumerate the masks a password policy allows

Generate the mask set a CISO-style policy permits — or everything it forbids.

```
policygen --minlength 8 --maxlength 8 --minlower 1 --minupper 1 --mindigit 1 --minspecial 1 -o complexity.hcmask
```

Outcome: writes every mask satisfying 8-character passwords with at least one lowercase, uppercase, digit and special character to complexity.hcmask — attack it directly, or add `--noncompliant` to enumerate the masks the policy forbids (the shapes users reach when they bend the rules).

### Derive mangling rules from cracked passwords

Work out which hashcat rules and source words produced the observed passwords.

```
rulegen --verbose --password 'Password1'
```

Outcome: analyzes the single password, proposes the most likely source words (via the Enchant/Aspell dictionaries) and the hashcat rule transforming each into the password; pointing it at a file instead (`rulegen cracked.txt -q`) builds a corpus-wide word/rule analysis saved to `analysis.*` files in the working directory.
