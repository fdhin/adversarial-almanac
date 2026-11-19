---
name: "rupture"
categories:
  - blackarch-crypto
version: "1383.131c61a"
homepage: "https://github.com/decrypto-org/rupture"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "decrypto-org/rupture"
github-stars: 238
github-pushed: "2020-02-11"
github-archived: false
github-issues: 52
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rupture

> Man-in-the-middle framework for BREACH and other compression side-channel attacks that recover secrets from encrypted-but-compressed HTTP responses.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Rupture automates compression-oracle attacks such as BREACH, where an HTTPS response is compressed before encryption and an attacker who can observe lengths and influence part of the request can guess the hidden secret byte by byte. Rather than one monolithic binary, it ships as a research framework: a Django-based command-and-control backend, a Node.js realtime server that talks to the injected JavaScript client, a network sniffer that watches the victim's traffic, and an injection component that pushes the client into the victim's HTTP responses. The operator drives a victim against a target described in YAML config files, and the framework orchestrates guessing rounds and records the recovered secret. It is research code from the University of Athens last touched in 2020 and built around Python 2 — expect dependency friction on a modern host, and note that a sniffer component needs root because it captures from a network interface. It does not attack the cryptography itself; it exploits information leaked by compression, so the target must reflect attacker input into a compressed response that also contains the secret.

## Typical use cases

### Initialize the framework against a target and victim

```
rupture setup
```

Outcome: after you describe the target in `rupture/backend/target_config.yml` and the victim in `rupture/backend/victim_config.yml`, `setup` prepares the backend (Django database and configuration) and reports configuration errors before any module is started.

### Launch the full attack stack at once

```
sudo rupture attack
```

Outcome: starts the backend, realtime server and sniffer together in one go — the same as running each module separately. Run it with sudo (or as root) because the sniffer needs direct access to a network interface; each module logs to the console as victims are enrolled and driven through guessing rounds.

### Run individual modules

```
rupture backend
rupture realtime
sudo rupture sniffer
```

Outcome: each command starts one component in its own terminal — the C2 backend, the Node.js realtime bridge, or the packet sniffer. Useful while configuring, since you can watch one module's logs without the noise of the others.

### Deliver the client to the victim's browser

```
/usr/share/rupture/client/client_1/inject.sh
```

Outcome: hooks outgoing HTTP responses so the client JavaScript for victim id 1 is served into the victim's browser (a local `test.html` page is also generated for lab use). With backend, realtime server and sniffer running, the client then acts as the in-browser oracle that leaks the secret through response sizes.
