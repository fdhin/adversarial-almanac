---
name: "memdump"
categories:
  - blackarch-forensic
version: "1.01"
homepage: "http://www.porcupine.org/forensics/tct.html"
packaged-by: [blackarch, kali]
blackarch-builddate: "2020-07-26"
build-system: make
license: custom:IPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/memdump"
github-stars: 0
github-pushed: "2019-05-30"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# memdump

> Physical-memory dumper for Linux, Solaris, FreeBSD and OpenBSD by Wietse Venema — streams /dev/mem to stdout while skipping holes in the memory map.

*No corpus evidence — derived from upstream documentation.*

## What it's for

memdump is Wietse Venema's memory dumper for UNIX-like systems, distributed from porcupine.org alongside The Coroner's Toolkit (TCT). It captures the physical memory of a running system for live-response forensics: bits of the operating system, running processes, and every file and directory accessed recently — including short-lived remnants of deleted files. It walks the memory map and skips the holes so the dump contains no bogus reads, writing raw output to stdout; the documentation recommends sending the dump off-host over the network, because writing it to a local file risks clobbering the very filesystem cache you want to capture. It images system RAM only — not individual processes (for that see a per-process dumper like memfetch) — and it is not a Windows tool despite sharing its name with several. On BlackArch the binary is installed as `memdump-kernel` to avoid a clash with libmemcached. The `-k` option switches to kernel virtual memory (/dev/kmem), which is slow on 64-bit systems and can lock the machine up hard — read the man page warnings first. GitHub: Seabreg/memdump is a third-party mirror of the author's porcupine.org distribution (its files verified byte-identical to the memdump 1.01 tarball the package builds); the canonical home remains porcupine.org.

## Typical use cases

### Stream physical memory to a remote collector

```
memdump | nc 198.51.100.10 9999
```

Outcome: raw physical memory streams across the network to the collecting host (receive it there with `nc -l -p 9999 > memory.img`); the local disk is never written, preserving the filesystem cache contents.

### Capture the memory map alongside the dump

```
memdump -m- 2> memmap.txt | nc 198.51.100.10 9999
```

Outcome: same raw dump over the network, while the memory map — the hex start and end address of each region, one per line — is saved to memmap.txt; use it to interpret offsets in the image afterwards.

### Limit the dump size

```
memdump -d 256m | nc 198.51.100.10 9999
```

Outcome: dumps only the first 256 MB of physical memory (size suffixes k, m and g are accepted) — a quick triage capture on large-memory hosts.
