---
name: "byepass"
categories:
  - blackarch-automation
version: "213.8cbfd9b"
homepage: "https://github.com/webpwnized/byepass"
packaged-by: [blackarch]
blackarch-builddate: "2024-04-28"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "webpwnized/byepass"
github-stars: 79
github-pushed: "2025-08-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# byepass

> Automates John the Ripper cracking sessions through a recommended escalation of techniques.

## What it's for

ByePass drives John the Ripper through the escalation path a human operator would follow manually, so a cracking session needs one command instead of a script: JTR single-crack from account metadata, a large known-password "hailmary" list, target-specific basewords under heavy mangling rules, 14 escalating dictionary/ruleset technique levels, the top-50 Pathwell mask patterns, statistical mask generation learned from already-cracked passwords, JTR prince mode, recycling basewords out of cracked passwords, and raw brute force over a length range. Input is a plain hash file; the hash format is any value `john --list=formats` accepts (give it explicitly with `-f`, since JTR otherwise guesses). ByePass is an orchestrator, not a cracker — John the Ripper must be installed and on PATH.

## Typical use cases

### Quick first pass with default techniques

Cheap strategies first: common passwords, small dictionaries with small rulesets, and calendar-related guesses.

```
python3 byepass.py -v -f Raw-SHA1 -i hashes.txt
```

Outcome: runs the default technique levels 1-3 against the hash file, printing per-phase progress and every cracked password as it falls.

### Crack with target-specific basewords

Passwords usually relate to the organization; seed them directly.

```
python3 byepass.py -v -f Raw-SHA1 -w example -i hashes.txt
```

Outcome: takes the lowercase basewords (comma-separated) and applies many mangling rules to each — fast, and only worth a handful of words because of the rule volume.

### Single-crack mode from account metadata

Use whatever is embedded in the hash lines themselves.

```
python3 byepass.py -v -f Raw-SHA1 -u -i hashes.txt
```

Outcome: runs JTR single-crack mode, deriving guesses from GECOS/username fields when the input hashes carry them; most effective with `user:hash`-style lines.

### Try the large known-password list

The "hailmary" pass against common real-world passwords.

```
python3 byepass.py -f Raw-MD5 -m -i hashes.txt
```

Outcome: attempts every password in the bundled large known-passwords list against the file, typically the highest-yield single step after basewords.

### Learn masks statistically, then crack more

Let early results shape later guesses.

```
python3 byepass.py -v -f Raw-MD5 -s -p 0.25 -i hashes.txt
```

Outcome: runs fast strategies first, derives "high probability" masks from what cracked, and retries the remaining hashes using only the masks needed to cover the given percentile (0.25 ≈ target 25% of passwords).

### Brute force a short length range as a last resort

Mask-free incremental guessing between a min and max length.

```
python3 byepass.py -v -f Raw-MD5 -b 3,5 -i hashes.txt
```

Outcome: brute-forces candidates of 3 to 5 characters; computationally expensive, so keep the range narrow and run it after the cheaper modes are exhausted.

*No corpus evidence — derived from upstream documentation.*
