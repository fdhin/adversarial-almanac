---
name: "outguess"
categories:
  - blackarch-crypto
version: "0.4"
homepage: "https://github.com/resurrecting-open-source-projects/outguess"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-01-31"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "resurrecting-open-source-projects/outguess"
github-stars: 347
github-pushed: "2021-09-03"
github-archived: true
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# outguess

> Classic universal steganography tool that hides data in the redundant bits of JPEG/PPM images while preserving the image's statistical properties.

## What it's for

OutGuess (originally by Niels Provos; the packaged build is the
resurrecting-open-source-projects maintenance of it) embeds hidden messages in
the redundant bits of data sources — currently JPEG, PPM and PNM images. Its
distinguishing feature is statistical awareness: for JPEG it preserves
statistics based on DCT frequency counts (on by default with `-F`), so
statistical steganalysis tests based on frequency counts cannot detect that
content was embedded. A key encrypts and determines the iterator seed used to
pick modified bits, and the tool searches for a seed that minimizes the number
of changes. It also supports embedding a second message with error correction
(Golay code) — a plausible-deniability mechanism, since the first message
remains extractable with its own key. It hides and retrieves; it is not a
detector for other tools' stego.

## Typical use cases

### Hide a file inside a JPEG

Embed a message into a cover image protected by a key.

```
outguess -k "my secret pass phrase" -d hidden.txt cover.jpg stego.jpg
```

Outcome: reports the usable bit count, the maximum correctable message size,
and the embedding statistics, then writes `stego.jpg` — statistically
equivalent to the cover but carrying the encrypted `hidden.txt`.

### Retrieve a hidden file

Extract a message previously embedded with the same key.

```
outguess -k "my secret pass phrase" -r stego.jpg message.txt
```

Outcome: decrypts and extracts the hidden payload into `message.txt`; with a
wrong key the extraction fails or yields garbage, so key knowledge is the
access control.

### Embed a second message for plausible deniability

Hide a second payload on top of the first using error correction.

```
outguess -k "secret1" -d hide1.txt -K "secret2" -D hide2.txt cover.jpg stego.jpg
outguess -k "secret2" -e -r stego.jpg message2.txt
```

Outcome: the first command embeds both messages (the second with error
correction on the locked bits of the first); the second retrieves the second
message under its own key while the first remains independently extractable
with `-k "secret1"`.

*No corpus evidence — derived from upstream documentation.*
