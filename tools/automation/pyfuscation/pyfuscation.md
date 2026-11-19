---
name: "pyfuscation"
categories:
  - blackarch-automation
version: "17.6d8d53f"
homepage: "https://github.com/CBHue/PyFuscation"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "CBHue/PyFuscation"
github-stars: 525
github-pushed: "2022-11-26"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pyfuscation

> PowerShell script obfuscator that renames functions, variables and parameters to random tokens to slip past signature-based AV screening.

## What it's for

Rewriting a PowerShell script you already have (your own tooling, a downloaded post-exploitation script) so its identifiers no longer match antivirus blacklists, while keeping the script functionally identical. It parses the script, builds a replacement map for function names, variable names and parameter names, and writes the renamed copy alongside mapping files so you can decode what became what. It does not encrypt, encode or pack anything — it is pure token renaming, it only handles PowerShell, and it does not attempt to hide the script from behavior-based detection.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Obfuscate function names only

The most conservative pass — function renaming is the transform most likely to keep the script working:

```
pyfuscation -f --ps ./Invoke-Grab.ps1
```

Outcome: prints a table of every replaced function name (for example `Replaced Invoke-Grab With: Xk29fa`) plus the path of a timestamped output directory created next to the input script; the obfuscated copy lives there and the script exits telling you `Obfuscated script located at : <path>`. Test the copy still runs before shipping it.

### Rename functions, variables and parameters together

The full pass; upstream recommends doing variables and parameters in the same run because they are interdependent:

```
pyfuscation -fvp --ps ./Invoke-Grab.ps1
```

Outcome: same as above, but the output directory now also contains mapping files for the renamed variables and parameters, and the printed summary counts each set (`Parameters Replaced : N`, `Obfuscated Variables located : <file>`). The resulting script needs its variables and parameters consumed by the renamed names, so keep the mapping files for troubleshooting.
