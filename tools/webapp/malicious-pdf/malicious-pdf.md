---
name: "malicious-pdf"
categories:
  - blackarch-webapp
version: "v1.0.1.r5.g3409ac7"
homepage: "https://github.com/jonaslejon/malicious-pdf"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: BSD-2-Clause
meta-checked: "2026-11-09"
github-repo: "jonaslejon/malicious-pdf"
github-stars: 4517
github-pushed: "2026-10-17"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 31
gh-activity-checked: "2026-11-09"
---

# malicious-pdf

> Generates malicious PDF test files that phone home to your callback URL.

## What it's for

Jonas Lejon's generator for PDF attack test files (67 variants). Each file uses a different callback mechanism — `/GoToE` external file access, XFA form submission, `/OpenAction` JavaScript, XSLT stylesheets, URI actions, and many more — all pointed at a callback URL you supply (Burp Collaborator or interactsh). Feed the results to PDF viewers, PDF-to-image converters, upload endpoints, and server-side processing libraries (PDFBox, iText, PDF.js, ImageMagick) to test for SSRF, XXE, NTLM credential leaks, and blind callbacks. Payload obfuscation levels 0-4 stage the JavaScript and compress streams so naive static scanners that grep for `/JS` see nothing.

## Typical use cases

### Generate the full test set

```
malicious-pdf https://callback.example.com
```

Outcome: writes `test1.pdf`, `test2.pdf`, ... to `output/` (67 files), each embedding the callback URL through a different feature; any DNS or HTTP hit on the callback after processing a file pinpoints the exact vulnerable mechanism by test number.

### Generate with obfuscation into a chosen directory

```
malicious-pdf https://callback.example.com --output-dir pdfs --obfuscate 2
```

Outcome: writes the set to `pdfs/` with level-2 obfuscation (PDF name hex encoding, octal/hex strings, JS bracket notation, `javascript:` URI casing) — use this when testing upload filters or antivirus that reject plainly malicious structure. Level 3 adds FlateDecode stream compression and level 4 wraps the JavaScript in a base64 decoder stub so the original API calls never appear literally.

No corpus evidence — derived from upstream documentation.
