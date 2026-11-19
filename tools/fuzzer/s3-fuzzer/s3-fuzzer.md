---
name: "s3-fuzzer"
categories:
  - blackarch-fuzzer
version: "4.0a2a6f0"
homepage: "https://github.com/petermbenjamin/s3-fuzzer"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: BSD
meta-checked: "2026-11-09"
github-repo: "petermbenjamin/s3-fuzzer"
github-stars: 45
github-pushed: "2017-10-14"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# s3-fuzzer

> Concurrent prober that checks candidate AWS S3 bucket names for anonymous public listing.

## What it's for

s3-fuzzer (Peter Benjamin, written in Go) answers one cloud-review question quickly: which of a set of guessed S3 bucket names actually exist and are publicly listable? Given a single bucket name or a file of names (one per line), it concurrently issues `GET http://<name>.s3.amazonaws.com` and logs every name that returns HTTP 200 — meaning the bucket exists and allows anonymous listing. Feed it the output of a permutation generator (company name, `backup`, `dev`, `assets`, `-staging`, …) to sweep likely names during an AWS exposure assessment. It deliberately does little else: no permission checking beyond anonymous listing, no object/key enumeration, and plain HTTP requests only. The upstream repository has been archived since 2017, so treat it as a finished utility rather than an evolving tool.

## Typical use cases

### Check a single bucket name

Quick existence and public-listing test for one guess.

```
s3-fuzzer -n example-backups
```

Outcome: issues a single GET to `http://example-backups.s3.amazonaws.com`; if the response is HTTP 200 it prints `Bucket found: example-backups.s3.amazonaws.com` — a bucket an anonymous internet user can list. Any other status means the name is either taken-but-private or free.

### Sweep a wordlist of candidate names

Test hundreds of permutations at once.

```
s3-fuzzer -f buckets.txt
```

Outcome: one goroutine per line of `buckets.txt`, all names probed concurrently; the output is exactly the subset of the wordlist that resolved to publicly listable buckets — the shortlist for manual review of what those buckets expose.

### Debug the sweep and open findings in a browser

See every probe and inspect hits immediately.

```
s3-fuzzer -f buckets.txt -d -o
```

Outcome: `-d` raises logging to debug level, printing each name as it is checked (useful for confirming the wordlist is being consumed correctly); `-o` opens each found bucket's URL in the default web browser so its contents can be reviewed on the spot.

*No corpus evidence — derived from upstream documentation.*
