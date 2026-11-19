---
name: "zulucrypt"
categories:
  - blackarch-crypto
version: "7.1.1"
homepage: "https://github.com/mhogomchungu/zuluCrypt"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-06-29"
build-system: cmake
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "mhogomchungu/zuluCrypt"
github-stars: 576
github-pushed: "2026-05-23"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# zulucrypt

> cryptsetup/tcplay front end for managing LUKS, VeraCrypt, TrueCrypt and plain dm-crypt volumes, via Qt GUI or rich CLI.

## What it's for

zuluCrypt manages encrypted block devices through a Qt GUI (`zuluCrypt-gui`) and a command-line front end (`zuluCrypt-cli`) to cryptsetup and tcplay. It creates, opens and closes LUKS, VeraCrypt (`-t vera`), TrueCrypt and plain volumes on removable media; manages LUKS keyslots (add/remove passphrases and keyfiles); backs up and restores volume headers; and offers device utilities such as UUID lookups and overwriting a device with random data. The CLI is installed setuid-root so regular users can manage their volumes without preconfigured sudo — when it is invoked through sudo or pkexec, `-K $USER` keeps the opened volume owned by the invoking user. It deliberately operates only on non-system partitions (partitions without active `/etc/fstab` or `/etc/crypttab` entries), and it manages encrypted containers, not full-disk encryption of running system drives.

## Typical use cases

### Create an encrypted volume

```
zuluCrypt-cli -c -d /dev/sdc1 -z ext4 -t luks -p Password1
```

Outcome: creates a LUKS volume on /dev/sdc1 with an ext4 filesystem inside and `Password1` as the initial passphrase; `-t vera` or `-t plain` select other container types, `-g` overrides the default crypto chain (`/dev/urandom.aes.xts-plain64.256.sha1`), and `-f keyfile` replaces the passphrase with a keyfile.

### Open a volume read-only and close it again

```
zuluCrypt-cli -o -d /dev/sdc1 -m sdc1 -e ro -p Password1
```

Outcome: unlocks the volume and mounts it read-only at `/run/media/private/$USER/sdc1` (the `-m` component names the mount point); `-e rw` is the default mode. Prompt for the passphrase interactively with `-h` instead of passing `-p`.

### Close an opened volume

```
zuluCrypt-cli -q -d /dev/sdc1
```

Outcome: closes the opened volume and removes its `/dev/mapper` entry; add `-K $USER` when the volume was opened through sudo/pkexec so ownership resolves correctly.

### Manage LUKS keyslots

```
zuluCrypt-cli -a -d /dev/sdc1 -y Password1 -l NewPass
zuluCrypt-cli -r -d /dev/sdc1 -p OldPass
```

Outcome: the first command adds a new passphrase (`-l`, or keyfile via `-n`) after authenticating with an existing one (`-y`/`-u`); the second removes the keyslot holding `OldPass` — a lost passphrase locks the volume out permanently, so keep a second slot active. `zuluCrypt-cli -b -d /dev/sdc1` shows per-slot status (`0` empty, `1` occupied).

### Back up and restore a volume header

```
zuluCrypt-cli -B -d /dev/sdc1 -f header-backup.img -z luks
zuluCrypt-cli -R -d /dev/sdc1 -f header-backup.img -z luks
```

Outcome: writes the LUKS header (all keyslots) to `header-backup.img`, then restores it later — the recovery path for damaged headers or forgotten-slot states. `-H -d /dev/sdc1 -f header-backup.img` compares a live header against a backup without writing.

### Inventory devices and check volume state

```
zuluCrypt-cli -N
zuluCrypt-cli -s -d /dev/sdc1
```

Outcome: the first lists non-system partitions available for volume operations (`-A` all, `-S` system, `-T`/`-Z` add mount info); the second reports whether /dev/sdc1 is an opened volume and prints its properties — `-i` tests for the presence of a LUKS volume, `-L` lists all open volumes with mount points.

### Wipe a device with random data

```
zuluCrypt-cli -X -d /dev/sdc1
```

Outcome: opens the device and overwrites it with random data, destroying previously written content beyond key-recovery — the standard way to prepare a device for a fresh encrypted volume.

*No corpus evidence — derived from upstream documentation.*
