---
name: "enum4linux"
categories:
  - blackarch-recon
version: "0.9.1"
homepage: "http://labs.portcullis.co.uk/application/enum4linux/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "CiscoCXSecurity/enum4linux"
github-stars: 1434
github-pushed: "2026-06-12"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# enum4linux

> Perl wrapper around the Samba client tools that enumerates users, shares, groups, password policy and OS info from Windows and Samba hosts over SMB/RPC.

## What it's for

enum4linux is the classic (Portcullis Labs, now Cisco CX Security Labs) Linux alternative to enum.exe: point it at a Windows or Samba host and it pulls as much configuration and account information as the server's permissions allow — user lists, computer accounts, shares, groups and members, password policy, OS version, printers and workgroup details. It is a wrapper around `nmblookup`, `net`, `rpcclient` and `smbclient`, so those must be installed. It performs read-only enumeration, not exploitation, and returns the most against hosts that permit anonymous SMB/RPC access; credentials expand what it can read. Reach for it during initial SMB recon to build target lists for later attacks.

## Typical use cases

### Run the full simple enumeration

Grab everything the tool can get in one pass.

```
enum4linux -a 198.51.100.10
```

Outcome: prints users, computer accounts, shares, groups, password policy, OS information, nmblookup results and printer info in sequence (equivalent to `-U -S -G -P -r -o -n -i`). Running `enum4linux 198.51.100.10` with no options does the same, since `-a` is the default when nothing else is given.

### List users only

Enumerate local/domain users without the rest of the noise.

```
enum4linux -U 198.51.100.10
```

Outcome: prints the user list extracted via SAMR/LSA lookups (add `-d` for detailed output). Compare against `-M` (computer account list) to separate real accounts from computer accounts.

### List shares only

Enumerate the exported shares on a host.

```
enum4linux -S 198.51.100.10
```

Outcome: prints all shares the server exposes, including hidden/administrative ones visible to the anonymous or supplied session, with their types — the input for follow-up share access attempts.

### Enumerate with credentials

Re-run enumeration authenticated to see restricted users, shares and policy.

```
enum4linux -u user -p Password1 -a 198.51.100.10
```

Outcome: the same full `-a` output, but with the session established as `user`, so restricted share and account details that anonymous access hides become visible.

Corpus-grounded patterns; flag semantics verified against the upstream `enum4linux.pl` usage text.

---

## Scope reality check before anything else

enum4linux speaks SMB/RPC on TCP 139/445 (plus 389 for the `-l` LDAP probe). **Those ports are almost never in scope for a public web bug bounty program** — they are rarely internet-exposed, and when they are, the program's rules usually exclude infrastructure. This tool earns its keep in three authorized contexts:

- **Internal network / infrastructure assessments** where the engagement letter covers host enumeration.
- **VDP or bounty programs with an explicit infra/network scope** (some enterprise and gov programs list CIDR ranges and name SMB as testable).
- **AD labs and CTFs** (HTB, PG, OSCP-style ranges) where everything is fair game.

If you cannot point to written authorization for SMB against the specific host, stop here. An unsolicited `enum4linux -a` against a company's edge is an unauthorized port-and-service enumeration of a Windows host, and it is loud. Everything below assumes you have that authorization and are working a host that is legitimately in scope.

Two versions exist and they behave differently — know which you are running:

- **enum4linux v0.9.1** — the original Perl wrapper (CiscoCXSecurity, formerly Portcullis Labs). A shell around `nmblookup`, `net`, `rpcclient`, `smbclient`, with `polenum` for password policy and `ldapsearch` for `-l`. Ships in Kali/BlackArch/parrot. Last meaningful development is old; it works but is unmaintained in spirit.
- **enum4linux-ng** — the Python rewrite by cddmp (`cddmp/enum4linux-ng`, GPL-3.0). Same job, different flags (capital `-A` not `-a`, `-Gm` for group members, `-R` opt-in RID cycling), native LDAP/polenum via `ldap3`/`impacket`, SMB-dialect/signing auto-detection, legacy SMBv1 fallback, Kerberos (`-K`) and pass-the-hash (`-H`) auth, and — the reason to prefer it on real engagements — **machine-readable `-oY`/`-oJ` output**. When this document shows an `-ng` command it is marked explicitly.

## Bug bounty workflow with enum4linux

A phased path from "is SMB even reachable" to a documented access-control or disclosure finding. enum4linux is a *recon* instrument — it builds the target map (users, shares, policy) that `smbmap`, `netexec`, and manual `smbclient` then act on. It does not exploit anything itself.

### Phase 0 — Confirm authorization and reachability

Before enum4linux runs a single RPC, confirm the ports are open and in scope. enum4linux will happily hang for a long time against a filtered host.

```
nmap -Pn -p139,445,389 -sV 198.51.100.10
```

Outcome: you want `445/tcp open microsoft-ds` (or `netbios-ssn` on 139) before proceeding. If 445 is filtered, enum4linux has nothing to talk to. The `-sV` banner also tells you Windows vs Samba, which changes your RID expectations later (Samba RIDs often sit at 3000-3050; Windows user RIDs start at 1000, with 500=Administrator, 501=Guest, 502=krbtgt).

### Phase 1 — Establish whether a null session is even possible

enum4linux's entire anonymous value rests on the server allowing an unauthenticated IPC$ session. The tool's `make_session` routine runs the equivalent of:

```
smbclient -W WORKGROUP //198.51.100.10/ipc$ -U'%' -c help
```

If that returns the command help (the tool looks for `case_sensitive` in the output) the null session is up. If it does not, enum4linux prints `Server doesn't allow session using username '', password ''` and exits. **On modern, patched, domain-joined Windows (Server 2016+ / Windows 10/11) anonymous SAMR/LSA enumeration is restricted by default** — Microsoft hardened the anonymous RPC paths (the MS16-047 SAMR/LSAD signing change and `RestrictAnonymous`/`RestrictAnonymousSAM` defaults), so a clean `-a` against a hardened host returns workgroup/OS crumbs and little else. You still run it, because the hosts that *do* leak — standalone servers, legacy boxes, Samba appliances, NAS devices, misconfigured member servers — are exactly the findings worth reporting.

### Phase 2 — Broad anonymous sweep, captured to a file

Run the full simple enumeration once, unauthenticated, and save everything. This is your baseline recon note.

```
enum4linux -a 198.51.100.10 | tee e4l-198.51.100.10-anon.txt
```

`-a` expands to `-U -S -G -P -r -o -n -i` (users, shares, groups, password policy, RID cycling, OS info, nmblookup, printers). It is also the implicit default if you pass no action flags. Note what `-a` does **not** include: `-M` (machine list), `-l` (LDAP), `-s` (share brute), `-k` (known-user SID lookups), `-K`, `-A` (aggressive write checks), and `-d` (detailed). Those are opt-in.

For the `-ng` fork the equivalent is `enum4linux-ng -A 198.51.100.10 -oY e4l-anon` — and `-A` *does* include the NetBIOS lookup and LDAP, but still does **not** include RID cycling (add `-R`) or group members (add `-Gm`).

### Phase 3 — Targeted follow-ups on what the sweep revealed

The sweep points you at the three high-value surfaces. Chase each with a narrow flag rather than re-running `-a`:

- **Shares looked interesting** → re-enumerate with `smbmap`/`smbclient` for *permissions and contents*, not just names (enum4linux lists shares and whether they map/list, but smbmap reads them). See recipes below.
- **Users came back thin** → add `-R` RID cycling with a wider range, or feed `-k` a known-user list to seed SID lookups on a host that blocks `enumdomusers` but allows `lookupsids`.
- **Target is a Domain Controller** → add `-l` for the LDAP `namingContexts` crumb, and pivot to `netexec ldap`/BloodHound for real domain enumeration (enum4linux's `-l` is deliberately "limited").

### Phase 4 — Authenticate and re-run to measure the gap

If the program gave you credentials (or you captured/guessed a low-priv set elsewhere in the engagement), re-run authenticated and diff against the anonymous baseline. The *delta* is the story: what a plain domain user can see that an anonymous attacker cannot.

```
enum4linux -u 'bbp-test' -p 'REDACTED' -a 198.51.100.10 | tee e4l-198.51.100.10-auth.txt
diff e4l-198.51.100.10-anon.txt e4l-198.51.100.10-auth.txt
```

Outcome: shares, users, and policy that anonymous access hid now appear. For `-ng`, the same with `-u/-p`, plus `-H <NTLM hash>` for pass-the-hash or `-K <ccache>` for Kerberos when you have those instead of a cleartext password.

### Phase 5 — Validate the finding with a second tool, then write it up

enum4linux output alone is weak evidence — it is a wrapper, and triagers want to see the underlying access. Confirm the specific issue (anonymous share read, exposed user list enabling password spray, weak lockout policy) with `smbmap`/`smbclient`/`netexec` so your report shows the actual impact, not just a wrapper's summary. Capture both. Then report per the evidence section below.

## Where the money is with enum4linux

enum4linux does not produce bounties directly; it surfaces the *conditions* that become bounties on an in-scope host. Severity is heavily program-dependent — on an infra-scoped program these land, on a web-only program they are out of scope regardless of what you find.

| Finding class | What reaches it | Typical severity | Dupe / rejection risk |
|---|---|---|---|
| Anonymous-readable share with sensitive data | `-S` finds the share → confirm read with `smbmap`/`smbclient` | Medium–High (High if creds/PII inside) | Low if data is real; **rejected** if share is empty/public-by-design |
| Null-session user enumeration | `-U` / `-r` RID cycling → full account list | Low–Medium on its own | **High dupe**; strong only as an *enabler* for spray/AS-REP |
| Weak password / lockout policy | `-P` (polenum + `getdompwinfo`) | Low–Medium | High dupe; report as amplifier, not standalone |
| Anonymous-writable share | `-A` aggressive write check (**see cautions**) | High | Low; but write-testing is intrusive — authorization-gated |
| Domain/OS/workgroup disclosure | `-o`, `-n`, `-l` | Info–Low | Very high dupe; context only |
| Exposed printer / spooler surface | `-i` (`enumprinters`) | Info → pivot | Context for PrintNightmare-class follow-up |
| SMBv1 enabled | `-ng` dialect detection / nmap | Low–Medium | Config finding; often known/accepted |

The honest read: **the only enum4linux output that stands alone as a paid finding is an anonymously accessible share (or writable share) that exposes real data.** Everything else (user lists, OS strings, policy) is reconnaissance that *strengthens a chain* — it is the "preconditions" paragraph of someone else's spray or relay finding, not a report by itself. Lead reports with the share; cite the enumeration as supporting context.

## Command recipes

Annotated command → expected-output blocks. All targets use TEST-NET (`198.51.100.0/24`) or `example.com`. Note the low-volume discipline: enum4linux hits one host with many sequential RPC calls — it is not a scanner, so the volume concern is *per-host intrusiveness and RID-range size*, not request floods.

### Full anonymous sweep (the default)

```
enum4linux -a 198.51.100.10
```

Runs `-U -S -G -P -r -o -n -i` in sequence. Expect sections titled `Enumerating Users`, `Share Enumeration`, `Getting domain group memberships`, `Password Policy Information`, `OS information`, `Nbtstat Information`, `Getting printer info`. On a hardened host most sections print `[E] Couldn't get ...` or empty results — that is itself data (the host is locked down).

### Users only, detailed

```
enum4linux -U -d 198.51.100.10
```

`-U` pulls the user list via `rpcclient querydispinfo` + `enumdomusers`. `-d` adds per-RID detail (`queryuser` for each account — description, flags, logon info). **Caveat worth knowing:** the usage text claims `-d` "applies to `-U` and `-S`", but the source only reads the detail flag in the user/group RID paths — `-d` has no effect on share enumeration. Do not expect richer share output from it.

### Machine (computer) accounts — separate the humans from the hosts

```
enum4linux -M 198.51.100.10
```

`-M` (not part of `-a`) enumerates computer accounts via `querygroupmem 0x203` then batched `samlookuprids`. Use it to subtract computer accounts (`HOST$`) from the `-U` list so your user list for a spray is real humans, not machine accounts.

### RID cycling with a widened range

```
enum4linux -R 500-1100 198.51.100.10
```

`-R range` sets the RID ranges (default `500-550,1000-1050`) and **implies `-r`**. RID cycling extracts users even when `enumdomusers` is blocked but anonymous SID→name translation is allowed (`RestrictAnonymous=1` on NT/2000, or "Allow anonymous SID/Name translation" on XP/2003). For a DC where you want to sweep until the accounts run out:

```
enum4linux -R 1000-1500 -K 20 198.51.100.10
```

`-K n` keeps cycling until `n` consecutive RIDs miss, pushing the range end toward 999999. **Gotcha from the source:** `-K` on its own does *not* turn on RID cycling — only `-R` (or `-r`) sets the cycling flag, so always pair `-K` with `-R`/`-r`. Keep the range bounded on real engagements; a 1000→999999 sweep is a long, chatty run against one host.

### Known-user SID seeding

```
enum4linux -k 'administrator,admin,backup,svc-sql,helpdesk' 198.51.100.10
```

`-k` (not in `-a`) feeds `lookupnames`/`lookupsids` a comma-separated candidate list to recover a domain SID when nothing else leaks one. A `NT_STATUS_NONE_MAPPED` means "no such name, move on"; a `NT_STATUS_ACCESS_DENIED` means the anonymous path is closed and cycling stops. The default list is a long built-in of common accounts (administrator, guest, krbtgt, root, bin, etc.).

### Shares only, then actually read them (the pairing that matters)

enum4linux lists shares and whether the session can map/list them, but it does not dump contents. Chain it with smbmap/smbclient:

```
# 1. enum4linux names the shares and their listability
enum4linux -S 198.51.100.10

# 2. smbmap shows per-share READ/WRITE permissions for this session
smbmap -H 198.51.100.10 -u '' -p ''

# 3. smbclient confirms and reads a specific share as evidence
smbclient //198.51.100.10/Backups -N -c 'ls'
```

`-N` is the null-session (no-password) flag for smbclient. The smbmap `READ ONLY`/`READ, WRITE` column is the single most report-worthy line — it is the access proof a triager wants, where enum4linux only asserts "mapping: ok".

### Cross-check with netexec for coverage

netexec (the maintained `crackmapexec` successor, `nxc`) consolidates most of enum4linux in one authenticated-or-anonymous command and is a good second opinion:

```
nxc smb 198.51.100.10 -u '' -p '' --users --shares --groups --pass-pol --rid-brute
```

Outcome: a compact table of users, shares (with read/write marks), groups, password policy, and RID-brute results. Where enum4linux and netexec *disagree* (one sees a share the other doesn't, usually due to SMB dialect negotiation) is itself worth a note — run `enum4linux-ng` for its explicit dialect/signing detection to resolve it.

### The `-ng` fork with structured output (preferred for real reports)

```
enum4linux-ng -A 198.51.100.10 -oY e4l-198.51.100.10
```

`-A` = all simple enumeration including NetBIOS and LDAP (`-U -G -S -P -O -N -I -L`); `-As` skips the NetBIOS step. `-oY` writes `e4l-198.51.100.10.yaml` (use `-oJ` for JSON, `-oA` for both). The YAML/JSON is parseable evidence you can attach and diff between anonymous and authenticated runs, and `-ng` saves partial results if you Ctrl-C mid-run. Add `-R` for RID cycling (not in `-A`), `-Gm` for group *members* (not enumerated by default in `-ng`), `-t 5` to tighten the per-connection timeout, and `-u/-p`, `-H <hash>`, or `-K <ccache>` for authentication.

### Aggressive write-check — authorization-gated, not default

```
enum4linux -A 198.51.100.10     # NOTE: capital -A in the Perl tool = Aggressive
```

The Perl tool's `-A` (**different meaning from `-ng`'s `-A`**) does write checks: for each readable share it creates an 8-character random directory, verifies it, then removes it. That is an active modification of the target filesystem. **Only run `-A` when write-testing is explicitly authorized** — creating directories on someone's file server is well past read-only recon and can trip DLP/EDR and file-integrity monitoring. Prefer confirming write access deliberately with a single `smbclient ... -c 'mkdir ...; rmdir ...'` you control and can describe exactly in the report.

## Pitfalls, noise and scope discipline

**Scope is the number-one way to get rejected or banned here.** SMB enumeration against an out-of-scope host — or any host when the program is web-only — is an unauthorized action regardless of what you find. Confirm the IP/CIDR is in scope and that infrastructure/SMB is explicitly testable before the first `nmap`.

- **It is loud and it lingers.** enum4linux fires dozens of sequential `rpcclient`/`net`/`smbclient` commands and, with wide `-R`/`-K`, thousands of `lookupsids` calls to one host. This is trivially logged (Windows event logs, EDR) and a full RID sweep to 999999 can run a very long time. Bound your RID ranges; do not leave `-K` cycling open-ended on a production DC.
- **`-A` (Perl aggressive) writes to the target.** Covered above — it creates and deletes directories on readable shares. Never run it on an unauthorized or production host; it is not read-only recon.
- **User lists alone are near-certain dupes.** Anonymous user enumeration is the single most-reported SMB finding and is usually already known/accepted. Report it only as the *enabler* in a chain (password spray, AS-REP roasting), with the downstream impact demonstrated, not as a standalone bug.
- **"Null session allowed" is not automatically a finding.** Many legitimately expose workgroup/OS info anonymously. The finding is *sensitive data or user lists* leaking anonymously — show the data, not the session.
- **Hangs and false negatives on modern hosts.** Against filtered ports or SMBv1-disabled hosts the Perl tool can hang or report nothing because its underlying `smbclient` fails dialect negotiation. If `enum4linux` comes back empty, re-test with `enum4linux-ng` (explicit dialect/SMBv1 handling) or `netexec` before concluding the host is clean — the empty result may be a tooling artifact, not a locked-down host.
- **polenum/ldapsearch dependencies.** `-P` needs `polenum` installed or password policy silently fails; `-l` needs `ldapsearch`. A missing dependency looks like "no policy" rather than an error — verify the tool actually ran.
- **Don't exfiltrate.** When a share is readable, prove access with a directory listing and one representative (non-sensitive) filename or a redacted snippet. Do not bulk-download data; copying a file server's contents is exactly the overreach that turns a valid report into a scope/ethics problem.
- **Redact before you paste.** enum4linux output embeds real usernames, hostnames, SIDs, and domain names. Scrub anything beyond what proves the finding before it leaves your machine.

## Reporting evidence

What to capture so the finding stands without the triager re-running your tools:

- **The in-scope authorization anchor.** Name the program's infra/SMB scope line or the engagement letter clause that authorizes SMB against this host. For an SMB finding this is not optional context — it is what separates a report from an incident.
- **Target and port proof.** The `nmap -p139,445 -sV` line showing the open service and whether it is Windows or Samba.
- **The session class.** State plainly whether access was **anonymous/null** (`-u '' -p ''`) or **authenticated** (which account, what privilege level). This determines severity more than anything else.
- **The specific access, from a second tool.** For a share finding, the `smbmap -H ... -u '' -p ''` table row showing `READ ONLY`/`READ, WRITE` on the named share, plus a `smbclient //host/share -N -c 'ls'` listing. enum4linux's own "mapping: ok" is supporting, not primary.
- **The enum4linux snippet, trimmed and labelled.** The relevant section only (the share block, the user list, the `Password Policy Information` block) — not the entire `-a` dump. Include the exact command you ran.
- **Anonymous-vs-authenticated diff.** If you ran both, the `diff` (or the two `-ng` YAML files) showing what unauthenticated access exposes is a compelling single artifact.
- **A redacted representative sample for share findings.** One directory listing and one filename (or a redacted snippet) that proves sensitive data is present — never a bulk copy.
- **Impact framing and remediation.** Tie it to the real consequence (anonymous read of HR/backup data; user list enabling spray against the weak lockout policy you also found) and name the fix: disable anonymous/guest SMB access, set `RestrictAnonymous`/`RestrictAnonymousSAM`, remove the share or apply least-privilege ACLs, disable SMBv1, enforce a lockout threshold.

A tight report reads: *scope authorization → open 445 → null session accepted → `Backups` share readable anonymously (smbmap `READ ONLY`, smbclient listing attached) → contains `payroll-2026.xlsx` (redacted) → fix: remove anonymous ACL.* The enum4linux `-S` block is the breadcrumb that led there; the smbmap/smbclient output is the proof.

## References

- enum4linux (Perl) source and usage — https://github.com/CiscoCXSecurity/enum4linux and https://raw.githubusercontent.com/CiscoCXSecurity/enum4linux/master/enum4linux.pl
- enum4linux-ng (Python rewrite) — https://github.com/cddmp/enum4linux-ng
- Portcullis Labs / Cisco CX Security Labs enum4linux page — https://labs.portcullis.co.uk/tools/enum4linux/
- smbmap — https://github.com/ShawnDEvans/smbmap
- netexec (CrackMapExec successor) — https://github.com/Pennyw0rth/NetExec
- Samba `smbclient`/`rpcclient` manual pages (the binaries enum4linux wraps) — https://www.samba.org/samba/docs/
- Microsoft, anonymous access / RestrictAnonymous hardening (MS16-047, Network access policies) — https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/network-access-do-not-allow-anonymous-enumeration-of-sam-accounts

*Research supplement added 2026-11-16, grounded in the enum4linux `enum4linux.pl` v0.9.1 source/usage text and the cddmp/enum4linux-ng README fetched this date, plus Samba and Microsoft anonymous-access documentation (model: claude-opus-4-8).*
