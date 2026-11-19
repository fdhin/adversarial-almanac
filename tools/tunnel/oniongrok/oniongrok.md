---
name: "oniongrok"
categories:
  - blackarch-tunnel
version: "v1.1.11.r0.gcbae9e7"
homepage: "https://github.com/cmars/onionpipe"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "cmars/onionpipe"
github-stars: 617
github-pushed: "2026-10-20"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# oniongrok

> Tor onion-service forwarding — "onion addresses for anything": publishes local ports as Tor hidden services and imports remote onion services onto local interfaces.

## What it's for

oniongrok builds socket-forwarding tunnels to and from Tor onion services — `socat(1)` for onions. In the export direction it publishes a local service at an onion address, which needs no public IP, port forwarding or ISP ingress: the endpoint is reachable from anywhere, including clients stuck behind heavily filtered networks. In the import direction it binds a remote onion's port onto a local address. Forwards use a compact expression syntax (`src~dst@nickname`, `~` meaning "forward to"), nicknames pin a persistent onion across runs, and Tor v3 client authorization (x25519 key pairs) can restrict who may connect. Note the upstream project renamed itself from oniongrok to onionpipe; the packaged binary keeps the old name, and its documentation speaks of `onionpipe`. The from-source binary needs a `tor` executable in PATH; upstream also ships static libtor builds and container images.

## Typical use cases

### Publish a local service at a throwaway onion address

```
oniongrok 8000
```

Outcome: starts Tor, allocates a temporary one-time onion service and logs the forwarding (source `127.0.0.1:8000` to the onion); the address lives as long as the process and is not reused.

### Publish on a chosen port with a persistent nickname

```
oniongrok 8000~80@my-app
```

Outcome: localhost:8000 answers on the onion's port 80, and the nickname `my-app` keeps the same onion address across runs — the closest thing to a stable hidden-service hostname without manual Tor configuration.

### Serve several ports under one onion

```
oniongrok 8000~80@minecraft 25565@minecraft
```

Outcome: both forwards share the onion bound to the `minecraft` nickname, so one address carries the web UI and the game port; forwards without a nickname continue to share the session's temporary address.

### Import a remote onion service locally

```
oniongrok xxx.onion:80
```

Outcome: the remote onion's port 80 is bound on localhost:80; use `xxx.onion:80~0.0.0.0:80` to bind on all interfaces instead, turning the local host into a gateway onto the onion for other machines.

### Restrict an export to authorized Tor clients

```
oniongrok client new alice
```

Outcome: prints a JSON object with `alice`'s public identity key; serve with `oniongrok --require-auth <identity> 8000~80@test` so only holders of the matching private key connect — the client side imports with `oniongrok --auth alice <onion>:80~7000`.

*No corpus evidence — derived from upstream documentation.*
