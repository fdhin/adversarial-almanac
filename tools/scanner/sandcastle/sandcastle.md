---
name: "sandcastle"
categories:
  - blackarch-scanner
version: "73.10af7c7"
homepage: "https://github.com/0xSearches/sandcastle"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "0xSearches/sandcastle"
github-stars: 146
github-pushed: "2022-12-01"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sandcastle

> Python script that enumerates AWS S3 buckets for a target organization by testing a permutation wordlist.

## What it's for

sandcastle (formerly bucketCrawler) takes an organization's name as a "stem" and probes `stem` plus each line of a bucket-permutation file (`-dev`, `-backup`, `-attachments`, …) to discover S3 buckets belonging to that organization. For every permutation that exists it reports the status Amazon returns and tests read access: 403 (Access Denied) and 200 (Publicly Accessible) are the interesting outcomes, while 404 (not found) is hidden by default. It only enumerates and checks read permissions — listing or downloading content is left to the AWS CLI, for which the README keeps a quick reference. Python 2 era tool; the PyPI distribution was disabled upstream, so it runs from a checkout or the distro package.

## Typical use cases

### Enumerate buckets for a target stem

Discover which guessed bucket names exist for an organization.

```
sandcastle -t example -f /usr/share/sandcastle/bucket-names.txt
```

Outcome: iterates the permutation file, printing each potential match with its status code and attempting a listing — an AccessDenied error confirms the bucket exists but is not readable; 200 means publicly accessible.

### Test a custom permutation list

Reuse your own bucket-name candidates.

```
sandcastle -t example -f custom-buckets.txt
```

Outcome: the same enumeration pass over your file; `-f` is optional and defaults to `bucket-names.txt` in the working directory.

*No corpus evidence — derived from upstream documentation (0xSearches/sandcastle README; packaged command and data path from the BlackArch recipe).*
