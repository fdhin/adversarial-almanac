---
name: "cloud_enum"
categories:
  - blackarch-recon
version: "0.9.r0.g21af4f9"
homepage: "https://github.com/initstring/cloud_enum"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
meta-checked: "2026-11-09"
github-repo: "initstring/cloud_enum"
github-stars: 2159
github-pushed: "2026-10-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# cloud_enum

> Keyword-driven enumeration of public cloud resources across AWS, Azure and Google Cloud.

## What it's for

cloud_enum takes keywords — company names, product names, domains — and checks the big three providers for resources named after them: AWS S3 buckets (open and protected) and awsapps endpoints (WorkMail, WorkDocs, Connect); Azure storage accounts, open blob containers, hosted databases, VMs and web apps; and GCP buckets, Firebase Realtime Databases, App Engine sites, Cloud Functions and Firebase apps. Keywords are mutated with a built-in fuzz list (`enum_tools/fuzz.txt`) or your own `-m` file; services needing a second brute-force round (Azure containers, GCP functions) use `-b` and are throttled by editable region lists in the source. The author has shifted maintenance toward Nuclei templates, but the tool remains packaged and functional.

## Typical use cases

### Enumerate a target by keywords

Check all three clouds for resources named after a company.

```
cloud_enum -k example -k example.com
```

Outcome: mutations of both keywords are tested against AWS, Azure and GCP; found resources print grouped by provider as the scan progresses (buckets, storage accounts, Firebase databases and so on).

### Drive from keyword and mutation files

Control the whole search surface yourself.

```
cloud_enum -kf keywords.txt -m mutations.txt -b brute.txt
```

Outcome: replaces the built-in defaults with your keyword list, first-level mutation list and second-level brute-force list — better results than the bundled fuzz strings for targeted engagements.

### Scoped, fast scan with a JSON log

Limit providers and record findings for tooling.

```
cloud_enum -k example --disable-azure -qs -l findings.txt -f json
```

Outcome: skips Azure checks and disables all mutations and second-level scans (`-qs`), then appends confirmed findings to findings.txt in JSON — a quick AWS/GCP-only pass suitable for scripting.

*No corpus evidence — derived from upstream documentation.*
