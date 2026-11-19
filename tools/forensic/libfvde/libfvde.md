---
name: "libfvde"
categories:
  - blackarch-forensic
version: "222.c2676fe"
homepage: "https://github.com/libyal/libfvde"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: autotools
license: LGPL3
meta-checked: "2026-11-09"
github-repo: "libyal/libfvde"
github-stars: 359
github-pushed: "2026-10-30"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# libfvde

> Library and tools to inspect and decrypt Apple Core Storage / FileVault 2 encrypted volumes.

## What it's for

libfvde is a library, by Omar Choudary and Joachim Metz, that parses Apple's Core Storage (CS) format — the volume manager behind FileVault 2 / FileVault Drive Encryption full-disk encryption on Mac OS X 10.7 through macOS 10.15 — together with the utilities built on it: `fvdeinfo` (report volume metadata), `fvdemount` (mount a decrypted logical volume via FUSE or Dokan) and `fvdewipekey` (decrypt the EncryptedRoot.plist.wipekey key file). Unlocking requires a credential: the user's password, the recovery password, or the volume master key (VMK) in base16. Earlier releases shipped a separate `fvdeunlock` tool; current versions fold unlocking into `fvdeinfo` and `fvdemount` through the `-p`, `-r` and `-k` options. The project is marked experimental upstream with known limits (multiple physical volumes and volumes of 1 TiB or larger are unsupported), and it does not handle the legacy FileVault sparsebundle home-directory format.

## Typical use cases

### Survey a Core Storage disk image

See the logical volume group, physical and logical volumes before hunting credentials.

```
fvdeinfo -o 20480 image.raw
```

Outcome: prints Core Storage information — logical volume group identifier and name, physical volume identifiers, sizes and encryption method (e.g. AES-XTS), and logical volume identifiers, names and sizes; volumes that remain locked are reported as "Is locked". `-o` gives the byte offset of the Core Storage volume inside the image.

### Check a password or recovery key against a volume

Validate a credential obtained from the user or the case file.

```
fvdeinfo -r '1111-2222-3333-4444-5555-6666' image.raw
```

Outcome: prints the metadata report with the logical volume shown unlocked when the recovery password is correct (`-p` takes the user's password instead, `-k` a base16 VMK).

### Mount a decrypted logical volume

Browse the decrypted filesystem through FUSE.

```
fvdemount -p 'Password1' image.raw /mnt/fvde
```

Outcome: unlocks the logical volume with the password and serves its content decrypted at `/mnt/fvde` (the tool stays in the foreground; `-v` gives verbose stderr), so files under the mount point can be examined with normal tools.

### Mount using an extracted volume master key

Unlock with a VMK recovered from memory or another tool rather than a password.

```
fvdemount -k 0f1e2d3c4b5a69788796a5b4c3d2e1f0 image.raw /mnt/fvde
```

Outcome: decrypts and mounts using the base16 volume master key directly; `-e EncryptedRoot.plist.wipekey` additionally supplies the key file when it was extracted from the system separately from the image.

### Decrypt the EncryptedRoot.plist.wipekey file

Recover the key material wrapped inside the wipekey plist.

```
fvdewipekey EncryptedRoot.plist.wipekey 0f1e2d3c4b5a69788796a5b4c3d2e1f0
```

Outcome: decrypts the wipekey file using the base16 volume key data, exposing the per-volume key material it wraps — useful when the plist came off a live Mac or a separate acquisition.

*No corpus evidence — derived from upstream documentation.*
