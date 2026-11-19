---
technology: iSCSI
kind: overview
audience: offensive-security
status: upstream-only
family: storage
layer: block-storage transport (SCSI over TCP)
github-repo: https://github.com/open-iscsi/open-iscsi
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — protocol facts verified against RFC 7143 (SendTargets discovery sessions, AuthMethod negotiation incl. AuthMethod=None, CHAP-with-MD5 mandatory algorithm, IPsec mandatory-to-implement guidance) and the open-iscsi upstream project (iscsiadm discovery/node model, node database layout) + rewritten from HackTricks (src/network-services-pentesting/3260-pentesting-iscsi.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# iSCSI

> The SAN that runs on plain TCP: one unauthenticated discovery query names every target, and a target that never turned on CHAP hands you the raw block device — deleted files, database extents and all.

## What it is

iSCSI wraps SCSI commands in TCP so a standard Ethernet network can carry a storage-area network. A client — the **initiator** — connects to a **target** on TCP **3260**, negotiates a login, selects a logical unit (**LUN**), and receives a block device the operating system treats exactly like a locally attached disk. Targets are named by **IQN** (`iqn.<yyyy-mm>.<reverse-dns>:<identifier>`), so a single discovery answer tells you vendor lineage and internal naming conventions for free.

The protocol (RFC 7143, consolidating the older RFC 3720 family) makes CHAP support mandatory to implement — CHAP with MD5 is the one algorithm every compliant peer must offer — but it does not force anyone to *use* authentication: `AuthMethod=None` is a legal login, and the RFC's own answer to confidentiality and integrity is IPsec ESP, which is likewise mandatory to implement and rare in practice. The gap between "must implement" and "must use" is the entire attack surface: exposed targets routinely accept logins with no credentials at all.

The operator takeaway: a misconfigured iSCSI target is not an information disclosure, it is a disk handover. You get the block layer — filesystem metadata, deleted-but-unallocated content, LVM and dm-crypt headers, raw database files — which is why the looting phase of an iSCSI finding is filesystem forensics, not service enumeration.

## The anatomy that matters on an engagement

    recon → 3260/tcp
      1. discovery session (pre-auth):  SendTargets text request
         → every target IQN + every portal address/port it advertises
      2. per-target login:              AuthMethod negotiated (None or CHAP)
         → success attaches the LUN as a local block device (/dev/sdX)
      3. loot:                          read-only mount, LVM scan, or raw image
    initiator state → /etc/iscsi/nodes/<iqn>/<portal>,<port>,<tpgt>/default
      persistent node records keyed by IQN *and* portal

Three structural properties drive findings here: (1) SendTargets discovery is a pre-authenticated information leak — targets advertise portal addresses, including internal ones that differ from the address you probed; (2) authentication is a deployment decision, and "CHAP is supported" says nothing about whether a given target demands it; (3) once logged in you are below the filesystem, so every storage-layer mistake — stale LUNs, snapshot exports, shared cluster volumes — is in scope.

## Where you'll meet it

- **Enterprise arrays and NAS appliances** exposing LUNs to virtualization clusters — the array's management ACL and the iSCSI access list are different settings, and only one of them gates your login.
- **Virtualization hosts** whose datastores are iSCSI — a LUN holding VM disk images is a full-estate exfil primitive in a single login.
- **Windows estates** using the built-in Microsoft iSCSI Initiator — the same target is reachable from a Windows foothold through PowerShell, no Linux tooling required.
- **Backup and DR appliances** presenting restored or replicated volumes as targets — habitually less hardened than the production arrays they shadow.

## Reading the framework's mind

1. `nmap --script iscsi-info` answers the only question that matters before you touch the target: does this portal require authentication? (see [[iscsi-bug-bounty]] Phase 0)
2. A SendTargets answer advertises portals you did not ask about — internal addresses behind NAT or a virtual IP are a free map of the storage network, and a login trap for the unwary.
3. Open-iSCSI keys each node record by IQN *and* portal; when discovery advertises an unreachable address, you create an explicit record for the reachable portal instead of fighting the generated one.
4. With no CHAP, login is one command and the LUN appears in `lsblk` — from there, mount **read-only** (`-o ro,noload`) or image the device; never let a kernel replay a journal onto evidence.
5. Exfil framing: prefer imaging (`dd`-class tooling) over live mounting when the rules of engagement allow — the image can be carved offline for deleted files with zero write-back risk to a production LUN.

## Where to go next

The companion methodology: [[iscsi-bug-bounty]] — discovery, auth posture, the NAT portal trap, LUN mounting on Linux and Windows, and the loot/exfil path.
