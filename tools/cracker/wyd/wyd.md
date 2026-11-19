---
name: "wyd"
categories:
  - blackarch-cracker
version: "0.2"
homepage: "http://www.remote-exploit.org/?page_id=418"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# wyd

> Password profiler: mines candidate passwords out of a target's own files — documents, PDFs, playlists, web pages — to build a personalized wordlist.

*No corpus evidence — derived from the packaged upstream source.*

## What it's for

wyd (Perl, `wyd.pl`, by Max Moser and Martin J. Muench) builds targeted
password wordlists by profiling the data associated with a person or
organization: a mirrored company website, office documents, PDFs,
presentations, MP3 playlists, JPEG metadata. Instead of running `strings`
over everything, format-aware modules (plain text, HTML/PHP, `.doc`,
`.ppt`, MP3, PDF, JPEG, OpenDocument) extract only meaningful words,
strip junk characters from word boundaries, and include filenames
themselves as candidates. The result is a much lower-false-positive list
than raw string dumps, meant to be fed to a password cracker — often with
mangling rules on top — when generic wordlists have already failed. The
same technique serves forensics: recovering a password to a protected
file from material on the same owner's system.

## Typical use cases

### Profile a mirrored website into a wordlist

Extract every promising word from a crawled copy of a target site.

```
wyd -o possible-passwords.txt ~/target-website
```

Outcome: walks the tree, parses HTML/PHP via the html module and text via
the plain module, writes one candidate per line to
`possible-passwords.txt` (words go to STDOUT when `-o` is omitted).
Expand the result with mangling rules, e.g.
`john --rules --wordlist=possible-passwords.txt --stdout`, before the
actual dictionary attack.

### Split candidates by source file type

Keep provenance by writing a separate list per file type.

```
wyd -t -o profile.txt /mnt/evidence
```

Outcome: in addition to the combined output, creates per-type files such
as `profile.txt.html`, `profile.txt.doc` and `profile.txt.mp3` so hits can
be traced back to the document class (and noisy types dropped) — `-t`
requires `-o`.

### Mine unsupported binary formats via strings

Fall back to `strings` extraction for files no module understands.

```
wyd -s 6 -o possible-passwords.txt ~/docs
```

Outcome: parses unknown file types with the UNIX `strings` command,
keeping only printable sequences of at least 6 characters — broader
coverage at the cost of more false positives; the minimum length raises
precision.

### Keep boundary junk and drop filename candidates

Tune the cleaning behaviour when passwords may contain punctuation.

```
wyd -b -e -f -o possible-passwords.txt ~/target-website
```

Outcome: `-b` and `-e` disable the default stripping of non-alphanumeric
characters at the start and end of each word (preserving candidates like
`#secret!`), and `-f` excludes filenames (without extensions) from the
list — useful when file naming is automated and leaks no human words.
