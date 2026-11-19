---
name: "sharesniffer"
categories:
  - blackarch-scanner
version: "v0.1.b.8.r8.ga0c5ed6"
homepage: "https://github.com/shirosaidev/sharesniffer"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "shirosaidev/sharesniffer"
github-stars: 214
github-pushed: "2022-03-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sharesniffer

> Network share discovery and read-only auto-mounter for SMB/CIFS and NFS, built to make whole subnets crawlable.

## What it's for

sharesniffer (by shirosaidev, Python 3 on top of nmap and its NSE
scripts) finds open and closed file shares across a local network and,
unlike a plain share scanner, can mount everything it finds read-only so
a crawler can walk the contents. It auto-discovers local networks when
given no target, accepts CIDRs and single hosts with exclusions, and
handles both NFS and SMB/CIFS with sensible read-only, no-exec mount
defaults. Use it when the goal is bulk access to share contents — data
surveying across a flat network — not fine-grained permission auditing of
a single host. It requires nmap in PATH and mounts via the host's
mount helpers, so it runs as root.

## Typical use cases

### Discover shares on all local networks

Let it find the networks itself.

```
sudo sharesniffer
```

Outcome: enumerates the host's interfaces, scans the attached subnets for
NFS and SMB services, and prints every host with the shares it exposes
(open or closed) — a one-command map of file-sharing across the LAN.

### Target a specific subnet, excluding gateways

Scan deliberately, skipping infrastructure IPs.

```
sudo sharesniffer -l 4 --hosts 198.51.100.0/24 -e 198.51.100.1,198.51.100.254
```

Outcome: only the given /24 is scanned, the two excluded addresses
(typically router/firewall) are skipped, and `-l` sets the nmap
aggressiveness level (3–5, lower for more accuracy); results list each
host's discovered shares.

### Auto-mount every open share read-only

Turn discovery into a crawlable filesystem.

```
sudo sharesniffer --hosts 198.51.100.0/24 -a -m /mnt
```

Outcome: every open NFS/SMB share found is mounted under /mnt with
read-only, nosuid, noexec options in per-host directories (prefix
adjustable with `-p`), so a file crawler can be pointed at one tree;
`-q` skips mounting and just prints the would-be mount points.

### Mount SMB shares with credentials

Use an account instead of guest access.

```
sudo sharesniffer --hosts 198.51.100.0/24 -s --smbtype cifs --smbuser user --smbpass Password1 -a -m /mnt
```

Outcome: SMB shares are mounted as the given user (defaults are
`guest`/no password with `smbfs`), reaching shares that deny anonymous
access; `--smbmntopt` overrides the mount options string wholesale.

*No corpus evidence — derived from upstream documentation (README).*
