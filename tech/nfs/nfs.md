---
technology: "NFS"
kind: overview
audience: offensive-security
family: network-services
layer: distributed file system (2049/tcp NFS + 111 rpcbind/mountd; UDP for v2/v3)
standards: "RFC 1094 (NFSv2), RFC 1813 (NFSv3), RFC 7530/8881 (NFSv4), exports(5)"
status: confirmed
provenance: "corpus-grounded (13 writeup files under command-context gate: showmount/mount -t nfs//etc/exports/no_root_squash markers, 13 files) + upstream-verified (exports(5) man page root_squash semantics and defaults, RFC 7530 ACL/auth model). Enrichment pass 1, 2026-11-12."
---

# NFS

> A file server with no per-user authentication at all: the protocol trusts whatever UID and GID the client puts in the RPC request, so whoever controls a mount controls the identity it arrives as — and `no_root_squash` hands them root.

## What it is

NFS (Network File System, originally Sun) is the standard Unix network filesystem: a server *exports* directories listed in `/etc/exports`, clients mount them over RPC (portmapper/rpcbind on 111 points the way, mountd/nfsd on 2049 and dynamic RPC ports do the work; NFSv4 consolidates on 2049/tcp). There is no credential exchange in the classic protocol — every request carries raw numeric UID/GID values in AUTH_SYS flavor, and the server enforces file permissions *against those numbers*. The server's only real gate is the export list: which client IPs may mount, read-write or read-only, and how user IDs get mapped.

The operator takeaway: on NFSv2/v3 with default `sec=sys`, "authorization" is a table entry plus arithmetic. The three questions that matter are which hosts the export allows, what it does to UID 0 (`root_squash` maps root→anonymous 65534 by default; `no_root_squash` does not), and whether anyone sensitive trusts files that land on the export.

## The anatomy that matters on an engagement

    client → rpcbind(111) → mountd → mount export → nfsd(2049) → file ops carrying raw UID/GID
        AUTH_SYS: server checks export ACL → maps uids (root_squash/all_squash/anonuid) → POSIX check
        NFSv4: same port, Kerberos-capable (sec=krb5/krb5i/krb5p possible), ACLs instead of mode bits

Three structural properties drive most findings:

- **Client-supplied identity.** exports(5) states it plainly: "nfsd bases its access control... on the uid and gid provided in each NFS RPC request." Create a local user with the target UID and the server treats you as that user (observed: craft UID 2017 locally → own the web root; other chains: same UID/GID trust over pivoted mounts).
- **Squashing is per-export and usually incomplete.** `root_squash` (the default) protects only UID 0; every *other* UID arrives unmapped. `no_root_squash` removes even that — the direct RCE class below.
- **Enumeration is unauthenticated.** showmount lists exports without any credential; the export table itself is recon output.

## Where you'll meet it

- **Internal Linux fleets** (observed majority): backup shares, web roots, home directories exported too widely — one observed chain leaked full application source from `/mnt/backups`; others exposed general-purpose shares that fed footholds.
- **Pivot targets** (observed): NFS bound to internal interfaces — enumerate via the pivot with showmount against the internal address; the export ACL likely allows the internal range you now sit in.
- **Windows services for NFS** (2 observed engagements): unauthenticated shares with `vers=3` mounts from Linux; one helpdesk share carried crackable credentials. Windows-hosted exports often map anonymous to a real SID — expect different squashing semantics.
- **HPC/dev clusters**: `no_root_squash` left in because diskless clients or legacy tooling needed it.

## Reading the protocol's mind

1. **`showmount -e host` answers with the export table pre-auth** — `*` in the client column means everyone (observed: two globally accessible shares).
2. **UID arithmetic beats passwords** — mount, `ls -ln` the tree, note owner UIDs, become them locally. Non-root UIDs are squashed only under `all_squash`.
3. **`no_root_squash` on a writable export = root file write** — the suid-shell chain in the methodology page.
4. **NFSv4 changes the auth story**: `sec=sys` is still default (no crypto), but krb5* flavors exist; v4 ACLs are Windows-model ALLOW/DENY ACE lists (RFC 7530), not POSIX mode bits — check which the server enforces before assuming `chmod` semantics.
5. **Filehandles outlive export edits** — v3 filehandles can keep working after exports change; subdirectory exports carry the subtree_check guessing caveat from exports(5).

## Where to go next

[[nfs-bug-bounty|NFS bug bounty methodology]] — export enumeration, the UID/GID impersonation workflow, the no_root_squash suid chain, NFSv4 ACL differences, and the export misconfiguration matrix.
