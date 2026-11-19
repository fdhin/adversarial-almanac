---
name: "nxcrypt"
categories:
  - blackarch-backdoor
version: "32.6ae06b5"
homepage: "https://github.com/Hadi999/NXcrypt"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Hadi999/NXcrypt"
github-stars: 367
github-pushed: "2017-06-26"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nxcrypt

> Polymorphic crypter for Python backdoors that obfuscates a payload script and can splice it into an innocent-looking one.

## What it's for

Defeating static signature matching on Python payload scripts. Two modules: an encryption/obfuscation module that adds junk code and round-trips the source through `py_compile` bytecode, changing both the visible source and the file's hash; and an injection module that merges a malicious script into a benign one, running the payload in a separate thread so the carrier program still behaves normally. Upstream demonstrates an AV detection ratio dropping from 2/54 to 0/55 after transformation. It transforms Python source files only — not compiled binaries — and is 2017-era tooling, so treat its undetectability claims as historical; modern EDR behavioral rules are a different problem. Run it with superuser permissions as the documentation instructs.

## Typical use cases

### Obfuscate a Python payload script

```
sudo ./NXcrypt.py --file=backdoor.py --output=output_backdoor.py
```

Outcome: writes `output_backdoor.py`, a behaviorally equivalent but obfuscated version of `backdoor.py` with different source text and a different checksum; run it exactly as you would the original. Omitting `--output` makes the tool write the result to a default filename (`backdoor.py` in the working directory).

### Hide a payload inside a benign script

```
sudo ./NXcrypt.py --backdoor-file=payload.py --file=notes.py --output=hacked.py
```

Outcome: produces `hacked.py` containing both the benign `notes.py` program and the payload, with the payload executing via its own thread — the file presents as an ordinary utility while the backdoor runs alongside it.

*No corpus evidence — derived from upstream documentation.*
