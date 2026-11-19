---
name: "nfcutils"
categories:
  - blackarch-nfc
version: "61.49aa178"
homepage: "https://github.com/nfc-tools/nfcutils"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: autotools
license: LGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "nfc-tools/nfcutils"
github-stars: 29
github-pushed: "2015-04-07"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nfcutils

> Tag enumerator for NFC readers supported by libnfc — a single command, `lsnfc`.

## What it's for

nfcutils is a small utility from the nfc-tools project (author Romuald Conty) that answers one question: which NFC tags are currently in the field of my reader? It builds a single binary, `lsnfc`, which links against libnfc and therefore supports every reader and tag family libnfc does, including identification of MIFARE DESFire EV1 cards from their version info. It takes no arguments and no options — there is no reading, writing or emulation here; for that use libnfc's own utilities (`nfc-mfclassic`, `nfc-mfultralight`, ...). Development stopped around 2015, but it tracks whatever libnfc version it is built against.

## Typical use cases

### List the tags in the reader field

```
lsnfc
```

Outcome: opens the first available libnfc-supported reader and prints one entry per tag in range with its technology type and UID (for example a MIFARE Classic 1K or a DESFire EV1); with no tag present it returns silently. Useful as a quick hardware sanity check before running heavier tag tools.

*No corpus evidence — derived from upstream documentation.*
