---
name: "grimwepa"
gentoo-category: "net-wireless"
version: "1.10_p5-r100"
homepage: "http://code.google.com/p/grimwepa/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-3
meta-checked: "2026-11-09"
github-repo: "nagyistoce/grimwepa"
github-stars: 0
github-pushed: "2015-03-15"
github-archived: false
github-issues: 10
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# grimwepa

> Java GUI from the BackTrack 4 era that automates aircrack-ng WEP cracking and WPA handshake capture plus dictionary attacks.

## What it's for

Grim WEPa (by derv82, 2010) wraps the aircrack-ng suite in a point-and-click Swing interface: it puts a card in monitor mode, scans for targets, then drives the standard attacks — WEP key recovery via the usual aircrack-ng methods and WPA handshake capture followed by a dictionary (or generated wordlist) attack. It automates tools that already work fine on the command line, which made it popular with beginners on BackTrack; everything it does can be done directly with aircrack-ng, airmon-ng and airodump-ng. It predates modern WPA2/WPA3 hardened networks, has no PMKID or enterprise attack support, and its only command-line option is verbose mode — target and attack selection happens in the GUI. Requires Java and a monitor-mode-capable wireless card.

## Typical use cases

### Launch the GUI

```
java -jar grimwepa.jar
```

Outcome: opens the Grim WEPa window (the original 1.10 release shipped as `grimwepa1.10a6.jar`); from there you pick the wireless interface, scan for networks, select a target and start the WEP or WPA function — the tool runs airodump-ng/aircrack-ng under the hood.

### Launch with command echo

```
java -jar grimwepa.jar -v
```

Outcome: same GUI, but every aircrack-ng suite command the tool executes is printed to stdout as it runs, which lets you follow what the automation is actually doing and reproduce it by hand later.

*No corpus evidence — derived from upstream documentation (launch form and options verified against the packaged source's README).*
