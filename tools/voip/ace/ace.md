---
name: "ace"
categories:
  - blackarch-voip
version: "1.11"
homepage: "https://ucsniff.sourceforge.net/ace.html"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-12-02"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/ace"
github-stars: 0
github-pushed: "2025-12-02"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# ace

> Impersonates a Cisco IP phone end-to-end — CDP/VLAN hop, DHCP, TFTP, HTTP — to pull the VoIP corporate directory (names and extensions) into a text file.

## What it's for

ACE (Automated Corporate Enumerator) harvests the "corporate directory" that VoIP hardphones can display, turning a name/extension listing into input for further VoIP assessment. Rather than watching RTP streams or scanning IP addresses, it targets users by name: it spoofs or sniffs CDP to learn the voice VLAN, hops onto it, sends a DHCP request, decodes the TFTP server from DHCP option 150, downloads the phone's configuration file over TFTP, extracts the corporate-directory URL from it, and fetches the XML directory over HTTP. Currently only Cisco Unified IP Phone directories are supported. The output is a formatted text file; the same functions are integrated into UCSniff. It needs root (raw packet access) and the MAC address of a real phone (supplied with `-m`). The packaged edition is a modernized build that compiles on current gcc/libtirpc Linux.

## Typical use cases

### Dump a phone's directory with the TFTP server auto-discovered

```bash
sudo ace -i eth0 -m 00:1E:F7:28:9C:8E
```

Outcome: ACE discovers the voice VLAN via CDP, DHCPs inside it, learns the TFTP server address from option 150, fetches the phone's configuration file, then the directory XML, and writes the parsed name/extension listing to a text file in the working directory.

### Dump the directory pointing at a known TFTP server

```bash
sudo ace -i eth0 -t 198.51.100.10 -m 00:1E:F7:28:9C:8E
```

Outcome: same directory dump, skipping the DHCP discovery step — use this when you already know where the phone firmware/configs are served from.

### Pin the voice VLAN ID instead of discovering it

```bash
sudo ace -i eth0 -v 96 -m 00:1E:F7:28:9C:8E -d
```

Outcome: ACE creates a voice-VLAN subinterface itself and proceeds against the phone; `-d` adds verbose/debug output showing each stage (CDP handling, DHCP, TFTP fetch, HTTP fetch).

### Learn the voice VLAN by spoofing CDP

```bash
sudo ace -i eth0 -c 1 -m 00:1E:F7:28:9C:8E
```

Outcome: instead of passively waiting for CDP announcements (`-c 0`), ACE sends spoofed CDP frames to extract the voice VLAN ID (VVID) from the switch, then continues the directory download through the voice VLAN interface.

### Clean up the VLAN interface it created

```bash
sudo ace -r eth0.96
```

Outcome: removes the `eth0.96` voice-VLAN interface left behind after a run, restoring the adapter to its pre-hop state.

*No corpus evidence — derived from upstream documentation.*
