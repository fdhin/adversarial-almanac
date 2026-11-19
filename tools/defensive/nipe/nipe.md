---
name: "nipe"
categories:
  - blackarch-defensive
version: "302.9e628df"
homepage: "https://github.com/htrgouvea/nipe"
packaged-by: [blackarch]
blackarch-builddate: "2020-06-07"
build-system: perl
license: MIT
meta-checked: "2026-11-09"
github-repo: "htrgouvea/nipe"
github-stars: 2409
github-pushed: "2026-09-13"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 38
gh-activity-checked: "2026-11-09"
---

# nipe

> Perl engine that makes the Tor network the machine's default gateway.

## What it's for

Nipe rewrites the system's iptables/ip6tables rules so that all IPv4 and IPv6 traffic egresses through Tor, leaving only local and loopback addresses untouched; UDP and ICMP, which Tor cannot carry, are blocked rather than leaked. It gives an entire host a Tor exit identity without configuring each application separately. It is host-wide and privileged — not a per-application sandbox (that is what orjail or torsocks are for) — and on `stop` it removes the egress rules it manages without distinguishing pre-existing ones, so snapshot your firewall state first.

## Typical use cases

### Route all host traffic through Tor

Turn the whole machine into a Tor client at the routing layer.

```
sudo nipe start
```

Outcome: Installs the iptables/ip6tables redirection rules and starts the Tor circuit; from then on every non-local TCP connection from this host exits through Tor.

### Verify the routing state

Confirm the tunnel is actually up before relying on it.

```
sudo nipe status
```

Outcome: Reports whether the machine's traffic is currently being routed through the Tor network; if it reports that Nipe is not running, start it before proceeding.

### Rotate the exit circuit

Get a different exit identity without tearing everything down.

```
sudo nipe restart
```

Outcome: The Tor circuit is rebuilt — traffic keeps flowing through Tor but egresses from a different exit node.

### Stop routing and clean up

Return the host to normal, direct egress.

```
sudo nipe stop
```

Outcome: Nipe's services stop and the egress rules are removed, restoring direct routing — including any rules that existed before Nipe started, which the removal does not differentiate.

*No corpus evidence — derived from upstream documentation.*
