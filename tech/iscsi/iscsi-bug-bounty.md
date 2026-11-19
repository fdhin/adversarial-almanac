---
technology: iSCSI
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: storage
version: "1.1"
last-refresh: "2026-11-19"
provenance: "upstream-only; zero corpus evidence by design — command surface grounded against the open-iscsi upstream project (iscsiadm discovery/node modes, node database layout, NAT portal guidance) and RFC 7143; the finding class is misconfiguration, not a CVE chain + rewritten from HackTricks (src/network-services-pentesting/3260-pentesting-iscsi.md), CC BY-NC 4.0, accessed 2026-11-13. v1.1 supplement 2026-11-19 (model gpt-6.1-sol via M365 Copilot senior-review pass, operator framing; claims dual-verified against github.com/open-iscsi releases + GHSA-34x6-w2v9-6h68, nmap NSE/nselib source + issue #1313, learn.microsoft.com, kernel.org/man7 xfs(5)+ext4 docs, and RFC 7143 text fetched directly): discovery-vs-normal authentication separation, seven-milestone evidence ladder, nonpersistent discoverydb procedure, nmap iscsi-info mislabel caveats, session-to-device mapping with sg_luns/sg_readcap, filesystem-specific read-only mounts (ext4 noload vs XFS norecovery/nouuid), blockdev --setro before mount, storage-layer decision tree, ddrescue mapfile discipline, Windows disk-policy-first procedure, initiator-side attack surface (open-iscsi CHAP disclosure <2.1.13), tiered reporting, cleanup order, silent-failure table."
---

# iSCSI bug-bounty methodology

Companion overview: [[iscsi]].

Scope: TCP 3260 targets, the SendTargets discovery surface, unauthenticated or weak-CHAP LUN access, and the filesystem-level loot that follows a successful login. Sibling storage surfaces with their own units: [[nfs]].

Two authentications, not one: SendTargets runs inside an iSCSI **discovery session** whose login can itself demand CHAP, and discovery authorization is a separate control from **normal-session** authorization (RFC 7143 §4.3, §6.3). Never infer one from the other. Transport note: HeaderDigest/DataDigest are CRC-based integrity checks — not encryption, not cryptographic peer authentication; confidentiality needs encrypted transport (the RFC's own IPsec guidance).

Evidence ladder — report which milestone you actually reached:

1. TCP reachable → 2. discovery login accepted → 3. SendTargets returned names/portals → 4. normal login accepted → 5. logical units enumerated → 6. block reads succeeded → 7. content interpreted. A no-CHAP login proves milestone 4 **only** — not a readable LUN, not sensitive data, not write.

Toolchain currency: open-iscsi **2.1.13** (2026-09-08) is upstream latest — record `iscsiadm --version` plus the distro package revision. GHSA-34x6-w2v9-6h68 (fixed 2.1.13): a **local** CHAP-disclosure issue on the *initiator* side (unprivileged local exec + privileged iscsiadm operation) — your assessment client is itself attack surface when a target controls discovery/login responses. 2.1.10+ accepts hostnames in node mode; older clients fail where a numeric portal works.

## Phase 0 — Discovery and auth posture

```bash
nmap -Pn -sV -p 3260 --script iscsi-info --reason 198.51.100.10
sudo iscsiadm -m discoverydb -t sendtargets -p 198.51.100.10:3260 \
  --op nonpersistent --discover
```

The `discoverydb … nonpersistent` form discovers **without writing node records** (plain `-m discovery` creates/updates/deletes records). Nmap caveats, verified in the script source: `iscsi-info` maps *any* failed no-auth login to "Authentication: required" (connection failures included — not a policy statement), probes only the scanned host/port, can collapse multi-address replies, and sends a **hard-coded initiator IQN** (`iqn.1991-05.com.microsoft:nmap_iscsi_probe`, nmap issue #1313) that identity-sensitive arrays reject with no output. Treat NSE output as a lead; the iscsiadm exchange is the evidence.

```bash
# nonpersistent discovery output shape:
# 198.51.100.10:3260,1 iqn.1992-05.com.emc:fl1001433000190000-3-vnxe
# [2001:db8::1]:3260,1 iqn.2000-01.com.example:target-1.d0280fd382
```

Read it as recon: each line names a portal (possibly an internal address behind NAT) and a target name. IQN naming authority *suggests* a vendor but is not product identification, and the date segment reflects domain ownership, not device age; `eui.` and `naa.` names also exist. Preserve complete target names — trailing hex segments are part of the identity. Internet-wide recon: Shodan-style `port:3260` queries.

## Phase 1 — Login and credential attacks

Attempt the no-CHAP login first; milestone-4 success is the finding:

```bash
iscsiadm -m node --targetname 'iqn.2000-01.com.example:target-1.d0280fd382' \
  --portal 198.51.100.10:3260 --login
```

If CHAP is required, credentials live in the node record (`node.session.auth.authmethod`, `.username`, `.password`, `_in` variants for mutual CHAP). Array-wide CHAP secrets are a shared-service credential-attack surface like any other. Two state classes juniors conflate: node records hold **saved/intended** configuration; `iscsiadm -m session -P 3` shows **actually negotiated** parameters and current portals. The upstream node-database root is `/var/lib/iscsi` (nodes under `/var/lib/iscsi/nodes`); `/etc/iscsi` remains common in distro builds — never hard-code the path.

Identity work: LUN masking and ACLs often key on initiator IQN or source IP. A controlled identity test binds a dedicated interface record (`iscsiadm -m iface -I assessment --op update -n iface.initiatorname -v '<TEST_IQN>'`, then `-I assessment` on discovery/node ops) — but an IQN change does not defeat an independent source-IP ACL, and IQN allowlists are identification, not authentication.

## Phase 2 — The NAT portal trap

A SendTargets reply may advertise a portal different from the discovery address (NAT, VIP). Advertised portal and **login redirection** are different mechanisms — a reachable VIP can accept login then redirect somewhere unreachable; compare `Current Portal` vs `Persistent Portal` in session output. Create an explicit static record for the reachable portal (preserve the TPGT — `IP:port,TPGT` is the record key; omitting the tag changes handling):

```bash
iscsiadm -m node --op new \
  --targetname 'iqn.2000-01.com.example:target-1.d0280fd382' \
  --portal 198.51.100.10:3260,1
iscsiadm -m node --targetname 'iqn.2000-01.com.example:target-1.d0280fd382' \
  --portal 198.51.100.10:3260,1 --login
```

Direct node-database surgery under the nodes tree is the exceptional path, not the default — database root, directory keys, and rediscovery behavior all vary by build. Log out cleanly with `--logout` when finished.

## Phase 3 — LUN loot (Linux)

Map session → device before touching anything (a target can expose zero, one, or many LUs; not every LU is a disk; multiple paths can be one LUN):

```bash
sudo iscsiadm -m session -P 3          # includes SCSI device mapping
lsscsi -g; lsblk -o NAME,PATH,TYPE,SIZE,RO,FSTYPE,UUID,WWN,SERIAL,MOUNTPOINTS
ls -l /dev/disk/by-path/               # select by session's stable identifiers
# expected devices missing:
sudo iscsiadm -m session -r <SID> --rescan
# SCSI-level inventory and geometry:
sudo sg_luns --readonly /dev/sgN       # REPORT LUNS: what this nexus sees
sudo sg_readcap --readonly /dev/sgN    # block size + capacity (count = last LBA + 1)
```

Signal interpretation: login OK + no disk = presentation policy or non-disk LU; LUN visible + reads rejected = SCSI access state or reservation (RESERVATION CONFLICT = cluster reservation, not filesystem damage); repeated hangs = recovery/multipath queuing.

Read-only discipline starts **before** mounting, at the block layer:

```bash
sudo blockdev --setro "$DEV"; blockdev --getro "$DEV"   # expect 1
sudo blkid -p "$DEV"; sudo wipefs -n "$DEV"             # identify layers first
```

Filesystem-specific read-only mounts (skipped log replay can leave content inconsistent or inaccessible — that is expected, not a failed mount):

```bash
mount -t ext4 -o ro,noload,nodev,nosuid,noexec "$PART" /mnt/loot    # ext3/4
mount -t xfs  -o ro,norecovery,nodev,nosuid,noexec "$PART" /mnt/loot  # add nouuid only for duplicate UUIDs
```

Storage-layer decision tree — "mount failed" means *identify the next layer*, not "no data": whole-device filesystems (no partition table), LVM PVs/thin pools (read-only scan `pvscan`/`pvs --readonly`; never blanket `vgchange -ay`), MD RAID members, VMFS/NTFS/ReFS/cluster filesystems, vSphere/VM disk containers, LUKS/BitLocker (note and move on), external journals (XFS log/realtime devices).

Imaging — safer *analysis*, not automatically a consistent acquisition (a live sequential image is not a point-in-time snapshot):

```bash
sudo ddrescue -n "$DEV" lun-image.raw lun-image.map
sha256sum lun-image.raw > lun-image.raw.sha256
```

Keep the mapfile; inspect unresolved regions before claiming completeness (a hash proves file identity, not source consistency or completeness).

## Phase 4 — LUN loot (Windows)

Disk policy **before** connection (newly seen disks stay offline; the policy is not retroactive):

```powershell
$Prev = (Get-StorageSetting).NewDiskPolicy
Set-StorageSetting -NewDiskPolicy OfflineAll
Start-Service MSiSCSI
New-IscsiTargetPortal -TargetPortalAddress 198.51.100.10
Get-IscsiTarget
$S = Connect-IscsiTarget -NodeAddress '<COMPLETE_TARGET_NAME>' `
    -TargetPortalAddress 198.51.100.10 -TargetPortalPortNumber 3260 `
    -AuthenticationType NONE -IsPersistent $false
Get-IscsiSession
Get-Disk | Select-Object Number,UniqueId,SerialNumber,BusType,Size,IsOffline,IsReadOnly,OperationalStatus
```

`-AuthenticationType` accepts `NONE`, `ONEWAYCHAP`, `MUTUALCHAP` — uppercase, per the docs. `-IsPersistent $false` controls connection persistence, **not** read-only access; set the disk read-only attribute before any deliberate online operation. Disconnect with `Disconnect-IscsiTarget -NodeAddress '<IQN>' -Confirm:$false`, restore `NewDiskPolicy` to `$Prev`. `iscsicpl.exe` / `iscsicli` drive the same operations.

## Phase 5 — Reporting

Tier the evidence: target-name/topology disclosure → no-CHAP normal session → unintended LUN presentation → demonstrated block reads → demonstrated sensitive-data exposure → demonstrated write (only if separately established). Never infer write from a read-only mount, or "no sensitive data" from an unsupported filesystem. Block access bypasses the exported filesystem's file-server permissions but not encryption. Remediation block: strong scoped CHAP + enforced mutual authentication where supported, initiator-specific LUN masking, portals off untrusted networks, encrypted transport where confidentiality matters — a VLAN is segmentation not encryption; an IQN allowlist is identification not proof of identity; stale/snapshot/DR LUNs are data exposures in their own right.

## Missed attack surface (add to every engagement)

- Discovery authorization vs normal-session authorization (test both).
- Initiator-name ACLs vs cryptographic authentication (different bypass classes).
- Per-initiator LUN masking — unintended snapshots, clones, backup, DR exports.
- Alternate portals, TPGTs, IPv6 paths, login redirects.
- Per-target/per-initiator CHAP scope, mutual enforcement, secret reuse across targets.
- SCSI command authorization beyond mountability (reservations, pass-through).
- Unencrypted transport exposure independent of CHAP success.
- **Initiator-side attack surface**: untrusted discovery/login responses and local IPC — the open-iscsi CHAP disclosure (<2.1.13) proves the assessment client belongs in the threat model.

## Silent failure modes

| Failure mode | Consequence |
|---|---|
| nmap "Authentication: required" trusted | Connection failure misread as CHAP policy |
| NSE's hard-coded IQN rejected by target | No output read as "no targets" |
| Discovery visibility = login authorization | Separate controls conflated both ways |
| Advertised-NAT portal retried forever | Static-record-for-reachable-portal fix never applied |
| `/dev/sdb1` assumed from discovery order | Wrong device; multipath and multi-LUN targets |
| ext4's `ro,noload` used on XFS | Journal replay writes to a production LUN |
| `mount` failure read as "no data" | LVM/RAID/VMFS/LUKS layer never identified |
| Live image treated as point-in-time | Inconsistent evidence overstated |
| Deleting node records as "cleanup" | In-use record deletion logs out its session (upstream-documented) |
| `nc` hand-rolled PDUs | Binary, stateful, sequenced protocol — false negatives |

## Cleanup (order matters)

Stop acquisition/analysis processes → unmount → `iscsiadm -m session -r <ASSESSMENT_SID> --logout` → verify session gone (`iscsiadm -m session`) → remove only assessment-created node/iface/discovery records → restore host policy (disk policy, automount). Never delete a node record as a shortcut while its session lives.

## Appendix — tools in prose

`nmap` (iscsi-info NSE — with the labeling/IQN caveats above), `iscsiadm`/`iscsid` from open-iscsi (nonpersistent discoverydb mode; session -P 3 mapping), `sg3-utils` (`sg_luns`, `sg_readcap`) for SCSI-level inventory, `lsblk`/`lsscsi`/`/dev/disk/by-path` for device triage, `blockdev` for read-only enforcement, `ddrescue` (mapfile!) over plain `dd` for imaging, loop-mount/LVM-read-only/caving tooling offline, Windows iSCSI PowerShell module + `Set-StorageSetting` disk policy, Shodan-style queries for exposure mapping. Hand-rolled `nc` PDUs are a false-negative generator against a stateful binary protocol — use an iscsiadm-speaking client.

## References (v1.1 verification trail)

- github.com/open-iscsi/open-iscsi: releases (2.1.13 2026-09-08, 2.1.12 2026-07-15 incl. security PR #535, 2.1.10 2024-05-31 hostname support), README (nonpersistent discoverydb; /var/lib/iscsi database; record-deletion-logout behavior), GHSA-34x6-w2v9-6h68 (CHAP disclosure <2.1.13, local-exec prerequisite).
- nmap.org / nmap GitHub: scripts/iscsi-info.nse (failure→"required" mapping, address collapsing) and nselib/iscsi.lua line 582 (hard-coded `iqn.1991-05.com.microsoft:nmap_iscsi_probe`), issue #1313 (open).
- learn.microsoft.com: Connect-IscsiTarget (AuthenticationType uppercase values), Set-StorageSetting NewDiskPolicy OfflineAll (newly attached disks, not retroactive).
- kernel.org / man7: xfs(5) norecovery + nouuid; ext4 mount options ro,noload (journal replay writes without noload).
- RFC 7143 (fetched directly): §4.3 session types, §6.3 login security negotiation, digest-as-CRC ("data segment may each be followed by a CRC"), §9.3/IPsec guidance for confidentiality.
