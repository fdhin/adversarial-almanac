---
name: "sticky-keys-hunter"
categories:
  - blackarch-scanner
version: "15.c816fc9"
homepage: "https://github.com/ztgrace/sticky_keys_hunter"
packaged-by: [blackarch]
blackarch-builddate: "2017-04-23"
build-system: scripts
license: GPL3
aliases: ["stickykeyshunter-git"]
meta-checked: "2026-11-09"
github-repo: "ztgrace/sticky_keys_hunter"
github-stars: 264
github-pushed: "2016-09-12"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sticky-keys-hunter

> Black-box detector for sticky-keys and utilman backdoors on RDP hosts.

## What it's for

A proof-of-concept bash script that checks whether a Windows host's
accessibility binaries have been swapped for backdoors — the classic
sticky-keys (sethc.exe) and utilman.exe tricks — without needing credentials.
It opens an RDP session with rdesktop, sends the Shift-five-times and Win+U
trigger sequences through xdotool, screenshots whatever window the host pops
up, and disconnects: a clean host shows the accessibility dialog, a backdoored
one shows a shell. It requires a Linux box running an X server with rdesktop,
xdotool, imagemagick and bc installed, and the screen must stay unlocked
while it runs or the screenshots come back black. The author ships it as a
POC and points to Sticky Keys Slayer as the improved successor. It only
evidences existing backdoors; it does not plant them.

## Typical use cases

### Test a single RDP host

Check one host for a replaced sethc/utilman binary (upstream script name
`stickyKeysHunter.sh`, installed as `sticky-keys-hunter`).

```
stickyKeysHunter.sh 198.51.100.10
```

Outcome: an RDP session opens, both trigger sequences are sent, and a
screenshot of the resulting window is saved locally — a command prompt in the
shot indicates a backdoor, the normal accessibility dialog indicates a clean
host.

### Sweep a list of RDP hosts

Batch-check a whole range during infrastructure review.

```
for i in $(cat rdp-hosts.txt); do ./stickyKeysHunter.sh "$i"; done
```

Outcome: one screenshot per host listed in rdp-hosts.txt, letting you review
an estate's RDP endpoints for sticky-keys and utilman backdoors in one pass.

*No corpus evidence — derived from upstream documentation
(ztgrace/sticky_keys_hunter README).*
