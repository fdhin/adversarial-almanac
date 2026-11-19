---
name: "mimipenguin"
categories:
  - blackarch-forensic
version: "153.ecb2ffc"
homepage: "https://github.com/huntergregal/mimipenguin"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "huntergregal/mimipenguin"
github-stars: 4183
github-pushed: "2025-09-05"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mimipenguin

> Dumps the cleartext login password of the current Linux desktop user from process memory.

## What it's for

A Linux post-exploitation credential harvester inspired by mimikatz. It reads the memory of session processes that hold cleartext passwords — GNOME Keyring, GDM and LightDM for desktop logins, plus VSFTPd (active FTP sessions), Apache2 (active HTTP Basic Auth sessions) and OpenSSH sessions with sudo usage — and prints candidate words ranked by probability, validating them against `/etc/shadow` hashes and hashes found in memory. It needs root and an active session that still holds the password in memory; it does not attack password stores at rest. The weakness it exploits is tracked as CVE-2018-20781 and was still present in the GNOME Keyring 3.28 era.

## Typical use cases

### Harvest the current desktop session's login password

Dump the password held in memory by the display manager or keyring of the current user.

```
sudo mimipenguin
```

Outcome: scans target process memory, then prints each candidate cleartext password with a probability score; on an affected system this typically recovers the active user's login password. Run as root.

### Run the Python port for the widest credential coverage

Use the Python implementation when the C binary misses a source, since it carries the fullest set of credential "needles".

```
sudo python3 /usr/share/mimipenguin/mimipenguin.py
```

Outcome: the Python version iterates the GNOME Keyring, GDM, LightDM, VSFTPd, Apache2 and OpenSSH needles and prints recovered password candidates for the current session.

*No corpus evidence — derived from upstream documentation.*
