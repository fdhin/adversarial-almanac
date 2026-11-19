---
technology: "Monitoring platforms"
kind: overview
audience: offensive-security
family: network-services
role: area-router
layer: infrastructure monitoring / fault-management platforms (HTTP consoles + agent/poller protocols; 10050-10051, 5666, 8443, 80/443)
status: confirmed
provenance: "corpus-grounded (strict command-context gate 2026-11-12: zabbix 4 files, cacti 4, nagios-core 1 real + 3 presence/banners reclassified, pandora 1, nsclient 1, centreon 1) + upstream-verified (Zabbix tracker ZBX-25614/25623/24505, Cacti GHSA/NVD advisories, Nagios XI CVE-2023-40931, Pandora FMS CVE-2021-32099, Centreon CVE-2019-13024, NSClient++ docs and exploit-db 46802). Router/dispatch page; implementation pages: [[monitoring-zabbix]], [[monitoring-cacti]]. Enrichment pass 2 new-unit wave A, 2026-11-12."
---

# Monitoring platforms (area router)

> The machine that watches every machine: one login page that holds credentials for the whole fleet and, by design, pushes code to everything it monitors. Own the monitor, own the monitored.

## What it is

Infrastructure-monitoring platforms — **Zabbix**, **Cacti**, the **Nagios** family (core, XI, NRPE, NSClient++), **Pandora FMS**, **Centreon** — share one architecture: a **PHP web console** over a SQL database, a **poller/server** process that collects metrics, and **agents** running on monitored hosts (Zabbix agent on 10050/tcp, NRPE and NSClient++ around 5666, NSClient++'s own web server on 8443). The console renders graphs and health dashboards; the server asks agents for data; agents execute local checks — scripts, plugins, counters — and return the output.

For an attacker the design is a gift twice over. First, a monitoring server **must store working credentials for everything it watches** — SNMP communities, database logins, SSH keys, API tokens — so any read primitive against the console or its database yields fleet-wide credential material. Second, "run this check on that host" is a *feature*: consoles and servers execute commands on monitored systems as a service, so console compromise converts directly into code execution on the monitored estate. Vendors draw a line between "monitoring" and "command execution" with a config flag; attackers do not.

Fingerprint which platform you are facing before anything else — the products look alike in a port scan and their attack playbooks are almost disjoint. The dispatch table below is the first move.

## The anatomy that matters on an engagement

    operator → web console (PHP + SQL) → poller/server daemon → agents on monitored hosts
        ├─ console    → credential store: every monitored device's login
        │              → version footer / login banners: free fingerprint
        │              → script/check editors: command input by design
        ├─ server     → connects OUT to agents (10050, 5666) — or agents dial in
        └─ agents     → execute local checks, often as root/SYSTEM
                       → trust whoever reaches them when auth is off/default

Three structural properties drive most findings:

- **Credential-reuse-into-monitoring-RCE is the dominant chain.** Monitoring consoles sit on "internal" networks and inherit passwords from everywhere — the BMC/IPMI administrator whose hash you cracked, the LDAP service account, the SNMP-derived credentials (cross-ref [[snmp]]). One reused login on the console becomes a `system.run` item, a scripted check, or a poller command on every monitored host.
- **Agents are reachable and trusting.** Zabbix agents answer on 10050 to anyone who can route to them; NRPE and NSClient++ historically run checks from any permitted source. An exposed agent with command items enabled is pre-auth code execution on a root/SYSTEM process.
- **Consoles leak their own identity.** Version strings in footers, distinctive paths (`api_jsonrpc.php`, `remote_agent.php`, `/nagiosxi/`, `/centreon`), and login-page titles pin the product and version before any authentication — turning a CVE index into a targeting list.

## Fingerprint / dispatch table

| You observe | Platform | Go to |
|---|---|---|
| `10050/tcp open zabbix-agent`, `10051/tcp open zabbix-trapper`, frontend footer "© … Zabbix SIA", `/api_jsonrpc.php` | Zabbix | [[monitoring-zabbix\|Zabbix methodology]] |
| Login page titled "Login to Cacti", `/remote_agent.php`, `graph_image.php`, poller/spine references, "Cacti" version in page footers | Cacti | [[monitoring-cacti\|Cacti methodology]] |
| `/nagios/` behind basic auth, `/nagiosxi/` paths, `/nagiosxi/api/v1/`, `5666/tcp open nagios-nsca` | Nagios / Nagios XI | Section below |
| `8443/tcp` with an NSClient++ login page; `nscp.exe` or `nsclient.ini` on a Windows host | NSClient++ | Section below |
| "Pandora FMS" branding, `pandora_console` paths, `/include/chart_generator.php` | Pandora FMS | Section below |
| Redirect to `/centreon/` path, `centreon_token` in login forms, poller configuration pages | Centreon | Section below |

Bare port banners (`5666/tcp open nagios-nsca` and the like) are presence, not attack surface — plenty of boxes ship an NSCA listener the engagement never touches. Confirm the product from its markers before committing to a playbook.

## Protocol-common: the credential-reuse-into-monitoring-RCE pattern

1. **Harvest credentials anywhere on the estate** — IPMI/BMC password hashes (cross-ref [[ipmi]]), SNMP-extracted configuration strings (a real target leaked a service account's password inside an SNMP walk), password stores, databases (cross-ref [[mysql]]), configuration trees in git or on shares (cross-ref [[smb]]).
2. **Replay them against the console.** Monitoring consoles rarely enforce unique passwords because they are "just dashboards". A BMC administrator's cracked password, an LDAP account, or the monitoring service account itself all land in the same login form. Default pairs (`Admin:zabbix`, `admin:centreon`) are documented product defaults — try them first, always.
3. **Read the credential store.** Once inside (or via any SQL injection / file read), pull what the platform *monitors with*: host credentials, SNMP communities, database logins, notification email secrets. These are reusable far beyond the monitor itself.
4. **Convert console access into execution.** Every mature platform has a legitimate "run something on a monitored host" feature — Zabbix's `system.run` agent items and global scripts, Nagios' check plugins and event handlers, Cacti's poller commands, Centreon's poller binary configuration, NSClient++'s external scripts. From the console this is one or two clicks; from the API it is a scripted POST.
5. **Skip the console when the agent is exposed.** Unauthenticated, command-enabled agents give the same execution with no console in the path.

## Nagios XI — token auth, disabled-account sessions, chained SQLi

Nagios XI (the commercial web platform around Nagios core) exposes a REST-ish API under `/nagiosxi/api/v1/`. The patterns that matter:

- **`/api/v1/authenticate` mints tokens for disabled accounts.** One observed chain: credentials harvested over SNMP failed at the web login because the account was disabled — but `POST /nagiosxi/api/v1/authenticate` with `username=…&password=…&valid_min=5` happily returned a working token. The UI's account state and the API's are not the same gate.
- **Token-bearing side endpoints leak data**: `/nagiosxi/includes/components/nagioscore/ui/trends.php?createimage&host=localhost&token=TOKEN` fetches rendered images with the token — a quick validation that the token is live.
- **CVE-2023-40931** (upstream-verified): authenticated SQL injection in `/nagiosxi/admin/banner_message-ajaxhelper.php` via the `id`/`ID` parameter, Nagios XI 5.11.0–5.11.1. An observed chain used it to dump backend records including an administrator **API key**, then used that key to create a fresh administrator account — SQLi to full admin without ever touching a password hash. The same research wave disclosed sibling issues (including CVE-2023-40932, a reflective XSS in the same component family) — check the vendor advisory for the current fixed release before relying on version gates.
- **Post-admin execution**: Nagios XI has documented "run a check command / manage plugins" surfaces; an admin API key plus the monitoring engine's plugin execution is host code execution in one hop.

## NSClient++ — Windows agent, web-configured code execution

NSClient++ is the Windows monitoring agent (NRPE-compatible checks plus its own protocol). With its web server enabled it listens on **8443** with a login page and a REST API (documented at the project's docs site: `/api/v1/scripts` lists, adds, and deletes scripts; `/api/v1/queries/…/commands/execute` runs them — script execution as the NSClient++ service, i.e. **SYSTEM**).

- **The web admin password is recoverable from the host**: the documented CLI `nscp.exe web --password --display` prints the configured web password from `nsclient.ini`. Any local read primitive (traversal, backup, world-readable config) converts into console access; a known privilege-escalation write-up for 0.5.2.35-era builds formalizes the local variant (exploit-db 46802).
- **Chain**: reach the web app (tunnel if it binds loopback — one observed engagement pivoted through SSH to reach it) → authenticate with the recovered password → add an external script pointing at your payload (`cmd /c`, PowerShell download cradle, or a batch file dropped on disk) → execute via the scripts API or restart the service so the script fires. Execution lands as the service account.
- The web interface also leaks the NSClient++ version on its login page — version-gate against the project's release notes before claiming a specific bug.

## Pandora FMS — internal console, unauthenticated SQLi

Pandora FMS (Artica) ships a PHP console (`pandora_console`) that deployments frequently bind to **localhost or an internal-only virtual host** — treat "pandora.conf in an apache sites-enabled listing" as a finding: port-forward or SSRF into it.

- **CVE-2021-32099** (upstream-verified): unauthenticated SQL injection in `/include/chart_generator.php` via the `session_id` parameter against Pandora FMS 742 (7.0NG.742). The injection upgrades an unprivileged/guest session to an authenticated administrator session — a login bypass without ever owning a password. Public proof-of-concept code exists; the page footer discloses the running version to gate it.
- Sibling issue in the same release train: **CVE-2021-32098**, OS command injection through `functions_netflow.php` — same era, same product family; verify the version against the vendor advisory before selecting payloads.
- Post-admin, Pandora FMS' alert/command definitions and agent deployment features are the usual execution sinks.

## Centreon — poller config writes code

Centreon (open-source monitoring on Nagios/Icinga engines) lives under `/centreon/`. Two facts shape the attack:

- **Documented default credentials** `admin:centreon` (vendor-documented) — and the login form's `centreon_token` CSRF field is scrapable, so credential testing scripts trivially.
- **CVE-2019-13024** (upstream-verified): Centreon 18.x before 18.10.6, 19.x before 19.04.3, and Centreon web before 2.8.29 allow arbitrary command execution by an authenticated admin: the **`nagios_bin`** poller field (and the related `init_script`) is written into the generated monitoring configuration without sanitization, and `/centreon/include/configuration/configGenerate/xml/generateFiles.php` (parameters `poller`, `generate`) makes the server execute that binary path. One observed chain reached it through an SQL-injection login bypass on the 19.04-era authentication form rather than valid credentials — the bypass and the command injection compose.

## Where you'll meet it

- **Dedicated monitoring boxes** in internal segments — high value, low hardening, forgotten patch cycles. The web console is often the only "app" on the host.
- **Agent software scattered across the estate** — every monitored Windows/Linux host runs the agent binary, frequently as root/SYSTEM. Config files (`zabbix_agentd.conf`, `nsclient.ini`) on a single host reveal the server's address and the trust model.
- **Credential stores and automation trees**: monitoring configuration lives in git repositories and file shares right next to the secrets it uses — an SMB share named for automation is a monitoring engagement in one download.
- **Internal-only virtual hosts**: consoles bound to localhost or hostname-based vhosts that only surface after host enumeration or a config read.

## Reading the platform's mind

1. **Version footers are on by default** — Zabbix, Cacti, and Pandora FMS all print the version at the login page. Free CVE targeting before any authentication.
2. **API auth and UI auth are different gates** — disabled accounts, guest accounts, and API tokens behave differently across the two (Nagios XI's token mint for a disabled account is the canonical example).
3. **The credential store points outward** — every entry in the console's host configuration is a credential for a *different* machine; treat a console compromise as the pivot, not the destination.
4. **Check-execution features are load-bearing** — removing them breaks monitoring, so they are never disabled, only permissioned; your job is to reach the permission boundary (admin role, API key, agent network path).
5. **Agents trust the network they can see** — an agent port reachable from your position with command items enabled is equivalent to console admin, minus the logs a console would write.

## Where to go next

[[monitoring-zabbix|Zabbix]] — JSON-RPC API abuse, agent `system.run`, guest sessions, API SQLi chain. [[monitoring-cacti|Cacti]] — unauthenticated `remote_agent.php` command injection, package-import and graph-template RCEs. SNMP credential harvesting that feeds this pattern: [[snmp]].
