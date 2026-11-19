---
name: "cloakify"
categories:
  - blackarch-misc
version: "117.f45c3b3"
homepage: "https://github.com/trycatchhcf/cloakify"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "trycatchhcf/cloakify"
github-stars: 1690
github-pushed: "2020-11-24"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cloakify

> Text-based steganography that turns any file into an innocuous list of everyday strings for exfiltration.

*No corpus evidence — derived from upstream documentation.*

## What it's for

CloakifyFactory (by Joe Gervais, TryCatchHCF) converts any file type — archives,
executables, Office documents — into a list of harmless-looking strings (Pokemon
names, desserts, ski resorts, IPv4 addresses, geo-coordinates, and more) so the
data can cross DLP/MLS monitoring, data-whitelisting controls and analyst review
without triggering alerts. It Base64-encodes the payload and maps that output
onto a word list (a "cipher"); the same cipher maps it back. It is deliberately
not encryption — cloaked output is vulnerable to frequency analysis, so encrypt
first if secrecy matters and use the noise generators to add entropy. Ships as a
menu-driven factory plus standalone cloakify/decloakify scripts.

## Typical use cases

### Cloak a payload as an innocuous list

Turn a file into a word list that survives content monitoring.

```
cloakify payload.zip ciphers/desserts > exfiltrate.txt
```

Outcome: writes a list of dessert names encoding the Base64 of payload.zip to
exfiltrate.txt; transfer it over any channel that permits plain text lists.

### Decloak an exfiltrated payload

Recover the original file on the receiving side.

```
decloakify exfiltrate.txt ciphers/desserts > payload.zip
```

Outcome: maps the list back through the same cipher and Base64-decodes the
result, writing the original file. Decloaking only works with the exact cipher
file used to cloak.

### Run the guided menu with noise generation

Pick ciphers interactively and pad the output with plausible context.

```
python2 cloakifyFactory.py
```

Outcome: opens the menu-driven walkthrough (cloak a file, preview, add noise,
decloak); the bundled noise generators (prependLatLonCoords.py,
prependTimestamps.py and friends) pad each line with randomized fields to
degrade frequency analysis, and removeNoise.py strips that padding before
decloaking.
