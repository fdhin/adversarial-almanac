---
name: "skul"
categories:
  - blackarch-cracker
version: "27.7bd83f1"
homepage: "https://github.com/cryptcoffee/skul"
packaged-by: [blackarch]
blackarch-builddate: "2023-03-01"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "cryptcoffee/skul"
github-stars: 15
github-pushed: "2018-07-12"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# skul

> Offline LUKS passphrase brute-forcer that works from a dumped disk header, not the whole disk.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Recovering or auditing the passphrase of a Linux Unified Key Setup (LUKS)
volume encrypted with cryptsetup. Skul is a proof-of-concept showing how fast
weak LUKS passphrases fall: it needs only a ~3 MB dump of the LUKS header
(all keyslots, none of the data), derives candidate keys with the fastpbkdf2
CPU implementation across multiple threads, and checks each key against the
keyslots in the dump. Attack behaviour is set in `conf/skul.cfg`: incremental
brute force over a configurable alphabet and length range (`SEL_MOD 1`), a
password-list attack (`SEL_MOD 2`, list read from `conf/pwlist`), or
list-then-brute (`SEL_MOD 3`). It stops at the passphrase — it does not
decrypt or mount the volume. Building requires OpenSSL.

## Typical use cases

### Run the bundled demo header

Verify a build works end to end against the shipped sample header.

```
./skul disks/test_disk_py
```

Outcome: runs the currently configured attack against the example LUKS header,
prints a progress bar and thread activity, and reports the passphrase if the
sample key lies inside the configured keyspace.

### Attack a real volume from its header dump

Test your own disk's passphrase strength without needing the disk attached.

```
dd if=/dev/sdX of=./luks_header.img bs=1024 count=3072
./skul ./luks_header.img
```

Outcome: the `dd` copies the LUKS header (keyslots and metadata, 3 MB) into a
file; skul then runs the configured attack against that image and prints the
matching passphrase when a derived key opens a keyslot.

### Configure the attack mode and keyspace

Switch between brute force, password list, or combined attacks.

```
# conf/skul.cfg
SEL_MOD 3        # 0 interactive, 1 brute force, 2 password list, 3 list then brute
MIN_LEN 2        # minimum length for incremental brute force
MAX_LEN 6        # maximum length for incremental brute force
ALP_SET 1        # 1 a-z, 2 A-Z, 3 0-9, ... 7 full printable ASCII
NUM_THR 8        # PBKDF2 threads
FST_CHK 1        # fast master-key check (EXT filesystems only, up to ~20% faster)
```

Outcome: on the next run skul reads this file at startup and announces the
chosen attack mode; the password-list mode (`SEL_MOD 2`/`3`) reads candidates
from `conf/pwlist`, while the brute-force bounds come from `MIN_LEN`,
`MAX_LEN` and `ALP_SET`.
