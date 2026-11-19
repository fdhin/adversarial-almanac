---
name: "stegcracker"
categories:
  - blackarch-stego
version: "2.1.0"
homepage: "https://github.com/Paradoxis/StegCracker/tags"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "Paradoxis/StegCracker"
github-stars: 605
github-pushed: "2020-12-25"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stegcracker

> Brute-force steghide passphrases against a carrier file with a wordlist.

## What it's for

StegCracker, by Paradoxis, wraps the `steghide` binary and runs a dictionary
attack: it retries a steghide extraction for every password in a wordlist
until one succeeds and the embedded data drops out. It is the tool to reach
for when a file is known to be a steghide carrier but the passphrase is
unknown. It requires `steghide` installed and does no steganography itself.
The upstream is archived and retired in favour of its declared successor
[[stegseek]], which implements the same attack orders of magnitude faster —
prefer that for new work.

## Typical use cases

### Crack a steghide-protected file with a wordlist

Try every password in a list against a carrier image.

```
stegcracker image.jpg wordlist.txt
```

Outcome: attempts a steghide extraction per wordlist entry; on success the working passphrase is reported and the embedded file is extracted. Omitting the wordlist argument falls back to the Kali-bundled `rockyou.txt` (`/usr/share/wordlists/rockyou.txt`) automatically.

*No corpus evidence — derived from upstream documentation.*
