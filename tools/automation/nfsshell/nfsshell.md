---
name: "nfsshell"
categories:
  - blackarch-automation
version: "19980519"
homepage: "http://www.paramecium.org/~leendert/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-06-18"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Supermathie/nfsshell"
github-stars: 135
github-pushed: "2025-06-13"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nfsshell

> Leendert van Doorn's 1998 userland NFS client with an ftp-like shell and per-session UID/GID spoofing.

## What it's for

nfsshell gives user-level access to an NFS server without mounting anything locally and without root: it speaks the NFS protocol directly over UDP or TCP (including "secure" privileged-port mounts), and its `uid`/`gid` commands set the credentials presented with every request. That makes it the classic tool for demonstrating NFS's client-trust weakness — pick the UID that owns the files you want and the server serves them — and for inspecting exports where you cannot or do not want to create a matching local user. The packaged source is Michael Brown's NFSv3-capable update of the original; on Linux the Python NfSpy project covers the same ground with a FUSE mount, but nfsshell remains a compact, dependency-light interactive client. It is 1990s software: expect it to talk NFSv2/v3 only.

## Typical use cases

### Connect to a server and enumerate its exports

See what an NFS host shares before attacking.

```
nfsshell
```

then, at the prompt:

```
host 198.51.100.10
export
```

Outcome: `nfsshell` opens the `nfs>` prompt; `host` sets (and resolves) the target, and `export` queries the mount daemon and lists every exported file system with its access flags. `status` prints a general report of the current connection state.

### Mount an export and browse it as a chosen user

Read files owned by an arbitrary UID.

```
mount /home
uid 1001
gid 1001
ls -l
cd .ssh
get id_rsa
```

Outcome: `mount` attaches the export inside the shell (no local mount point needed); `ls -l` initially shows directory entries with their numeric owner — if `.ssh` is owned by UID 1001, setting `uid`/`gid` to match makes the server treat you as that user, so `ls`, `cat`, and `get id_rsa` succeed and the key lands in your local working directory. `umount` detaches when done.

### Talk NFS on a nonstandard port or different transport

Handle servers where mountd/nfsd are not where expected.

```
mount -P 2049 /export
```

Outcome: `-P` directs the mount at a specific port instead of resolving it through the portmapper, which helps when rpcbind is filtered or nfsd listens nonstandard (the mount command also accepts transport selector flags; `help mount` from the prompt shows the current set). Combined with `dump` (list clients the server believes have file systems mounted) this is useful for probing NFS configs that portmap enumeration misses.

*No corpus evidence — derived from upstream documentation.*
