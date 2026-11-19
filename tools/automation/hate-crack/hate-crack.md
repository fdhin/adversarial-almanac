---
name: "hate-crack"
categories:
  - blackarch-automation
version: "224.821ff60"
homepage: "https://github.com/trustedsec/hate_crack"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "trustedsec/hate_crack"
github-stars: 1865
github-pushed: "2026-11-07"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1151
gh-activity-checked: "2026-11-09"
---

# hate-crack

> TrustedSec's menu-driven wrapper that runs a proven chain of hashcat cracking methodologies for you.

## What it's for

hate_crack (TrustedSec) automates password-cracking methodology: instead of hand-composing hashcat command lines, you hand it a hash file and hash type and pick an attack from a menu — quick dictionary with rules, password profiling with generated wordlists, PRINCE, mask attacks (top masks, corporate masks, smart masks with a keyspace budget), fingerprint, hybrid combinator attacks, OMEN Markov, and loopback over already-cracked plaintexts. It bundles the helper toolchains (hashcat-utils, princeprocessor, pcfg_cracker), applies sensible preprocessing (computer-account filtering, dedup, LM-first ordering), and records what ran. Tuning lives in `config.json`. It wraps hashcat rather than replacing it, so GPU drivers and hashcat itself must already work.

## Typical use cases

### Work through attacks interactively

```
./hate_crack.py
```

Outcome: the menu-driven interface opens; supply the hash file and hashcat hash type, then pick numbered attacks — each runs the full methodology (wordlist selection, rules, mask ordering) without further hand-holding. On BlackArch the packaged command is `hate-crack`.

### Script a single dictionary pass

```
hate_crack quick hashes.txt 1000 --wordlist rockyou.txt --rules best64.rule
```

Outcome: one non-interactive hashcat run of that wordlist with the named rule(s) — `--rules best64.rule+d3ad0ne.rule` chains two rules in one pass, space-separated values run them as separate passes; preprocessing prompts auto-accept defaults and the process exits 0 only on success.

### Budget a mask attack by time

```
hate_crack topmask hashes.txt 1000 --target-time 4
```

Outcome: the most common hashcat masks are ranked and executed in an order sized to roughly four hours of cracking, so the highest-probability masks complete within the budget you set.

### Re-crack with already-found plaintexts

```
hate_crack loopback hashes.txt 1000 --rules best64.rule
```

Outcome: every plaintext already recovered in the potfile is mutated through the given rules and re-run against the remaining hashes — routinely recovers password-reuse variants inside the same dump.

*No corpus evidence — derived from upstream documentation.*
