---
technology: "SSH"
kind: overview
audience: offensive-security
family: network-services
layer: remote-access protocol (TCP/22 default)
standards: "SSH protocol 2 (RFC 4251-4254 family)"
provenance: "evidence-grounded (281 files with command-context ssh usage; 185 beyond bare client connect) + upstream-verified (OpenSSH sshd(8)/ssh(1) man pages, CVE records). Enrichment pass 1, 2026-11-12."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# SSH

> The one remote-admin protocol everyone exposes — and the surface where most engagements end: password brute, stolen key files, cracked passphrases, or a hijacked agent turns "secure shell" into your shell.

## What it is

SSH (Secure Shell, protocol 2 per the RFC 4251–4254 family) is the encrypted remote-login and tunneling protocol that replaced telnet/rsh, default port **22/tcp**. One connection carries several separable phases an operator cares about: **banner exchange** (both sides announce software versions in the clear), **key exchange** (server proves its host key), **authentication** (publickey, password, keyboard-interactive, hostbased — tried in order), then **session/channel setup** (shell, exec, sftp subsystem, TCP/agent/X11 forwarding). Every phase leaks something different, and every phase has its own bug class.

The implementation landscape is a near-monoculture with an embedded edge: **OpenSSH** (OpenBSD-derived, the default on effectively every Linux/BSD and the source of nearly all SSH CVEs you will quote) is covered in depth on [[ssh-openssh|the OpenSSH page]]. **Dropbear** is the small static-linked SSH server of embedded Linux (OpenWrt routers, IoT firmware) — see the section below. **libssh** appears as a library inside other products (and carries its own auth-bypass CVE line, e.g. CVE-2018-10933). Fingerprint the implementation before applying any version-specific finding.

## The anatomy that matters on an engagement

    client → banner swap (cleartext: "SSH-2.0-OpenSSH_7.4" both ways — free version intel)
          → key exchange → host key presented (TOFU: first use trusts forever)
          → auth dialog: publickey → password → keyboard-interactive (order = recon data)
          → session: shell/exec/sftp + channels for -L/-R/-D forwarding, agent socket relay
    server files: /etc/ssh/ssh_host_* keys · sshd_config (+ sshd_config.d drop-ins)
    user files: ~/.ssh/id_rsa|id_ed25519 · authorized_keys · known_hosts

Three structural properties drive most findings:

- **The banner is free recon.** Version strings pass in the clear before any crypto; one nmap `-sV` or a raw connect tells you implementation, version, and often the OS flavor — enough to gate the CVE table.
- **The host-key trust model is TOFU.** First connection writes `known_hosts` and every later client (including cron jobs and CI pipelines that set `StrictHostKeyChecking=no`) trusts silently. Identical host keys across a fleet — or baked into firmware — are themselves reportable findings.
- **Credentials are files.** Private keys (`id_rsa`, `id_ed25519`), `authorized_keys`, and forwarded agent sockets are all filesystem objects that other bugs (LFI, redis write, backup leaks, world-readable homes) can read, write, or reuse. Most observed SSH footholds arrived through another service touching these files.

## Protocol-common attacks (any implementation)

These apply regardless of server implementation.

- **Password brute force** — rare in practice: hydra's ssh module, e.g. `hydra -L users.txt -P passes.txt ssh://198.51.100.10`. Slow by design (server-side rate behavior, MaxAuthTries); prefer wordlists built from the target's own leaks first.
- **Private-key hunting + passphrase cracking** — the dominant pattern. Any file-read primitive is worth pointing at `~/.ssh/id_rsa`, `/opt/*.key`, backup tars. A passphrase-protected key is only a speed bump: `/usr/share/john/ssh2john.py key > key.hash`, then john/hashcat with rockyou, then `chmod 600 key && ssh -i key user@198.51.100.10`. Observed repeatedly; one engagement cracked an `id_rsa.bak` found in /opt.
- **authorized_keys as a write target**. When you get file-write via another service (redis `CONFIG SET dir ~/.ssh`, webshell, NFS no_root_squash), appending your own public key to `authorized_keys` is the standard persistence: `echo "ssh-ed25519 AAAA... ATTACKER_KEY" >> ~/.ssh/authorized_keys`. Flip side: read an existing `authorized_keys` for usernames, key comments (often `user@host` or a generation hint) and forced-command options; One observed chain recovered a private key from the comment of a public key found this way.
- **Agent-forwarding hijack** — observed: with root on a box that users connect through with `ssh -A`, list the forwarded agent sockets (per-connection `ssh-XXXXXXXX` dirs, classically under /tmp), point `SSH_AUTH_SOCK` at the newest socket and authenticate as its owner — no key material needed, the agent signs for you. The ssh(1) man page itself warns users with file-permission bypass on the socket host can drive the forwarded agent.
- **Host-key trust abuse** — observed repeatedly: scripts/CI using `StrictHostKeyChecking=no` will connect to a MITM'd endpoint without complaint; `known_hosts` (hashed or not) on a conquered box maps every host the victim SSHes to — a pivot map.
- **Tunneling as post-foothold utility** — `ssh -L`, `-R`, `-D` (SOCKS) to reach interior services. Not a finding, but the standard way in/out once a key or cred lands; know the flags cold.
- **User-enumeration oracles** — implementation-specific timing/count side channels (the big one is CVE-2018-15473, on the OpenSSH page).

## Dropbear (embedded section)

Dropbear is the SSH server of embedded Linux: OpenWrt (config in UCI at `/etc/config/dropbear`, host keys under `/etc/dropbear/`), routers, cameras, buildroot images — statically linked, small, frequently untouched for years.

Engagement angles that differ from OpenSSH:

- **Config and secrets travel in firmware/backups.** Observed once: an OpenWrt backup tarball exposed `etc/config/dropbear` alongside `etc/passwd` — device config plus credential file in one download. Any firmware-extraction or backup-read path should target these paths first.
- **Root-by-default logins** — embedded images routinely ship `root` with no password or a vendor default on the Dropbear listener; `dropbear -s` (password auth disabled) is the mitigation and its absence is the finding.
- **Identical host keys across a model line** — keys generated at image-build time instead of first boot mean every unit shares a key; fingerprint two devices and compare.
- **Old crypto** — long-lived firmware lines run decade-old Dropbear versions against modern clients; treat the banner version as a hard gate for any Dropbear advisory before reporting.

## Where you'll meet it

- **Every Linux target, honestly** — 22/tcp is the default last hop of nearly every observed engagement chain; the interesting part is what authenticates (password reuse, cracked key, agent, or a written authorized_key).
- **Jump hosts and CI** — agent forwarding and `StrictHostKeyChecking=no` automation concentrate on bastions, cron and pipeline runners; conquer one and the trust relationships fan out.
- **Embedded/IoT** — Dropbear on OpenWrt-class devices: default root, firmware-baked keys, config-in-backup leaks.

## Reading the protocol's mind

1. **Banner = version gate**: `SSH-2.0-OpenSSH_7.4` answers before any auth; match against the CVE table on the OpenSSH page before promising an exploit.
2. **`auth publickey` failures leak username validity** on unpatched servers (CVE-2018-15473, ≤7.7) — enumerate users before spending brute-force budget.
3. **First-seen host keys are forever trusted** — clients with `StrictHostKeyChecking=no` (automation) never notice a swapped server key.
4. **A forwarded agent is a live credential** — no key file needed, `SSH_AUTH_SOCK` at a socket you can reach signs challenges as its owner.
5. **Key files beat passwords** — wherever you get any file read, try `id_rsa*`, `*.key`, backup archives; ssh2john converts, rockyou does the rest.

## Ports cheat-sheet additions (2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); NSE names and the `ssh_hostkey` script-arg verified against the nmap repo, patator's `ssh_login` module against its source. Banner grabbing, ssh-audit, hydra/medusa, CVE-2018-15473 and the libssh CVE-2018-10933 split are already covered above and on the OpenSSH page — skipped.

- **Algorithm and host-key inventory via NSE** — `nmap -p22 --script ssh2-enum-algos` lists every key-exchange, cipher and MAC the server will negotiate (hardening-report material, and the gate for any downgrade claim); `nmap -p22 --script ssh-hostkey --script-args ssh_hostkey=full` prints the complete key rather than just a fingerprint. Collect the full key for every SSH listener on the segment and diff — keys reused across a golden image or appliance line are a trust-relationship finding of their own.
- **Read the offered auth methods, then pin one** — a bare `ssh -v 198.51.100.10` prints the `Authentications that can continue:` line before any credential is spent, and `-o PreferredAuthentications=password` stops the client burning its attempt budget on publickey first. The offered list is the decision input for the credential attack that follows.
- **Username-as-password first** — before wiring any engine, try each harvested username as its own password. Single deliberate guesses survive rate limiting that kills rockyou-scale runs, and naming-convention credentials (`user:user`) outlive password rotations.
- **Two more brute engines** — beyond hydra/medusa: patator's `ssh_login` module drives wordlists with per-response actions (`-x ignore:mesg='...'` style), and nmap's companion cracker ncrack takes `ssh://` targets when the pipeline already speaks nmap.

## Where to go next

[[ssh-openssh|OpenSSH attack surface]] — authorized_keys tricks, sshd_config misconfigs (PermitRootLogin/PasswordAuthentication), user-enumeration CVEs, and the config-audit method.
