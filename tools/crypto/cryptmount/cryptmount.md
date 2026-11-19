---
name: "cryptmount"
categories:
  - crypto
version: "6.2.0"
homepage: "http://cryptmount.sourceforge.net/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur]
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "rwpenney/cryptmount"
github-stars: 13
github-pushed: "2026-06-06"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# cryptmount

> Lets ordinary users mount and unmount dm-crypt/LUKS encrypted filesystems with just the decryption password.

## What it's for

cryptmount manages encrypted filing systems on GNU/Linux (2.6+ kernels) built on the kernel's dm-crypt device-mapper target. After the superuser defines a filesystem in `/etc/cryptmount/cmtab` — or generates a basic LUKS setup interactively with `cryptmount-setup` — an ordinary user can mount and unmount it solely by providing the decryption password, with any system devices needed configured automatically. The protected filesystems can reside in ordinary files or raw disk partitions, and a wide variety of encryption schemes (from the kernel and the libgcrypt library) can protect both the filing system and the access key. It is a mount-management utility, not a cracking or forensics tool: the interesting parts for a security operator are the admin-side initialization path (`--generate-key`, `--prepare`, `--release`) and the fact that user-triggered mounting needs no root rights.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Create a basic encrypted filesystem interactively

```
cryptmount-setup
```

Outcome: an interactive wizard (typically installed in /usr/local/sbin) prompts for size, location, mount point and passphrase, then creates a new LUKS-encrypted filesystem and writes its definition — afterwards the filesystem is ready for ordinary-user mounting by name.

### Initialize a cmtab-defined filesystem by hand

```
test -e /home/crypt.fs || dd if=/dev/zero of=/home/crypt.fs bs=1M count=128
mkdir /mnt/crypt
cryptmount --generate-key 32 crypt
cryptmount --prepare crypt
mke2fs -t ext4 /dev/disk/by-id/dm-name-crypt
cryptmount --release crypt
```

Outcome: creates the 128 MB backing file, generates a 32-byte access key, sets up the dm-crypt mapping (visible as /dev/disk/by-id/dm-name-crypt), builds an ext4 filesystem on the decrypted device, then releases the mapping — the one-time initialization for an entry named `crypt` in /etc/cryptmount/cmtab.

### Mount and unmount as an ordinary user

```
cryptmount crypt
cryptmount -u crypt
```

Outcome: the first command prompts for the decryption password, configures the dm-crypt target and mounts the filesystem at its configured directory (e.g. /mnt/crypt) without root privileges; `cryptmount -u crypt` unmounts it and releases the underlying device.
