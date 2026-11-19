---
name: "ncpfs"
categories:
  - blackarch-networking
version: "2.2.6"
homepage: "http://www.novell.com/"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-31"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ncpfs

> Linux client for Novell NetWare: mount NCP volumes into the filesystem and administer NetWare servers from userspace.

## What it's for

ncpfs 2.2.6 is the classic, long-discontinued NetWare client suite for Linux (the ncpfs project, later maintained by Petr Vandrovec). It ships the `ncpmount`/`ncpumount`/`ncplogin` helpers that attach NetWare 3.x-and-later volumes into the Linux VFS, and a family of NCP utilities: `slist` to enumerate servers, `nwuserlist`, `nwvolinfo` and `nwauth` for server interrogation, the bindery/NDS object and property tools (`nwbols`, `nwbpvalues`, `nwtrustee`, ...), printing via `nprint` and `pserver`, plus `ipx_probe` for IPX-level diagnostics. Mounting requires IPX support, which mainline Linux removed in 4.18 — the kernel-side module survives only as a third-party out-of-tree effort — so on modern kernels the package is chiefly useful for the userspace protocol tools against emulated or preserved NetWare services. It explicitly does not support NetWare 2.x.

## Typical use cases

### List the NetWare servers visible on the network

```
slist
```

Outcome: prints every NetWare server reachable from your host with its full tree name; an optional pattern argument (for example `slist SALES*`) filters the list before you commit to mounting.

### Mount a NetWare volume

```
ncpmount -S FILES1 -U user -P Password1 /mnt/netware
```

Outcome: authenticates to server FILES1 as `user` and mounts its default volume at /mnt/netware (`-V` selects a different volume), after which NetWare files behave like local ones. Fails on kernels without NCP/IPX support.

### See who is logged in to a server

```
nwuserlist -S FILES1 -U user -P Password1
```

Outcome: prints each user logged in to the server with connection details — the NetWare counterpart of running `who` against the file server.

### Submit a file to a NetWare print queue

```
nprint -S FILES1 -q LASERQ -U user -P Password1 report.txt
```

Outcome: queues report.txt on print queue LASERQ of server FILES1 with banner options controllable by flags; a `pserver` on the NetWare side services the job.

*No corpus evidence — derived from upstream documentation.*
