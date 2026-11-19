---
technology: "Amazon Redshift"
kind: overview
audience: offensive-security
status: upstream-only
family: databases
layer: data store
homepage: https://aws.amazon.com/redshift
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — driver SQLi CVE-2024-12744/12745/12746 versions and fixes verified against the AWS security bulletin AWS-2024-015 (published 2024-12-24) and the 2025-default-behavior changes against the AWS Security Blog (Jan 2025); IAM credential path verified against AWS docs + rewritten from HackTricks (src/network-services-pentesting/5439-pentesting-redshift.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Amazon Redshift

> A Postgres wire protocol hiding a cloud IAM door: the same API that describes the cluster can mint database credentials for an IAM principal — so the "database" perimeter and the AWS account perimeter are one system. See [[aws]] for the cloud side.

## What it is

Amazon Redshift is AWS's managed petabyte-scale data warehouse, speaking a lightly modified **PostgreSQL wire protocol** on TCP **5439** (customizable; serverless workgroups default there too). Most Postgres client tooling connects — psql, psycopg2, JDBC/ODBC — but the auth model and the TLS requirements are Redshift's own. Deployment shapes: **provisioned clusters** (master user, historically `awsuser`, with broad rights) and **serverless workgroups** (no superuser concept at all).

Two eras define the attack surface. Legacy estates: clusters created before 2025 could be `PubliclyAccessible=true` behind wide security groups — a Postgres-like internet surface for brute force and SQLi, discoverable by scanning services. Modern estates (AWS changed default behavior in January 2025): public access off, encryption at rest on, TLS enforced — which moves the realistic entry points to **stolen credentials, IAM abuse, and driver-side bugs** rather than network exposure.

The IAM door is the distinctive one: the Redshift API's `GetClusterCredentials` (and the serverless equivalents) returns short-lived database credentials for an arbitrary `--db-user` name to any principal allowed to call it — an IAM foothold becomes a database session, with `CreateClusterUser`/`JoinGroup` on the policy widening what the resulting session can be. This is a distinct action space from RDS IAM auth (`rds-db:connect`) — wrong permission names are a classic red-team dead end.

## The anatomy that matters on an engagement

    client → <endpoint>:5439  (Postgres wire; TLS 1.2+ w/ PFS ciphers)
      auth path A: database password (master user awsuser / created users)
      auth path B: IAM temp creds — JDBC/ODBC authMech=IAM + provider plugins (Okta,
                   IdC/SAML/Azure AD → local-webserver loopback callback leaks assertions)
      auth path C: cloud-side mint — redshift:GetClusterCredentials
                   (+ CreateClusterUser/JoinGroup expansions)  ← see [[aws]]
    post-auth SQL surface (provisioned master user):
      COPY from S3 · UNLOAD to S3 · external schemas (Spectrum) · UDFs
        Python UDFs: creation blocked after 2025-10-30 (existing ones keep running;
                     no network/filesystem — database compute, not host RCE)
        Lambda UDFs: execute in the function's IAM role and VPC — a pivot, not host access
    drivers (app side): JDBC/Python/ODBC metadata APIs — CVE-2024-12744/5/6 SQLi

Properties that drive findings: (1) error messages distinguish bad password from unknown user — username enumeration under brute force; (2) the parameter group (`require_ssl`, activity logging) is the stealth/transport gate; (3) data movement is built in — COPY/UNLOAD make S3 the natural exfil and staging medium, gated by IAM and the enhanced-VPC-routing setting.

## Where you'll meet it

- **Data-platform segments of AWS estates** — reachable from CI, Glue jobs, BI tools, and analyst workstations; the credential reuse between those and the warehouse is the usual way in.
- **Legacy public clusters** (`PubliclyAccessible`, permissive SG, default port) — a shrinking but real population of internet-facing Postgres-compatible surfaces.
- **Applications embedding Redshift drivers** — the December 2024 metadata-API SQLi (below) turns any app that forwards user-controlled catalog/pattern arguments into a query executor under the app's DB user.

## Reading the framework's mind

1. psql works: `psql "host=<endpoint> port=5439 user=<u> dbname=dev sslmode=require"` — but plan for TLS 1.2+; old clients fail, and modern parameter groups (`require_ssl=true`) close plaintext downgrade entirely.
2. Authentication error text is a username oracle — exploit it in spraying, and read it defensively when rating your own attempts.
3. An IAM principal is a database credential waiting to be minted — check `redshift:GetClusterCredentials` and friends in every role you capture; do not bother testing `rds-db:*` against Redshift.
4. Post-auth, the master user is a data-movement engine: COPY from attacker-readable S3, UNLOAD of query results outward, Spectrum external schemas — and serverless has no superuser, so expectations must be shaped per deployment.
5. Driver version gates matter as much as server versions: JDBC 2.1.0.31 / Python connector 2.1.4 / ODBC 2.1.5.0 carry the metadata-API SQLi (fixed in 2.1.0.32 / 2.1.5 / 2.1.6.0 respectively).

## Where to go next

The companion methodology: [[redshift-bug-bounty]] — connectivity and auth paths, the IAM pivot, driver SQLi, and post-auth data movement. Related: [[postgres]], [[aws]].
