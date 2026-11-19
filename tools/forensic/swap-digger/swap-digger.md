---
name: "swap-digger"
categories:
  - blackarch-forensic
version: "51.4d18ce0"
homepage: "https://github.com/sevagas/swap_digger"
packaged-by: [blackarch]
blackarch-builddate: "2021-07-04"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "sevagas/swap_digger"
github-stars: 534
github-pushed: "2021-06-26"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# swap-digger

> Bash script that automates Linux swap analysis to recover credentials and other secrets.

## What it's for

swap_digger (Emeric "Sio" Nasi) automates the analysis of Linux swap space during post-exploitation or forensics. It extracts the swap content and mines it for cleartext Linux user passwords, web form credentials and email addresses, HTTP basic authentication strings, WiFi SSIDs and keys, and most-accessed URLs. It can run against the live system's active swap, or forensically against a mounted image or swap dump via `-r`/`-s`. It only recovers data that was actually paged out to swap, and the optional guessing mode `-g` is explicitly documented as unreliable (many false positives). BlackArch installs the upstream `swap_digger.sh` script as `swap-digger`.

## Typical use cases

### Recover cleartext user passwords from live swap

Quick credential hunt on the running system.

```
sudo swap-digger
```

Outcome: locates the active swap device, extracts it into a working directory under /tmp and searches it for cleartext credentials of local Linux users, printing any candidate user/password pairs found.

### Extended search for web and WiFi secrets

Go beyond Linux user passwords.

```
sudo swap-digger -vx
```

Outcome: runs the extended tests on top of the basic hunt, surfacing web form credentials and emails, HTTP basic auth strings, WiFi SSIDs/keys and most-accessed URLs; `-v` prints progress along the way.

### Locate swap devices for forensic analysis

Find swap candidates on attached media.

```
sudo swap-digger -S
```

Outcome: lists all available swap devices/partitions so you can pick the exact device or dump path to pass to `-s` when analyzing a mounted forensic image.

### Analyze swap from a mounted image

Work on evidence without touching the live system.

```
sudo swap-digger -vx -r /mnt/target-root -s /mnt/target-root/swapfile
```

Outcome: analyzes the swap dump or partition given by `-s` against the target's root filesystem given by `-r` (which must contain `<root>/etc/shadow`), recovering user passwords and other secrets from the image. Add `-c` to erase the generated working directory when finished, `-l` to keep a log inside it.

*No corpus evidence — derived from upstream documentation.*
