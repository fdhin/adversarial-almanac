---
name: "bsdiff"
categories:
  - blackarch-binary
version: "4.3"
homepage: "https://www.daemonology.net/bsdiff/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-12-06"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "beikehanbao23/bsdiff"
github-match: "search"
github-stars: 0
github-pushed: "2018-02-05"
github-archived: false
github-issues: 0
gh-checked: "2026-11-09"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bsdiff

> Colin Percival's classic delta tools: bsdiff creates very small binary patches between two versions of a file, bspatch applies them.

## What it's for

bsdiff compares an old and a new file using suffix sorting plus bytewise subtraction tuned for the way executable code changes, and writes a bzip2-compressed patch that bspatch later applies to the old file to reconstruct the new one exactly — no execution or source access needed at patch time. Patches between two builds of the same program are routinely far smaller than generic delta formats, which made the pair a standard building block for software updaters. Know the costs: bsdiff is memory-hungry (on the order of 17× the old file's size), both tools expect bzip2 on the system, and the patch embeds no checksum of its own — verify the reconstructed file's hash yourself. Upstream is daemonology.net (version 4.3, BSD-licensed, originally named bdiff/bpatch); the popular GitHub line mendsley/bsdiff is a separate embeddable-library variant whose patch format is deliberately incompatible with these tools.

*No corpus evidence — derived from upstream documentation.*

GitHub: beikehanbao23/bsdiff is a third-party mirror of the daemonology.net bsdiff 4.3 release (bsdiff.c and bspatch.c verified byte-identical to the pristine 4.3 tarball).

## Typical use cases

### Create a compact binary patch

```
bsdiff program_v1.bin program_v2.bin program.patch
```

Outcome: writes program.patch containing the delta from v1 to v2; expect patch creation to take time and memory proportional to the old file (max(17·old, 9·old+new) bytes of RAM), while the resulting patch for two builds of the same program is typically a fraction of either file's size.

### Apply a binary patch

```
bspatch program_v1.bin program_v2.restored.bin program.patch
```

Outcome: reconstructs the new file byte-for-byte into program_v2.restored.bin in linear time; because the patch format carries no integrity check of its input, compare a hash of the restored file against the original v2 to confirm the right patch was applied to the right base.
