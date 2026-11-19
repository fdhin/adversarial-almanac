---
name: "recuperabit"
categories:
  - blackarch-forensic
version: "98.90930dc"
homepage: "https://github.com/Lazza/RecuperaBit"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Lazza/RecuperaBit"
github-stars: 624
github-pushed: "2026-07-26"
github-archived: false
github-issues: 36
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# recuperabit

> Reconstruct the NTFS directory structure and recover files from images with missing or damaged metadata.

## What it's for

RecuperaBit (by Andrea Lazzarotto) rebuilds filesystem structures from a possibly damaged disk image even when the partition table is missing, partition boundaries are unknown, metadata is partially overwritten, or the volume was quick-formatted. After scanning, it drops you into a small interactive shell where you list the recoverable partitions, browse their reconstructed trees, restore files, and export listings as CSV or body-file for timeline tools. It never modifies the source image, and read-only access to real devices is possible though discouraged for failing drives. Current limitations: only NTFS is supported, and compressed files cannot be recovered.

## Typical use cases

### Scan an image and save the scan state

Point it at a bitstream image; persist the scan so later runs load instantly.

```
recuperabit -s scan.save disk.img
```

Outcome: the image is scanned for NTFS structures (missing partitions are located automatically), results are saved to `scan.save`, and an interactive shell opens at the prompt.

### List what can be recovered

From the interactive shell, see the partitions found in the image.

```
recoverable
```

Outcome: prints one line per partition, e.g. `Partition #0 -> Partition (NTFS, 15.00 MB, 11 files, Recoverable, Offset: 2048, ...)` — note the partition index and file count for the next step.

### Restore all files of a reconstructed partition

Extract the root tree plus everything orphaned, using the root id for NTFS (5) and the universal lost-files id (-1).

```
restore 0 5
restore 0 -1
```

Outcome: files from partition #0 are written into the output directory (default `recuperabit_output`, override with `-o` on launch), reconstructing the recovered hierarchy on disk.

### Export the reconstructed tree for review

Print or export the directory structure instead of extracting content.

```
tree
```

Outcome: the full reconstructed directory tree of the selected partition is printed to screen (verbose on large drives); CSV/body-file exports written by the shell go to the output directory for use with mactime.

*No corpus evidence — derived from upstream documentation.*
