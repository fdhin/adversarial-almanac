---
technology: "SMB (Server Message Block)"
kind: overview
audience: offensive-security
family: directory-services
layer: protocol
language: "C (Windows srv2.sys / Samba smbd); clients everywhere"
homepage: "https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/"
github-repo: "https://github.com/samba-team/samba"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/137-138-139-pentesting-netbios.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "evidence-grounded (95 files pass the re-gated command-context gate: SMB-bound client invocations, Samba admin/config markers, SMB attack/fingerprint strings, or null-session with an SMB client — 127 files text+ocr; structure-pass hint was 32 files gated on smbclient/smbmap/smbexec/enum4linux only) + upstream-verified: NVD CVE-2007-2447, CVE-2015-0240, CVE-2010-0926 JSON records, Microsoft MS17-010 bulletin, Microsoft MS-SMB2 openspecs landing, 2026-11-12. This page is the ROUTER: protocol-common surface + dispatch to the implementation pages [[smb-samba|Samba]] and [[smb-windows-native|Windows-native]]."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# SMB

> One port, two species: everything above the dialect negotiation is shared, and almost every finding lives below it — fingerprint the implementation first, then dispatch.

## What it is

SMB is the file-and-print (and named-pipe, and IPC) protocol of the Windows world, re-specified publicly as [MS-SMB2] with SMB1 as the legacy CIFS dialect. It listens on **445/tcp** directly (nmap service `microsoft-ds`) and, on legacy hosts, on **139/tcp** wrapped in NetBIOS (`netbios-ssn`). The negotiation on connect fixes your whole engagement: the **dialect** (SMB1 `NT LM 0.12` → SMB2.x → SMB3.x), whether **signing** is required, and whether **anonymous/guest sessions** get a foot in the door.

Two implementations own the deployed world — **Windows' own server stack** (the Server Service/LanmanServer and the srv2.sys driver) and **Samba** (smbd, the open-source re-implementation that also powers most Linux/NAS filers and can act as a standalone server, domain member, or full AD DC). They interoperate but do not misconfigure the same way: Samba leaks through its **config file** (`smb.conf` directives like `map to guest`, `force user`, `wide links`) and its version history of pre-auth RCEs; Windows leaks through **account policy** (guest enabled, null-session enumeration, SMBv1 left on) and relay-enabling signing gaps. Both implementations are common in the field, so each gets its own page.

The router logic for an operator is short: **banner + probe → implementation → dispatch**. `Samba smbd 4.6.2` in the nmap line, an `IPC Service (Samba …)` share remark, or any `smb.conf` artifact on the box means the [[smb-samba|Samba page]]; a Windows build string, the crackmapexec/netexec probe line `(signing:True) (SMBv1:False)`, or guest/null behavior on a Windows host means the [[smb-windows-native|Windows-native page]]. Everything below — enumeration workflow, null sessions, share triage — you run identically on both.

## The anatomy that matters on an engagement

    connect 445/tcp (or 139/tcp netbios-ssn)
      → NEGOTIATE   : dialect table  → SMB1 / 2.002 / 2.1 / 3.0 / 3.0.2 / 3.1.1
                      → signing capability, encryption (SMB3)
      → SESSION SETUP : anonymous / null session / guest / NTLM / Kerberos
                      → this is where "no auth needed" findings are born
      → TREE CONNECT : share enum + access (READ/WRITE per credential)
                      → IPC$ = RPC over SMB (SAMR/LSAT/SRVS pipes)
      → file ops / named pipes / printer RAP
    dispatch:
      banner says Samba            → [[smb-samba]]     (smb.conf, version RCE history)
      banner says Windows build    → [[smb-windows-native]] (guest, SMBv1/MS17-010, signing)

Three structural properties drive most findings:

- **The dialect table is the target's confession.** SMBv1 still answering on a 2008-era host (observed: `SMBv1:True` on Server 2008 R2) is an exploit-class signal, not a cosmetic one; SMB3.1.1 with signing required tells you relay is dead there.
- **Authentication is a ladder, and estates skip rungs.** Anonymous → null session → guest → cred-spray all ride the same SESSION SETUP; observed estates still hand shares to `-u 'guest' -p ''` against Windows Server 2022 DCs.
- **IPC$ is a door into everything directory-ish.** Once any session exists, the SAMR/LSAT/SRVS named pipes are reachable — share enumeration becomes user/group enumeration (the full pipe surface lives in the rpc unit).

## Where you'll meet it

- **Every Windows box and every Linux filer** — 445 is the second port in every internal scan; the fingerprint step is free and decides the next hour.
- **Domain engagements** — SMB is the credential-validation channel (crackmapexec/netexec-style spray at SMB speed) and the carrier for named-pipe enumeration, secretsdump-style dumps, and psexec-style exec; those tools are named in the methodology pages but their primary surfaces live in the rpc/ntds units.
- **NAS/appliance estates** — Samba everywhere: config-driven misconfigurations (`guest ok`, `force user`, wide links) and version-pinned pre-auth RCE history.
- **Post-shell on Linux** — reading `/etc/samba/smb.conf` (and `shares.conf` includes) tells you what the shares really do — including options the banner never shows.

## Reading the server's mind

1. **`445/tcp open microsoft-ds`** with a version string is the implementation verdict — `Samba smbd X.Y` vs `Windows Server NNNN` — and the dispatch input; the full split probe is on each implementation page.
2. **The netexec/crackmapexec probe line** (`(name:DC01) (domain:example.com) (signing:True) (SMBv1:False) (Null Auth:True)`) is a four-field target model: relay viability, SMBv1 era, anonymous reachability — read it before running anything else.
3. **Signing disabled on a member host** (observed: `signing:False`) marks it an NTLM-relay target — cross-reference the ntlm unit before you spray.
4. **`smbclient -L` with no credentials** is the cheapest triage in the protocol: it separates estates that fixed null sessions from those that didn't (observed repeatedly).
5. **Share remarks leak software**: `IPC Service (Samba 4.x.y-Distro)` (observed) — secondary version evidence when the banner was proxied or stripped.

## The NetBIOS underlay: 137/138/139 (added 2026-11-13, rewritten from HackTricks)

Before 445-direct SMB, the protocol rode a NetBIOS over TCP/IP underlay
(RFC 1001/1002) split across three services: the **name service** on
**137/udp + 137/tcp** (name registration and resolution — broadcast Name
Query packets claim a unique name, or a name server answers queries
directly), the **datagram service** on **138/udp** (connectionless
broadcasts), and the **session service** on **139/tcp** (the
connection-oriented wrapper SMB used before direct hosting). Samba's `nmbd`
still speaks all of it; the nmap line names it outright
(`137/udp open netbios-ns Samba nmbd netbios-ns (workgroup: WORKGROUP)`).

Why the underlay still pays on legacy and mixed estates: **the name table is
pre-auth target intelligence** — names (computer, user, workgroup/domain,
browser elections in the suffix bytes), plus the device MAC. Two tools and
one nmap script collect it:

```bash
nmblookup -A 198.51.100.10      # NetBIOS name table for the address
nbtscan 198.51.100.10/30        # batch sweep of a segment
sudo nmap -sU -sV -T4 --script nbstat.nse -p137 -Pn -n 198.51.100.10
```

`nbstat` returns the computer name, the logged-in user when it can, the MAC,
and — with verbosity — the full suffix-coded name table (`<00>` workstation,
`<20>` file server, `<1e>`/`<1d>` browse/service markers,
`__MSBROWSE__<01>`): the file-server `<20>` entry is the "SMB lives here"
answer before you ever touch 445. Session and datagram services
(`139/tcp netbios-ssn`, `138/udp netbios-dgm`) are transports, not separate
attack surfaces — once 139 carries SMB you are back in the negotiation flow
above; a host with 139 but no 445 is a legacy NetBIOS-only SMB estate, which
by itself dates the patch posture. Name-resolution trust (some stacks still
honoring NBNS answers over DNS for legacy services) makes the name service
useful to spoofing lanes as well.

## Protocol-common enumeration workflow

Tools named in prose; flags are the canonical ones — safe-verify variants below.

```bash
# 1. fingerprint: dialect + signing + implementation
nmap -p139,445 --script smb-os-discovery,smb2-security-mode 198.51.100.10
nxc smb 198.51.100.10 -u 'guest' -p '' --shares     # probe line + share list

# 2. anonymous / null-session triage
smbclient -L //198.51.100.10 -N                      # null session share list
smbmap -H 198.51.100.10                              # per-share READ/WRITE map
enum4linux -a 198.51.100.10                          # shares+users+groups via pipes

# 3. credentiated sweep (password validation at SMB speed)
nxc smb 198.51.100.10 -u users.txt -p 'Password123!' --continue-on-success --shares

# 4. into a share
smbclient //198.51.100.10/share -N                   # or -U 'user' ; get/put/ls
mount -t cifs //198.51.100.10/share /mnt/s -o user=guest   # bulk file work
```

Expected outputs: step 1 fixes the dialect and implementation (dispatch); step 2's `anonymous login succeeded` / `NT_STATUS_ACCESS_DENIED` is the null-session verdict; step 3's `[+]` lines are validated credentials; step 4 `NT_STATUS_BAD_NETWORK_NAME` = share name wrong, `ACCESS_DENIED` = exists but gated.

**Null-session notes:** legacy estates (and Samba with `map to guest`) still answer anonymous IPC$; on modern Windows the equivalent misconfiguration shows as `(Null Auth:True)` in the probe line or as guest with an empty password. Anonymous pipe-walking (RID cycling, samrdump) is covered in the rpc unit; null-session *share* access is here.

## Ports cheat-sheet additions (2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); NSE names verified against the nmap repo, medusa's smbnt module against its source, smbclient's `--option` semantics against its man page. enum4linux, nmblookup, null-session triage, the CIFS mount, rpcclient commands, usermap-script (CVE-2007-2447) and the netexec family were already covered here or on the implementation pages — skipped, as was the GPP Groups.xml SYSVOL check, which the gpo unit owns.

- **Full NSE inventory layer** — `nmap -p139,445 --script smb-enum-shares,smb-enum-users,smb-enum-groups,smb-enum-sessions,smb-enum-domains,smb-enum-processes` is the script-side equivalent of steps 2–3 above when the workflow is already nmap-shaped.
- **SMBv1-era vuln battery** — `--script smb-vuln-*` one-lights the classic set (conficker, ms06-025, ms07-029, ms08-067, cve2009-3103, ms10-054, ms10-061, regsvc-dos). Treat any hit as an era marker to confirm against the implementation pages — never as a reportable finding on the script's word alone.
- **SMB-shaped credential brute** — nmap's `smb-brute` script (`--script smb-brute --script-args userdb=users.txt,passdb=passes.txt`) and medusa's smbnt module (`-M smbnt`) drive wordlists against 445 when hydra is not the house engine.
- **Negotiation failure as an SMBv1 fingerprint** — `protocol negotiation failed: NT_STATUS_CONNECTION_DISCONNECTED` against a legacy host usually means the modern client refuses SMBv1; retry with `smbclient -L //198.51.100.10 -N --option='client min protocol=NT1'` (per-connection override, no smb.conf edit). The error itself has told you the target speaks only SMBv1.
- **Passive version sniffing** — with a capture position on the segment, ngrep over the negotiation traffic (`ngrep -i 's.?a.?m.?b.?a.*[[:digit:]]'`) pulls dialect and version strings out of live conversations — for targets that refuse or mislead active probes.
- **winexe** — remote command execution over SMB from Linux (impacket's psexec family is the usual route; winexe is the standalone alternative), running cmd.exe under the named credential once step 3 has validated one.

## Where to go next

- [[smb-samba|Samba implementation]] — smb.conf attack directives, version-to-CVE history (CVE-2007-2447, CVE-2015-0240, CVE-2010-0926 wide links), smbpasswd/reset tooling.
- [[smb-windows-native|Windows-native implementation]] — guest and null-session behavior on modern DCs, SMBv1/MS17-010 (EternalBlue) context, signing posture and relay consequences.
