---
name: "secure-socket-funneling-windows-binaries"
categories:
  - windows-resources
version: "3.0.0-0kali4"
homepage: "https://github.com/securesocketfunneling/ssf"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: windows
meta-checked: "2026-11-09"
github-repo: "securesocketfunneling/ssf"
github-stars: 1673
github-pushed: "2021-05-24"
github-archived: false
github-issues: 72
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# secure-socket-funneling-windows-binaries

> Windows builds of SSF: funnel many TCP/UDP sockets through one TLS tunnel between a Windows host and an SSF server.

## What it's for

This Kali package ships the Windows standalone binaries of Secure Socket
Funneling (SSF), a cross-platform network toolkit that multiplexes
multiple socket forwards through a single TLS-protected connection to a
remote computer. On the Windows side you run `ssf.exe` (the client)
against an `ssfd` server anywhere on the network; both directions are
supported — local (`-L`, `-U`) and remote (`-R`, `-V`) TCP/UDP port
forwarding, local (`-F`) and remote (`-D`) SOCKS proxies, and even
shell relaying (`-X` gives you a shell on the server, `-Y` offers one
to it) plus file copy. All traffic rides one strong-TLS tunnel described
by a `config.json` (which can also pin CA and client certificates), so
it is used both as a sysadmin's swiss-army tunnel and as a red-team
egress/pivot channel. The upstream project has been quiet since 2021;
these notes cover the last released 3.0.0 binaries.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start the server end of the tunnel

Run the funnel endpoint the Windows client connects to.

```
ssfd -p 8011 -c config.json
```

Outcome: listens on port 8011 (the default) for SSF clients, applying
the TLS settings from `config.json`; on the Windows side the same config
file governs `ssf.exe`.

### Forward a local Windows port through the tunnel

Reach a service from the Windows host via the server.

```
ssf.exe -L 3389:198.51.100.20:3389 198.51.100.10
```

Outcome: connects to the SSF server at 198.51.100.10 and opens local
port 3389; traffic to `127.0.0.1:3389` is tunneled and exits from the
server to 198.51.100.20:3389 — point any client at localhost to use it.

### Get a SOCKS proxy that egresses from the server

Pivot through the tunnel with any SOCKS-aware tool.

```
ssf.exe -D 1080 198.51.100.10
```

Outcome: runs a SOCKS server on the Windows side reachable at port 1080
whose connections egress from the SSF server; `-F 1080` inverts this —
a SOCKS proxy on the server that the local side can use.

### Open a remote shell over the tunnel

Drop to a shell on the far end.

```
ssf.exe -X 4444 198.51.100.10
```

Outcome: forwards the SSF server's shell I/O to local port 4444 — every
connection to that port spawns/feeds a shell process on the server;
`-Y` instead offers the local machine's shell to the server side.
