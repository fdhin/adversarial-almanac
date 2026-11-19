---
name: "dscan"
categories:
  - scanner
version: "0.1.5-0parrot1"
homepage: "https://github.com/0x4E0x650x6F/dscan"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "0x4E0x650x6F/dscan"
github-stars: 21
github-pushed: "2026-10-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# dscan

> Client/server wrapper that splits large nmap jobs across remote agents, with certificate-authenticated task handout and hash-verified result collection.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Distributing reconnaissance when a single host cannot cover the IP space in time. DScan separates orchestration from execution: a central server normalizes and subdivides targets, walks them through multistage scan definitions (common-service ports first, wider ranges later), and hands task slices to agents; each agent runs its nmap workload locally and returns results, which the server accepts only after hash verification. Task state is persistent, so interrupted scans can be recovered instead of restarted, and a live status view shows stages, agents and completion percentages. It schedules nmap — it assumes nmap and network reachability from the agents to the targets; it does not replace the scanner or add probe types of its own.

## Typical use cases

### Bootstrap a project with certificates and configs

```
dscan --name project1 config -email user@example.com -cn dscan -c pt -l earth -st nrw -o dscan -ou it -days 365
```

Outcome: generates a self-signed certificate and key pair plus default `dscan.conf` and `agent.conf` files inside the `project1` directory; these are the credentials and settings the server and the agents load at startup.

### Start the central scan server

```
dscan --name project1 srv --config dscan.conf targets.txt
```

Outcome: loads the config and the target list (IPs/networks, one per line), begins stage processing, and prints a status table of stages, pending tasks and connected agents; agents can attach from this point on.

### Attach an agent to the server

```
dscan --name agent1 agent --config agent.conf -s 198.51.100.10 -p 8443
```

Outcome: connects to the server at the given address and port, downloads assigned nmap task slices, executes them locally, uploads the results and prints a per-target progress table while it works.
