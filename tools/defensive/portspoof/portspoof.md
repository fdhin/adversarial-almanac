---
name: "portspoof"
categories:
  - blackarch-defensive
version: "152.89d5ecc"
homepage: "https://drk1wi.github.io/portspoof/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-28"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "drk1wi/Portspoof"
github-stars: 2427
github-pushed: "2026-08-26"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# portspoof

> Service-signature emulation daemon that makes every TCP port look open and inhabited.

## What it's for

Portspoof runs in userland on a single local TCP port while iptables REDIRECT rules send it all other incoming TCP traffic; it answers on behalf of every one of the 65535 ports with SYN+ACK and feeds port scanners thousands of realistic, dynamically generated service banners from a 9000+ signature regex database. Version 2.0's tarpit engine holds connections open with randomized, per-port-variable timing, so scanners cannot separate real services from fake ones by closure behavior, timing, or banner analysis — a full `nmap -sV -p-` sweep stretches from seconds to 10+ hours. It can also fuzz the scanning tool itself with payload responses. Real services keep working via iptables RETURN rules carved out before the redirect; the point is to bury the true attack surface, not to hide that ports exist.

## Typical use cases

### Deploy full deception behind a firewall redirect

Make the whole port range answer while real services stay reachable.

```
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 22 -j RETURN
iptables -t nat -A PREROUTING -i eth0 -p tcp -j REDIRECT --to-ports 4444
portspoof -c /etc/portspoof/portspoof.conf -s /etc/portspoof/portspoof_signatures -D
```

Outcome: The daemon detaches (-D) and answers for every TCP port except the ones you RETURN (here SSH): an `nmap -p-` scan now reports all ports open and `nmap -sV` shows a different convincing fake service on each.

### Tune the tarpit hold times

Stretch scanner sweeps further with bounded resource cost.

```
portspoof -s /etc/portspoof/portspoof_signatures -t 10 -T 60 -D
```

Outcome: Same all-port deception, but each connection is held open between 10 and 60 seconds, so version-detection sweeps stall while the defender-side cost stays a few KB of kernel memory per idle connection.

### Open-port-only mode

Answer everything with SYN+ACK and no banners at all.

```
portspoof -D
```

Outcome: Every probed port appears open with no service fingerprint returned and connections are still tarpitted — the lightest configuration, useful when you only want to waste scanner time.

### Fuzz the scanner with random payloads

Turn the defense into an offense against the scanning tool itself.

```
portspoof -1 -v
```

Outcome: Probes are answered with randomly generated payloads of random size (with verbose logging to stdout), targeting parser weaknesses in the attacker's scanner rather than bluffing inventory.

### Fuzz with a controlled payload list

Send chosen payloads instead of random ones.

```
portspoof -f payloads.txt -v
```

Outcome: Probe responses are drawn from payloads.txt, letting you aim at specific scanner-side parsing behaviors.

*No corpus evidence — derived from upstream documentation.*
