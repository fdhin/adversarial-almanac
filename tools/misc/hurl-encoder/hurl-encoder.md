---
name: "hurl-encoder"
categories:
  - blackarch-misc
version: "20.afca9c5"
homepage: "https://github.com/fnord0/hURL"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "fnord0/hURL"
github-stars: 78
github-pushed: "2017-09-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hurl-encoder

> Multi-format encoder/decoder for URLs, hex, base64, hashes and byte-order conversions.

*No corpus evidence — derived from upstream documentation.*

## What it's for

hURL v2.1 (by fnord0; BlackArch installs the `hURL` script as `hurl-encoder`) is a Perl command-line converter that chains encoding and decoding operations in a single invocation: URL (single and double), base64, HTML entities, hex to/from ASCII, integer/hex/float/octal/binary conversions including network byte order, MD5 and the SHA family, ROT13, and file-to-file hex/binary conversion. Output styles made for payload work — `\x`-escaped strings, hex pairs, C arrays — make it a staple for preparing shellcode and encoded web payloads. Flags combine (later flags apply to the earlier operation's output), `-s` suppresses decoration so results are pipe-friendly, and `-f` switches input to files.

## Typical use cases

### URL-encode a string

Prepare a value for a URL context.

```
hurl-encoder -U "hello world"
```

Outcome: prints the URL-encoded form (`hello%20world`); the lowercase mirror `-u "hello%20world"` decodes back. For twice-mangled input use double-URL decode: `-d "hello%2520world"`.

### Base64-decode with clean output

Decode a value for use in a pipeline.

```
hurl-encoder -b -s "aGVsbG8gd29ybGQ="
```

Outcome: prints just the decoded string (`hello world`) with banners suppressed by `-s` — directly usable as stdin for another command. `-B` is the encode direction.

### Turn a binary file into escaped shellcode

Render a payload file as an embeddable `\x` string.

```
hurl-encoder --nocolor --esc -X -f shellcode.bin
```

Outcome: prints the file's bytes as a `\x00\x01...`-style escaped string, ready to paste into exploit code. Swap `--esc` for `--pair` (concatenated hex pairs) or `--ansiC` (`0x00, 0x01, ...`) for other languages; `--nocolor` keeps the output copy-pasteable.

### Convert a hex dump file back to binary

Reverse a hex encoding into a raw file.

```
hurl-encoder -w -f encoded.txt decoded.bin
```

Outcome: reads hexadecimal from `encoded.txt` and writes the raw bytes to `decoded.bin`. The mirror operation `-r -f shellcode.bin` prints a binary file as hex instead.

### Convert a port number to network-byte-order hex

Prepare the connect-back port for a raw payload.

```
hurl-encoder -e 4444
```

Outcome: prints `5c11`, the byte-swapped hex form of 4444 that network-order port fields expect — round-trip with `-E 5c11` to verify before use.

### Hash a string

Grab a quick digest without leaving the converter.

```
hurl-encoder -4 "hello world"
```

Outcome: prints the SHA-256 digest of the string; `-m` gives MD5 and `-2`/`-3`/`-5`/`-6` the other SHA variants.
