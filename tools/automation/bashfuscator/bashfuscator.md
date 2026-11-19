---
name: "bashfuscator"
categories:
  - blackarch-automation
version: "338.7487348"
homepage: "https://github.com/Bashfuscator/Bashfuscator"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "Bashfuscator/Bashfuscator"
github-stars: 2015
github-pushed: "2023-09-05"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bashfuscator

> Modular Bash obfuscation framework that rewrites commands and scripts into randomized, layered Bash which still executes the original input.

## What it's for

Bashfuscator turns a Bash one-liner or script into semantically equivalent but human- and signature-hostile Bash by chaining "mutators" from several layers — token-level mangling, string encoding, compression (e.g. bzip2 with a self-extracting decoder), and command reordering (e.g. reversed commands) — with randomization so every generation is unique. Red teams use it to probe static detection on Linux endpoints; blue teams use it to mass-generate obfuscated samples for building and testing detections. Payloads target GNU/Linux with Bash 4.0+ — most UNIX/BSD systems break on the GNU-specific constructs. It works both as a CLI and as a Python 3.6+ library, and the obfuscation strength/payload size trade-off is tunable.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Obfuscate a one-liner with default mutators

Quick check of what the framework produces out of the box.

```
bashfuscator -c "cat /etc/passwd"
```

Outcome: prints the mutator chain used (e.g. `Token/ForCode -> Command/Reverse`), the obfuscated payload, and its size; pasting the payload into a Bash 4+ shell executes it and prints `/etc/passwd` unchanged.

### Save the payload to a file or clipboard

Get the result straight where you deploy it from.

```
bashfuscator -c "nmap -sV 198.51.100.10" -o payload.sh
```

Outcome: writes the obfuscated one-liner to `payload.sh` instead of stdout; swap `-o` for `--clip` to copy it to the clipboard (requires xclip).

### Pick an exact mutator chain and strength

Control size-versus-obfuscation instead of accepting the defaults.

```
bashfuscator -c "cat /etc/passwd" --choose-mutators token/special_char_only compress/bzip2 string/file_glob -s 1
```

Outcome: runs exactly the three named mutators in that order with obfuscation level 1 (`-s` raises strength and payload size), producing a reproducible obfuscation strategy you can reuse across samples.

### Obfuscate a whole script

Protect a multi-line script rather than a single command.

```
bashfuscator -f script.sh -o script.obf.sh
```

Outcome: reads `script.sh` via `-f`, obfuscates it with the configured mutators, and writes the self-executing obfuscated script to `script.obf.sh`.
