---
name: "xprobe2"
categories:
  - blackarch-fingerprint
version: "0.3"
homepage: "https://sourceforge.net/apps/mediawiki/xprobe/index.php?title=Main_Page"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-09-12"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "binarytrails/xprobe2"
github-stars: 30
github-pushed: "2021-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xprobe2

> The classic remote active OS fingerprinter from the Sys-Security Group (Ofir Arkin, Fyodor Yarochkin): sends ICMP/TCP/UDP probes and matches responses against its signature database with fuzzy logic to name the target operating system.

## What it's for

Remotely determining a host's operating system by actively probing it — the early-2000s successor to the original Xprobe, rewritten in C++ with a modular test engine and fuzzy-logic signature matching, where each module contributes signatures and the engine scores how well the collected responses fit each OS in `xprobe2.conf`. Its strength is fingerprinting with very little prior knowledge: it can bootstrap from a single closed or open port, optionally run its own quick TCP/UDP portscan to find one, and accept known port states you supply. It needs raw-socket privileges (run as root) and one packet exchange with the target. Expect an aging signature database — current-era systems will match poorly or not at all — so treat it as a tool for legacy networks and gear (printers, old routers, embedded hosts) and for generating signatures from known systems. It does not port-scan broadly or detect services; nmap's OS engine is the modern substitute for general use.

## Typical use cases

### Fingerprint a target's operating system

Take a single host and name its OS.

```
xprobe2 198.51.100.10
```

Outcome: runs the default probe modules, prints each module's intermediate results, and finishes with a ranked list of operating system matches and confidence — the final line names the most likely OS.

### Feed known port state for accuracy

Tell xprobe2 about a port you already know to sharpen the match.

```
xprobe2 -p tcp:22:open 198.51.100.10
```

Outcome: uses the declared open TCP port 22 to select and calibrate probe modules (UDP works the same way, e.g. `udp:53:closed`), typically producing fewer, higher-confidence OS matches.

### Let it find a port first

Enable xprobe2's own TCP portscan when no port state is known.

```
xprobe2 -T21-23,80 198.51.100.10
```

Outcome: quick-scans the listed TCP ports, uses whichever are found open/closed to drive the fingerprinting modules; `-U` does the same for UDP port specs, and adding `-A` analyses the gathered packets for suspicious middleboxes such as transparent proxies or IDS resets.

### Generate a signature for the database

Produce a raw fingerprint from a host whose OS you know.

```
xprobe2 -F -o signature.txt 198.51.100.10
```

Outcome: instead of matching, emits the target's signature in database format to `signature.txt` (module layout per test), suitable for contributing new OS entries; `-X` writes scan results as XML to the `-o` logfile, and `-m 3` limits printed matches to the top three.

*No corpus evidence — derived from upstream documentation.*
