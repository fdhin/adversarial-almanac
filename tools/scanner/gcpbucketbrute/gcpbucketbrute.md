---
name: "gcpbucketbrute"
categories:
  - blackarch-scanner
version: "17.6866bd2"
homepage: "https://github.com/RhinoSecurityLabs/GCPBucketBrute"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "RhinoSecurityLabs/GCPBucketBrute"
github-stars: 573
github-pushed: "2023-05-26"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gcpbucketbrute

> Enumerates Google Cloud Storage buckets from a keyword, then reports exactly what access — authenticated or anonymous — you have to each one it finds.

## What it's for

Given an organization keyword (a company or project name), GCPBucketBrute (by Rhino Security Labs) generates a large set of permuted bucket-name candidates, checks which exist as Google Cloud Storage buckets, and for each existing bucket queries the TestIamPermissions API to list the concrete permissions held — unauthenticated, and again with credentials when supplied. This turns "is there a leaked bucket somewhere" into a ranked answer: bucket exists, is publicly listable, is readable/writable, or allows privilege escalation (e.g. `storage.buckets.setIamPolicy`). It works fully unauthenticated; a service-account key or user access token adds the authenticated-permission view, revealing buckets that look closed anonymously but open to any logged-in identity. It only covers GCS buckets — not S3, Azure Blob or GCP services other than storage — and it does not read bucket contents itself. Binary: `gcpbucketbrute` (runs `gcpbucketbrute.py`).

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Enumerate buckets for a keyword unauthenticated

No Google credentials of any kind:

```
gcpbucketbrute -k example-corp -u
```

Outcome: permutes the keyword into bucket-name candidates, checks each for existence, and prints every existing bucket with the permissions an anonymous user holds on it (list/read/write). `-u` suppresses all authentication prompts, so the run needs nothing but network access.

### Enumerate with a service account

When you hold a compromised or assessment-provided service-account private key:

```
gcpbucketbrute -k example-corp -f sa-key.pem -o results.txt
```

Outcome: `-f` points at the service-account credential file (PEM/JSON private key); each discovered bucket is additionally tested via TestIamPermissions with that identity, so the report shows both the anonymous and the authenticated view — differences between the two mark buckets reachable only when logged in. `-o` writes the findings to a file for the report.

### Speed up scans with a custom wordlist

For wider or targeted candidate coverage:

```
gcpbucketbrute -k example-corp -u -w custom-words.txt -s 10
```

Outcome: `-w` replaces the built-in permutation list with your own candidate names (one bucket name per line) — useful after harvesting naming conventions from a web crawl — and `-s` raises the number of parallel subprocesses from the default 5, trading requests-per-second against Google rate limits for scan time.
