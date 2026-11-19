---
name: "xerosploit"
categories:
  - blackarch-networking
version: "38.e2c3c7b"
homepage: "https://github.com/LionSec/xerosploit"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "LionSec/xerosploit"
github-stars: 2203
github-pushed: "2023-03-23"
github-archived: false
github-issues: 287
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xerosploit

> Menu-driven man-in-the-middle testing framework for LANs, automating bettercap and nmap behind an interactive console.

## What it's for

Xerosploit is a penetration-testing toolkit for performing man-in-the-middle attacks against hosts on a LAN. It wraps bettercap (traffic manipulation) and nmap (discovery/scanning) in a console workflow: sweep the subnet, pick a target, load a module, run. Modules cover port scanning, sniffing, injecting HTML or JavaScript into pages the target browses, intercepting and replacing downloads, replacing images, defacing pages, DNS spoofing, background audio playback (with driftnet-style image sniffing), and denial of service. It is a demonstration/testing tool for audit labs — not a stealth platform — and its Python 2.7 codebase is unmaintained upstream. Its supporting files live under /opt/xerosploit.

## Typical use cases

### Start the console and map the network

Enumerate live hosts before choosing a victim.

```
sudo xerosploit
```

Outcome: Opens the interactive Xero console (root is required for packet manipulation). Inside it, `scan` runs an nmap sweep of the subnet and lists discovered hosts with IP and MAC addresses; selecting one (for example 198.51.100.10) switches the console to module-selection mode for that target.

### Port-scan the selected target

Enumerate open services on the host chosen in the console.

```
pscan
run
```

Outcome: After a target is selected, `pscan` loads the port-scanner module and `run` fires nmap against that single host, printing open ports and service guesses in the console.

### Inject content into the target's web traffic

Modify pages the victim browses while the MITM position is active.

```
injecthtml
run
```

Outcome: Prompts for an HTML snippet and injects it into every web page the target requests while the man-in-the-middle session is running; the sibling modules work the same way — `injectjs` for JavaScript, `rdownload` to intercept and replace downloaded files, `replace` for image substitution, `deface` for full-page replacement, `dspoof` for DNS spoofing, and `yplay`/`driftnet` for audio and image capture.

### Capture credentials and traffic from the target

Passively watch what the victim sends.

```
sniff
run
```

Outcome: Starts bettercap's sniffer against the selected target and prints captured traffic — URLs, credentials, and cookies — as it flows through the attack host.

*No corpus evidence — derived from upstream documentation.*
