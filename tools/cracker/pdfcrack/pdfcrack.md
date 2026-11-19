---
name: "pdfcrack"
categories:
  - passwords
version: "0.21-1"
homepage: "https://pdfcrack.sourceforge.net/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-04-01"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "alitrack/PDFCrack"
github-stars: 105
github-pushed: "2026-06-25"
github-archived: true
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# pdfcrack

> Recovers the user or owner password of standard-security-handler encrypted PDF files by wordlist or charset brute force.

*No corpus evidence — derived from upstream documentation.*

## What it's for

pdfcrack (Henning Norén) is a small, dependency-free C tool for recovering the passwords of PDF files encrypted with the standard security handler (revisions 2, 3 and 4). It cracks either the user password (required to open the document — the default target) or the owner password (the permissions password), using a wordlist or brute force over a chosen charset with minimum/maximum length bounds, simple permutations (first character uppercased), and an optimized owner-password search when the user password is already known. Interrupted sessions auto-save and can be resumed, and a built-in benchmark (`-b`) measures candidate speed. It does not decrypt or strip protection itself — once a password is recovered, produce the unencrypted copy with a tool such as qpdf or pdftk.

## Typical use cases

### Run a wordlist attack

The default starting point for a user password.

```
pdfcrack -f document.pdf -w passwords.txt
```

Outcome: tries every wordlist entry as the user password, printing progress and average guesses per second; a hit prints `found user-password: '<password>'`. Sorting the wordlist by length improves performance, and most PDF passwords are ISO Latin-1 encoded.

### Brute force with a charset and length bounds

When no suitable list exists.

```
pdfcrack -f document.pdf -c abcdefghijklmnopqrstuvwxyz0123456789 -n 8 -m 12
```

Outcome: enumerates every 8–12 character combination of the given charset (default bounds are 0–32), reporting speed statistics as it goes — realistic only for short or narrow-charset passwords; run a `-b` benchmark first to size the keyspace you can cover.

### Recover the owner password when the document opens

The user password is known but the permissions password is not.

```
pdfcrack -f document.pdf -p 'Password1' -o
```

Outcome: `-p` supplies the known user password so pdfcrack runs its optimized owner-password search directly against the key material, and `-o` selects the owner password as the target (`-p` implies it); a hit prints `found owner-password: '<password>'`, which then unlocks permission changes in any PDF tool.

### Resume an interrupted session

Continue a long brute force after a stop.

```
pdfcrack -f document.pdf -l savedstate.sav
```

Outcome: Ctrl-C (SIGINT) during a run makes pdfcrack save its position to savedstate.sav in the working directory; this command reloads that state and continues the search from the same position instead of restarting from scratch.
