---
name: "wifite"
categories:
  - 802-11
version: "2:2.8.2-3"
homepage: "https://github.com/kimocoder/wifite2"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2026-10-11"
build-system: python
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "kimocoder/wifite2"
github-stars: 1743
github-pushed: "2026-11-06"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 307
gh-activity-checked: "2026-11-09"
---

# wifite

> One-command wireless auditor: scans, attacks and cracks WEP, WPS, WPA/WPA2 and WPA3 networks by orchestrating aircrack-ng, reaver, hcxdumptool and hashcat.

## What it's for

Wifite automates the whole audit workflow against nearby access points: it puts the wireless card into monitor mode, lists targets with their encryption, and — once you pick targets — runs the appropriate attack for each: WPS Pixie-Dust and online PIN brute-force, WPA/WPA2 handshake capture, clientless PMKID harvesting, WPA3-SAE capture and transition-mode downgrade, the classic WEP attacks, and an evil-twin mode with a captive portal. It is a driver, not a cracker itself: it coordinates aircrack-ng, reaver/bully, hcxdumptool and hashcat and verifies captures with tshark/cowpatty. This is the maintained continuation of derv82's wifite2 (the derv82 repo itself points here as "the most-supported version"); derv82's current generation, a USB-only cross-platform rewrite, is [[wifit3]]. Wifite needs root, a monitor-mode/injection-capable adapter, and current versions of the underlying tools (`wifite --syscheck` reports what is missing).

## Typical use cases

### Check tool and adapter readiness

Verify dependencies and attack readiness before an audit.

```
sudo wifite --syscheck
```

Outcome: prints a report of root/RF-kill state, conflicting processes, each required and optional tool, detected wireless interfaces with their monitor-mode/injection capability, and which attacks (WPS, PMKID, WPA3, ...) are possible with the current setup — fix what it flags before scanning.

### Run a full automated audit

Scan and attack every nearby network with the method matching its encryption.

```
sudo wifite
```

Outcome: enables monitor mode, channel-hops across 2.4 GHz (add `-5` for 5 GHz), and shows a target table; select targets and it captures handshakes/PMKIDs, attempts WPS Pixie-Dust, and cracks with available wordlists — captured handshakes, PMKIDs and cracked passwords are saved automatically, and `sudo wifite --cracked` lists past results.

### Attack one access point

Skip the scan and target a single BSSID.

```
sudo wifite -b 00:11:22:33:44:55
```

Outcome: locks onto the given access point and runs the attacks valid for its encryption without touching other networks; use `-c 6` to pin the channel (also needed to decloak hidden SSIDs).

### WPS-only Pixie-Dust run

Restrict the attack to the offline WPS Pixie-Dust method.

```
sudo wifite --wps-only --pixie
```

Outcome: only networks with WPS enabled are attacked, using the offline Pixie-Dust attack (reaver/pixiewps) — vulnerable routers yield the WPS PIN and PSK in seconds without any client traffic; `--no-pixie` flips to the online PIN brute-force instead.

### Use two adapters in parallel

Speed up captures by dedicating one adapter to capture and one to deauthentication.

```
sudo wifite --dual-interface
```

Outcome: wifite detects both adapters, assigns primary/secondary roles by capability, and keeps capturing continuously while deauthenticating (no mode switching, reported 30-50% faster); falls back to a single interface automatically.

### Monitor the air for ongoing attacks

Passively watch for deauthentication/disassociation attacks against nearby networks.

```
sudo wifite --monitor-attacks --monitor-duration 3600 --monitor-log security_audit.log
```

Outcome: runs a passive TUI dashboard for one hour that flags deauth/disassociation events with attacker and victim MACs, keeps per-network and per-attacker statistics, and writes every event with ISO-8601 timestamps to security_audit.log — no packets are injected.

*No corpus evidence — derived from upstream documentation (flags verified against the kimocoder/wifite2 README and its bundled usage/help).*
