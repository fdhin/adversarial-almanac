---
name: "ucsniff"
gentoo-category: "net-analyzer"
version: "3.20"
homepage: "http://ucsniff.sourceforge.net"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: autotools
license: GPL-3
meta-checked: "2026-11-09"
github-repo: "Seabreg/ucsniff"
github-stars: 9
github-pushed: "2019-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ucsniff

> VoIP/UC assessment sniffer that combines Voice VLAN hopping, MitM and automatic call recording.

## What it's for

UCSniff is a VoIP/UC security assessment tool that demonstrates the risk of unauthorized call recording in enterprise voice networks: it discovers Voice VLANs by CDP spoofing, hops into them, takes a man-in-the-middle position via ARP poisoning, and automatically reassembles RTP audio into WAV files and H.264 video into AVI files. Around that core it decodes SIP and SCCP signaling, supports realtime audio/video monitoring, targeted eavesdropping of specific Corporate Directory users, learning vs. target modes, and TFTP MitM modification of phone settings. It is a 2012-era C/C++ tool (final release 3.20) for authorized testing only: after any MitM run, quit with `q` so victim phones are re-ARPed, or the segment may suffer an outage.

## Typical use cases

### Passively monitor a SPAN or hub segment

```bash
ucsniff -i eth0 -M
```

Outcome: Monitor Mode passively decodes VoIP signaling and media crossing the interface and records calls — the safest mode; it needs both directions of traffic (SPAN/mirror port) to reconstruct conversations.

### Discover and hop into a Voice VLAN (Cisco CDP)

```bash
ucsniff -i eth0 -c 1 // //
```

Outcome: spoofs a single CDP message to learn the Voice VLAN ID, hops onto it (capture continues on the VLAN interface) and runs MitM Learning Mode, mapping observed users and extensions into `targets.txt`.

### Eavesdrop a specific phone found in the Corporate Directory

```bash
ucsniff -i eth0 -c 1 -a -m 00:11:22:33:44:55 -T
```

Outcome: MitM Target Mode ARP-poisons only the phone with the given MAC, pulls the Cisco Corporate Directory entries and restricts capture and recording to the targeted users; when the Voice VLAN ID is already known, `-v 200 -T` skips CDP and hops directly.

### Launch the GUI

```bash
ucsniff -G
```

Outcome: opens the graphical interface exposing the same monitor and MitM features with checkboxes such as audio/video mixing control; the console options still apply.

*No corpus evidence — derived from upstream documentation (UCSniff README, USAGE and EXAMPLES files from the Seabreg source mirror of the original ucsniff.sourceforge.net release).*
