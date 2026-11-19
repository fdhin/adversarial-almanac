---
name: "smbmap"
categories:
  - blackarch-scanner
version: "v1.10.8.r0.g31b8a8f"
homepage: "https://github.com/ShawnDEvans/smbmap"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ShawnDEvans/smbmap"
github-stars: 2095
github-pushed: "2026-01-06"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# smbmap

> Samba/SMB share enumerator with permission mapping, share-content search, file transfer, and remote command execution.

## What it's for

SMBMap (by Shawn Evans) enumerates SMB share drives on single hosts or whole
subnets and reports each share with the effective access of your session —
READ, WRITE, or NO ACCESS — which makes it the standard first pass for finding
writable shares and sensitive files during an authenticated internal
assessment. Beyond listing, it recursively lists share contents, filters
listings by a filename regex and can auto-download matches, uploads, downloads
and deletes files, runs remote commands (WMI or PSExec style), and supports
null sessions, pass-the-hash, and Kerberos authentication. It is an
enumeration and file-interaction tool, not an SMB vulnerability scanner.

## Typical use cases

### Null-session share and permission overview

Check what an SMB host exposes to anonymous sessions.

```
smbmap -H 198.51.100.10 --no-banner
```

Outcome: a table of every share on the host with the anonymous session's
permission on each (READ, WRITE, NO ACCESS) plus host name and status;
NO ACCESS everywhere means null sessions are closed and credentials are
needed for anything further.

### Credentialed enumeration with a domain

Enumerate shares as a domain user to see what that account can reach.

```
smbmap -d EXAMPLE -u user -p Password1 -H 198.51.100.10
```

Outcome: the share/permission table rendered for that user's session; a
row showing READ, WRITE flags a writable share to pivot on, and ADMIN!!!
in the status field means the account is a local administrator on the host.

### Pass-the-hash authentication

Reuse a captured NTLM hash instead of a plaintext password.

```
smbmap -u administrator -p 'aad3b435b51404eeaad3b435b51404ee:da76f2c4c96028b7a6111aef4a50a94d' -H 198.51.100.10
```

Outcome: `-p` accepts a full LM:NT hash pair and authenticates with it
(pass-the-hash); the output is the same permission table a password login
produces, so hash-only access can be triaged without cracking anything.

### Sweep a list of hosts

Run the permission check across a whole range from one file.

```
smbmap --host-file hosts.txt -u user -p Password1 -q --no-banner
```

Outcome: the host file supports IPs, hostnames, and CIDR entries, one per
line; each host with open SMB gets a share/permission table, and `-q`
keeps the output to shares you can actually read or write.

### Recursively list one share

Walk a share's directory tree to a fixed depth.

```
smbmap -H 198.51.100.10 -u user -p Password1 -r 'software_updates' --depth 2 -q
```

Outcome: an `ls`-style listing of the share (path with no share argument
lists the root of all shares), recursed to the given depth, with file
sizes and timestamps; useful for spotting config, backup, or archive files
before pulling anything down.

### Search share contents by filename pattern and auto-download

Find interesting files across a recursive walk without listing everything.

```
smbmap -H 198.51.100.10 -u user -p Password1 -r data -A '(xlsx|docx|xml)'
```

Outcome: `-A` takes a case-insensitive regex applied to filenames during
the `-r` walk; every match is downloaded automatically to the current
directory, so a share can be mined for document types in one pass.

### Grepable or CSV output for large sweeps

Capture listings in a format other tools can consume.

```
smbmap -H 198.51.100.10 -u user -p Password1 -r 'C$' -g grep_out.txt
```

Outcome: `-g` writes the recursive listing to a grep-friendly file (used
with `-r`); `--csv shares.csv` produces the same data as CSV for
spreadsheet or script post-processing.

### Download a single known file

Pull one file out of a share by its full path.

```
smbmap -H 198.51.100.10 -u user -p Password1 --download 'C$\temp\passwords.txt'
```

Outcome: the file is fetched over the SMB session and saved locally,
printing the byte count; `--upload SRC DST` and `--delete` cover the
write side of the same interface.

### Execute a command on the host

Run a single command with the session's privileges.

```
smbmap -H 198.51.100.10 -u user -p Password1 -x 'net group "Domain Admins" /domain'
```

Outcome: the command runs remotely via WMI by default (`--mode psexec`
switches to a PSExec-style service execution) and its output is printed;
combined with an ADMIN!!! session this gives quick remote execution
without loading a full framework.

### Check SMB signing policy across a range

Identify hosts where relay attacks will work.

```
smbmap --host-file hosts.txt --signing
```

Outcome: each host is reported as signing disabled (relaying possible,
flagged), signing enabled but not required, or signing required; the same
host-file sweep with `-v` returns server OS versions instead.

Kerberos authentication (`-k --no-pass --dc-ip` with an exported ccache),
admin-only triage (`--admin`), local drive listing (`-L`), and
experimental remote file-content search (`-F '[Pp]assword'`) round out the
interface; see `smbmap -h`.

## Bug bounty workflow with smbmap

SMBMap is an internal-assessment tool wearing a bug-bounty hat. The single most
important fact before you type `smbmap` on a public program: **TCP 445/139 is
out of scope on most bug bounty programs.** Internet-exposed SMB is rare,
usually a misconfiguration, and almost always excluded by the "no network
scanning / no infrastructure" clause. smbmap earns its place only when the
program explicitly lists internal-network, VPN-delivered, Active Directory, or
reachable file-server scope — corporate pentest-style programs, cloud-hosted
SMB (Azure Files, FSx, EFS-over-SMB gateways), or an authorized internal
engagement. Confirm that in writing first; everything below assumes it.

The workflow mirrors the FastAPI guide's shape — program-safe recon first, the
payout phases in the middle, impact and corroboration last — but the risk
profile is inverted. With FastAPI, a probe is one HTTP request. With smbmap,
the *default* permission check **writes to the target** (see Phase 3), so the
most dangerous thing you can do here is run the tool with its defaults on a
share you were only authorized to read.

### Where the money is (triage priority)

| Priority | Finding class | smbmap feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Anonymous/null-session **readable** share exposing secrets (creds, backups, configs, PII) | `-H host` with no `-u`/`-p` → READ rows, then `-r --dir-only` | High–Critical | Medium (low-hanging; often already found) |
| 2 | Anonymous/low-priv **writable** share (logon-script/web-root/startup poisoning path) | WRITE in the privs column | High–Critical | Medium |
| 3 | Authenticated-users can read a share they shouldn't (over-broad share ACL) | credentialed `-H` as a plain domain user | Medium–High | Low (needs your two-account logic) |
| 4 | Sensitive file class mineable across a share (`*.kdbx`, `web.config`, `unattend.xml`, `*.ps1`, `id_rsa`) | `-r -A '(pattern)'` (metadata first) | High | Low–Medium |
| 5 | Local-admin reuse surfaced as `ADMIN!!!` on a host | credentialed `-H`, status column | Critical (but often beyond pure enum) | Low |
| 6 | SMB **signing disabled** (NTLM relay enabler) | `--signing` | Low–Medium (infra/hardening) | **High** (near-universally known/duped) |
| 7 | Guest account enabled / treated as authenticated | guest login returns share table | Low–Medium | Medium |

Priorities 1–4 are the share-enumeration core. Priorities 5–7 are context you
report but rarely the headline. Priority 6 is almost always a known issue —
lead with the data a share exposes, not the signing posture.

### Phase 0 — Authorization and scope gate

Before the first packet:

- Confirm **445/139 and the specific hosts/subnets are in scope**, in writing.
  A CIDR in scope does not authorize scanning the CIDR — re-read the rules on
  host discovery and network scanning (`--host-file` with a range is a scan).
- Confirm which **sessions** you may use: null/anonymous only, a provided test
  account, guest, or credentials/hashes you recovered earlier. Pivoting with a
  hash you cracked in a *different* finding can exceed the engagement's RoE.
- Confirm whether **write-testing and command execution** are permitted. The
  default behavior and `-x`/`--upload`/`--delete` are active and modifying —
  default to no.

### Phase 1 — Reachability, version, and signing posture

Passive-ish fingerprinting of a single, authorized host. `--timeout` is the
per-host port-scan socket timeout (default 0.5s); raise it for slow links,
don't lower it to hammer.

```
smbmap -H 198.51.100.10 --signing --no-banner
smbmap -H 198.51.100.10 -v --no-banner        # -v = remote OS version, not smbmap's version
```

`--signing` reports *disabled* (relay possible), *enabled but not required*, or
*required*. `-v` returns the server OS string. Record both; they frame every
later finding (an unsigned 2008-era file server reads very differently from a
hardened 2022 one).

### Phase 2 — Null, guest, and anonymous session enumeration

The highest-value, lowest-privilege question: what does this host hand an
unauthenticated caller? Omit `-u`/`-p` entirely for a null session.

```
smbmap -H 198.51.100.10 --no-write-check -q --no-banner
```

`--no-write-check` is non-negotiable here (Phase 3 explains why). `-q` trims the
output to shares you can actually READ or WRITE. A table of `NO ACCESS`
everywhere means null sessions are locked down — note it and move to
credentialed testing. Any `READ` row on a non-default share is your Priority-1
lead. Repeat with an explicit guest attempt where the program allows
(`-u guest -p ''`), because "null closed, guest open" is a real and separate
finding.

### Phase 3 — Credentialed share and permission mapping (and the write-check trap)

As a provided low-privilege account, map what that identity can reach:

```
smbmap -d EXAMPLE -u testuser -p - --prompt --no-write-check -q -H 198.51.100.10
```

**The write-check trap — read this before removing `--no-write-check`.** By
default smbmap determines WRITE access by *actually writing to every share it
can*: it creates a randomly-named 10-character uppercase directory
(`PERM_DIR = ''.join(random.sample('ABCDEFGHIGJLMNOPQRSTUVWXYZ', 10))`), and if
that fails, falls back to creating a `<random>.txt` file, then deletes what it
made. This is done inside `get_shares()` whenever the internal `write_check`
flag is true (the default). Consequences on a real target:

- It generates **write/create/delete events** and can trip file-integrity
  monitoring, honeypot shares, and EDR.
- A crash, timeout, or permission quirk mid-operation can **leave a
  stray directory or `.txt` file** behind on the target.
- On an engagement where you were authorized to *read*, writing is a scope
  violation regardless of cleanup.

`--no-write-check` (which sets `write_check` to false via
`dest='write_check', action='store_false'`) disables all of it: shares are
reported only as `READ ONLY` or `NO ACCESS`, based on a read of the share root,
and nothing is created. **Make `--no-write-check` your default.** Only drop it
when writable-share confirmation is explicitly in scope, and then prefer to
confirm write access manually on a *single* named share rather than letting the
tool write to every share on every host.

Authentication variants (same output table, different session):

```
# Pass-the-hash: -p takes a full LM:NT pair, no cracking needed
smbmap -u administrator -p 'aad3b435b51404eeaad3b435b51404ee:da76f2c4c96028b7a6111aef4a50a94d' \
  --no-write-check -q -H 198.51.100.10

# Kerberos with an exported ccache (KRB5CCNAME), DC specified
export KRB5CCNAME=/path/to/user.ccache
smbmap -k --no-pass --dc-ip 198.51.100.2 -H fileserver.example.com --no-write-check -q
```

`ADMIN!!!` in the status column means the account is a local admin on that host
— a Priority-5 escalation lead, not an invitation to run `-x` without
authorization.

### Phase 4 — Read-only content discovery and download discipline

Map structure before pulling anything. `--dir-only` keeps the walk to
directories; `--depth` caps recursion (default 1, so you must raise it to see
deeper); `-g`/`--csv` capture the listing for offline triage.

```
# metadata only: directory tree of one share, two levels, nothing downloaded
smbmap -H 198.51.100.10 -u testuser -p - --prompt --no-write-check \
  -r 'finance_archive' --dir-only --depth 2 -q

# capture a full recursive listing to a grep-friendly file (paths, sizes, timestamps)
smbmap -H 198.51.100.10 -u testuser -p - --prompt --no-write-check \
  -r 'finance_archive' --depth 5 -g finance_listing.txt
```

**Download discipline — metadata over mass exfil.** `-A PATTERN` auto-downloads
every filename matching a case-insensitive regex during the `-r` walk, to the
current directory. That is exfiltration: on a share full of real data it can
pull gigabytes of PII you now have to store, justify, and destroy. The safe
pattern is: discover with a listing (`-g`), review the paths, then download the
**single** file you need as proof by exact path:

```
# PROOF, not harvest: one file, by path
smbmap -H 198.51.100.10 -u testuser -p - --prompt --no-write-check \
  --download 'finance_archive\2021\vendor_bank_details.xlsx'
```

Reserve `-A` for narrow, high-signal patterns (`-A '(id_rsa|\.kdbx$)'`) only
when the program's rules permit downloading matched files, and never point it at
`C$` or a whole-server walk.

### Phase 5 — Impact framing without crossing the line

`-x` runs a command (WMI by default, `--mode psexec` for a service-based path),
`--upload`/`--delete` write and remove files, and `-L`/`-F` read drives and
search file *contents* (both require admin on the target, and `-F` also needs
PowerShell on the victim). These are post-exploitation. On a bug bounty program,
demonstrating a writable share or an admin session is almost always sufficient
impact — **describe** what the access permits rather than executing it. If
command execution is explicitly authorized, run one benign, auditable command
(`-x whoami`) and stop; do not stage payloads, upload tools, or delete anything.

### Phase 6 — Corroborate and hand off (enum4linux / netexec)

smbmap answers "which shares, what access, for this session." It does not do RID
cycling, password-policy dumps, user/group enumeration, or multi-protocol
spraying. Corroborate and widen with the standard partners — same hosts, same
authorized credentials:

- **enum4linux-ng** — null-session RID cycling, users, groups, shares, password
  policy, OS info. Run it alongside smbmap to turn "null session reads a share"
  into "null session also enumerates every domain user."
- **netexec (nxc)** — `nxc smb <host> -u u -p p --shares` reproduces smbmap's
  share/permission view across many hosts, and adds `--users`, `--pass-pol`,
  `-M spider_plus` for content mapping, and signing/SMBv1 flags. Use nxc for the
  breadth sweep and smbmap for focused per-share interaction and download.

Cross-confirming the same share ACL with a second tool hardens the report and
guards against a single tool's quirk being mistaken for a finding.

## Command recipes

Every recipe below is read-only and low-volume unless noted. `-p -` with
`--prompt` keeps the password off the command line and out of shell history and
the process list.

```
# 1. Single-host null-session map, read-only, trimmed output
smbmap -H 198.51.100.10 --no-write-check -q --no-banner
#   -> share table; privs column shows READ ONLY / NO ACCESS only (no writes attempted)

# 2. Credentialed map, password prompted (not echoed, not in history)
smbmap -d EXAMPLE -u testuser -p - --prompt --no-write-check -q -H 198.51.100.10
#   -> that user's readable/writable shares; ADMIN!!! in status = local admin

# 3. Signing-posture + OS version for the relay/hardening section of a report
smbmap -H 198.51.100.10 --signing --no-banner
smbmap -H 198.51.100.10 -v --no-banner

# 4. Structure-first discovery: directories only, bounded depth, nothing downloaded
smbmap -H 198.51.100.10 -u testuser -p - --prompt --no-write-check \
  -r 'share_name' --dir-only --depth 2 -q

# 5. Capture a recursive listing for offline review (grep + CSV)
smbmap -H 198.51.100.10 -u testuser -p - --prompt --no-write-check \
  -r 'share_name' --depth 5 -g listing.txt
smbmap -H 198.51.100.10 -u testuser -p - --prompt --no-write-check --csv shares.csv

# 6. Pull ONE proof file by exact path (no mass download)
smbmap -H 198.51.100.10 -u testuser -p - --prompt --no-write-check \
  --download 'share_name\path\to\proof.txt'

# 7. Authorized multi-host map from a vetted list (NOT a CIDR scan)
smbmap --host-file authorized_hosts.txt -u testuser -p - --prompt \
  --no-write-check -q --no-banner --timeout 2
#   authorized_hosts.txt = explicit in-scope IPs/FQDNs, one per line; --timeout is the
#   per-host port socket wait, raise it for slow links, don't lower it to go faster
```

Low-volume settings to standardize on: `--no-write-check` (no writes),
`-q` (only actionable shares), `--no-banner`/`--no-color` for clean captured
logs, bounded `--depth`, and a curated `--host-file` rather than a CIDR. Avoid
`-A` mass-download, `-x`, `--upload`, `--delete`, `-L`, and `-F` unless each is
separately authorized.

## Pitfalls, noise and scope discipline

- **Default write check writes to the target.** The number-one way to turn a
  read authorization into a violation. Without `--no-write-check`, smbmap
  creates (and deletes) a random directory/file on *every* writable share on
  *every* host in your sweep. Always pass `--no-write-check` unless write
  confirmation is explicitly in scope. (Mechanism: Phase 3.)
- **445 on the internet is usually out of scope.** Confirm infrastructure/SMB
  is in the program before scanning. "I found an open SMB share on the internet"
  is frequently a fast path to a closed-as-out-of-scope report or a ban.
- **`--host-file` with CIDR entries is network scanning.** It parses IPs,
  hostnames, and CIDR, so one line can expand to hundreds of hosts. Many
  programs forbid this outright. Use an explicit, vetted host list, and never
  sweep ranges you were not handed.
- **`-A` auto-download is bulk exfiltration.** Matched files land on your disk
  automatically. On shares holding PII/PHI this creates a data-handling and
  legal problem the moment it runs. Enumerate with `-g` first; download single
  files by path for proof.
- **`-x`, `--upload`, `--delete` are active exploitation / modification.**
  Command execution and file writes are beyond "enumeration" and almost always
  beyond a bug bounty's RoE. `--delete` is destructive; `--skip` only removes
  the *confirmation prompt*, it does not make deletion safe.
- **Credentials on the command line leak.** `-p 'Password1'` lands in shell
  history, the process table (visible to other users via `ps`), and any command
  logging. Use `-p -` with `--prompt`, or pass hashes/ccache rather than
  plaintext where possible.
- **`-L`/`-F` need admin (and `-F` needs PowerShell on the victim) and read
  contents.** Content search is beta, admin-gated, and runs on the remote host —
  treat it as post-exploitation, not recon.
- **Writable ≠ exploited; admin ≠ owned.** Report the capability and its
  concrete impact (logon-script poisoning, web-root write, config tampering)
  rather than proving it by modifying production data.
- **Beta features drift.** The README marks content search "beta" and Kerberos
  "super beta"; re-verify behavior against `smbmap -h` on your installed build
  (frontmatter pins `v1.10.8`; upstream README shows `v1.10.7`) before relying
  on edge-case flags in a report.

## Reporting evidence

What to capture for an SMB share finding — the privilege level of the session is
half the impact, so state it explicitly:

- **The session context.** Which identity produced the result: null/anonymous,
  guest, or a named low-privilege account (and its group memberships). "A null
  session reads `\\host\HR`" and "a domain user reads `\\host\HR`" are different
  severities.
- **The share/permission table, verbatim.** smbmap's output — host, share name,
  `privs` (READ ONLY / READ, WRITE / NO ACCESS), and the share `Comment` column.
  This is the ACL evidence; `--csv`/`-g` give you an attachable artifact.
- **A bounded directory listing, not the data.** For a readable sensitive share,
  include the `-r --dir-only` or `-g` listing (paths, sizes, timestamps) showing
  *what class* of data is exposed. Prove sensitivity with **one** representative,
  redacted file excerpt — never the bulk contents.
- **Signing / OS context** (`--signing`, `-v`) where relevant to impact (relay
  chains, legacy-host risk).
- **The exact command run, with secrets redacted,** and a timestamp. Redact
  passwords/hashes; show `--no-write-check` was set to document that you did not
  write to the target.
- **Remediation pointer.** Tie it to the root cause: tighten the share ACL,
  disable anonymous/guest access (`RestrictNullSessAccess`,
  `RestrictAnonymous`), remove sensitive files from general-access shares,
  enable and require SMB signing, and (for writable shares) restrict write to
  the roles that need it.

Keep the proof minimal and reversible: no mass downloads, no uploads, no
deletions, and clean up any test artifact if a write was authorized and
performed. The report should let a triager reproduce the *listing* with a
read-only command, not re-run your exfiltration.

## References

- smbmap repository and README (ShawnDEvans/smbmap) — https://github.com/ShawnDEvans/smbmap
- smbmap source, write-check mechanism (`get_shares`, `PERM_DIR`, `write_check` / `--no-write-check`) — https://raw.githubusercontent.com/ShawnDEvans/smbmap/master/smbmap/smbmap.py
- enum4linux-ng (null-session enumeration companion) — https://github.com/cddmp/enum4linux-ng
- NetExec (nxc) SMB share/spray companion — https://github.com/Pennyw0rth/NetExec

*Research supplement added 2026-11-16, grounded in the upstream smbmap README and source (ShawnDEvans/smbmap, v1.10.7/v1.10.8; write-check mechanism verified in smbmap/smbmap.py) with enum4linux-ng/NetExec as pairing references; all flags verified against the upstream usage block (model: claude-opus-4-8).*
