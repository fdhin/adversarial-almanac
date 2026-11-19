---
name: "bletchley"
categories:
  - blackarch-crypto
version: "0.0.1"
homepage: "https://code.google.com/p/bletchley/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: other
license: GPL
meta-checked: "2026-11-09"
github-repo: "ecbftw/bletchley"
github-stars: 11
github-pushed: "2016-08-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bletchley

> A suite of practical cryptanalysis helpers for web-application ciphertexts: encoders/decoders, a token pattern analyzer, and padding-oracle tooling, written in Python 3.

## What it's for

Bletchley (by Timothy D. Morgan, Virtual Security Research) targets the crypto found in real applications rather than textbook ciphers: session tokens, encrypted blobs fetched from a web app, and the oracles they leak. The installed suite provides `bletchley-decode`/`bletchley-encode` for peeling encoding layers off captured blobs, `bletchley-analyze` for spotting block patterns across token samples (repeated blocks, ECB structure, block size), `bletchley-http2py` for turning a captured raw HTTP request into a replayable Python script, plus `bletchley-clonecertchain` and `bletchley-nextrand` and CBC padding-oracle library code (including a timing web padding oracle). It is analysis-and-exploitation tooling, not a general encryptor, and the project is old (2012-era) — expect Python 3 era quirks.

## Typical use cases

### Decode a captured token through its encoding chain

```
echo 616263 | bletchley-decode -e hex
```

Outcome: applies the decode chain named by `-e` to the input (here hex, yielding `abc`) and writes the raw decoded bytes to stdout — designed to be piped onward in shell pipelines. `-e` accepts a comma-separated chain of the encoding layers that were applied to the token; run with `-e "?"` to list every encoding the tool supports (base64 variants, hex, URL, and more).

### Encode data for crafting a request

```
echo -n abc | bletchley-encode -e base64
```

Outcome: encodes stdin with the chain given by `-e` (`YWJj` here) and appends a newline, so the output can be pasted straight into a cookie, parameter, or header you are constructing. As with decode, `-e "?"` lists supported encodings and multiple comma-separated layers are applied in sequence.

### Analyze token samples for block structure

```
bletchley-analyze -b 16 -p 20 samples.txt
```

Outcome: reads `samples.txt` (one token per line), auto-detects the encoding (or honors `-e`), decodes each sample, and prints them aligned in blocks of `-b` bytes with highlighting (`-c`, or `-c256` on 256-color terminals) so repeated or structured blocks stand out — the visual tell for ECB mode and fixed fields inside encrypted tokens. `-p 20` controls how many lines get rendered.

### Turn a captured HTTP request into a replayable Python script

```
bletchley-http2py request.txt --requests > replay.py
```

Outcome: reads the raw HTTP request in `request.txt` (or stdin) and prints a standalone Python script that reproduces the same request — with `--requests` via the friendlier python-requests library, otherwise stdlib httplib/http.client. Edit the generated script to vary the ciphertext or padding bytes; this is the intended companion for building an automated padding-oracle or chosen-ciphertext attack against the application behind the request.

*No corpus evidence — derived from upstream documentation.*
