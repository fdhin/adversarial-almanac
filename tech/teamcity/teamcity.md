---
technology: "JetBrains TeamCity"
kind: overview
audience: offensive-security
family: web-platforms
layer: CI/CD build server (Tomcat-served web UI on 8111, REST API at /app/rest, build agents executing jobs, HSQLDB-backed data directory)
language: Java (Kotlin internals); build steps in whatever the pipeline runs
first-released: 2006 (JetBrains)
license: "proprietary (free for small teams)"
homepage: "https://www.jetbrains.com/teamcity"
github-repo: "JetBrains/teamcity-rest-api-sample" (product is closed-source; docs at jetbrains.com/help/teamcity)
status: thin
provenance: "field-grounded (3 contributing writeups gated by command-context markers — TeamCity login/version strings, /app/rest token and debug-process calls, SSH -L 8111 tunnels, buildAgent paths, data-directory SSH-key loot; exclusions: a stray teamcity-server-log4j.xml filename inside an unrelated file listing, and four files where '8111' is only hex digits, multipart boundaries, or timestamps) + upstream-verified (JetBrains/Sonar/Rapid7 advisories for CVE-2023-42793 and CVE-2024-27198/-27199, TeamCity documentation for the data-directory and uploaded-SSH-key layout). Thin unit, first-class treatment per standing rule. Enrichment pass 2, 2026-11-12."
---

# JetBrains TeamCity

> A build server whose product promise is "run arbitrary instructions from version control" — so every path in is a privilege escalation, and it is routinely deployed as root, bound to loopback, behind an SSH tunnel nobody monitors.

## What it is

TeamCity (JetBrains, 2006; proprietary with a free tier) is a continuous-integration server: a Java web application served through Tomcat on port 8111, a REST API under `/app/rest`, an internal database (HSQLDB by default, files named `buildserver.*` in the data directory), and a fleet of **build agents** that check out your VCS root and execute the build steps you define. That description is also the threat model — a TeamCity login of almost any rank is a scripted request to run code on an agent, and the server frequently runs as root or SYSTEM because "it needs to build anything."

Two deployment habits make it an operator's favorite: it is often bound to `127.0.0.1:8111` (exposed only after pivoting, through an SSH local forward), and its version history carries two unauthenticated authentication bypasses (CVE-2023-42793 before 2023.05.4, CVE-2024-27198 before 2024.03) that turn a port-forward into admin.

## The anatomy that matters on an engagement

    client → (ssh -L 8111:127.0.0.1:8111) → Tomcat → TeamCity web UI (/login.html)
                                          ↘ REST API /app/rest (token or session auth)
    server → data directory (TEAMCITY_DATA_PATH):
               config/projects/<project>/pluginData/ssh_keys   ← uploaded SSH keys
               system/buildserver.*                           ← HSQLDB users/roles
    server → build agents (own work dirs, run builds as the agent's OS user; agent terminals)

Three structural properties drive most findings:

- **The REST API is the real front door.** `/app/rest/users/id:1/tokens/<name>` is the endpoint the 2023 bypass abused — and, once you hold any token, `/app/rest/debug/processes` is a command runner if debug mode was ever enabled.
- **The data directory is a credential store.** Projects' uploaded SSH keys live on disk under `pluginData/ssh_keys`; the HSQLDB `buildserver` database holds the user table with bcrypt hashes.
- **Agents execute by design.** Project-level roles that can patch or define builds are code execution on the agent; an "agent terminal" on a root-run server is a root shell with no exploit at all.

## Where you'll meet it

- **Loopback-only on a compromised host** — `ss -tulnp` shows `127.0.0.1:8111`; the intended reach is an SSH local forward, then the login page greets you with the build number, which picks the bypass.
- **Internal subdomains in dev-shop environments** — directory brute-forcing on the main domain (or a credentials store entry — see [[keepass]]) yields a `teamcity-dev`-style vhost with 401.
- **Windows estates with AD-integrated agents** — agent machines live in their own OUs; control of that OU (or of BuildAgent-management groups) means you can enroll rogue agents.
- **Dockerized servers** — the official image runs as `tcuser` inside the container; RCE there is a foothold, and the loot path runs through the mounted data directory to the host.

## Reading the server's mind

- **The login page prints the exact build** (`TeamCity Professional 2023.05.3 (build 129390)`) — compare against 2023.05.4 and 2024.03 to pick your bypass.
- **Token generation is a single unauthenticated POST on vulnerable builds** — user id 1 is the admin; name the token anything and it comes back in XML.
- **Debug features are file-backed** — `rest.debug.processes.enable=true` is just a line in `config/internal.properties` under the data directory, writable through the admin data-dir page, after which `/app/rest/debug/processes?exePath=` executes.
- **Uploaded SSH keys never leave** — even a rotated admin password doesn't clean `<datadir>/config/projects/*/pluginData/ssh_keys`, and the key's trailing comment names its owner.
- **Patch permissions are code execution** — a Project Developer that may "change build source with a custom patch" can submit a git diff as the build; your edited script becomes the agent's job.

```bash
# the whole 2023 bypass in one line (builds before 2023.05.4, no auth required):
curl -X POST http://teamcity.example.com/app/rest/users/id:1/tokens/RPC2
# -> <token name="RPC2" value="..."/>  = admin API access
```

## Where to go next

Discovery and tunneling, both authentication bypasses, the debug-processes RCE chain, patch-based build RCE, agent-terminal abuse, and the key/database loot path are in [[teamcity-bug-bounty]].
