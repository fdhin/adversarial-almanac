---
technology: "FTP"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "upstream-verified methodology, written against the FTP overview [[ftp]] and companion [[ftp-vsftpd]]. Primary sources consulted and verified 2026-11-19: RFC 959 (PORT/PASV semantics), RFC 2428 (EPSV/EPRT), RFC 4217 (explicit FTPS / AUTH TLS); vsftpd.conf(5) (anonymous_enable/anon_root/chroot_local_user defaults) via Arch/Debian/RHEL manpages; ProFTPD configuration docs (<Anonymous> block + implicit daemon-user anonymous rule); Pure-FTPd NoAnonymous default; Microsoft IIS anonymousAuthentication config reference (IUSR identity); nmap ftp-bounce/ftp-anon NSE docs; FileZilla credential-storage format (sitemanager.xml / recentservers.xml, <Pass encoding=\"base64\">); THC-hydra and medusa FTP module usage; lftp mirror docs; ProFTPD 2026 advisory cluster (one-line version-gate context only)."
source: "RFC 959/2428/4217; vsftpd.conf(5), proftpd configuration.html, pure-ftpd README; IIS anonymousAuthentication reference (iis.net/learn.microsoft.com); nmap.org NSE docs (ftp-bounce, ftp-anon); FileZilla project trac + credential-recovery writeups; mankier hydra(1) + aldeid medusa; lftp manpage/cheatsheet — all accessed 2026-11-19"
---

# FTP bug bounty methodology

Companion pages: [[ftp|FTP overview]] (the protocol anatomy, product monoculture, and reading-the-banner notes) and [[ftp-vsftpd|vsftpd attack surface]] (the dominant implementation's config-audit method and 2.3.4 backdoor history). This page is the methodology: how you turn a 21/tcp listener into a report without pulling a byte more than the proof needs.

Target model: **21/tcp control channel** (plus a data channel — active per PORT, or passive per PASV), speaking RFC 959. Implementation is read off the banner, not assumed. Everything here is read-first, single-connection, low-volume, and evidence-oriented; the offensive craft is the product, the discipline is what keeps it inside an authorized program. Targets throughout are `example.com` / `*.example.com` / `198.51.100.0/24`.

## Where the money is (triage priority)

| Priority | Finding class | Gate | Typical severity |
|---|---|---|---|
| 1 | Anonymous read lands on real data (backups, configs, source, creds) | `anonymous`/`ftp` accepted + a non-empty share | High–Critical |
| 2 | Credentials recovered from a reachable FTP client/config file | read access to a host or repo | High–Critical |
| 3 | Anonymous **write** (upload/overwrite, webroot-backed) | `anon_upload_enable` or world-writable dir | High–Critical |
| 4 | Credential reuse — FTP password is an SSH/OS/app password | `local_enable`, PAM-backed users | High |
| 5 | Plaintext control+data channel (no FTPS available/enforced) | no `AUTH TLS` offered or not required | Medium (hardening) → High on a shared segment |
| 6 | FTP bounce (PORT redirection to a third host) accepted | legacy/misconfigured server | Medium–High, **report-class** (confirm before claiming) |
| 7 | Username oracle via `331` vs `530` reply shapes | any login prompt | Low–Info (amplifier for #4) |
| 8 | Version-gated CVE on an outdated daemon | banner pins a vulnerable build | per-CVE |

Phase 0 fingerprints; Phase 1 is the anonymous-auth decision tree (where most quick wins live); Phase 2 is enumeration discipline; Phases 3–5 are the exposure classes; Phase 6 is brute-force restraint. Work them in order on a fresh target.

## Phase 0 — Probe, banner, and version

The `220` banner arrives before any crypto and before any credential, and it usually names the product and version. Gate every later claim on it.

```bash
nmap -sV -p 21 198.51.100.10
# -> 21/tcp open  ftp  vsftpd 3.0.5
nc -nv 198.51.100.10 21
# -> 220 (vsFTPd 3.0.5)                         # vsftpd
# -> 220 ProFTPD Server (Debian) [::ffff:198.51.100.10]   # ProFTPD
# -> 220---------- Welcome to Pure-FTPd [privsep] [TLS] ----------   # Pure-FTPd (announces TLS/privsep in-banner)
# -> 220 Microsoft FTP Service                  # IIS FTP
# -> 220 FileZilla Server 1.x                   # FileZilla Server (Windows)
```

```bash
# One NSE pass that fingerprints + probes anonymous + dumps the FTP syst/stat in one shot
nmap -p 21 --script ftp-anon,ftp-syst 198.51.100.10
# ftp-anon -> "Anonymous FTP login allowed (FTP code 230)" + a directory listing when it fires
# ftp-syst -> STAT/SYST output: server type, local time, connection counts
```

Record the banner verbatim — it is your product pin for the anonymous-semantics table below and your CVE gate. `220 (vsFTPd 3.0.5)` leaks version directly; Pure-FTPd and IIS leak product but often not version; a stripped banner (`220 FTP server ready`) means you fingerprint by behavior (reply shapes, `SYST` output, `FEAT` capabilities — next).

### Reply codes worth parsing

An agent reading FTP responses should key decisions off the three-digit code, not the prose. The ones that change your next move:

| Code | Meaning | What it tells you |
|---|---|---|
| `220` | Service ready | banner — product/version pin |
| `230` | Login successful | you are in — go to Phase 2 |
| `331` | Username OK, need password | the account exists / is accepted → **username oracle** |
| `530` | Not logged in | rejected — the wording distinguishes "anonymous only" vs "login incorrect" |
| `150` / `226` | Data connection opening / transfer complete | the data channel worked (passive/active is fine) |
| `425` / `426` | Can't open / connection closed data channel | **passive range filtered or active blocked** — switch mode, not a server fault |
| `257` | Pathname created / `PWD` reply | current directory — anon root location |
| `550` | File unavailable / permission denied | the permission boundary — note which paths give it vs `226` |
| `234` | AUTH command OK, start TLS | explicit FTPS upgrade accepted (Phase 5) |

```bash
# FEAT enumerates server capabilities without logging in — TLS support, UTF8, MLST, REST, etc.
printf 'FEAT\r\nQUIT\r\n' | nc -nv 198.51.100.10 21
# 211-Features:
#  AUTH TLS        <- explicit FTPS available (Phase 5)
#  EPSV            <- RFC 2428 extended passive
#  MLST/MLSD       <- machine-readable listings (better than LIST for parsing)
#  REST STREAM     <- resume supported
# 211 End
```

## Phase 1 — Anonymous authentication semantics (the decision tree)

"Anonymous login" is not one behavior. **What anonymous lands you depends entirely on the implementation and its config**, and the difference decides whether you have a finding or a dead end. The username is `anonymous` or `ftp`; convention supplies `anonymous@` or any string as the password.

```bash
ftp -inv 198.51.100.10 <<'EOF'
user anonymous anonymous@example.com
pwd
ls -la
quit
EOF
# 230 Login successful.        -> you are in; map it (Phase 2)
# 530 Login incorrect / 530 This FTP server is anonymous only -> no anon, pivot to creds
```

Per-implementation semantics — verified against each project's config docs:

| Server | Anonymous controlled by | Default | What it lands you |
|---|---|---|---|
| **vsftpd** | `anonymous_enable=YES`; root is `anon_root` (falls back to `/var/ftp`, the `ftp` user's home) | **distro-dependent** — upstream NO, but several distros ship YES | a chrooted anon dir; writable only if `anon_upload_enable`/`anon_mkdir_write_enable` **and** `write_enable` are all set (all default NO) |
| **ProFTPD** | an explicit `<Anonymous>` block (Debian ships it commented out). Also an **implicit rule**: a login as the daemon's own run-user is treated as anonymous (never for root) | off unless the block is uncommented | the block's `User`/`Group` uid after a chroot to the block's directory — so anon can land on a *real* system account's reach if misconfigured |
| **Pure-FTPd** | `NoAnonymous yes` is the shipped default; anonymous requires turning it off | off | the `ftp` user's home when enabled; Pure-FTPd drops anon connects by default (confuses Windows Explorer, which logs in anonymously) |
| **IIS FTP** | per-site `anonymousAuthentication enabled`, identity `IUSR` (member of Users+Guests) | per-site toggle, not globally on | a Windows share tree; `dir` returns the true layout (`\Backups\`, `\Engineer\`) — IIS anon shares frequently sit next to the web app they feed |

The decision the tree forces:

1. **Anon accepted + non-empty listing → you have a file oracle.** Go to Phase 2; this is the #1 payout class.
2. **Anon accepted + empty/incoming-only dir → test write, don't assume read value.** A world-writable `/incoming` or an `anon_upload_enable` vsftpd is a finding *if* the upload lands somewhere that executes or is served (a webroot-backed FTP dir is the classic chain). Upload a single benign marker, confirm it took, delete it:

```bash
echo "authorized-poc-$(date +%F)" > /tmp/poc.txt
ftp -inv 198.51.100.10 <<'EOF'
user anonymous anonymous@example.com
put /tmp/poc.txt poc.txt      # 226 Transfer complete -> write confirmed
ls poc.txt                    # the listing row is the evidence
delete poc.txt                # 250 Delete operation successful -> clean up
quit
EOF
# The chain to name (don't necessarily fire): if this dir is served at http://example.com/,
# the next step is uploading a handler the server will execute (shell.php on PHP, .aspx on IIS).
# Prove write + prove the dir is web-served (fetch poc.txt over HTTP) = the finding, before any shell.
```
3. **ProFTPD implicit-anonymous surprise** — if the banner is ProFTPD and `anonymous`/`ftp` fails, test the daemon's own run-user name (`nobody`, `proftpd`, `ftp`) as the username: the implicit rule can treat that as an anonymous login into a more privileged directory than a normal anon block.
4. **Anon rejected → the account prompt is still a username oracle.** `331 Please specify the password` (user exists / anon allowed) vs `530 This FTP server is anonymous only` vs `530 Login incorrect` differ per product and per account; calibrate with a known-valid and a random name before Phase 6.

## Phase 2 — Post-login enumeration discipline (the listing is the recon)

Once you are in — anonymous or credentialed — **inventory metadata first, pull files second.** The discipline that keeps this inside an authorized program: a directory listing is free recon and leaves almost no footprint; a mass `mirror` of someone else's data is exfiltration you have to justify. Build the map, then pull only what proves the finding.

```bash
# Inventory the whole tree as metadata, no file contents — this is the safe recon pass
lftp -u anonymous,anonymous@example.com 198.51.100.10 -e \
  'set ftp:list-options -a; find; quit'
# find walks every directory recursively and prints paths only — your target map, zero file pulls

# Sizes without contents: du tells you what is worth pulling before you pull it
lftp -u anonymous,anonymous@example.com 198.51.100.10 -e 'du -h; quit'
```

Read the listing like a recon artifact:

- **Triage by name, not by curiosity.** The high-value names: `*.bak`, `backup*`, `*.mdb`/`*.sql`/`*.dump`, `*.zip`/`*.tar.gz`, `.git`/`.svn`, dotfiles (`.env`, `.htpasswd`, `.ssh`, `.drupal.txt.enc`), `web.config`/`app.config`, `*.pem`/`*.key`. Use `ls -la` (or MLSD where `FEAT` advertised it) so hidden files and permissions both show.
- **Permission mapping is a finding in itself.** The listing's mode column tells you which directories are world-writable (`drwxrwxrwx`) — an anon-writable dir that is also web-served is a write-to-RCE chain; a writable config dir is a tampering surface. Note the owner/group column too: files owned by a service account say which system user the daemon runs as, and whose creds a reused password would be.
- **Pull only the proving file.** When a `backup.sql` or `web.config` is the finding, fetch *that one file* (`type binary` first for anything non-text), redact secrets in the report, and quote the minimal excerpt. Do not mirror the share "to be thorough" — you are holding another org's data, and the listing already proved the exposure.

```bash
# Targeted single-file pull with a binary-safe transfer, then stop
ftp -inv 198.51.100.10 <<'EOF'
user anonymous anonymous@example.com
binary
get backup.sql
quit
EOF
```

### Passive vs active, and the firewall

Which channel the data takes is a tooling property that masquerades as a hang, not an error — know it before you blame the target:

- **Passive (PASV / EPSV)** — the client opens the second connection to a server-chosen high port. Normal today; the passive port range must be open through the firewall. When the data connection stalls after a working control channel, the passive range is filtered: `ls` hangs, login does not.
- **Active (PORT / EPRT)** — the server connects *back* to the client. Needed when the server's passive range is blocked, and the mode where bounce-class behavior lives (Phase 6). Force it when passive hangs: `ftp` interactive `passive` toggles it; `lftp` uses `set ftp:passive-mode off`; `wget` uses `--no-passive-ftp`.
- The EPSV/EPRT (RFC 2428) variants carry an explicit address family — relevant when the host is dual-stacked and a service is bound only on the IPv6 face (see [[ftp]]'s FXP section for the bounce-to-IPv6 pivot).

## Phase 3 — Credential exposure classes

FTP is a credential surface in three directions: on the wire, in the share, and in the clients that connect to it.

**Class A — plaintext on the wire (a finding by itself).** Unless FTPS is negotiated, `USER`/`PASS` and every byte of every transfer cross the network in cleartext. On a segment you are authorized to observe, or after an interception foothold, FTP creds are a gift — and even without capture, *the absence of mandatory TLS* is a reportable hardening finding on any program that treats credentials-in-transit as in-scope (Phase 5 covers detection). Frame impact honestly: "an attacker positioned on a shared segment or upstream path reads the credential," not "anyone on the internet."

**Class B — credentials inside the share.** The file-oracle triage of Phase 2 is a credential hunt: `web.config`/`app.config` connection strings, `.env` files, `.htpasswd`, `wp-config.php`, `*.sql` dumps with a `users` table, `.ssh/id_rsa`, and encrypted blobs (`Salted__` OpenSSL files that crack offline). A single config file pulled from an anonymous share routinely owns the rest of the engagement.

**Class C — credentials in FTP clients and their config.** When you have a foothold on a host (or a repo is in scope), the FTP *clients* store saved server passwords, and the storage is encoding, not encryption:

- **FileZilla** (verified format) — saved sites live in `sitemanager.xml`; Quickconnect-bar servers live in `recentservers.xml`. On Windows both sit under `%AppData%\FileZilla\` (`C:\Users\<user>\AppData\Roaming\FileZilla\`); on Linux under `~/.config/filezilla/`. Since v3.13 the password is a `<Pass encoding="base64">` element — **base64, not a cipher** — decodable by anyone who can read the file. Older versions stored it in plain text. A master password (if the user set one) is the only real protection.

```bash
# recover any FileZilla creds from a readable profile — base64 decode, no cracking
grep -oP '(?<=<Pass encoding="base64">)[^<]+' ~/.config/filezilla/sitemanager.xml \
  | base64 -d
# -> each decoded line is a stored FTP/SFTP password in cleartext
```

- Other clients store similarly: WinSCP (registry or `WinSCP.ini`, weakly obfuscated), and generic FTP clients that keep a `recentservers`/`sites` file. Seatbelt's `filezilla` command enumerates these on Windows hosts during post-exploitation. The report framing: a stored-credential cache on a shared or compromised host hands over every server that user connects to.

## Phase 4 — FTP bounce (PORT redirection): a REPORT-class technique

The mechanics, from RFC 959: the `PORT a1,a2,a3,a4,p1,p2` command tells the server which IP and port to open the *data* connection to. Nothing in RFC 959 requires that address to be the client's — so a server that honors a `PORT` pointing at a **third** host becomes your proxy. Point `PORT` at `198.51.100.50:22`, issue a `LIST`, and the server's response to the data connection (success vs refused) reflects whether that third host's port is open: a port scanner wearing the FTP server's source address, able to reach hosts behind the FTP box that you cannot. The same primitive historically relayed mail and bypassed source-IP ACLs.

**This is a report-class technique, not one you fire.** Modern servers refuse `PORT` to any host but the originating client by default (vsftpd's `port_promiscuous` defaults off; most daemons sanity-check the address) — so the finding is **detection of a server configured to allow it**, not an actual third-party scan through a production target. Detect it with a single confirming probe and report the config:

```bash
# nmap's ftp-bounce checks whether PORT redirection to a third host is accepted
nmap -p 21 --script ftp-bounce \
  --script-args ftp-bounce.username=anonymous,ftp-bounce.password=anonymous@example.com \
  198.51.100.10
# vulnerable  -> "ftp-bounce: bounce working!"
# hardened    -> "ftp-bounce: server forbids bouncing to low ports <1025"  (or no output)
```

Discipline: a positive `bounce working!` is the finding on its own — the server's willingness to redirect data to an arbitrary host. Do **not** then use it to scan the internal network of a bounty target; the config exposure is the report. (On an explicitly authorized assessment with a defined internal scope, the actual bounce-scan and the IPv6/FXP pivot in [[ftp]] become live techniques — different rules of engagement.) Note that bounce results are historically noisy (public re-tests have been inconclusive), so quote the exact NSE output and the banner version; a single clean "bounce working!" against a named daemon build is the defensible evidence.

## Phase 5 — TLS exposure: FTPS availability vs plaintext downgrade

FTPS wraps the control and data channels in TLS (RFC 4217). Two modes:

- **Explicit FTPS** (RFC 4217, the standardized one) — the session starts as plain FTP on **21/tcp**, then the client issues `AUTH TLS` (or legacy `AUTH SSL`) to upgrade before authenticating. Advertised in `FEAT` as `AUTH TLS`.
- **Implicit FTPS** (deprecated, never in RFC 4217) — TLS from the first byte on **990/tcp**.

The finding is not "FTPS is absent" alone — it is the **downgrade gap**: an explicit-FTPS server that *offers* `AUTH TLS` but does not *require* it lets any client (or a MITM stripping the capability) authenticate in cleartext on 21. Detect the posture:

```bash
# Is TLS even offered? (FEAT) — and does the server force it, or accept plaintext login?
printf 'FEAT\r\nQUIT\r\n' | nc -nv 198.51.100.10 21 | grep -i AUTH
# AUTH TLS present  -> explicit FTPS available
# (no AUTH line)    -> no TLS at all: everything is cleartext (Phase 3 Class A)

# Does a plaintext login still succeed when TLS is available? (the downgrade finding)
ftp -inv 198.51.100.10 <<'EOF'
user anonymous anonymous@example.com
quit
EOF
# 230 without any AUTH TLS  -> server accepts cleartext auth despite offering FTPS = downgrade-exposed

# Inspect the cert and TLS config on the implicit port, or after an explicit upgrade
nmap -p 21,990 --script ftp-syst,ssl-cert,ssl-enum-ciphers 198.51.100.10
# ssl-cert -> self-signed? expired? wrong CN? (self-signed-cert warning dialog is also a social-eng surface)
# ssl-enum-ciphers -> weak/legacy ciphers, SSLv3/TLS1.0 still enabled
```

Report the three distinct findings separately: **no TLS offered** (cleartext mandatory), **TLS offered but not enforced** (downgrade), and **weak TLS** (self-signed cert, legacy ciphers). The first two are credential-in-transit findings; the self-signed cert is a lower-severity note plus a phishing surface.

## Phase 6 — Credential brute-force, with restraint

Brute-forcing FTP is the last resort and the one most likely to be destructive or noisy, so frame it carefully. FTP has **no account lockout** on stock daemons and **no failed-auth rate limit by default** — which makes a guessable password a real finding, but also means a flood drops replies (and on a program, violates volume rules). The payout that actually recurs is not brute force but **reuse**: a password harvested from a web app, a config, or a database (Phase 3) replayed against FTP user auth, because `local_enable` FTP passwords are PAM-backed system passwords.

```bash
# Reuse check first — a handful of known-candidate pairs, not a wordlist flood
hydra -L users.txt -P candidates.txt 198.51.100.10 ftp -t 4 -o ftp-reuse.txt
#   -t 4 = four parallel tasks (keep low; UDP-less TCP still trips IDS at volume)
#   non-standard port: -s 2121 ;  URL form: hydra -l alice -P candidates.txt ftp://198.51.100.10

# medusa equivalent (different flag shape): -U users, -P passwords, -h host, -M ftp
medusa -U users.txt -P candidates.txt -h 198.51.100.10 -M ftp -O ftp-reuse.log
#   -q with -M prints the module's own options for this build before you assume any flag
```

Discipline baked in: run a **small, targeted candidate set** (reuse pairs, org-derived guesses, vendor defaults for appliances), never `rockyou` against a production login. The username oracle from Phase 1 (`331` vs `530`) narrows the user list before you spend a single guess. Report "weak/guessable FTP password + no lockout + no rate limit + plaintext protocol" as a chain, not the guess alone.

## Version-gated CVE history (one line)

Gate every CVE claim on the Phase 0 banner, and lead the report with the live misconfiguration (anonymous read, writable share, cleartext creds) rather than an old CVE: the perennial headliner is vsftpd 2.3.4's backdoor (CVE-2011-2523 — see [[ftp-vsftpd]]), while the current-year churn is in ProFTPD (2026 cluster: `mod_sftp` SCP integer overflow CVE-2026-63091, Directory-ACL bypass CVE-2026-35025, `mod_sql`/`mod_wrap2_sql` injection CVE-2026-44331 — fixed in 1.3.9e / 1.3.10rc5), all of which you only cite after the banner pins a vulnerable build.

## Reporting and evidence standards

The report is the product. For each finding:

1. **Title — impact first.** "Anonymous FTP read exposes production database backup (`backup.sql`, 198.51.100.10)" beats "anonymous FTP enabled."
2. **Preconditions.** The exact login used and its discovery class (default-anon, derived credential, reused web-app password).
3. **PoC.** Copy-pasteable `ftp`/`lftp`/`nmap` commands, one step each, with the verbatim server replies (`230 Login successful.`, the `ls -la` row that proves the sensitive file exists).
4. **Evidence, minimized.** Quote the proving *listing row* and a redacted excerpt of the one proving file — never the whole mirror. Hash any artifact you must attach (`sha256sum`). Delete pulled data after the report is accepted; never retain another org's backup.
5. **Remediation.** Server-specific: disable `anonymous_enable` / the `<Anonymous>` block / `anonymousAuthentication`; enforce `AUTH TLS` and reject cleartext; set `port_promiscuous=NO` (or equivalent) for bounce; rotate any exposed credential.

Evidence framing per class: a **topology/listing finding** quotes the directory map and states it needed only anonymous login; a **credential-in-transit finding** names the segment you could observe and that TLS was absent or not enforced; a **bounce finding** quotes the exact NSE output plus the banner build; a **stored-credential finding** shows the decoded element's *presence and location*, with the value redacted.

## CTF notes

FTP is a staple of anonymous-FTP boxes (HTB/OSCP-style), and the classic chains are exactly the exposure classes above — with the aggression dialed up because the box is yours:

- **The canonical anon box:** `ftp -inv <ip>` → `anonymous`/`anonymous@` → `ls -la` → a dropped file (`creds.txt`, a `.bak`, a private key, a password-bearing `note.txt`) → reuse it for SSH or the web app. Always `binary` before `get` on archives and images; always check hidden files.
- **Anonymous write → webroot:** an upload-enabled anon FTP whose directory is the web server's root → upload a webshell → RCE. Test write with a benign marker first, then the payload.
- **Credential reuse:** the FTP password *is* the SSH password (`local_enable`) — the box rewards trying the same pair against 22/tcp immediately.
- **Config-file loot:** a pulled `web.config`/`wp-config.php`/`.env` with a DB or admin credential that unlocks the next service.
- **What differs from a bounty program:** in a CTF you brute-force strings hard (`hydra -P rockyou.txt`), fire the bounce scan, and pull the whole share freely because the target is disposable. On an authorized program every one of those is throttled, logged, or exfiltration — you single-shot the reuse check, you *detect* bounce rather than scan through it, and you pull only the proving file. The reading tradecraft is identical; only the volume and the write discipline change. (Sibling network-service CTF surfaces often sit next to FTP on the same box: [[tftp]], [[smb]], [[ssh]], [[telnet]], [[nfs]], [[rsync]], [[iis]].)

## Tool mapping (prose)

`ftp`/`lftp` (OpenBSD/GNU clients) for interactive login, recursive `find` inventory, `du` sizing, and targeted single-file `get`/`mirror`; `nmap` with `ftp-anon` (anonymous check), `ftp-syst` (STAT/SYST dump), `ftp-bounce` (PORT-redirection detection), and `ssl-cert`/`ssl-enum-ciphers` (FTPS posture); `nc` for raw banner and `FEAT`/`AUTH` probing; `hydra` and `medusa` for restrained credential/reuse testing (named here for the FTP module shape — each build's `-U`/`-q` prints its own options, do not assume flags); `wget`/`curl` for non-interactive pulls with explicit passive/active control; Seatbelt for enumerating stored FTP-client credentials during post-exploitation; `base64` for decoding FileZilla `<Pass>` elements. Per-tool pages live in the tools vault.

## References

- RFC 959 (FTP, PORT/PASV semantics) — https://www.rfc-editor.org/rfc/rfc959
- RFC 2428 (FTP extensions for IPv6 / EPSV / EPRT) — https://www.rfc-editor.org/rfc/rfc2428
- RFC 4217 (Securing FTP with TLS / explicit FTPS / AUTH TLS) — https://www.rfc-editor.org/rfc/rfc4217
- vsftpd.conf(5) — anonymous_enable / anon_root / chroot_local_user / anon_upload_enable defaults — https://manpages.debian.org/man/5/vsftpd.conf
- ProFTPD configuration directives (`<Anonymous>` block + implicit daemon-user anonymous rule, AnonymousGroup) — https://www.proftpd.org/docs/directives/configuration_full.html
- Pure-FTPd README (NoAnonymous default) — https://github.com/jedisct1/pure-ftpd
- Microsoft IIS — Anonymous Authentication `<anonymousAuthentication>` (IUSR identity) — https://learn.microsoft.com/en-US/iis/configuration/system.webServer/security/authentication/anonymousauthentication
- nmap NSE — ftp-anon — https://nmap.org/nsedoc/scripts/ftp-anon.html
- nmap NSE — ftp-bounce (PORT-redirection detection, "bounce working!") — https://nmap.org/nsedoc/scripts/ftp-bounce.html
- FTP bounce attack (mechanics; modern servers refuse by default) — https://en.wikipedia.org/wiki/FTP_bounce_attack
- FileZilla credential storage (sitemanager.xml / recentservers.xml, `<Pass encoding="base64">`) — https://trac.filezilla-project.org/ticket/10270
- Seatbelt `filezilla` command (stored-credential enumeration) — https://docs.specterops.io/ghostpack-docs/Seatbelt-mdx/commands/filezilla
- THC-hydra man page (FTP module, `-L`/`-P`/`-s`/`-t`) — https://www.mankier.com/1/hydra
- medusa FTP module usage (`-U`/`-P`/`-h`/`-M`/`-q`) — https://www.aldeid.com/wiki/Medusa
- lftp mirror / find / du (recursive inventory, passive-mode control) — https://lftp.yar.ru/lftp-man.html
- FTPS explicit vs implicit (RFC 4217 standardized; implicit deprecated) — https://www.files.com/docs/clients-and-protocols/ftp-and-ftps/implicit-vs-explicit-security-ftps
- ProFTPD 2026 advisory cluster (CVE-2026-63091 / -35025 / -44331) — https://github.com/advisories/GHSA-gqm5-f386-9r92

*Methodology authored 2026-11-19 against the [[ftp]] overview and [[ftp-vsftpd]] companion. Sources consulted and verified this date: RFC 959/2428/4217; vsftpd.conf(5), ProFTPD configuration docs, Pure-FTPd README, IIS anonymousAuthentication reference; nmap ftp-anon/ftp-bounce/ftp-syst NSE docs; FileZilla credential-storage format and Seatbelt docs; THC-hydra(1) and medusa module usage; lftp manpage; ProFTPD 2026 GHSA cluster (version-gate context only). Model: claude-opus-4-8.*
