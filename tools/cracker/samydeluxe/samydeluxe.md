---
name: "samydeluxe"
categories:
  - blackarch-cracker
version: "2.2ed1bac"
homepage: "http://github.com/jensp/samydeluxe"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "jensp/samydeluxe"
github-stars: 7
github-pushed: "2009-02-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# samydeluxe

> Batch SAM hash extraction from every Windows partition attached to the host.

## What it's for

A bash automation wrapper by jensp (2009) around samdump2 for the "disk
pulled from a Windows box" scenario. With no arguments it enumerates the
host's block devices via `fdisk`, tries to mount each NTFS or FAT
partition under `/mnt`, locates the Windows `system32/config` SYSTEM and
SAM hives by glob, and runs samdump2 on them, saving each partition's
output as `~/dump-<device>`. Requires root, `samdump2` and ntfs-3g; it is
Linux-only and offers no options. It only automates hash extraction —
cracking the hashes is still a separate step.

## Typical use cases

### Dump hashes from all attached Windows partitions

Run it once as root; it discovers and processes every mountable partition
by itself.

```
sudo samydeluxe
```

Outcome: for each NTFS/FAT partition holding a Windows config directory,
runs samdump2 and writes `~/dump-<partition>` with the pwdump-format
hashes, printing `sam dumped to ...` per success; partitions are unmounted
afterwards.

*No corpus evidence — derived from upstream documentation.*
