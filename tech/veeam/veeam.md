---
technology: "Veeam Backup & Replication"
kind: overview
audience: offensive-security
status: upstream-only
family: backup
layer: data protection / Windows server software
homepage: https://www.veeam.com
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design — owner-approved bucket-2 new-unit wave 2026-11-13; CVE-2023-27532 gate verified against Veeam KB4424, Enterprise Manager advisory set against KB4581/NVD, credential-store and DPAPI model against helpcenter.veeam.com + rewritten from HackTricks (src/network-services-pentesting/pentesting-veeam-backup-and-replication.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Veeam Backup & Replication

> The Windows management plane that stores working credentials for everything it protects and complete disk images of the domain's most privileged hosts — compromise of the backup tier is functionally compromise of the tier it backs up.

## What it is

Veeam Backup & Replication (VBR) is the default enterprise backup product for vSphere, Hyper-V, and Active Directory estates: a Windows server (or cluster) that runs jobs, mounts repositories, and holds the **credential store** — the usernames and passwords for vCenters, ESXi hosts, guest-VM accounts, SQL instances, and tape/object storage, kept so unattended jobs can reach every protected system. That store is the crown jewel: it is the one place an operator can expect to find privileged credentials for the *production* tier, collected conveniently in one configuration database (`VeeamBackup` on the local Microsoft SQL Server instance historically, with PostgreSQL as an alternative from v12). Sensitive columns are protected with DPAPI and Veeam's own database encryption, which stops you decrypting a stolen copy on your own box — but on the backup server itself the service must decrypt them to function, so local Administrator/SYSTEM compromise yields the plaintext set.

The backup tier also inherits every normal Windows attack path: an NTLM relay onto the server, a reused local-administrator password, an unencrypted WSUS channel feeding it patches. Any of those becomes access to the stored credentials and to the repositories.

The operator framing that organizes everything below: **backup-host compromise is domain-compromise-adjacent by design**. Not because Veeam is broken, but because the product's job is to hold (a) credentials powerful enough to read every protected host and (b) full disk images of Domain Controllers. Defenders who keep Domain Admin credentials out of the credential manager and DCs on immutable repositories blunt exactly this; most estates do not.

## The anatomy that matters on an engagement

    production (vCenter/ESXi/AD/SQL) ◄──jobs── VBR server ──► repositories (.vbk/.vbm/.vib, .vhd/.vhdx/.vmdk)
    VBR server holds:
      config DB (VeeamBackup; MS SQL or PostgreSQL) → dbo.Credentials rows (DPAPI-protected)
      Veeam.Backup.Service.exe — TCP 9401 default (CVE-2023-27532 surface)
      console (admin) → credential manager, job editor, "add vSphere server" wizard
    attacker paths onto the box:
      generic Windows: NTLM relay / reused local admin / WSUS http / host vulns
      network-reachable service: TCP 9401 (pre-patch: unauth credential pull)
    payoff on the box or on exposed backups:
      stored credentials (DPAPI-protected, decryptable on-box) → production tier
      DC backup image → ntds.dit + SYSTEM hive → the domain

Structural properties that drive findings: (1) the credential store is a one-stop shop for the production tier — every recovered account is a separate trust path to follow; (2) backup files concentrate the domain's disks on SMB shares whose ACLs are routinely looser than the servers they mirror; (3) the same console that decrypts credentials will *voluntarily send them to an endpoint of your choosing* (the rogue-vSphere capture) — intended behavior, not a bypass.

## Where you'll meet it

- **Mid-to-large enterprises and MSPs** — VBR servers are domain-joined Windows boxes in the "management" VLAN, often administered by storage/backup staff rather than security.
- **Exposed repository shares** — NAS devices holding `.vbk`/`.vhdx` trees, reachable with guest or weak credentials from userland networks.
- **After any foothold in the management tier** — the backup server is a priority pivot precisely because its compromise converts to production credentials plus recovery-point destruction (the ransomware double-blow).

## Reading the framework's mind

1. Port 9401 is the service socket to fingerprint (`Veeam.Backup.Service.exe` by default); pre-patch builds answered an unauthenticated request for the configuration database's credential rows (CVE-2023-27532, fixed in the March 2023 builds — every earlier version is affected).
2. DPAPI protects the store only off-box: on the backup server, tooling (NetExec's `veeam` module over SMB) reads the registry configuration, finds the MS SQL or PostgreSQL layout, and runs the recovery script remotely.
3. The console is a credential oracle: add an attacker-controlled host as a "vSphere server" and select a stored credential during the wizard — Veeam retrieves the plaintext and presents it to your listener (veeampot technique; VeeamThief implements the v12+ `RetrieveInternalContent` responses).
4. Job creation is a decryption order: a console admin can start a new job for a Domain Controller with encryption disabled, run it once, and walk off with an unencrypted `.vbk` — strong encryption on old restore points is irrelevant to the copy you just caused to exist.
5. Veeam Backup Enterprise Manager (the optional web front end) carried its own 2024 advisory set (KB4581: CVE-2024-29849 through CVE-2024-29852, including an unauthenticated log-in-as-any-user flaw) — enumerate it separately; it is a different service with a different exposure.

## Where to go next

The companion methodology: [[veeam-bug-bounty]] — service fingerprinting, credential recovery on-box and over the network, the rogue-vSphere capture, unencrypted-backup job creation, offline `.vbk`/image extraction to [[ntds]], and recovery-destruction testing boundaries. Related units: [[smb]] (repository shares), [[ntlm]] (relay onto the server), [[mssql]] (config DB), [[windows-secrets]] (DPAPI).
