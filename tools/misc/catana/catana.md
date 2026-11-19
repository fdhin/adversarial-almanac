---
name: "catana"
categories:
  - blackarch-misc
version: "31.9ea1f0d"
homepage: "https://github.com/D3vil0p3r/catana"
packaged-by: [blackarch]
blackarch-builddate: "2022-10-30"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "D3vil0p3r/catana"
github-stars: 18
github-pushed: "2022-10-02"
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

# catana

> Interactively cut a wordlist down to the passwords a target password policy allows.

*No corpus evidence — derived from upstream documentation.*

## What it's for

CATANA ("CUT your Wordlist") is an interactive bash script that filters an existing wordlist against a password policy. It prompts for the character class of the first and last character (uppercase, lowercase, digit, alphanumeric, alphabetic, special, space, or a custom regex), any characters that must be present or removed, and the minimum/maximum length, then greps the list down step by step. Use it when a target's password format is known — from a policy document, password hints or observed hash formats — to shrink rockyou-style lists so every remaining candidate is structurally valid before hashcat or john runs. It only filters; it does not generate, mutate or crack passwords.

## Typical use cases

### Cut a wordlist interactively

```
catana -i wordlist.txt
```

Outcome: walks through the policy prompts (first character class → last character class → characters to remove → required characters/regex → minimum and maximum length) and prints the surviving entries to the terminal.

### Save the filtered list for a cracker

```
catana -i wordlist.txt -o filtered.txt
```

Outcome: the same interactive filtering, but survivors are written to `filtered.txt` (with an overwrite prompt if the file exists) — a ready-to-use candidate list. Run with sudo when the input wordlist is root-owned; `-c` colorizes output and `-h` prints help.
