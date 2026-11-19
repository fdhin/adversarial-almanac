---
name: "cryptsetup"
categories:
  - protect
version: "2.8.8-1"
homepage: "https://gitlab.com/cryptsetup/cryptsetup/"
origin: "arch"
arch-repo: "core"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-09-07"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "mbroz/cryptsetup"
github-stars: 187
github-pushed: "2026-10-16"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 315
gh-activity-checked: "2026-11-09"
---

# cryptsetup

> Linux disk-encryption management: creates and unlocks LUKS (and plain dm-crypt) volumes and manages their keys, slots and headers.

## What it's for

cryptsetup drives the dm-crypt subsystem: it formats devices or file containers as LUKS volumes, maps them to /dev/mapper devices for mounting, and manages passphrases, key files, keyslots, detached headers and LUKS2 reencryption. LUKS1 is the classic header layout; LUKS2 (the default) adds redundant JSON metadata, Argon2 key derivation and integrity support. The LUKS header holds the keyslots — if it is lost or corrupted the data is unrecoverable, so header backups are part of routine operation. Used both for full-disk encryption on hosts and for handling LUKS container images in forensic and backup workflows.

## Typical use cases

### Create a LUKS container

Initialize encryption on a partition or a file image.

```
cryptsetup luksFormat backup.img
```

Outcome: after an explicit confirmation prompt, asks twice for a passphrase and writes a LUKS header with one keyslot, destroying prior contents. --type luks2 (default) or --type luks1 selects the header format; -d keyfile takes the passphrase from a file instead of the terminal.

### Open and close a LUKS volume

Map an encrypted device for use, then tear the mapping down.

```
sudo cryptsetup luksOpen backup.img vault
sudo mount /dev/mapper/vault /mnt/vault
...
sudo umount /mnt/vault
sudo cryptsetup luksClose vault
```

Outcome: after passphrase entry (or -d keyfile) a decrypted mapping appears at /dev/mapper/vault that mounts like any block device; luksClose removes the mapping. luksOpen/luksClose are aliases of open --type luks and close.

### Identify a LUKS device and inspect its header

Examine a container without unlocking it.

```
cryptsetup isLuks backup.img
cryptsetup luksDump backup.img
```

Outcome: isLuks exits 0 when a LUKS header is present and non-zero otherwise; luksDump prints header version, UUID, cipher, keyslot status and the payload/data offset. On LUKS1 headers the "Payload offset" line (in 512-byte sectors) gives the header size, so offset times 512 bytes is how much to carve with dd to extract the header from a raw image.

### Test a passphrase without opening

Verify key material non-interactively, for example inside a script.

```
echo -n "$candidate" | cryptsetup open --test-passphrase backup.img
```

Outcome: exit status 0 if the passphrase matches any keyslot; otherwise prints "No key available with this passphrase." and exits 2. No mapper device is created, so candidate passphrases can be looped over safely.

### Add, change and remove keys

Manage who can unlock the volume.

```
cryptsetup luksAddKey backup.img
cryptsetup luksChangeKey backup.img
cryptsetup luksRemoveKey backup.img
```

Outcome: luksAddKey authenticates with an existing passphrase and stores a new one in a free keyslot; luksChangeKey replaces the supplied passphrase in place; luksRemoveKey deletes the keyslot matching the entered passphrase. -S targets a specific slot number, and luksKillSlot removes a slot by index.

### Back up and restore the LUKS header

Protect against header corruption — without the header the data is gone.

```
cryptsetup luksHeaderBackup backup.img --header-backup-file header.img
```

Outcome: the full header and keyslots area is copied to header.img (16 MiB with default LUKS2 sizing, created read-only) for offline storage; luksHeaderRestore writes such a backup back, which also drops any keys added since it was taken.

### Check an active mapping's status

Confirm the type, cipher and backing device of an open volume.

```
cryptsetup status vault
```

Outcome: prints the mapping's type, the underlying device or file, cipher, sector size and size, and whether it is active — the first check when a mapped volume misbehaves.

### Wipe a volume's keyslots

Cryptographically destroy access to a LUKS2 volume.

```
cryptsetup erase backup.img
```

Outcome: all keyslots are erased so the encrypted payload can no longer be unlocked — a fast alternative to overwriting the whole device when decommissioning.
