---
name: "pdgmail"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "http://www.jeffbryner.com/code/pdgmail"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "qashqao/pdgmail"
github-stars: 2
github-pushed: "2018-10-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pdgmail

> Extracts Gmail session artifacts — account name, contacts, e-mail addresses, message bodies, last-access times and IP addresses — from the strings of a web browser's process-memory dump (Jeff Bryner, 2008, Python).

*No corpus evidence — derived from upstream documentation. The canonical source is the author's site (frontmatter `homepage`, still live); the linked GitHub repo is a third-party mirror of the same script. The packaged BlackArch description ("password dictionary attack tool that targets windows authentication via the SMB protocol") is a mis-attribution — the tool has nothing to do with SMB or dictionary attacks.*

## What it's for

Turning a raw browser memory dump into readable Gmail artifacts. A live Gmail session keeps data such as the logged-in account, contact lists, message snippets and last-account-access information in the browser's process memory; pdgmail re-finds it by pattern-matching the dump's string content. Intended workflow: dump the memory of the browser process (the script header suggests `pd` from trapkit.de on Windows), extract little-endian UTF-16 strings with `strings -el`, and feed the result to pdgmail. Typical use is incident response and forensics on sessions you are authorized to examine. It is not an attack tool against Gmail and not a password cracker — everything it reports was simply present in the captured memory. It is Python 2 era code, and the author warns that for input other than a `strings -el` dump "your mileage will certainly vary."

## Typical use cases

### Pull Gmail artifacts out of a browser memory dump

```
strings -el browser.dmp > memorystrings.txt
pdgmail -f memorystrings.txt
```

Outcome: prints every artifact it recognizes — the Gmail account name, contacts and their e-mail addresses, message bodies, last-access times and IP addresses seen in the session.

### Suppress message-body matches when they drown out the rest

```
pdgmail -b -f memorystrings.txt
```

Outcome: skips the message-body patterns (the author flags them as prone to false positives) and reports only the other artifact classes; add `-v` for verbose output including the source filename.

### Read a strings dump straight from a pipe

```
strings -el browser.dmp | pdgmail
```

Outcome: with no `-f` file the tool reads from stdin and produces the same artifact report — handy for screening several dumps in a loop.
