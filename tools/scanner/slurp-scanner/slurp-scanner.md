---
name: "slurp-scanner"
categories:
  - blackarch-scanner
version: "93.2dea27e"
homepage: "https://github.com/0xbharath/slurp"
packaged-by: [blackarch]
blackarch-builddate: "2025-06-07"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0xbharath/slurp"
github-stars: 97
github-pushed: "2022-08-18"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# slurp-scanner

> S3 bucket enumerator: permutation-driven blackbox discovery by domain or keyword, plus whitebox audits of your own AWS account.

## What it's for

slurp (by 0xbharath, Go, GPLv3; BlackArch packages the invirtuate Codeberg
fork and installs it as `slurp-scanner` — upstream's binary is just
`slurp`) finds Amazon S3 buckets two ways. In blackbox mode it generates
over 28,000 permutations per seed (domain or keyword), checks which
resolve as buckets, and colorizes the hits — fast, unauthenticated, and
inherently noisy: it cannot tie a discovered bucket to an AWS account, so
expect false positives to verify by hand. In whitebox mode it uses your
own credentials from `~/.aws/credentials` to list every bucket in the
account and audit its policy/ACL exposure — authoritative, but only for
accounts you control. Use domain/keyword mode for recon against a target's
naming habits, and internal mode to find your organization's own
misconfigurations.

## Typical use cases

### Enumerate buckets derived from a domain

Blackbox recon keyed to a target's name.

```
slurp-scanner domain -t example.com
```

Outcome: every permutation of the domain is checked and existing buckets
are printed color-coded as they're found; treat each hit as a lead to
verify (open/read state) before reporting, since blackbox mode cannot
prove ownership.

### Enumerate buckets from keywords

Widen the net with product or project names.

```
slurp-scanner keyword -t linux,golang,python
```

Outcome: the comma-separated keyword list seeds the same permutation
engine, surfacing buckets named after each keyword — useful for finding a
target's build artifacts, backups, or data exports that don't carry the
domain itself.

### Audit your own AWS account

Whitebox check for exposure you can fix.

```
slurp-scanner internal
```

Outcome: using the AWS credentials configured in `~/.aws/credentials`,
every bucket in the account is pulled and its Policy/ACL permissions are
checked, reporting which of your own buckets are exposed and how.

*No corpus evidence — derived from upstream documentation (README).*
