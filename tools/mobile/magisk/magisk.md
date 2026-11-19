---
name: "magisk"
categories:
  - mobile
homepage: "https://github.com/topjohnwu/Magisk"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: cpp
meta-checked: "2026-11-13"
github-repo: "topjohnwu/Magisk"
github-stars: 63984
github-pushed: "2026-11-10"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
github-commits-year: 437
gh-activity-checked: "2026-11-13"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "GPL3"
---

# magisk

> Android rooting suite whose bundled binaries — `magiskpolicy`, `magiskboot`, `su`, `resetprop` — form a systemless-root toolkit: SELinux policy patching, boot-image unpack/repack, and root-shell access.

## What it's for

Magisk modifies the Android operating system systemlessly — through a patched boot image rather than touched system partitions — providing root (`MagiskSU`), loadable modules, and `Zygisk` code injection into every app process. For offensive work the interesting parts are the four binaries it ships: `magiskpolicy` (patch SELinux policies at the binary or live-policy level, aliased `supolicy`), `magiskboot` (unpack/repack Android boot images, edit ramdisk CPIO and DTB, hexpatch kernels), plus the `su` and `resetprop` applets and a denylist CLI for hiding root from specific apps. It is the standard substrate on which Android instrumentation frameworks and Wi-Fi research patches (e.g. nexmon on Pixel devices) are installed. It does not itself exploit anything — the bootloader of the target device must already be unlockable/unlocked to flash a Magisk-patched boot image.

## Typical use cases

### Patch a live SELinux policy

```
magiskpolicy --live "permissive su"
```

Outcome: the `su` domain is immediately switched to permissive in the kernel's running policy (`/sys/fs/selinux/policy`), so SELinux denials no longer block root-shell actions — no reboot or policy recompile needed; verified in the next `dmesg` denial log (which now shows `permissive=1` entries).

### Patch a policy file on disk

```
magiskpolicy --load sepolicy --save sepolicy.patched "allow magisk * * *"
```

Outcome: reads the monolithic sepolicy blob from `sepolicy`, applies the allow rule (the same built-in rule set `--magisk` applies), and writes the modified binary policy to `sepolicy.patched` — suitable for baking into an image before first boot.

### Apply a whole ruleset from a file

```
magiskpolicy --apply rules.txt
```

Outcome: every line of `rules.txt` is parsed as a policy statement (allow/deny/auditallow/type/permissive/…, one statement per line) and applied to the currently loaded policy; multiple `--apply` files are allowed in one invocation.

### Dump and inspect current rules

```
magiskpolicy --print-rules
```

Outcome: prints every rule in the loaded sepolicy to stdout — the quick way to audit what a root environment or module has already allowed before adding more.

### Unpack and repack a boot image

```
magiskboot unpack -h boot.img
magiskboot repack boot.img new-boot.img
```

Outcome: `unpack` splits `boot.img` into kernel, ramdisk.cpio, dtb and header files in the current directory (`-h` also dumps header info for editing); after editing, `repack` rebuilds a flashable image reusing the original headers, compressing each component back to its detected format.

### Edit the ramdisk in place

```
magiskboot cpio ramdisk.cpio "mkdir 750 su.d" "add 750 su.d/stage.sh stage.sh"
magiskboot cpio ramdisk.cpio test
```

Outcome: creates `su.d` and inserts a script into the ramdisk CPIO without repacking anything else; `test` reports the ramdisk state (0 stock, 1 Magisk-patched). Use before `repack` to persist init-time changes.

### Hide root from a target app

```
magisk --denylist enable
magisk --denylist add com.example.target
```

Outcome: denylist enforcement is switched on and the package is registered, so Magisk's mounts are hidden from that app's processes — the counter-move when an app detects root and refuses to run on an instrumented device.

*No corpus evidence — derived from upstream documentation.*
