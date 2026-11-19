---
name: "autopwn"
categories:
  - blackarch-automation
version: "190.fc80cef"
homepage: "https://github.com/nccgroup/autopwn"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: AGPL
meta-checked: "2026-11-09"
github-repo: "nccgroup/autopwn"
github-stars: 387
github-pushed: "2019-04-23"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# autopwn

> Client/server tool from NCC Group: a local REST server where you register command/tool sets and an interactive CLI that fires them at targets, so repeated per-engagement commands become reusable definitions.

## What it's for

autopwn v2 lets a pentester describe the tools and command sets they want to run against targets once, and then invoke them consistently instead of retyping shell commands or maintaining per-engagement scripts. It ships as two entry points: `autopwn`, a Flask REST server (settings, tools, and jobs API namespaces with Swagger documentation, state in `~/.autopwn/autopwn.db`), and `autopwn-cli`, an interactive client shell for that API. Run it only on a loopback address: anyone who can reach the server can execute commands as the server's user, and the project explicitly warns against exposing it on a public IP. The v1 Docker image and Java GUI workflow are gone, and development stopped in 2019, so treat it as legacy infrastructure for a personal lab workflow.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start the tool server

Bring up the API that holds tool and job definitions.

```
autopwn
```

Outcome: on first run it creates `~/.autopwn/autopwn.db` and initializes it, then logs `Starting AutoPwn server at http://127.0.0.1:5000/` and serves the REST API (ping, settings, tools, jobs endpoints — the API layout is browsable via the Swagger UI at the root).

### Connect the interactive client

Manage tools and launch jobs from a shell.

```
autopwn-cli
```

Outcome: the client first checks the server (a request to `/ping`); if unreachable it prints `Server not running, exiting`, otherwise it opens the `autopwn` interactive shell where the `tools`, `jobs`, `settings`, `ping`, and `migrate` command groups manage what the server runs.

### Point the client at a specific server

Use a non-default server address when client and server run on different machines.

```
autopwn-cli -u http://198.51.100.10:5000
```

Outcome: same interactive shell as above, but all API calls go to the server at `198.51.100.10:5000` instead of the default `http://localhost:5000`; add `-d` before the subcommand context for debug output of the requests.
