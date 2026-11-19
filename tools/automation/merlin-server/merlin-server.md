---
name: "merlin-server"
categories:
  - blackarch-automation
version: "1.3.0"
homepage: "https://github.com/Ne0nd0g/merlin"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Ne0nd0g/merlin"
github-stars: 5627
github-pushed: "2026-10-15"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 33
gh-activity-checked: "2026-11-09"
---

# merlin-server

> Server half of Merlin, a cross-platform post-exploitation HTTP/2 command-and-control framework written in Go.

## What it's for

Merlin is a post-exploitation C2: agents implant themselves on targets and check in to this server over HTTP/2 (clear-text h2c and HTTPS; later releases add HTTP/3 and SMB/TCP/UDP peer-to-peer links). The server hosts listeners, tracks agent sessions, and queues jobs. In the packaged 1.3.0 release the server binary itself presents the interactive `Merlin»` REPL; newer upstream versions split operator interaction into a separate `merlinCLI` that drives the server over gRPC. It does not build implants — agent binaries come from the merlin-agent releases. Reach for it when you want a lightweight Go C2 with modern TLS transport options in a lab or engagement.

## Typical use cases

### Start the C2 server

Open the interactive server console.

```
merlin-server
```

Outcome: prints the Merlin ASCII banner with version and drops into the `Merlin»` main-menu prompt. Tab completion (double-tab) lists the commands available in the current menu; `help` prints the full table.

### Stand up an HTTPS listener

Create a listener that agents will connect back to.

```
listeners
use https
set Port 8443
start
```

Outcome: `use https` opens the listener template menu (`Merlin[listeners][https]»`), `set Port 8443` changes the default 443, and `start` creates and starts the listener — output like `[+] Started HTTPS listener on 0.0.0.0:8443`, with a warning that the bundled Merlin testing certificate is in use (bring your own x509 keypair via `set X509Key`/`set X509Cert` for realistic engagements). `listeners` → `list` shows every listener with name, interface, port, protocol, and status.

### Review and task agent sessions

See who has checked in and interact with one agent.

```
sessions
interact 99dbe632-984c-4c98-8f38-11535cb5d937
```

Outcome: `sessions` prints a table of established agents (GUID, transport, platform, host, user, process, status, last check-in). `interact <agent-id>` switches the prompt to `Merlin[agent][<id>]»`, from which that agent can be tasked; `back` returns to the main menu. The agent ID comes from the `sessions` output.

### Run a local command without leaving the console

Execute a shell command on the machine hosting the server.

```
! ip a show eth0
```

Outcome: the server runs the command locally and prints its output inline — useful for grabbing your own interface address when configuring listener options, without opening another terminal.

*No corpus evidence — derived from upstream documentation.*
