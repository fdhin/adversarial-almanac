---
technology: "vsftpd"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: FTP daemon (TCP/21)
license: GPL-2.0
homepage: "https://security.appspot.com/vsftpd.html"
provenance: "corpus-grounded (a 2.3.4 backdoor reproduction, a vsftpd.conf/PAM analysis, banner evidence on 12+ hosts) + upstream-verified (Wikipedia/security.appspot.com vsftpd history for CVE-2011-2523, Red Hat docs for user_list/ftpusers semantics). Companion overview: [[ftp|FTP]]."
---

# vsftpd attack surface

Target model: vsftpd on 21/tcp, banner `220 (vsFTPd X.Y.Z)`, config at
`/etc/vsftpd.conf` (Red Hat family: `/etc/vsftpd/vsftpd.conf`), anonymous root
`/var/ftp`, PAM service `vsftpd`. Authorization assumed; the backdoor check below has a
non-destructive verify. Protocol-level FTP patterns (anonymous mirror, reuse, bounce) live
on [[ftp|the FTP router page]].

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| 2.3.4 backdoor (CVE-2011-2523) | banner says 2.3.4 | yes | Root shell via port 6200 — Critical |
| Anonymous access | anonymous_enable (default YES in stock conf) | yes | File oracle — High if data is sensitive |
| Local-user password auth | local_enable + PAM | credentialed | System credential reuse — Critical if shared |
| userlist/ftpusers misconfig | userlist_enable/userlist_deny | pre-auth | Username oracle, auth bypass edges — Info/Medium |
| Config/file-read via another bug | any read primitive on the host | n/a | chroot escape targets, PAM chain, paths — recon |

Banner first, always: `nmap -p21 -sV 198.51.100.10` → `21/tcp open ftp vsftpd 3.0.3`.
Observed banners: 2.0.8 or later (one, via nmap's fuzzy match), 2.3.4 (one),
3.0.3 (five engagements), 3.0.5 (two engagements).

## Phase 1 — The 2.3.4 backdoor (CVE-2011-2523)

History, verified upstream: in July 2011 the vsftpd master site's
`vsftpd-2.3.4.tar.gz` was discovered **compromised** — the tarball (not any released
source tree) carried a backdoor. A username containing `:)` during login triggers a
listen on **port 6200** on the target; connecting to 6200 yields a root shell. Any
internet-facing 2.3.4 banner is therefore near-conclusive by itself.

Non-destructive verify (an observed reproduction):

```bash
# 1. Trigger: any USER containing ':)'
ftp 198.51.100.10        # user: backdoor:)   pass: anything -> login "fails", that's fine
# 2. Check the side channel without sending commands
nc -zv 198.51.100.10 6200 && echo "BACKDOOR PORT OPEN"
```

For authorized full exploitation, connect to 6200 and get a root shell (public PoCs do
trigger+connect in one script). Report evidence: the 2.3.4 banner, the 6200 open-port
proof after the `:)` login attempt, and the timeline note that this was a supply-chain
compromise, not a coding bug — remediation is version replacement, not patching.

## Phase 2 — Anonymous and file-oracle work

Stock `vsftpd.conf` ships `anonymous_enable=YES`; `/var/ftp` (plus `/var/ftp/pub`) is the
anon root, world-readable. Method as on the protocol page: mirror everything, triage by
name (`.bak`, `*.enc`, zip/mdb, dotfiles). One observed anonymous share held
`.drupal.txt.enc` → OpenSSL salted file → CMS credentials.

## Phase 3 — userlist and ftpusers: deny behavior you can turn

Two separate lists, different mechanics (Red Hat-documented semantics):

- **`/etc/vsftpd.ftpusers`** (path varies: `/etc/ftpusers`) — PAM-enforced always-deny
  list for FTP: `root`, `bin`, `daemon`, ... If a *newly interesting* user is missing from
  it, `root` may be authable over FTP even when sshd refuses passwords.
- **`/etc/vsftpd.user_list`** with `userlist_enable=YES` — checked **before the password
  prompt**:
  - `userlist_deny=YES` (default): listed users get instant `530 Permission denied`
    while unlisted users reach the password prompt → **a live username oracle** (calibrate
    reply shapes first).
  - `userlist_deny=NO`: the list flips to an **allow-list** — only listed users may
    authenticate (and they must not also be in ftpusers). Discovering this mode explains
    "why can't I brute that user" and sometimes hands you the valid-user list if you can
    read the file.

Username-oracle verify: `ftp 198.51.100.10` with USER `root` vs USER `randomxyz`;
a reproducible instant-530 vs password-prompt split is the oracle. Low severity alone;
feed the list to Phase 4.

## Phase 4 — Local users, PAM, and config lockdowns

With `local_enable=YES`, system users authenticate via the PAM service named by
`pam_service_name=vsftpd` — FTP passwords are system passwords (one observed chain replayed
web-app creds against FTP successfully). Config-audit checklist when you can read
`vsftpd.conf` (one observed audit did this file by file):

- `anonymous_enable` — should be NO on anything holding real data.
- `local_enable` + `write_enable` — user writes land in their home unless chrooted.
- `chroot_local_user` / `chroot_list_enable` — who escapes the chroot; one observed deployment
  used per-user files under `/etc/vsftpd/user_conf/` (e.g. `user_config_dir`) — read
  those for per-user roots and permissions.
- `pam_service_name` — a nonstandard value points at a custom `/etc/pam.d/*` chain;
  that chain (one observed deployment used MariaDB-backed auth) is both a bug surface and a
  credential-source hint (shared service passwords).
- `userlist_*`, `ftpusers` — Phase 3 behaviors.
- `ssl_enable` — if NO, everything is plaintext capture material on-path.

## Phase 5 — Post-foothold value

- Readable config reveals paths and per-user roots for later chroot-escape claims.
- Writable FTP roots (`write_enable` + `anon_upload_enable` or a writable user dir) +
  any local file-inclusion/web path = upload-to-execution chains; the FTP write is the
  primitive, report the chain.
- FTP service user (`ftp`/`nobody`) context: shells via FTP-borne webshells execute as
  that user — scope the impact statement accordingly.

## Reporting notes

Version claims must quote the banner (`220 (vsFTPd 2.3.4)`); nmap's `vsftpd 2.0.8 or
later` fuzzy form is NOT sufficient to claim the backdoor — confirm with a raw connect.
The 2.3.4 finding is Critical and pre-auth; anonymous access severity scales with the
data exposed (cite the mirrored file list); the userlist oracle is Info/Low.

## Appendix — Tool map

- **nmap** (`-sV`, `--script ftp-anon,ftp-bounce`) — banner, anonymous check, bounce check.
- **ftp / lftp / wget -m** — client, mirror, passive-mode control — Phases 2-4.
- **hydra / medusa** — ftp module for targeted credential tests — Phase 4.
- **nc** — 6200 backdoor port check — Phase 1.
- Tools vault companions by name: nmap, wget, lftp, hydra, netcat.

CVE index: 2011-2523 (2.3.4 backdoor tarball compromise, July 2011 — banner-gated) ·
note CVE-2015-1419 (vsftpd ≤3.0.2 on Red Hat, access-restriction bypass via
deny_file parsing — rare, but it is the one other vsftpd CVE worth a banner check).
