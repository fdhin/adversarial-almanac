---
name: "dufflebag"
categories:
  - recon
version: "0.0~git20200205.9a01942-0parrot1"
homepage: "https://github.com/BishopFox/dufflebag"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "BishopFox/dufflebag"
github-stars: 304
github-pushed: "2023-04-24"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dufflebag

> BishopFox tool that mass-searches public AWS EBS snapshots for accidentally exposed secrets such as private keys, passwords and AWS keys.

## What it's for

Dufflebag (BishopFox) searches public Elastic Block Storage (EBS) snapshots across an AWS region for secrets that were accidentally left in them — private keys, passwords, AWS credentials and similar. It has no standalone CLI: it is packaged as an Elastic Beanstalk worker application (written in Go) because reading EBS volumes in practice requires copying each snapshot, creating a volume from it, attaching and mounting it, and tearing it down afterwards — work the Elastic Beanstalk/SQS worker tier automates and scales. Findings are written to an S3 bucket whose name starts with `dufflebag`, which you create yourself; the queue metrics in the AWS console show processing progress, and the region it scans is set in `region.go`. Operating it requires an AWS environment and IAM permissions for the snapshot/volume lifecycle plus SQS/S3 access.

By default it inspects only 20 snapshots as a safety valve (removed in `populate.go` to widen to the whole region), and what counts as "interesting" is defined in `inspector.go`: the `pilfer()` goroutine applies filename blacklists/whitelists (`IsSensitiveFileName()`) and content regexes in `checkContentsRegex()` — edit the regexes there to hunt for organization-specific strings instead of the built-in secret patterns. Expect AWS costs from the volume churn it creates.

No corpus evidence — derived from upstream documentation. No verifiable CLI: it is a deployed Elastic Beanstalk application rather than a command-line tool, so this note is description-only.
