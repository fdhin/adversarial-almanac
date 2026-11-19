---
name: "qrgen"
categories:
  - blackarch-misc
version: "37.82a015b"
homepage: "https://github.com/h0nus/QRGen"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-12-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "h0nus/QRGen"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# qrgen

> Generates QR codes carrying attack payloads, for testing how QR scanners handle hostile input.

## What it's for

A small Python 3 script that renders QR codes encoding deliberately malicious content drawn from built-in payload groups: SQL injection, XSS, command injection, format string, XXE, string fuzzing, SSI injection, and LFI/directory traversal. Scanning the generated codes with a target reader exercises the application's QR data-handling — parsers, decoders, and whatever the app does with decoded content. The author's noted limitation is that codes must be scanned with a camera; there is no automated injection channel. It needs the `qrcode` and `Pillow` modules for Python 3. The original GitHub repository has been taken down; this description is reconstructed from an archived snapshot of the project page, so obtain packaged builds from your distribution rather than the gone upstream.

## Typical use cases

### Generate malformed QR codes from the built-in payload groups

Run the tool interactively and pick an attack class.

```
python3 qrgen.py
```

Outcome: QR code images encoding payloads from the default groups (SQL injection, XSS, command injection, format string, XXE, string fuzzing, SSI injection, LFI/directory traversal) are rendered, ready to be scanned by the reader under test.

### Generate QR codes from a custom payload wordlist

Swap the built-in wordlists for your own list of payloads.

```
python3 qrgen.py -w payloads.txt
```

Outcome: the QR codes are generated from the entries of `payloads.txt` instead of the built-in groups, so the test targets your own payload set.

*No corpus evidence — derived from upstream documentation (archived project page; upstream repository gone).*
