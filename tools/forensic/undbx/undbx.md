---
name: "undbx"
categories:
  - blackarch-forensic
version: "0.21.r3.g5e31c75"
homepage: "https://github.com/ZungBang/undbx"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2023-06-28"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "ZungBang/undbx"
github-stars: 48
github-pushed: "2019-02-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# undbx

> Extracts, recovers and undeletes e-mail messages from Outlook Express DBX files.

## What it's for

UnDBX (Avi Rozen) works on Outlook Express `.dbx` mailbox files: it extracts every message as an individual `.eml` file, opens corrupted DBX files (including ones larger than 2 GB) in recovery mode, and can undelete messages whose fragments still exist in an uncompacted DBX. Subsequent runs synchronize the output folder with the DBX — new messages are extracted, `.eml` files with no matching message are removed — which also makes it an incremental-backup tool. Upstream caveat: Outlook Express zeroes the first 4 bytes of every 512-byte chunk of a deleted message, so undeleted messages can be incomplete and there is no general way to recover them fully.

## Typical use cases

### Extract all messages from a DBX folder

Bulk export of a mail store for review.

```
undbx /mnt/case/outlook-express /mnt/case/eml-out
```

Outcome: extracts the messages of every `.dbx` file in the input folder into corresponding subfolders of `eml-out` as `.eml` files named from the From/To/Subject headers, with modification times set from each Date header. A single `.dbx` file works as input too, and omitting the output folder extracts into the current directory.

### Recover messages from a corrupted DBX

Salvage what standard parsing cannot read.

```
undbx --recover /mnt/case/inbox.dbx /mnt/case/recovered
```

Outcome: scans the corrupted DBX for message fragments and collects everything it finds into `.eml` files under `recovered`, including fragments of deleted messages. Recovery always re-extracts all messages (not just new ones), can take a long time, and recovered messages may themselves be corrupted.

*No corpus evidence — derived from upstream documentation.*
