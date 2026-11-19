---
name: "s3scanner"
categories:
  - blackarch-scanner
version: "v3.1.1.r12.g64a00e2"
homepage: "https://github.com/sa7mon/S3Scanner"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-07-19"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "sa7mon/S3Scanner"
github-stars: 3179
github-pushed: "2026-08-03"
github-archived: false
github-issues: 41
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# s3scanner

> Finds S3 buckets across AWS and other S3-compatible providers and audits their permissions for public exposure.

## What it's for

Takes bucket names (one, a file of many, or a RabbitMQ queue) and determines
which exist, in which region, and — the point of the tool — what their
permissions allow: whether anonymous users can list or read the bucket, and
with `-enumerate` what objects sit inside open buckets. It speaks not just AWS
but DigitalOcean Spaces, DreamHost, GCP, Linode and Scaleway object storage,
plus custom S3-compatible endpoints defined in its `config.yml`. Use it during
cloud posture reviews to prove which of an organization's guessed or harvested
bucket names are actually exposed, and in continuous pipelines feeding results
to Postgres or a message queue. It does not attempt bucket takeover or
credential attacks; it is a misconfiguration finder.

## Typical use cases

### Check a list of bucket names

Sweep candidate names harvested from elsewhere (certs, JS files, wordlists).

```
s3scanner -bucket-file names.txt
```

Outcome: each name in the file (one per line, duplicates skipped) is probed on
the default AWS provider; existing buckets are printed with their region and
their permission findings, flagging anything anonymously listable or readable.

### Enumerate objects inside open buckets

Go beyond permissions and record what is actually exposed.

```
s3scanner -bucket-file names.txt -enumerate
```

Outcome: for every bucket the scan finds open, the object listing is walked
and the keys printed/recorded — expect this to be time-consuming on large
buckets, which is why enumeration is opt-in.

### Scan a non-AWS provider

Run the same checks against another S3-compatible object store.

```
s3scanner -provider gcp -bucket company-assets -enumerate
```

Outcome: the named bucket is checked against Google Cloud Storage instead of
AWS (`-provider` accepts `aws`, `gcp`, `digitalocean`, `dreamhost`, `linode`,
`scaleway`, and `custom` with a config-file endpoint definition), reporting
existence, region and permissions identically.

### Produce machine-readable output for pipelines

Emit JSON instead of human-readable tables.

```
s3scanner -bucket-file names.txt -json | jq -r '. | select(.bucket.exists==1) | .bucket.name'
```

Outcome: one JSON object per scanned bucket is written to stdout, which the jq
filter reduces to the names of buckets that exist — downstream tooling can
likewise select on permission fields; add `-db` (with a `db.uri` config key)
to also persist all results to PostgreSQL.

*No corpus evidence — derived from upstream documentation (sa7mon/S3Scanner
README usage and options sections).*
