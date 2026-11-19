---
technology: "MinIO"
kind: overview
audience: offensive-security
family: databases
layer: object storage
language: Go
homepage: https://min.io
github-repo: https://github.com/minio/minio
provenance: "corpus-grounded (2 writeups after strict re-gate; one dispatch-sample candidate was 'dominio' substring noise, adjudicated out) + upstream-verified (GHSA-6xvq-wj2x-3h3q / CVE-2023-28432, GHSA-m4qq-5f7c-693q / CVE-2021-21287, MinIO docs root-credential settings)"
---

# MinIO

> A self-hosted S3 clone where the root credential pair defaults to `minioadmin:minioadmin` — upstream's own docs print the warning — and whose cluster-deployment bootstrap endpoint once handed every `MINIO_*` environment variable, root password included, to any unauthenticated POST.

## What it is

MinIO is a single-binary, S3-compatible object storage server written in Go (AGPL-licensed, MinIO Inc., the dominant self-hosted S3 alternative). One process serves the **S3 API and an embedded web console** on the same port — **9000** by default (`MINIO_SERVER_URL`/`--address` to move it; consoles were split onto 9001 in newer releases) — and speaks the AWS vocabulary end to end: buckets, access keys, presigned URLs, `aws s3api`-shaped JSON/XML. In k8s environments it fronts distributed erasure-coded clusters; in labs and on-prem stacks it is *the* "S3 but ours" component, holding backups, user uploads, home directories, and the secrets that applications stash in buckets.

The operator's first insight: **credentials are the whole attack**. MinIO has no SQL grammar and no template engine — every compromise in practice is a credential event: the default `minioadmin:minioadmin` pair (docs: "If `MINIO_ROOT_USER` is unset, minio defaults to `minioadmin`"), keys leaked in application config (one observed engagement: a CMS's S3 settings page displayed the access key and secret in cleartext), or the bootstrap endpoint that printed them for you (one observed chain: `POST /minio/bootstrap/v1/verify` returned `MINIO_ROOT_USER`/`MINIO_ROOT_PASSWORD` from the cluster's env). With root, `mc` (the official client) or the AWS CLI with `--endpoint-url` reads and writes every bucket on the deployment.

The second insight: **the console and its URL-fetching features are a server-side surface**. The browser UI talks to MinIO's own HTTP API, and features that take URLs (importing, notifying, replication probes) have produced real SSRF — CVE-2021-21287 (Browser API SSRF, fixed RELEASE.2021-01-30) — reachable by any console-authenticated user against cloud-metadata and internal services.

## The anatomy that matters on an engagement

```
client ──AWS SigV4──► :9000 S3 API  ──► buckets (uploads, backups, ~/.ssh tarballs, app secrets)
browser ──HTTP──►     :9000/:9001 console (login: root or IAM users)
                          ├─ /minio/bootstrap/v1/verify   (★ CVE-2023-28432: unauth env dump, clustered only)
                          ├─ console URL features         (★ CVE-2021-21287 SSRF)
                          └─ metrics endpoints            (version + topology recon; auth-gated or public)
app config / env files ──► MINIO_ROOT_USER / access+secret keys  (★ the usual real leak)
```

Structural properties that drive findings:

1. **Root = everything** — the root user bypasses all IAM policies; one leaked pair reads the entire store (`mc ls`, `aws s3 ls`).
2. **Credentials live in many plaintext places** — app config pages, `~/.aws/credentials`, docker-compose/k8s env blocks, `mc` alias JSON (`~/.mc/config.json`) on any foothold box.
3. **Version disclosure precedes exploitation** — the `Server: MinIO` header plus metrics endpoint names the release (e.g. `2023-03-13T19:46:17Z`), which decides whether 28432 applies (fixed in `RELEASE.2023-03-20T20-16-18Z`).

## Where you'll meet it

- **Internal S3 for web apps** — CMS/media platforms storing uploads in MinIO; the app's own settings/config leaks the access pair (field-observed). Custom ports are common (one such deployment used 54321) — service-detect by the `Server: MinIO` header and XML error bodies, not port number.
- **Kubernetes clusters / on-prem "private cloud"** — MinIO as the cluster's object store; a metrics or console exposure leaks topology and versions (an observed `/metrics` exposure disclosed node names + the exact release).
- **ML/AI pipelines and backup infrastructure** — datasets, model artifacts, database dumps; bucket names enumerate via the S3 ListBuckets API once any key works.
- **Homelab-adjacent production** — tutorials start MinIO with defaults; `minioadmin:minioadmin` against internet-facing consoles is a standing bot-target.

## Reading the daemon's mind

- Default login `minioadmin:minioadmin` applies whenever root env vars are unset — try it on every console before anything clever.
- `MINIO_BROWSER=off` disables the web console entirely; a console-less deployment still exposes the S3 API (SigV4-authenticated) and possibly metrics.
- CVE-2023-28432 applies only to **cluster** (distributed) deployments and only builds before `RELEASE.2023-03-20T20-16-18Z` — the verify endpoint returns all `MINIO_*` env vars unauthenticated.
- Buckets may be public (anonymous download/policy `*`) even when credentials fail — always test ListObjects/GetObject unsigned per bucket.
- `mc alias set` stores plaintext creds in `~/.mc/config.json`; `~/.aws/credentials` and compose env blocks are equally lootable on footholds.

## Where to go next

- [[minio-bug-bounty]] — full method: header/version recon → default creds → config-harvested keys → mc/aws enumeration → CVE-2023-28432 env dump → console SSRF (CVE-2021-21287) → public-bucket checks.
