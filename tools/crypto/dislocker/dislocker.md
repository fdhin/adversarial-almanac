---
name: "dislocker"
categories:
  - blackarch-crypto
version: "v0.7.3.r46.g4572dc7"
homepage: "https://github.com/Aorimn/dislocker"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-07-21"
build-system: cmake
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Aorimn/dislocker"
github-stars: 1955
github-pushed: "2026-10-21"
github-archived: false
github-issues: 117
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 31
gh-activity-checked: "2026-11-09"
---

# dislocker

> Reads (and writes) BitLocker-encrypted volumes on Linux, macOS and FreeBSD.

## What it's for

dislocker is a library plus a set of binaries that mount or decrypt Microsoft BitLocker volumes — AES-CBC/AES-XTS partitions from Vista through 10, with or without the Elephant diffuser, and BitLocker-To-Go removable media — on non-Windows systems. The two workhorses are `dislocker-fuse`, which live-decrypts through a FUSE mount exposing a virtual NTFS file that you loop-mount, and `dislocker-file`, which decrypts the entire volume into a standalone flat NTFS image. Any decryption mean works: user password, 48-digit recovery password, a `.BEK` startup-key file, the raw FVEK/VMK, or an on-volume clear key. You must already hold a valid credential — dislocker mounts volumes, it does not crack them; `dislocker-metadata` and `dislocker-bek` exist separately for inspecting volume metadata and BEK files.

## Typical use cases

### Mount a BitLocker partition with the user password

```
sudo dislocker-fuse -V /dev/sdb2 -uPassword1 -- /mnt/bitlocker
sudo mount -o loop /mnt/bitlocker/dislocker-file /mnt/clear
```

Outcome: the FUSE mount produces the virtual NTFS file `/mnt/bitlocker/dislocker-file`; the second command loop-mounts it read-write at `/mnt/clear`. Invoking it as plain `dislocker` runs this same binary.

### Use a recovery password without leaking it in the process list

```
sudo dislocker-fuse -V /dev/sdb2 -p -- /mnt/bitlocker
```

Outcome: prompts interactively for the 48-digit recovery password, validating each block as you type it; the credential never appears in shell history or `ps` output.

### Decrypt a whole volume into a flat NTFS image

```
dislocker-file -V encrypted.bitlocker -uPassword1 -- clear.ntfs
```

Outcome: writes `clear.ntfs`, a complete standalone NTFS image (as large as the source volume) that mounts with `mount -o loop`; unlike the FUSE route, writes to it no longer touch the underlying BitLocker volume.

### Decrypt a BitLocker-To-Go USB stick via its BEK key file

```
sudo dislocker-fuse -V /dev/sdb1 -f /path/to/file.BEK -- /mnt/bitlocker
```

Outcome: decrypts the whole partition using the startup-key file — the FAT32/BitLocker dual format of To-Go media is handled transparently, with no special flags.

*No corpus evidence — derived from upstream documentation.*
