---
name: "libbde"
categories:
  - blackarch-crypto
version: "20260916.r2.gdd48d47"
homepage: "https://github.com/libyal/libbde"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: autotools
license: LGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "libyal/libbde"
github-stars: 262
github-pushed: "2026-11-01"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 23
gh-activity-checked: "2026-11-09"
---

# libbde

> Library and tools to access BitLocker Drive Encryption (BDE) volumes.

## What it's for

libbde is Joachim Metz's libyal-project C library for reading BitLocker Drive Encryption volumes, with the format support that implies: BitLocker Windows Vista (version 1), Windows 7 and later (version 2), BitLocker To Go, and used-disk-space-only encryption, under AES-CBC (with and without the Elephant diffuser) and AES-XTS. Unlock methods supported: clear key, password, recovery password, start-up key (.BEK file), and FVEK/TWEAK key data supplied directly. TPM- and SID-based protectors are not supported, so volumes sealed solely to a TPM cannot be opened. The package ships two tools built on the library: `bdeinfo` for inspecting volume metadata and `bdemount` for FUSE-mounting a decrypted volume; it is a forensic/read access toolkit, not a BitLocker management suite.

## Typical use cases

### Inspect a BitLocker volume unlocked with a password

```
bdeinfo -p 'Password1' /dev/sdb1
```

Outcome: unlocks the volume metadata with the given user password and prints the volume information (format version, encryption method, protectors); a wrong password produces an unlock-failure error instead.

### Inspect with a recovery password or start-up key

```
bdeinfo -r 111111-222222-333333-444444-555555-666666-777777-888888 -s /mnt/usb/startup.BEK /dev/sdb1
```

Outcome: unlocks the same metadata using the 48-digit recovery password and/or the .BEK start-up key file instead of a user password — useful when the user password is unknown but either recovery material is.

### Mount a decrypted BitLocker volume via FUSE

```
mkdir mnt && bdemount -p 'Password1' /dev/sdb1 mnt
```

Outcome: decrypts the volume with the password and serves its contents through the mount point `mnt`, so files can be read with normal tools without altering the source volume.

### Mount a BitLocker partition embedded in a disk image

```
bdemount -p 'Password1' -o 32256 disk.dd mnt
```

Outcome: treats the BitLocker volume as starting at byte offset 32256 (default sector 63) inside the raw image and mounts it at `mnt` — `-o` takes the volume offset in bytes; the same flag works with raw FVEK/TWEAK keys via `-k FVEK:TWEAK` in base16 when no password is available.

*No corpus evidence — derived from upstream documentation.*
