---
name: "nfspy"
categories:
  - blackarch-automation
version: "1.0"
homepage: "https://github.com/bonsaiviking/NfSpy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "bonsaiviking/NfSpy"
github-stars: 301
github-pushed: "2019-12-03"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nfspy

> FUSE NFS client and ftp-like shell that forge arbitrary UID/GID credentials against NFSv3 servers.

## What it's for

NFS before version 4 authenticates only by the UID/GID a client claims, trusting the client machine to have verified its users. NfSpy exploits exactly that: it mounts an export while spoofing whatever UID/GID you choose, defeating `root_squash` (spoof a non-root UID that owns the interesting files) and letting plain users read other people's exports. It ships two clients — `nfspy`, which mounts the share through FUSE so normal tools (`grep`, `find`) work on it, and `nfspysh`, an ftp-like interactive shell that needs no FUSE and runs anywhere. NFSv3 only; mounts read-only by default because write support is beta (`rw` to override). Use it to demonstrate NFS trust misconfigurations after `showmount -e` has revealed exports.

## Typical use cases

### Mount an export with spoofed credentials and hide the trace

FUSE-mount using the classic option string.

```
showmount -e 198.51.100.10
sudo nfspy -o server=198.51.100.10:/home,hide,allow_other,ro,intr /mnt
sudo fusermount -u /mnt
```

Outcome: after `showmount` confirms the export, `nfspy` FUSE-mounts `/home` from the server on `/mnt`; `sudo` lets it bind a privileged port (for servers enforcing nfs_portmon), `allow_other` lets non-root users browse, and `hide` immediately "unmounts" on the server side while keeping the file handle — so `showmount -a` on the server no longer shows you. Any local UID's files are readable via ordinary `ls`/`cat`/`grep` under `/mnt` without owning that UID locally. Unmount with `fusermount -u` when done.

### Mount when portmapper and mountd are blocked

Skip the mount daemon using a sniffed file handle.

```
sudo tshark -n -i eth0 -T fields -e nfs.fhandle
sudo nfspy -o rw,server=198.51.100.10:,nfsport=2049/udp,dirhandle=<captured-file-handle>,getroot mnt
```

Outcome: `tshark` (or `tcpdump -u port 2049`) sniffs a live NFS conversation to recover a directory file handle; NfSpy then talks straight to nfsd on port 2049 (`nfsport` avoids the portmapper, `dirhandle`/`getroot` avoid mountd), mounting the directory the handle refers to at `mnt`. Work quickly — stale handles expire — and note the handle must point at a directory.

### Browse an export interactively without FUSE

ftp-style shell on any platform.

```
sudo nfspysh -o server=198.51.100.10:/home
```

Outcome: opens the `nfspy@198.51.100.10:/home:/>` prompt supporting `ls`, `cd`, `get <file>`, `put`, `mkdir`, `rm`, `chmod`, `chown`, `umask`, `lcd`/`lpwd`, and `help`; `get id_rsa` copies a remote file into your current directory. Options are the same comma-separated format as the FUSE client, and there is no read-only mode — writes take effect immediately.

*No corpus evidence — derived from upstream documentation.*
