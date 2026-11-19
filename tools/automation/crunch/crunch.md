---
name: "crunch"
categories:
  - blackarch-automation
version: "3.6"
homepage: "http://sourceforge.net/projects/crunch-wordlist/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2021-05-01"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "crunchsec/crunch"
github-match: "search"
github-stars: 254
github-pushed: "2018-12-25"
github-archived: false
github-issues: 13
gh-checked: "2026-11-09"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crunch

> Wordlist generator that enumerates every combination, permutation, or masked pattern over a character set.

## What it's for

crunch builds password candidate lists from a character set and a length range, writing them to a file, to stdout, or piping them straight into another program such as a password cracker. Reach for it when you know the *shape* of a password — its length, its allowed characters, or a fixed prefix/suffix — rather than a dictionary word: it exhaustively covers that space. It supports named character sets from a `charset.lst` file, per-position pattern masks, permutation mode for known words, duplicate-character limits, and splitting large outputs into chunks by size or line count. It does not mutate existing wordlists (that is a rules/mangling job for hashcat or John); it only generates from criteria.

## Typical use cases

### Generate a masked pattern to a file

Build candidates matching a known prefix plus variable trailing characters.

```
crunch 12 12 -t 'company202%^' -o wordlist.txt
```

Outcome: writes every 12-character string to `wordlist.txt` where the first ten characters are the literal `company202`, the eleventh is any digit (`%`), and the twelfth is any symbol (`^`); in a `-t` mask, `@` inserts lowercase letters and `,` inserts uppercase letters in those positions instead.

### Use a named character set from charset.lst

Generate over a standard mixed set without typing the charset inline.

```
crunch 1 8 -f /usr/share/crunch/charset.lst mixalpha-numeric-all-space -o wordlist.txt
```

Outcome: `wordlist.txt` containing every 1–8 character string over the `mixalpha-numeric-all-space` set from charset.lst, starting at `a` and ending at eight spaces.

### Generate a numeric range

Produce all candidates of a short fixed length over a custom charset.

```
crunch 4 4 0123456789 -o pins.txt
```

Outcome: `pins.txt` with all 10,000 four-digit PINs (0000–9999); with no `-o` the same candidates stream to stdout, ready to pipe into a cracker.

### Permute a set of known words

Generate orderings of words you already know appear in the password.

```
crunch 1 1 -p winter spring 2026
```

Outcome: prints the 3! = 6 orderings of the three tokens (`-p` ignores min/max but the two length numbers must still be present, and `-p` must be the last option).

### Split a large generation into sized chunks

Keep file sizes manageable for very large spaces.

```
crunch 6 6 -f /usr/share/crunch/charset.lst loweralpha -o START -b 10mb
```

Outcome: multiple files named `<firstline>-<lastline>.txt` (e.g. `aaaaaa-euzfmb.txt`), each under 10 MB, instead of one huge file; `-b` only works together with `-o START` and accepts kb/mb/gb/kib/mib/gib.

### Suppress runs of repeated characters

Skip candidates with long duplicate-character runs.

```
crunch 8 8 -f /usr/share/crunch/charset.lst loweralpha -d 2@ -o wordlist.txt
```

Outcome: an 8-character lowercase list with no candidate containing more than two consecutive identical letters (`aaa...` is never emitted), which sharply cuts the space for policies that forbid repeats.

GitHub: third-party mirror of the SourceForge crunch-wordlist project.
