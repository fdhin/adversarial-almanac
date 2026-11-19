---
name: "krbjack"
categories:
  - blackarch-networking
version: "1.2.0"
homepage: "https://github.com/almandin/krbjack"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: custom:THE BEER-WARE LICENSE
meta-checked: "2026-11-09"
github-repo: "almandin/krbjack"
github-stars: 139
github-pushed: "2025-01-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# krbjack

> Man-in-the-middle Active Directory hosts by poisoning ADIDNS records and hijacking Kerberos AP-REQ tickets in transit.

## What it's for

krbjack exploits the `ZONE_UPDATE_UNSECURE` misconfiguration on an Active Directory DNS zone, which lets unauthenticated attackers rewrite DNS records anonymously. It repoints a target host's DNS records at the attacker, then forwards TCP traffic for the declared ports between clients and the real host (SSH-style port forwarding) while inspecting every stream. When a client sends a Kerberos AP-REQ toward the target, krbjack reuses that ticket in real time to authenticate to the legitimate service as the client — currently SMB only — and, if the client was a local administrator, executes a supplied service executable psexec-style. Because poisoning works at the DNS layer it reaches across routers, unlike ARP spoofing; it does not work against hosts that require SMB signing (so domain controllers are usually out). It requires root and can make new connections to the target briefly unreliable (tickets are consumed, not replayed).

## Typical use cases

### Check a domain for the insecure zone update misconfiguration

```
sudo krbjack check --domain corp.example.com --dc-ip 198.51.100.10
```

Outcome: queries the domain controller's DNS zone flags and reports whether `ZONE_UPDATE_UNSECURE` is set — the prerequisite for everything else the tool does. Run this before any attack run.

### Full MitM with Kerberos AP-REQ hijacking and psexec execution

```
sudo krbjack run --domain corp.example.com --dc-ip 198.51.100.10 --target-name fileserver --ports 135,139,445 --executable payload.exe
```

Outcome: poisons the DNS records of `fileserver` to point at this machine, keeps re-poisoning them, and forwards TCP for the listed ports to the real host. When a client's hijacked AP-REQ authenticates as a local admin over SMB, `payload.exe` is uploaded and run as a service on the target; without admin tickets it just keeps intercepting. `--target-ip` can pin the target when DNS returns several addresses, and `--no-poison` sets up the forwarding without touching DNS if records are already poisoned another way.

### DNS-poison-only run to chain with an NTLM relay

```
sudo krbjack run --target-name fileserver --domain corp.example.com --dc-ip 198.51.100.10
```

Outcome: with no `--ports` and no `--executable`, no TCP forwarding or code execution happens — only the DNS records are poisoned and kept poisoned. Point an NTLM relay tool (e.g. ntlmrelayx) at a chosen victim while its traffic is misdirected; note this is a denial of service for the target's name resolution for the duration.

*No corpus evidence — derived from upstream documentation.*
