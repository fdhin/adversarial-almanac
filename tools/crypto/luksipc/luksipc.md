---
name: "luksipc"
categories:
  - blackarch-crypto
version: "0.01"
homepage: "http://www.johannes-bauer.com/linux/luksipc"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "johndoe31415/luksipc"
github-stars: 102
github-pushed: "2019-10-19"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# luksipc

> Converts an existing unencrypted block device into a LUKS-encrypted device in place, without an intermediate copy of the data.

## What it's for

luksipc ("LUKS in-place conversion") encrypts a populated block device without
freeing disk space for a second copy: it writes a LUKS header at the start of
the device and copies the plaintext content into the encrypted area from the
back. It also supports "reLUKSification" — re-encrypting an existing LUKS
volume with a fresh bulk key and new header parameters (useful when a keyslot
holder must be fully revoked, or when old algorithms/alignment need replacing).

Preparation is mandatory and luksipc will not check it for you: shrink the
contained filesystem by at least 128 MiB (the LUKS header needs room) and
unmount it before starting. The conversion bootstraps with a random key saved
to `/root/initial_keyfile.bin`; afterwards you add your own passphrase/key with
`cryptsetup luksAddKey` and kill the bootstrap keyslot with `cryptsetup
luksKillSlot`. The upstream author now recommends `cryptsetup-reencrypt` for
new work instead — luksipc predates it and can fail destructively when the LUKS
header format changes, so keep backups. luksipc does not detect filesystems or
validate the resize step.

## Typical use cases

### Convert a plain device to LUKS in place

Encrypt a populated, already-shrunk and unmounted device without copying data
elsewhere.

```
luksipc -d /dev/loop0
```

Outcome: prints a conversion plan and a five-point safety checklist to confirm,
then writes the LUKS header, copies the data into the encrypted area, and saves
the bootstrap random key to `/root/initial_keyfile.bin`. Open the result with
`cryptsetup luksOpen /dev/loop0 cryptdev --key-file /root/initial_keyfile.bin`,
add a permanent passphrase, then remove the bootstrap keyslot.

### Re-encrypt a LUKS volume with a new key (reLUKSification)

Replace the bulk-encryption key and header parameters of an existing LUKS
volume, e.g. after revoking a keyslot holder who once had the old header.

```
luksipc --device /dev/sdh2 --readdev /dev/mapper/oldluks --luksparams='-h,sha256'
```

Outcome: reads plaintext through the already-unlocked mapper device
(`/dev/mapper/oldluks`, opened beforehand with `cryptsetup luksOpen`), performs
a fresh luksFormat on the raw device with the given parameters, and re-encrypts
the whole volume in place. Requires luksipc 0.03 or newer (the BlackArch
package tracks the current release line).

*No corpus evidence — derived from upstream documentation.*
