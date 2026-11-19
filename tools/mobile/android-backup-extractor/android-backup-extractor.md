---
name: "android-backup-extractor"
categories:
  - blackarch-mobile
version: "latest.r0.g38fd634"
homepage: "https://github.com/nelenkov/android-backup-extractor"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-07-21"
build-system: java
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "nelenkov/android-backup-extractor"
github-stars: 2610
github-pushed: "2026-11-06"
github-archived: false
github-issues: 45
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# android-backup-extractor

> Java utility (`abe`) that converts Android `.ab` backups made with `adb backup` into plain tar archives — and repacks tars into restorable backups, decrypting and encrypting them on the way.

## What it's for

android-backup-extractor (Nikolay Elenkov's `abe`) converts between Android's proprietary backup format — the `.ab` files produced by `adb backup` on Android 4.0 and later — and ordinary tar archives, reimplementing the format handled by AOSP's BackupManagerService. Unpacking a backup turns opaque app data into a browsable tar (per-app trees under `apps/`); repacking turns a modified tar back into a `.ab` that `adb restore` will accept. Encrypted backups are decrypted with the password that was set during `adb backup`; the password can be passed as an argument or read from the `ABE_PASSWD` environment variable, and backups without a password are merely compressed. The tool does not create backups from a device — that is `adb backup`'s job — and cannot help with apps that set `allowBackup="false"` (Android skips those entirely). The BlackArch package installs the runnable jar as the `abe` command.

### Unpack a backup into a tar archive

Open up an `.ab` file for inspection.

```
abe unpack backup.ab backup.tar
```

Outcome: writes `backup.tar` containing the decompressed (and decrypted, if applicable) backup contents; extract it with `tar xf backup.tar` and browse the per-app data under `apps/`. For an encrypted backup pass the password as a third argument (`abe unpack backup.ab backup.tar 'Password1'`) or export `ABE_PASSWD`; a filename of `-` streams via standard input/output.

### Repack a modified tar into a restorable backup

Push edited app data back onto a device.

```
abe pack restore.tar restore.ab 'Password1'
```

Outcome: writes `restore.ab` in the Android backup format, compressed and encrypted with the given password; load it on the device with `adb restore restore.ab`. Keep the tar's internal file order identical to the original backup — Android is very particular about it, and incompatible archives cause restore errors or crashes that surface only in logcat under BackupManagerService. Safest procedure: build the new tar from a file list taken from the original (`tar tf backup.tar | grep -F com.example.app > package.list`, then `tar cf restore.tar -T package.list`).

### Produce a backup for Android 4.4.3

Target the version-2 backup format of that era.

```
abe pack-kk restore.tar restore.ab
```

Outcome: writes a version-2 backup instead of the current format, as required for compatibility with Android 4.4.3 restores; otherwise behaves exactly like `pack`.

*No corpus evidence — derived from upstream documentation.*
