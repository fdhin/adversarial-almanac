---
name: "filegps"
categories:
  - blackarch-webapp
version: "90.03cbc75"
homepage: "https://github.com/0blio/fileGPS"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0blio/fileGPS"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# filegps

> Guesses the stored filename of an uploaded file by brute-forcing the server's renaming logic.

## What it's for

fileGPS (by 0blio, subtitled "the HTTP filename upload guesser") solves a narrow post-exploitation problem: you uploaded a payload or web shell through a form, the server-side handler renamed it, and the response does not say where it landed. The tool (Python 2 with requests) takes the upload endpoint and systematically guesses filename transformations — the renaming patterns upload handlers commonly apply — to locate the stored file on the server. Its GitHub repository has been deleted and archive captures of its documentation did not survive, so its full option set is unrecoverable; the packaged source still runs under Python 2.

No corpus evidence — derived from the packaged source.

## Typical use cases

### Print the available options

Discover the tool's current argument set from the binary itself.

```
filegps -h
```

Outcome: the packaged wrapper runs `fileGPS.py` under Python 2, and argparse prints the usage message listing its arguments — the authoritative option reference now that upstream documentation is gone.
