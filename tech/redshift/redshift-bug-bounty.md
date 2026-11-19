---
technology: "Amazon Redshift"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: databases
version: "1.0"
source: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — CVE-2024-12744/12745/12746 affected+fixed versions verified against AWS security bulletin AWS-2024-015 (2024-12-24); 2025 defaults verified against the AWS Security Blog 'Amazon Redshift enhances security by changing default behavior in 2025'; IAM credential flow per AWS docs (GetClusterCredentials, IAM/SAML driver plugins, enhanced VPC routing) + rewritten from HackTricks (src/network-services-pentesting/5439-pentesting-redshift.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# Amazon Redshift bug-bounty methodology

Companion overview: [[redshift]]. Cloud-side enumeration and privilege analysis: [[aws]]. Wire-level SQL method shared with the Postgres family: [[postgres]].

## Phase 0 — Endpoint and network posture

- Port **5439/TCP**; endpoint patterns: provisioned `<clusterid>.<random>.<region>.redshift.amazonaws.com` (`.amazonaws.com.cn` in China), serverless `<workgroup>.<random>.<region>.redshift-serverless.amazonaws.com`. Default port + default SG made these Shodan/Censys-visible for years.
- Check `PubliclyAccessible` and the security group: `0.0.0.0/0` on 5439 is the legacy-exposure signature. Since **January 2025**, new clusters/workgroups and snapshot restores default to public access **disabled**, encryption on, secure connections enforced — legacy resources predate this.
- **Enhanced VPC routing**: without it, COPY/UNLOAD traffic egresses via the internet path (including to other AWS services); with it, traffic stays in the VPC. The setting routes packets — it grants no bucket permissions by itself.
- Serverless (Jun 2025 change): workgroup endpoints are created in up to three AZs — enumerate all workgroup VPCE DNS names to find the reachable one.

## Phase 1 — Connect and enumerate (Postgres tooling)

```bash
psql "host=cluster.example.redshift.amazonaws.com port=5439 user=awsuser dbname=dev sslmode=require"
```

TLS 1.2+ with PFS ciphers is required by the service; modern parameter groups set `require_ssl=true` (the `default.redshift-2.0` family), so plaintext downgrade is closed. In-session recon:

```sql
select version();
\l                      -- databases
\du                     -- roles
select * from pg_user;
select * from svv_redshift_sessions;
```

Error oracle: authentication failures differentiate wrong password from nonexistent user — exploit for username enumeration during approved brute-force; the master user is often `awsuser`.

## Phase 2 — Authentication paths (test all three)

**A. Database passwords** — master user and created users; candidate sets from the environment's service accounts.

**B. IAM-driven drivers** — JDBC `authMech=IAM` with provider plug-ins (`plugin_name=com.amazon.redshift.plugin.OktaCredentialsProvider` and SAML/Azure AD/Identity Center siblings). SSO plug-ins spin up a **local webserver for the loopback callback** — a captured callback leaks the SAML assertion or temp credentials; position to observe loopback traffic on operator workstations.

**C. Cloud-side mint — the pivot from [[aws]]:** a compromised IAM principal that can call the Redshift credential action gets a database session without ever knowing a DB password:

```bash
aws redshift get-cluster-credentials --cluster-identifier CLUSTER_ID \
    --db-user pentest --db-name dev --duration-seconds 900
psql "host=cluster.example.redshift.amazonaws.com user=pentest password='<TEMP_PW>' dbname=dev sslmode=require"
```

Test: does the role have `redshift:GetClusterCredentials` (or the serverless equivalent)? Do `CreateClusterUser`/`JoinGroup` widen the resulting access (auto-provisioned users, group-granted rights)? This is a **different action space from RDS** — `rds-db:connect` does nothing here; don't burn the check.

## Phase 3 — Driver-side metadata SQLi (CVE-2024-12744 / -12745 / -12746)

Client-side bug class: the drivers build metadata queries with **unquoted** user input. Any application that passes attacker-controlled catalog/schema/pattern arguments into a metadata API executes injected SQL under the app's DB user.

| Driver | Affected | Vulnerable APIs | Fixed |
| --- | --- | --- | --- |
| JDBC | 2.1.0.31 | `getSchemas` / `getTables` / `getColumns` | 2.1.0.32 (or revert 2.1.0.30) |
| Python connector | 2.1.4 | `get_schemas` / `get_tables` / `get_columns` | 2.1.5 (or revert 2.1.3) |
| ODBC | 2.1.5.0 | `SQLTables` / `SQLColumns` | 2.1.6.0 (or revert 2.1.4.0) |

(Published 2024-12-24, AWS bulletin AWS-2024-015.) Python connector shape:

```python
import redshift_connector
conn = redshift_connector.connect(host='cluster.example.redshift.amazonaws.com',
                                  database='dev', user='lowpriv', password='<PASSWORD>')
cur = conn.cursor()
# table_name_pattern is concatenated unquoted in 2.1.4:
cur.get_tables(table_schema='public',
               table_name_pattern="%' UNION SELECT usename, passwd FROM pg_user--")
```

Impact framing: runs as the connector's database user — a low-priv app account escalates its read scope, not server RCE.

## Phase 4 — Post-auth data movement (provisioned master user)

- **Exfil**: `UNLOAD` of query results to S3 the role can write; `COPY` **from** attacker-controlled/reader S3 for staging.
- **External schemas (Spectrum)** — extend the readable surface to the data lake.
- **UDFs**: creation of new **Python** UDFs ended **2025-10-30** (existing ones keep working); Python UDFs have no network/filesystem access — database compute, never host RCE. **Lambda** UDFs run in the function's IAM role and VPC — an over-privileged or VPC-connected function is a pivot into AWS/network/private endpoints, but not into the Redshift host filesystem.
- Parameter-group checks that change your stealth calculus: `require_ssl`, `enable_user_activity_logging` (off = quieter), `max_concurrency_scaling_clusters`.
- Serverless: same TCP/SQL surface, **no superuser** — shape the report accordingly.

## Phase 5 — Report against the 2025 baseline

New estates: public-off + enforced TLS + encryption at rest mean sniffing/MITM is out; entry is credentials, IAM, SSRF-into-VPC-path, or driver bugs. Legacy estates: everything Phase 0 said, plus the delta to the current defaults as the finding.

## Appendix — tools in prose

psql (primary client), the AWS CLI (`redshift get-cluster-credentials` and peer actions — see [[aws]]), redshift_connector / JDBC / ODBC drivers (both the vulnerable and fixed versions above), Shodan/Censys for legacy-endpoint discovery.
