---
name: "aws-extender-cli"
categories:
  - blackarch-scanner
version: "17.a351154"
homepage: "https://github.com/VirtueSecurity/aws-extender-cli"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "VirtueSecurity/aws-extender-cli"
github-stars: 84
github-pushed: "2020-04-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aws-extender-cli

> Command-line checker for cloud-storage exposure — tests Amazon S3 buckets, Google Storage buckets and Azure Storage containers for the public-access misconfigurations that let outsiders list or read their contents.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Auditing whether a storage bucket's permissions leak its contents. Given a bucket name (or a file of names), it probes the storage service with boto/boto3 and reports which capabilities an outsider can exercise — public listing, object reads, multipart-upload parts — followed by the object names it can actually see. Written by VirtueSecurity as the CLI companion to their AWS Extender browser extension; the `-s` service flag selects `S3`, `GS` or `Azure` so the same checks run against the three clouds. It tests exposure of named buckets; it is not a bucket-name brute-forcer (bring your own name list or wordlist) and it does not test ACL write paths beyond the reported capabilities.

## Typical use cases

### Test a single S3 bucket for public exposure

```
aws-extender-cli -s S3 -b example-bucket -k keys.ini
```

Outcome: probes the bucket and prints a header block for it, then one line per capability granted (`[*] s3:ListBucket` and friends) followed by the object names those rights expose. Any listed object is by definition publicly retrievable — evidence for the finding. The keys file uses the AWS ini format (`aws_access_key_id` / `aws_secret_access_key`).

### Audit a list of bucket names

```
aws-extender-cli -s S3 -f buckets.txt -k keys.ini
```

Outcome: runs the same capability and object check for every name in the file, one result block per bucket — the bulk form for auditing an organization's naming estate (dev/test/backup variants of a known name).

### Run the same check against Google Storage

```
aws-extender-cli -s GS -b example-gs-bucket -k gs-keys.ini
```

Outcome: the identical exposure test against a Google Storage bucket, using a GS-format keys file; `-s Azure` applies it to Azure Storage containers. Results carry the equivalent GS/Azure permissions and object listings.
