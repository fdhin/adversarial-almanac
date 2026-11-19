---
name: "secure-delete"
categories:
  - blackarch-anti-forensic
version: "1.b63d814"
homepage: "http://www.thc.org/"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-15"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "BlackArch/secure-delete"
github-stars: 31
github-pushed: "2013-11-11"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# secure-delete

> THC's secure_deletion toolkit — four focused wipers (`srm`, `sfill`, `sswap`, `sdmem`) implementing the Gutmann 38-pass pattern against files, free disk space, swap and RAM.

*No corpus evidence — derived from the upstream manual pages.*

## What it's for

Making deleted data actually gone. Each tool targets one residue the others can't reach: `srm` overwrites files before unlinking them, `sfill` overwrites the free space and free inodes of a filesystem (where deleted copies linger), `sswap` wipes swap devices, `sdmem` wipes RAM. All use the same algorithm — one 0xff pass, five random passes, the 27 Gutmann-pattern passes, five more random passes, then rename and truncate — with O_SYNC and fsync per pass to force cache flushes. On modern SSDs and with flash wear-leveling, multi-pass patterns add little over a single pass; the practical value today is coverage (swap, free space, RAM) rather than the pass count. Flags are shared across the tools: `-f` fast/insecure, `-l` reduce to two passes, `-ll` one random pass, `-v` verbose, `-z` final zero pass.

## Typical use cases

### Securely remove a file or directory

```
srm -vz secret.kdbx
srm -rvz /home/user/staging/
```

Outcome: each file is overwritten with the full pass sequence (`-z` finishes with zeros instead of random data so the last write doesn't itself look wiped), renamed to a random name and truncated before deletion; `-r` descends into subdirectories. Verbose mode reports each pass as it completes.

### Wipe the free space of a filesystem

```
sfill -vz /mnt/data
```

Outcome: the argument is any directory on the target partition; sfill creates temporary files until all free disk space is consumed and overwritten, then exhausts free inodes the same way, and cleans up after itself. Use `-l` or `-ll` on large partitions where a full Gutmann run would take days.

### Wipe swap and RAM before shutdown

```
sudo swapoff -a && sudo sswap -vz /dev/sda2
sudo sdmem -ll
```

Outcome: `sswap` overwrites the (now disabled) swap device — running it on active swap can crash the system, hence the `swapoff` first. `sdmem` fills RAM with the pass pattern; it is very slow at full strength, which is why the manpage's own tip is the single-pass `-ll` variant. Together they close the cold-boot and swap-residue windows.
