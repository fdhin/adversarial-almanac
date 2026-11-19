---
name: "apkurlgrep"
categories:
  - blackarch-mobile
version: "8.a188a98"
homepage: "https://github.com/ndelphit/apkurlgrep"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "ndelphit/apkurlgrep"
github-stars: 904
github-pushed: "2024-05-22"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# apkurlgrep

> Extracts URLs and URL paths from an APK by decompiling it with apktool and grepping the recovered files.

## What it's for

A single-purpose Go tool: hand it an APK and it decodes it into a temporary directory with apktool, regex-greps the recovered files for URLs, prints the distinct full URLs and the URL paths seen in them, then deletes the temp directory. Useful for quickly collecting endpoint candidates before proxying traffic or testing an API — output is exactly two lists with no further analysis. Its only option is `-a` (the APK path); apktool must be installed and on PATH, and the tool checks for it before doing anything.

## Typical use cases

### Pull endpoints out of an APK

Collect the URL surface an app embeds.

```
apkurlgrep -a target.apk
```

Outcome: apktool decodes `target.apk` to a temp dir, then the tool prints a `Result of URLs:` list (e.g. `https://example.com/...`) followed by `Result of URLs Paths:` (the distinct paths seen in those URLs) and removes the temp dir. Feed the lists into proxy scoping or further endpoint testing.

*No corpus evidence — derived from upstream documentation.*
