---
technology: "rsync"
kind: overview
audience: offensive-security
family: network-services
layer: file transfer / daemon (TCP/873 default; SSH transport mode via -e ssh; custom ports common)
homepage: "https://rsync.samba.org"
status: confirmed
provenance: "command-context re-gate (2026-11-12): 7 files with real evidence (structure-pass claim 29 was inflated by bare-word 'rsync' client mentions). Evidence chains: custom-port daemon 8730 (anonymous module -> world-readable /etc/rsyncd.conf -> hidden list=false module + /etc/rsyncd.secrets, 13-byte secrets-file length side-channel, RSYNC_PASSWORD rockyou brute -> authenticated write); module enum (rsync -av --list-only rsync://target/ -> unauth backups module -> jenkins.tar.gz); no-auth daemon (read /src/etc/shadow + arbitrary write via cron.d push); module list -> EncFS conf_backups -> encfs2john; sudo NOPASSWD rsync wildcard -> flag-filename argument injection copying /root/.ssh/id_rsa; cron rsync wildcard * -> '-e bash shell.sh' filename injection -> root shell; client-side transfer usage. Upstream-verified: rsyncd.conf(5) man page (module/auth/secrets/list semantics), GTFOBins rsync, CVE-2014-9512 (NVD/Gentoo GLSA 201605-04: just-sent symlink arbitrary write, fixed 3.1.2). Enrichment pass 1, wave 2. Supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/873-pentesting-rsync.md), CC BY-NC 4.0, accessed 2026-11-13 (rsync 3.4.0/3.5.0/3.5.1 security releases, banner-vs-package-version semantics, extended config keys, rsync-ssl CVE-2026-70454 — verified against upstream security page + NEWS; see the methodology supplement)."
---

# rsync

> A daemon whose default deployment questions are "which directories, anonymous?" — and whose config file, readable through the very module it exports, tells you exactly where the secrets are.

## What it is

rsync (Andrew Tridgell / Samba team, GPL) is the standard delta-transfer file-copy tool: the same binary is client and daemon. As a **daemon** (`rsync --daemon`, default port 873/tcp, configured by `/etc/rsyncd.conf`) it exports named **modules** — `[name]` sections mapping a symbolic name to a `path`, with per-module access rules. The operator-critical semantics from rsyncd.conf(5): modules are **read-only by default** but admins flip `read only = no` for backup flows; `auth users` + `secrets file` (plain `user:password` lines) add challenge-response auth **only if configured** — otherwise the module is anonymous; `list = false` hides a module from listings (the daemon even "pretends the module does not exist" for denied clients, but **the name still works if you know it**); `hosts allow/deny` gate by source IP; the daemon drops to `nobody` by default unless `uid` says otherwise. There is no encryption on the daemon protocol — passwords traverse as challenge-response, files in cleartext; the "secure" deployment is the **SSH transport mode** (`rsync -e ssh host:path`), which is just rsync over an SSH session and exposes no daemon surface at all.

The attack model splits cleanly. **Daemon-facing**: `rsync target::` with no credentials lists modules (when `list` allows it); anonymous modules hand over files; write-enabled modules accept pushes (the arbitrary-file-write lane — an observed chain pushed a `cron.d` entry to root); and the config file itself, when an anonymous module exports `/etc`, leaks hidden module names, secrets-file paths, and ACL logic (observed). **Client-facing on a won box**: rsync under sudo/cron with a **shell wildcard** in the path is argument-injection fuel (observed twice), and GTFOBins documents the direct `-e '/bin/sh -c ...'` shell escape when you control the full command line. There is also genuine memory-corruption history on the daemon side — CVE-2014-9512 (fixed in 3.1.2) let a malicious sender write outside the destination via a just-sent symlink — but modern engagements are won on configuration, not memory bugs.

The operator takeaway: **`873/tcp open rsync` is a directory-listing prompt**. One `rsync target::` away from knowing exactly what the operator accidentally shared, and if the config is world-readable through any module, the whole daemon's security model is disclosed in one file.

## The anatomy that matters on an engagement

    rsync -av --list-only rsync://198.51.100.30/      <- anonymous module listing (gate: list=)
      module found -> rsync rsync://198.51.100.30/backups ./loot    <- anonymous read
    auth required?  RSYNC_PASSWORD=... / --password-file    <- challenge-response (secrets file)
    write module (read only = no) -> rsync localfile rsync://198.51.100.30/src/etc/cron.d/x
      <- push lane: arbitrary file write as the module's uid
    config disclosure: /etc exported anywhere -> rsyncd.conf -> hidden modules + secrets path
    sudo/cron client with wildcard path -> filename '-e ...' / '--chmod ...' injection -> root

Three structural properties drive most findings:

- **Anonymous access is the default posture, not the exception** — every protection (`auth users`, `hosts allow`, `list = false`) exists only if someone wrote it; the observed daemon footholds were all anonymous reads.
- **The config file is the map to the treasure** — rsyncd.conf names hidden modules (`list = false`) and their `secrets file`; one engagement even measured the secrets file through a module (13 bytes = `user:` + newline + 8-char password) to size a brute-force — the man page itself notes passwords longer than 8 characters often don't work client-side, so operators choose short ones.
- **Write modules are file-write primitives** — a `read only = no` module that maps into `/etc`, cron dirs, or service paths converts directly to code execution as the module's uid (root, if misconfigured): one observed pivot was a single pushed cron file.

## Where you'll meet it

- **Backup and sync infrastructure** — 873/tcp (or custom ports — 8730 observed) on storage/backup hosts; backup modules are the classic anonymous read (an observed jenkins.tar.gz, EncFS conf backups).
- **Internal appliance fleets** — NAS/backup agents shipping rsyncd with permissive defaults; expect `hosts allow` to be the only (IP-based, spoofable-adjacent) control.
- **Cron/ci automation on Linux boxes** — client-mode rsync with wildcards under root cron or sudo (both observed); no daemon involved, pure argument-injection surface.
- **As a post-exploit convenience** — operators routinely use rsync over scp for big-file transfer; on the target it also tells you where writable network paths live.

## Reading the daemon's mind

1. **`rsync target::` is the whole recon phase** — an empty listing means `list = false` everywhere or `hosts allow` rejection; both still serve modules by name, so guess common ones (`etc`, `backups`, `home`, `src`, `www`, `share`).
2. **`list = false` hides but does not protect** — rsyncd.conf(5): the daemon pretends a hidden module doesn't exist when a denied client asks, but a known-name access attempt goes straight to the auth/ACL check.
3. **`read only = no` + a privileged `uid` = file-write RCE lane** — the daemon writes as the module's uid; cron.d, ssh authorized_keys paths, and service configs are the push targets (an observed hidden per-user module was exactly a `read only = no` + `uid = <user>` module).
4. **Secrets files are short-password warehouses** — plaintext `user:password` lines, often ≤8 chars by protocol necessity; `RSYNC_PASSWORD` makes them brute-able in a tight loop without prompts.
5. **Daemon mode has no encryption** — credentials are challenge-response but traffic is cleartext; the hardened alternative (SSH mode) is a different surface entirely, so don't report an rsync daemon finding because you saw `rsync -e ssh` in a cron file — that's SSH-exposure analysis.

## Where to go next

[[rsync-bug-bounty|rsync bug bounty methodology]] — module enumeration, anonymous read, secrets-file side channels and brute force, write-module abuse, and the sudo/cron wildcard injections.

## Supplement (2026-11-13): what changed since 2014

Rewritten from HackTricks (src/network-services-pentesting/873-pentesting-rsync.md), CC BY-NC 4.0, accessed 2026-11-13; full verification detail in the [[rsync-bug-bounty|methodology supplement]].

- **The malicious-peer CVE record is current and large**: 3.4.0 (Jan 2025) fixed six issues affecting ≤3.3.0 — a daemon heap overflow, an uninitialised-memory leak, and a malicious **server** reading arbitrary files from or writing outside the destination of a pulling **client** (CVE-2024-12084..12088, -12747). 3.5.0 (Aug 2026) fixed 33 more across path confinement, daemon parsing, `rrsync`, and exec hooks; 3.5.1 (Sep 2026) cleaned up path regressions. "We only pull" is an attack position, not a safety property, on an unpatched client.
- **The `@RSYNCD: 31.0` banner is the protocol version, not the package version** — protocol 31 spans 3.1.0 through modern releases, so it cannot gate any CVE threshold; get `rsync --version` or the distro changelog instead.
- **Config keys beyond modules and secrets** carry their own surfaces: `pre-xfer exec`/`post-xfer exec` hooks, `proxy protocol` (spoofable source ACLs if the trusted range is reachable), `name converter` (execution as daemon), and the `munge symlinks`/`insecure links`/`use chroot` confinement knobs.
- **`rsync-ssl` was not endpoint authentication before 3.5.0** (CVE-2026-70454): stunnel mode did no CA verification or hostname binding, so TLS-wrapped rsync could be MITM'd by an active attacker.
