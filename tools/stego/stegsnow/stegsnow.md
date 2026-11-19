---
name: "stegsnow"
categories:
  - crypto-stego
version: "20130616-8"
homepage: "https://www.darkside.com.au/snow"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur]
build-system: other
meta-checked: "2026-11-09"
github-repo: "ferideberisha/Stegsnow"
github-stars: 0
github-pushed: "2022-04-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stegsnow

> Hide messages in the trailing whitespace of ASCII text files.

## What it's for

stegsnow packages SNOW, Matthew Kwan's whitespace steganography program: it
conceals messages in ASCII text by appending sequences of up to seven spaces
and tabs to the end of lines — roughly three bits of payload every eight
columns — which are invisible in most text viewers. Optionally the payload is
Huffman-compressed (`-C`) and/or encrypted with the ICE algorithm under a
password (`-p`). The same binary both hides (when a message is given) and
extracts (when it is not). It survives only where trailing whitespace
survives: any editor, mailer or formatter that strips or rewraps trailing
whitespace destroys the payload.

## Typical use cases

### Hide a message in a text file

Conceal a message, compressed and encrypted, in a cover text.

```
stegsnow -C -p Password1 -m "hidden message" letter.txt letter_stego.txt
```

Outcome: writes `letter_stego.txt`, visually identical to `letter.txt` but with the compressed, encrypted message encoded in its trailing whitespace; `-m` takes the message inline and `-f file` reads it from a file instead.

### Extract a hidden message

Recover a payload from a stego text file.

```
stegsnow -C -p Password1 letter_stego.txt
```

Outcome: with no `-m`/`-f` given, stegsnow switches to extraction mode, decodes the trailing whitespace (decrypting and uncompressing as flagged) and prints the recovered message to stdout.

### Measure available hiding space

Check how much payload a text can carry before embedding.

```
stegsnow -S letter.txt
```

Outcome: reports the approximate number of bytes of hidden-message space available in the file, accounting for line length — use it to pick a cover large enough for the intended payload.

*No corpus evidence — derived from upstream documentation.*
