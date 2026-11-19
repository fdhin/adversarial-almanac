---
name: "snow"
categories:
  - blackarch-crypto
version: "20130616"
homepage: "https://darkside.com.au/snow/index.html"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-09-12"
build-system: make
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "mattkwan-zz/snow"
github-stars: 68
github-pushed: "2021-02-15"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snow

> Whitespace steganography (the Steganographic Nature Of Whitespace) — conceal messages in the trailing tabs and spaces of ordinary ASCII text, optionally Huffman-compressed and ICE-encrypted.

*No corpus evidence — derived from upstream documentation.*

*GitHub mirror: `mattkwan-zz/snow` — a faithful mirror; every file is byte-identical to the darkside.com.au 20130616 release.*

## What it's for

Matthew Kwan's SNOW hides data in plain text files by appending runs of spaces and tabs to the end of lines — whitespace most viewers never display — storing roughly 3 bits every 8 columns. The same command conceals and extracts: with a message supplied (`-m` or `-f`) it embeds; with none it extracts. A built-in Huffman compression (`-C`, tuned for English text) stretches capacity, and ICE encryption in 1-bit CFB mode (`-p`) means the message stays unreadable even if the whitespace is noticed; a leading tab marks the start of the data so prepended mail/news headers do not corrupt it. It works only on text whose trailing whitespace survives the transport: editors that strip trailing spaces, or mailers that rewrap lines, destroy the message, so keep lines short (`-l 72`) for anything that will be indented or forwarded. It is not for images, audio, or binary files — text carriers only.

## Typical use cases

### Conceal a message with compression and a password

```
snow -C -m 'meet me at the pier' -p 'hello world' plain.txt carrier.txt
```

Outcome: writes `carrier.txt` — visibly the same text as `plain.txt`, with the message hidden in end-of-line whitespace, Huffman-compressed and ICE-encrypted with the given password. Unless `-Q` is passed, snow prints statistics such as the space consumed and available.

### Extract the message back

```
snow -C -p 'hello world' carrier.txt
```

Outcome: reads `carrier.txt` and prints the concealed message to standard output (it will not end in a newline). Mirror the options used for concealment: compression must be re-enabled with `-C` and the same `-p` password supplied.

### Conceal an entire file instead of a string

```
snow -C -p 'hello world' -f secret.txt plain.txt carrier.txt
```

Outcome: the contents of `secret.txt` are embedded in the carrier instead of a command-line string — same compression and encryption, but nothing secret ever appears in the process list or shell history.

### Keep the carrier safe from rewrapping

```
snow -C -l 72 -m 'meet me at the pier' plain.txt carrier.txt
```

Outcome: caps generated lines at 72 columns instead of the default 80, leaving room for quoting/indentation prefixes that mail and news readers add, so the hidden whitespace is not pushed past a wrap point and corrupted in transit.

### Check a carrier's capacity first

```
snow -S -l 72 plain.txt
```

Outcome: reports the approximate number of bytes `plain.txt` can conceal at that line length without embedding anything — use it to pick a carrier large enough for the intended message.
