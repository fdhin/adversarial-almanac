---
name: "mac-robber"
categories:
  - blackarch-forensic
version: "1.02"
homepage: "http://www.sleuthkit.org/mac-robber/download.php"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "daqiang430/mac-robber"
github-stars: 3
github-pushed: "2015-04-03"
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

# mac-robber

> Collects modified/access/change (MAC) timestamps from allocated files into a body file for timeline analysis, by Brian Carrier.

## What it's for

mac-robber is a forensic and incident-response utility that recursively reads the modified, access, and change times of every allocated file and directory under a mount point and prints them in "body" (time machine) format on stdout — the same format the mactime tool from The Sleuth Kit and TCT reads. It was written as a Perl-free equivalent of TCT grave-robber's `-m` mode, in plain statically-linkable C, so it can be carried on removable media and run on a live compromised host. It deliberately covers only allocated files: deleted entries, unallocated space, and rootkit-hidden files require The Sleuth Kit's `fls`/`ils` on a disk image. The tool is legacy (last release 1.02); modern TSK `fls -m` produces the same body format directly from images.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Capture a body file from a mounted filesystem

```
mac-robber /mnt/analysis > bodyfile
```

Outcome: writes one body-format line per allocated file under /mnt/analysis to bodyfile, ready for timeline creation with `mactime -b bodyfile -d > timeline.csv`.

### Collect times from a live host to an analysis box

Run from trusted media and stream the output off the compromised host, as the README suggests.

```
mac-robber / | nc 198.51.100.10 9001
```

Outcome: streams the body-format output for the entire live root filesystem to a listener on the analysis machine (e.g. `nc -l -p 9001 > bodyfile`), avoiding writes to the suspect host's disks.

### Check the binary before use

```
mac-robber -V
```

Outcome: prints the version, author, and URL — a quick reference check when running from trusted media on a suspect host.
