---
name: "xssless"
categories:
  - blackarch-webapp
version: "45.8e7ebe1"
homepage: "https://github.com/mandatoryprogrammer/xssless"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "mandatoryprogrammer/xssless"
github-stars: 313
github-pushed: "2016-06-02"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xssless

> Automated XSS payload generator that compiles Burp-recorded request sequences into one self-contained JavaScript payload.

## What it's for

xssless (by Matthew Bryant, mandatoryprogrammer) takes HTTP requests exported from Burp Proxy ("Save items") and compiles them into a single asynchronous JavaScript payload that replays the whole request chain inside a victim's browser — fetching pages, re-parsing responses to extract fresh CSRF tokens, replaying POSTs, and even performing multipart file uploads, all with the victim's credentials. It also supports self-propagation: a POST field can be set to the payload itself, producing JavaScript worms. It is a payload generator for an injection point you already control, not a scanner, and it is a Python 2 era tool (the distro package wraps `xssless.py` with python2).

## Typical use cases

### Generate a payload from a Burp export

```
xssless burp_export.txt
```

Outcome: prints the generated JavaScript payload to stdout, ready to be delivered through the XSS injection point; the payload replays each recorded request relative to the victim's origin.

### Handle CSRF-protected forms

```
xssless -p=csrf_tokens.txt burp_export.txt
```

Outcome: for every token name listed in `csrf_tokens.txt`, the payload fetches the form page first, scrapes the current token from the response, and submits it with the follow-up request.

### Include a file upload in the payload

```
xssless -f=uploads.txt burp_export.txt
```

Outcome: the payload performs a multipart POST for each entry of `uploads.txt` (format `post_field,path/to/file`, e.g. `upload_filename,~/shell.bin`), uploading files through the XSS.

### Build a self-propagating worm

```
xssless -m=vectors.txt burp_export.txt
```

Outcome: for each POST parameter named in `vectors.txt` the payload submits the payload's own source, so the XSS spreads to other stored contexts on the target.

### Save the payload without the banner

```
xssless -s -o=payload.js burp_export.txt
```

Outcome: writes the generated payload to `payload.js` instead of stdout (`-s` suppresses the logo, `-n` would disable output optimization).

*No corpus evidence — derived from upstream documentation (README plus the packaged `xssless.py` help menu).*
