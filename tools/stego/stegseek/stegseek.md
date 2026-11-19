---
name: "stegseek"
categories:
  - blackarch-stego
version: "v0.6.r10.g5896978"
homepage: "https://github.com/RickdeJager/stegseek"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-07-02"
build-system: cmake
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "RickdeJager/stegseek"
github-stars: 1352
github-pushed: "2023-10-10"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stegseek

> Lightning-fast steghide cracker and unencrypted-payload extractor.

## What it's for

StegSeek, by Rick de Jager, is a fork of steghide optimized for brute-force:
it cracks steghide passphrases with a wordlist thousands of times faster than
wrapper scripts like [[stegcracker]], running the whole of rockyou.txt in a
couple of seconds. It also exposes a passwordless mode built on steghide's
weak 32-bit RNG seed space (CVE-2021-27211), which can both detect whether a
file carries steghide data at all and, when the embedding was written without
encryption, recover the embedded file outright. Reach for it whenever a file
might be a steghide carrier — as detector first, wordlist cracker second.

## Typical use cases

### Crack a steghide file with a wordlist

Run the dictionary attack against a carrier file.

```
stegseek cover.jpg wordlist.txt
```

Outcome: tries every wordlist password with all cores; on success reports the passphrase and extracts the embedded file (an optional third argument names the output file). Without `-s`/`--skipdefault`, obvious guesses (empty password, the filename itself, ...) are prepended to the candidate list automatically.

### Detect and extract without any password

Exploit the seeding weakness to test for — and possibly recover — embedded data.

```
stegseek --seed cover.jpg
```

Outcome: brute-forces the 2^32 embedding patterns in minutes and reports whether the file contains steghide content, how much, and how it was encrypted; if the data was embedded without encryption it recovers the embedded file directly, with no passphrase ever guessed.

*No corpus evidence — derived from upstream documentation.*
