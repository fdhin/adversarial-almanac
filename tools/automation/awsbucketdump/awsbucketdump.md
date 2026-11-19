---
name: "awsbucketdump"
categories:
  - blackarch-automation
version: "82.4684670"
homepage: "https://github.com/jordanpotti/AWSBucketDump"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "jordanpotti/AWSBucketDump"
github-stars: 1470
github-pushed: "2024-04-10"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# awsbucketdump

> Brute-forces guessed S3 bucket names, then greps and optionally downloads "interesting" files from any bucket that is publicly listable.

## What it's for

AWSBucketDump is to S3 buckets what a subdomain bruteforcer is to DNS: you give it a wordlist of candidate bucket names, it checks which exist as S3 buckets, lists their contents, and matches filenames against a keyword list to surface interesting files (backups, keys, exports), optionally downloading them. It only reads buckets whose contents are publicly listable — it is a misconfiguration-discovery tool, not an S3 exploitation framework. Buckets wordlists and grep keyword lists are your own (SecLists-style lists are the usual source), downloads default to 2 check + 2 download threads, and the `-D` download mode can fill a disk quickly, so pair it with `-m` (max file size in bytes). Written in Python 3 (requests, xmltodict); whether AWS rate-limits this kind of enumeration is undocumented, so expect throttling on large runs.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Enumerate buckets and grep filenames without downloading

Find exposed buckets and list only the interesting filenames.

```
awsbucketdump -l BucketNames.txt -g interesting_Keywords.txt
```

Outcome: each word in `BucketNames.txt` is tested as a bucket (2 threads by default); existing buckets are listed and filenames containing any grep keyword are reported — no files are written.

### Download interesting files with a size cap

Pull the loot once the grep shows something worth having.

```
awsbucketdump -l BucketNames.txt -g interesting_Keywords.txt -D -m 500000 -d 1
```

Outcome: matching files are downloaded (`-D`) up to 500000 bytes each (`-m` is in bytes), with `-d 1` sorting results into a directory per host so multiple targets stay separated.

### Speed up enumeration with more threads

Trade stealth for throughput on large bucket lists.

```
awsbucketdump -l BucketNames.txt -g interesting_Keywords.txt -t 10
```

Outcome: bucket existence checking runs with 10 threads instead of the default 2, shortening large sweeps (download threads remain separate and are set the same way).
