---
name: "fcrackzip"
categories:
  - forensics
  - passwords
version: "1.0-7"
homepage: "http://oldhome.schmorp.de/marc/fcrackzip.html"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-14"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "hyc/fcrackzip"
github-stars: 467
github-pushed: "2023-03-23"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fcrackzip

> Password cracker for classic (ZipCrypto) zip archives, supporting dictionary and brute-force attacks.

## What it's for

fcrackzip recovers lost passwords of encrypted zip archives. It guesses the password either from a wordlist (dictionary mode) or by trying all combinations of a given character set (brute force), and all files in the archive must share the same password — providing several files improves its ability to tell a correct guess from a false positive. With `-u` it verifies candidates by actually decompressing the first file with `unzip`, which weeds out false positives. It targets the traditional ZipCrypto encryption of classic zip files; AES-encrypted archives (WinZip AES) are not cracked by it. Since the ZipCrypto stream depends only on the password, checking candidates is fast, which is what makes the tool effective even on modest hardware.

*Corpus evidence: dictionary-mode attacks with a wordlist (`-D -p <wordlist>`) combined with `-u` and verbose `-v` are the recurring real-world pattern; the brute-force forms below are documented in the upstream man page.*

## Typical use cases

### Dictionary attack with a wordlist

```
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt secret.zip
```

Outcome: tries every line of the wordlist (one password per line, alphabetically sorted) against the archive; `-u` verifies each hit by decompressing with `unzip`, and the recovered password is printed when a match is found.

### Verbose dictionary run for progress feedback

```
fcrackzip -v -u -D -p /usr/share/wordlists/rockyou.txt secret.zip
```

Outcome: same attack, with each `-v` raising verbosity so current candidates stream past while the run progresses — useful to confirm it is actually working through a large list.

### Brute force with a character set and length range

```
fcrackzip -u -b -c aA1 -l 4-8 secret.zip
```

Outcome: tries every combination of lowercase letters, uppercase letters and digits (`a`, `A`, `1`; `!` adds symbols, `:` introduces literal characters) for password lengths 4 through 8, verifying hits with `unzip` and printing the password when found.

### Resume or seed a brute-force run at a starting password

```
fcrackzip -u -b -c a1 -p aaaa0 secret.zip
```

Outcome: brute force over lowercase letters and digits that starts trying at the given initial password instead of the first combination, so an interrupted or split run can continue where it stopped.
