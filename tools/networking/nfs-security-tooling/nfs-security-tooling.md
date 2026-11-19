---
name: "nfs-security-tooling"
categories:
  - networking
homepage: "https://github.com/hvs-consulting/nfs-security-tooling"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: GPL3
meta-checked: "2026-11-13"
github-repo: "hvs-consulting/nfs-security-tooling"
github-match: "exact"
github-stars: 115
gh-checked: "2026-11-13"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# nfs-security-tooling

> HVS-Consulting's NFS pentest pair — `nfs_analyze` maps exports and flags misconfigurations, `fuse_nfs` mounts with spoofed UIDs and escaped file handles to read past the export root.

## What it's for

Two Linux tools released alongside a blog series on NFS from a pentester's perspective. `nfs_analyze` (built on a modified pynfs) interrogates an NFS server — portmap protocols, NFSv3 exports with allowed clients and auth flavors, connected clients, supported versions (3/4.0/4.1/4.2), Windows file-handle signing, an NFSv4 directory overview with guessed export roots, and a server OS guess — and highlights the misconfigurations that matter offensively: exports whose file handles let you escape to the file-system root (reading `/etc/shadow` when conditions allow) and `no_root_squash`. It leaves traces in server logs and rmtab, so treat it as noisy. `fuse_nfs` (built on anfs) is the exploitation half: a FUSE client for NFSv3 that sends the UID/GID of your choosing — or fakes them automatically to maximize readable files — and can mount arbitrary file handles, including file-system root handles discovered by `nfs_analyze`, by-passing what a normal `mount` can reach. Defaults are read-only; writing needs an explicit flag.

## Typical use cases

### Profile an NFS server

The starting sweep.

```
nfs_analyze 198.51.100.10
```

Outcome: colored terminal report of protocols, exports, client list, version support, handle-signing state and OS guess, with detected misconfigurations highlighted in red — including the Escape check that, on Linux servers without `subtree_check` on ext/xfs/btrfs, returns the file-system root file handle that `fuse_nfs` can mount; targets can also be read from a file (one host per line) and the version probe skipped with `--skip-version-check` when scanning many hosts.

### Test for no_root_squash

The privesc-enabling export option.

```
nfs_analyze --check-no-root-squash 198.51.100.10
```

Outcome: attempts to create and delete a root-owned directory in each writable AUTH_SYS export — success means `no_root_squash` is enabled and file operations run as root (setuid binaries, device files); it is opt-in precisely because it writes data on the server.

### Produce a findings report

Machine-readable output for the engagement report.

```
nfs_analyze --findings-file findings.json targets.txt
```

Outcome: writes a JSON summary of all mountable exports and detected misconfigurations across every host in `targets.txt` (one per line); `--json-file`/`--json-dir` capture the full per-check detail, and any combination of the three outputs can be combined.

### Mount an export as a maximally-privileged user

Browse what the export really exposes.

```
fuse_nfs --export /export --fake-uid /mnt/nfs 198.51.100.10
```

Outcome: FUSE-mounts the NFSv3 export at /mnt/nfs, automatically choosing UID/GID values that unlock the most files and bypassing client-side permission checks — browse it with any local account; add `--fake-uid-allow-root` when the export has `no_root_squash` to also read root-owned files, and `--allow-write` only when you intend to modify the server (default is read-only). Unmount with `umount /mnt/nfs`.

### Mount an escaped root file handle

Read outside the exported directory.

```
fuse_nfs --manual-fh FH --fake-uid /mnt/nfs 198.51.100.10
```

Outcome: mounts the file handle `FH` (hex string) reported by `nfs_analyze`'s Escape check — typically the file-system root — exposing the server's whole partition through the mountpoint, `/etc/shadow` included when the export sits on the OS partition; on btrfs, different snapshots may hold different versions of it.

### Upload a file as a specific UID

Plant a setuid binary for client-side privesc.

```
fuse_nfs --export /export --uid 0:0 --allow-write /mnt/nfs 198.51.100.10
```

Outcome: mounts the export read-write with all file operations attributed to UID:GID 0:0 — copy a payload in with root ownership against a `no_root_squash` export, exactly the privilege-escalation chain the wiki documents.

No corpus evidence — derived from upstream documentation.
