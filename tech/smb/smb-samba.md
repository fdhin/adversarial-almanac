---
technology: "SMB — Samba implementation"
kind: implementation
audience: offensive-security
family: directory-services
layer: "protocol implementation (file/print server, optional AD DC)"
language: C
homepage: "https://www.samba.org/"
github-repo: "https://gitlab.com/samba-team/samba"
provenance: "evidence-grounded (13 files pass the Samba-side gate: smb.conf/testparm/smbpasswd/pdbedit/nmbd/smbd/username map/map to guest/wide links/unix extensions/Samba version strings) + upstream-verified: NVD records for CVE-2007-2447 (username map script RCE), CVE-2015-0240 (Netlogon uninit-free RCE), CVE-2010-0926 (symlink traversal); samba.org symlink-attack advisory referenced from the CVE record. Note: smb.conf evidence splits into TARGET-side misconfiguration and ATTACKER-side share hosting — only the former is target attack surface. Companion router: [[smb|SMB]]."
---

# SMB — Samba

> A config file with a network protocol attached: every dangerous behavior here is one `smb.conf` directive away, and Samba's version history includes pre-auth root RCEs you can date from the banner.

## What it is

Samba's `smbd` speaks SMB on 445/139, `nmbd` handles NetBIOS naming on 137/138, and `winbindd` bridges domain identities. One daemon triple, three roles chosen by `server role` in `/etc/samba/smb.conf`: **standalone server** (the classic NAS case), **domain member** of an AD forest, and **full AD DC** (where Samba also runs LDAP, Kerberos and DNS — meeting the directory-services units from the other side). The attacker-relevant truth is that Samba's security posture is **declarative**: shares do exactly what the config says, and operators routinely write configs that say too much — `guest ok = yes`, `force user =`, `wide links = yes` — while the version banner in nmap (`Samba smbd 4.6.2`) dates the daemon against a small set of famous memory-corruption bugs.

Fingerprint first: nmap's service line on 139/445 (`netbios-ssn Samba smbd 4.6.2`, observed; `Samba smbd 3.X - 4.X` range form also observed), the `IPC$` share remark (`IPC Service (Samba 4.13.13-Debian)`), and `testparm` output once you have a shell. The banner range matters: Samba prints a **range** (`3.X - 4.X`) when version probing was inconclusive — treat it as "old enough to matter" and confirm from inside.

## The anatomy that matters on an engagement

    banner: "Samba smbd X.Y"  ──►  date the daemon ──►  known-RCE check
    config: /etc/samba/smb.conf (+ includes like shares.conf)
      [global]: map to guest | unix extensions | allow insecure wide links | username map
      [share]: guest ok / guest only | force user | read only | path | wide links
    accounts: passdb (pdbedit/smbpasswd) | winbind | machine accounts
    abuse classes:
      version RCE: CVE-2007-2447 (username map script) · CVE-2015-0240 (netlogon free)
      config traversal: wide links + unix extensions=no  (CVE-2010-0926 class)
      identity confusion: force user / guest ok → files land as the wrong owner
      account ops: smbpasswd -r remote reset · impacket smbpasswd.py / changepasswd.py

Three structural properties drive most findings:

- **The config is the attack surface.** Shell on a Samba host = read `smb.conf` and every include before anything else; one observed chain is two directives (`force user = <user>` + `wide links = yes` with `unix extensions = no` and `allow insecure wide links = yes` in `[global]`).
- **Old daemons are public exploits.** CVE-2007-2447 and CVE-2015-0240 are banner-datable pre-auth/pre-cred RCEs; the evidence base is silent on them (modern targets), so treat them as **history that still ships in embedded/NAS estates**, verified upstream, not as the expected path.
- **Guest and force user invert ownership.** `guest ok` + `force user` means any anonymous write lands on disk as a real local user — file-planting primitives (SSH keys, cron files, `.bashrc`) follow.

## Version-to-CVE history (all verified upstream, absent from the evidence base)

- **CVE-2007-2447** — Samba 3.0.0–3.0.25rc3. MS-RPC in `smbd` passes shell metacharacters to commands: `SamrChangePassword` with the **`username map script`** smb.conf option enabled gives unauthenticated RCE (any username containing shell characters is executed when mapping); other MS-RPC paths (remote printer, file share management) give authenticated command injection. CVSSv2 6.0 AV:N/AC:M/Au:S. The metasploit module is `usermap_script`.
- **CVE-2015-0240** — Samba 3.5.x, 3.6.x < 3.6.25, 4.0.x < 4.0.25, 4.1.x < 4.1.17, 4.2.x < 4.2.0rc5. The Netlogon server implementation frees an **uninitialized stack pointer** in `_netr_ServerPasswordSet` (`rpc_server/netlogon/srv_netlog_nt.c`) — crafted Netlogon packets, **no authentication**, code execution as root. CVSSv2 10.0.
- **CVE-2010-0926** — Samba before 3.3.11 / 3.4.6 / 3.5.0rc3, **default configuration** with a writable share: the `smbclient` `symlink` command plants a symlink containing `..` sequences; the combination of `unix extensions` and `wide links` lets the client read/write arbitrary files outside the share. Modern Samba keeps `wide links` off while unix extensions are on — unless `allow insecure wide links = yes` re-enables the class (observed: exactly that re-enable).

## Config directive watchlist

| Directive | Why it matters | Observed |
|---|---|---|
| `map to guest = Bad User` (etc.) | unknown usernames become guest — anonymous share access | attacker-side hosting; era defaults |
| `guest ok = yes` / `guest only = yes` | unauthenticated share access, writes if `read only = no` | attacker-side hosting |
| `force user = X` | all file ops run (and files are owned) as X regardless of client | observed |
| `wide links = yes` + `unix extensions = no` (+ `allow insecure wide links = yes`) | symlink escape from share tree to any path on the host | observed |
| `username map script = ...` | CVE-2007-2447 command injection if version ≤ 3.0.25rc3 | upstream only |
| `server role = active directory domain controller` | Samba also runs LDAP/Kerberos/DNS — pivot to those units | — |

Distinguish **attacker-side Samba**: operators stand up their own `smb.conf` with a `guest ok` share to move files to/from a Windows target (observed repeatedly). Seeing `smb.conf` edited on the attacker side is not itself a finding on the target — check which side of the connection the daemon runs on.

## Account operations surface

- **`smbpasswd`** — local passdb management; remotely, `smbpasswd -r 198.51.100.10 -U EXAMPLE\\user` performs a SAMR password *change* (observed).
- **Impacket `smbpasswd.py` / `changepasswd.py`** — SAMR reset over SMB from Linux; works against Samba and Windows alike. `smbpasswd.py -newpass` resets with the old password (observed: expired-password bind falls back to null session, reset still succeeds); `smbpasswd.py -newhashes :<LM>:<NT>` writes known hashes (observed). This is SAMR `SetUserInfo` riding SMB — the ACL story is in the ad-acl unit.
- **`pdbedit`** / **`testparm`** — post-shell: enumerate passdb users and dump the effective config (`testparm -s` resolves defaults — what smbd actually enforces).

## Workflow

```bash
# fingerprint + enum (protocol-common; see [[smb]] router)
nmap -p139,445 --script smb-os-discovery,smb-enum-shares 198.51.100.10
smbclient -L //198.51.100.10 -N ; smbmap -H 198.51.100.10

# version-dated RCE check (upstream-verified versions above)
# banner "Samba 3.0.x" + username-map/script hints → CVE-2007-2447 class
# banner "Samba 3.5–4.1 unpatched 2015 era" → CVE-2015-0240 class

# with a shell: the config IS the enumeration
testparm -s ; cat /etc/samba/smb.conf /etc/samba/shares.conf
grep -E 'map to guest|guest ok|force user|wide links|unix extensions|username map' /etc/samba/smb.conf

# symlink escape verify (lab-safe: touch a marker under /tmp on the target share)
smbclient //198.51.100.10/share -N -c 'symlink escape ../../../../tmp/marker marker; get marker'

# remote password reset (creds you own, target's SAMR)
python3 smbpasswd.py EXAMPLE.COM/user:'OldPass!'@198.51.100.10 -newpass 'NewPass!'
```

Expected outcomes: `testparm -s` prints the resolved config (every directive in the watchlist above is a finding); the symlink `get` succeeding proves traversal; the smbpasswd reset prints `Password was changed successfully` (observed output shape).

## Where to go next

Back to the [[smb|SMB router]] for protocol-common enumeration; the Windows counterpart is [[smb-windows-native|Windows-native SMB]].
