---
name: "bob-the-butcher"
categories:
  - blackarch-cracker
version: "0.8.1"
homepage: "https://btb.banquise.net/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-03"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/bob-the-butcher"
github-stars: 0
github-pushed: "2025-12-14"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# bob-the-butcher

> Distributed password cracking: a dispatcher server, worker clients, and an admin console coordinating jobs over the network.

## What it's for

Bob the Butcher splits password cracking across many machines. `bob_server` is the dispatcher that holds jobs and state; `bob_client` nodes connect to it, fetch hash sets, crack them with the built-in formats (DES, BSDi, MD5, raw-MD5, raw-SHA1, NT, MySQL, Oracle, MSCash, nsLDAP, Lotus5, Eggdrop) or by driving John the Ripper (`-w` wordlist, `-j` path to john), and report results back; `bob_admin` submits and inspects jobs. Traffic between components is protected with a pre-shared key (`-k`) that must match on every side. On BlackArch the binaries are installed with a `-sse2` suffix: bob_server-sse2, bob_client-sse2, bob_admin-sse2.

## Typical use cases

### Start the dispatcher

```
bob_server -p 4025 -k 'shared-psk' -f jobs.state -D
```

Outcome: daemonizes (`-D`), listens on port 4025, persists job and client state to jobs.state, and waits for admin commands and worker connections.

### Attach a worker to the dispatcher

```
bob_client -k 'shared-psk' server.example.com 4025
```

Outcome: connects to the dispatcher, fetches pending hash work, cracks it, and streams results back; add `-p 10` to lower the worker's CPU priority or `-b` to only benchmark the available formats.

### Query the server from the admin console

```
bob_admin -k 'shared-psk' status
```

Outcome: connects to the dispatcher and prints its status; other commands include newjob, deljob, setopt, getopt, jobinf, client and cltinf.

*No corpus evidence — derived from upstream documentation.*
