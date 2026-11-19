---
name: "fuxploider"
categories:
  - blackarch-webapp
version: "v1.0.r50.g75a81ff"
homepage: "https://github.com/almandin/fuxploider"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "almandin/fuxploider"
github-stars: 3340
github-pushed: "2025-05-08"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fuxploider

> Automates detection and exploitation of file upload form weaknesses.

## What it's for

Fuxploider (by almandin) points at a page containing a file upload form, first fingerprints which file types the server actually accepts (the recon phase), then tries the matching upload techniques — extension mangling, content-type tricks, and so on — to land an executable payload, testing each uploaded file for code execution. Reach for it whenever a target exposes an upload feature; success yields the location of a directly usable web shell. Python 3, interactive follow-up after the scan.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Test an upload form

Run the default recon-then-exploit flow.

```
fuxploider --url http://target.example.com/upload --not-regex 'wrong file type'
```

Outcome: crawls the form, learns the accepted extensions, uploads probe files and reports which techniques succeed; `--not-regex` names the response text that signals a rejected upload.

### Test behind authentication

Attack members-only upload forms.

```
fuxploider --url http://target.example.com/profile/avatar --cookies 'PHPSESSID=abc123' --true-regex 'upload successful'
```

Outcome: the session cookie rides every request and `--true-regex` detects successful uploads, letting both the recon and exploitation phases run against the authenticated form.

### Skip recon with known accepted extensions

Go straight to exploitation when the types are known.

```
fuxploider --url http://target.example.com/upload -l 'jpg,png,bmp' -s -T 8
```

Outcome: skips extension discovery (`-s`, requires the legit list `-l`) and jumps directly to upload attempts with 8 parallel threads (`-T`, default 4).
