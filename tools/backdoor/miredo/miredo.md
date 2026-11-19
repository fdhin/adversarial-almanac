---
name: "miredo"
categories:
  - post-exploitation
version: "1.2.6-7.2"
homepage: "http://www.remlab.net/miredo/"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur, nixpkgs]
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "darconeous/miredo"
github-stars: 17
github-pushed: "2011-04-20"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# miredo

> Open-source Teredo client, relay and server (RFC 4380): tunnels IPv6 over plain UDP through NATs, giving IPv6 reachability to hosts with no native or proto-41 path.

## What it's for

Miredo is a legitimate Unix daemon implementing the Teredo tunneling standard. As a client it gives a NATed host a public IPv6 address and IPv6 Internet access via a Teredo server, using ordinary UDP — the fallback for networks where IPv6 is unavailable and 6to4/proto-41 forwarding is blocked; it needs the kernel TUN/TAP driver and IPv6, starts as root, then drops privileges. The package also ships `miredo-server` for running your own Teredo server, and relay mode. Its security relevance is dual-use: UDP-encapsulated IPv6 tunnels like Teredo can punch through NAT and firewall policy and hide traffic from IPv4-blind monitoring, so defenders and testers use miredo to generate real Teredo flows, write detections for IPv6-over-UDP tunneling, and verify that egress controls actually block it. It is not an attack tool itself — it moves packets, nothing more.

## Typical use cases

### Bring up a Teredo client and get IPv6 connectivity

```
sudo miredo
```

Outcome: the daemon detaches into the background; after roughly 20 seconds (longer behind restricted NATs) a `teredo` interface appears with a public `2001:0:.../32` IPv6 address. Confirm with `ping6 -c 4 example.com` or `ifconfig teredo` — replies mean the tunnel works end to end. By default it uses the project's public test server `teredo.remlab.net`, set in `miredo.conf`.

### Debug the tunnel in the foreground

```
sudo miredo --foreground
```

Outcome: miredo stays attached to the terminal and reports initialization progress and errors directly instead of only to syslog — the fastest way to see why a tunnel is not coming up (blocked UDP egress, NAT type, unreachable server). Give it ~20 seconds before judging failure.

### Run the daemon under a dedicated account

```
sudo miredo -u miredo
```

Outcome: after the privileged tunnel-interface setup, the daemon drops to the named unprivileged user instead of the default `nobody`, limiting the impact of a daemon compromise; a small privileged helper retains only what interface management needs. (A `-t` option additionally jails the daemon in a chroot, mainly useful for dedicated relay/server deployments.)

*No corpus evidence — derived from upstream documentation.*
