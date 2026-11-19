---
technology: "SSH"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: remote-access protocol (TCP/22 default)
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "upstream-verified against ssh(1)/sshd(8)/ssh_config(5)/ssh-keygen(1) man7 pages, OpenSSH release notes, ssh-audit docs, and NVD/GHSA records for the 2025 OpenSSH CVE cluster (26465/26466/61984/61985) and Terrapin (CVE-2023-48795). Companion overview [[ssh|SSH]]; OpenSSH config/CVE depth [[ssh-openssh|OpenSSH attack surface]]. Researched/verified 2026-11-19."
source: "OpenSSH man pages and release notes (openssh.com), ssh-audit (jtesta/ssh-audit), terrapin-attack.com, NVD + GitHub Advisory Database — all accessed 2026-11-19"
---

# SSH attack-surface methodology

Companion pages: [[ssh|SSH overview]] — the human introduction and the protocol-common
attack catalogue; [[ssh-openssh|OpenSSH attack surface]] — the OpenSSH-specific config
audit, `authorized_keys` options, certificate-CA forgery, and the per-version CVE index.
**This page is the method**: the ordered workflow, the decision trees, the detection craft,
and the evidence standard that turn a reachable `22/tcp` into findings — service-side,
client-side, and in the pivot. Where a technique has OpenSSH-specific depth it is named
here and deferred there; nothing is duplicated at length.

**Scope.** SSH protocol 2 as an engagement surface regardless of implementation (OpenSSH,
Dropbear, libssh-backed products, embedded stacks). Targets throughout: `example.com`,
`*.example.com`, `198.51.100.0/24`.

**Discipline (baked in, not bolted on).** Every step below is single-connection,
low-volume, and evidence-first. SSH servers are built to resist brute force and they log
every attempt; a flood is both useless and the fastest way to get your authorization
revoked. Prefer one deliberate guess over a wordlist, one crafted connection over a scan,
and capture the transcript as you go. The offensive craft is the product — the discipline
is what keeps it deployable on an authorized program.

## Where the money is (triage priority)

| Priority | Class | Where it lives | Typical severity |
|---|---|---|---|
| 1 | Private-key exposure (file read → `id_*`, `*.key`, backups) | Service-side, any impl | Critical |
| 2 | `authorized_keys` write via another bug (persistence/foothold) | Service-side | Critical |
| 3 | Weak/predictable host or user keys (Debian PRNG, short RSA, shared fleet keys) | Service-side | High–Critical |
| 4 | Credentialed misconfig escalation (`PermitRootLogin`, restricted-key escape) | Service-side → [[ssh-openssh]] | High–Critical |
| 5 | Agent-forwarding hijack on a bastion you hold | Pivot | High |
| 6 | Client-side RCE/MITM CVE class (ProxyCommand username injection, `VerifyHostKeyDNS` MITM) | Client-side | High–Critical |
| 7 | Weak credential / password reuse (targeted spray, not rockyou) | Service-side | High if it lands |
| 8 | User enumeration (timing/auth-method oracle) | Service-side | Low/Info (feeds 7) |
| 9 | `known_hosts` / `ssh_config` / agent-socket recon on a conquered host | Pivot intel | Info (multiplies 5) |
| 10 | Algorithm/downgrade posture (Terrapin gate, weak kex/cipher/MAC) | Service-side | Low/Info |

Rows 1–4 are where payouts concentrate once you have any adjacent foothold; 5–6 are the
high-value pivots and the fresh CVE class; 7–10 build and prove the map. Phase 0 first from
outside, then work the table top-down against whatever access you already hold.

---

## Phase 0 — Fingerprint beyond the banner

Goal: fix the implementation, the exact version, and the **negotiated** crypto — three
different gates for three different finding classes. The banner answers before any crypto,
so this phase costs one connection.

```bash
# Banner + auth methods in one probe
nmap -p22 -sV --script ssh-auth-methods,ssh2-enum-algos 198.51.100.10
# 22/tcp open ssh OpenSSH 9.6p1 Ubuntu 3ubuntu13.5 (protocol 2.0)   <- impl + version + OS
# ssh-auth-methods: publickey,password            <- PasswordAuthentication yes (gate for enum + spray)
# ssh2-enum-algos: lists every kex/cipher/MAC the server will negotiate
```

The banner is the version gate for every CVE you will quote — record it verbatim. But the
banner is also **forgeable and often wrong**: distros backport fixes while leaving the
version string stale (`OpenSSH 8.9p1` on an Ubuntu host may carry 9.x security patches),
and appliances lie on purpose. So never report a version-gated CVE from the banner alone —
confirm the behaviour (Phase 1 oracle, a negotiated-algorithm tell, or a patch-level note
from the distro) before you claim exploitability.

### 0.1 Protocol-negotiation quirks worth reading

Fingerprint *beyond* the version string from what the server is willing to negotiate:

- **Algorithm inventory → implementation tell.** `ssh2-enum-algos` (or `ssh-audit`,
  below) prints the kex/cipher/MAC/host-key lists. The *shape* of those lists fingerprints
  the implementation when the banner is scrubbed: Dropbear, libssh, PuTTY-derived servers,
  and Go `x/crypto/ssh` each offer distinctive sets and ordering. A host-key-type list
  missing `ssh-ed25519` but offering `ssh-dss` is an ancient or embedded stack.
- **Strict-KEX support = Terrapin gate.** If the server advertises
  `kex-strict-s-v00@openssh.com` in its KEXINIT it supports strict key exchange and is
  Terrapin-mitigated (see 0.2). Absence *plus* a vulnerable cipher is the finding.
- **Host-key inventory for the fleet diff.** `nmap -p22 --script ssh-hostkey
  --script-args ssh_hostkey=full 198.51.100.0/24` collects full host keys across the
  segment. Identical keys on two hosts = a golden-image or firmware-baked key, a
  trust-relationship finding on its own (detailed in [[ssh]]).

### 0.2 ssh-audit — the posture and downgrade pass

`ssh-audit` (Python tool, jtesta/ssh-audit) is the one-command crypto-posture report and
the right tool to cite for any downgrade/weak-algorithm or Terrapin claim:

```bash
ssh-audit 198.51.100.10                 # full kex/cipher/MAC/host-key grade
ssh-audit -P "Hardened OpenSSH Server"  # -P: check against a named built-in policy (-L lists them)
ssh-audit --json 198.51.100.10 > posture.json   # machine-readable evidence to attach
```

It colour-grades each primitive (yellow = weak, red = flawed), flags CVEs tied to the
detected version, and since v3.1.0 tests directly for **Terrapin (CVE-2023-48795)** — the
prefix-truncation downgrade against `chacha20-poly1305@openssh.com` and the `*-etm@openssh.com`
(Encrypt-then-MAC, incl. CBC-EtM) families, fixed by strict KEX in OpenSSH 9.6 (2024). A
one-line gate for reports: **Terrapin needs an active MITM plus a vulnerable negotiated
cipher plus a peer without strict KEX** — a scanner result that the cipher is *offered* is
a hardening note, not a demonstrated attack. The dedicated **Terrapin Scanner** confirms
whether strict KEX is actually negotiated on both sides when you need that precision.

Decision point: weak crypto posture is almost always Low/Info on a bounty (no secret
crosses without the MITM). Bank it as a hardening finding and move on — the payouts are in
Phases 1–6.

---

## Phase 1 — User enumeration and the auth-method oracle

Goal: learn *which users exist* and *which auth methods are live* before spending a single
credential guess. Both are free recon that scope the credential attack that follows — key
theft (Phase 2) and, where password auth is live, the targeted spray (triage priority 7;
its brute-force depth and rate-limit handling live on [[ssh]] and [[ssh-openssh]]).

### 1.1 Auth methods as an oracle

The server tells you what it will accept, in order, before any credential is spent:

```bash
ssh -v -o PreferredAuthentications=none -o StrictHostKeyChecking=accept-new \
  nonexistent@198.51.100.10 2>&1 | grep -i 'authentications that can continue'
# debug1: Authentications that can continue: publickey,password  <- the live method list
```

Read it as a decision tree:

- `publickey` only → password spray is dead; pivot to key theft / `authorized_keys` write
  (Phase 2). Most hardened hosts land here.
- `publickey,password` → password auth is on; targeted spray is viable (triage #7; brute
  depth on [[ssh-openssh]]). Pin it
  with `-o PreferredAuthentications=password` so the client does not burn its `MaxAuthTries`
  budget attempting publickey first.
- `keyboard-interactive` present → often PAM, sometimes a second factor; the method chain
  itself is recon (which factor, what order).
- `publickey,password` on root specifically → check `PermitRootLogin` on the config page
  ([[ssh-openssh]]); root + password is the classic Critical pair.

### 1.2 Differential / timing enumeration

The publickey-vs-password **error shape** differs across implementations and versions, and
the shape itself is the oracle. Two distinct primitives:

- **Response differential.** Send a malformed/early publickey auth request for a candidate
  username and compare the reply to a known-invalid one. The historical headline is OpenSSH
  **CVE-2018-15473** (≤ 7.7): invalid users bail out *before* the publickey packet is fully
  parsed, so valid vs invalid are distinguishable by timing/packet count (fixed 7.8). Full
  PoC workflow and the exact tooling live on [[ssh-openssh]]; this is the method, that is
  the depth.
- **Timing side channel.** Even on patched servers, a server that computes a real password
  hash for valid users but short-circuits for invalid ones leaks existence by latency.
  Measure, do not guess:

```bash
for u in alice bob no-such-user-xyz; do
  /usr/bin/time -f "$u %e" ssh -o PreferredAuthentications=password -o BatchMode=yes \
    -o ConnectTimeout=5 "$u"@198.51.100.10 2>/dev/null
done
# a reproducible latency gap between real and random usernames is the finding
```

Evidence standard for enumeration: a **reproducible** valid-vs-invalid distinction across
several runs (one lucky gap is noise), captured as paired transcripts. Severity is Low/Info
on its own — its worth is the username list it hands the targeted spray, so chain the report
("enumeration → viable spray surface"), never file it bare.

Decision point: if auth methods show `publickey` only, **skip enumeration entirely** — a
username list buys nothing without password auth. Spend the budget on key hunting instead.

---

## Phase 2 — Credential and key exposure (the dominant class)

Goal: the payout phase. Most SSH footholds arrive not through SSH but through *another* bug
that reads or writes an SSH credential file. Point every file-read and file-write primitive
you hold at the SSH filesystem objects first.

### 2.1 Private-key hunting + passphrase cracking

Any file-read primitive (LFI, path traversal, SSRF-to-file, world-readable home, leaked
backup, redis dump) is worth aiming at key material before anything else:

```bash
# targets, in order of payoff
~/.ssh/id_ed25519  ~/.ssh/id_rsa  ~/.ssh/id_ecdsa     # user private keys
/home/*/.ssh/id_*  /root/.ssh/id_*                    # every home
/opt/**/*.key  /etc/**/*.pem  *.bak  backup*.tar*      # stray keys and archives
/etc/ssh/ssh_host_*_key                                # host private keys (MITM material)
```

A passphrase is only a speed bump:

```bash
ssh2john id_rsa > id_rsa.hash        # ships with john (ssh2john.py on older installs)
john --wordlist=rockyou.txt id_rsa.hash
hashcat -m 22921 id_rsa.hash rockyou.txt    # hashcat mode for SSH private keys (RSA/DSA/EC/Ed25519)
# then:
chmod 600 id_rsa && ssh -i id_rsa user@198.51.100.10
```

Evidence standard: show the file-read primitive that exposed the key (that is the root
cause and the severity anchor), the `ssh-keygen -l -f` fingerprint of the recovered key,
and a single authenticated `id` as proof — never harvest more than the proof needs.

### 2.2 `authorized_keys` hygiene — read it as an audit

Wherever you can **read** an `authorized_keys` file, it is a small security audit in its
own right. Each line is `[options] keytype base64 comment`; the options before the key
decide what the key can do, and what they *forgot* to restrict is the finding:

| Hygiene failure | How it reads on the line | Why it pays |
|---|---|---|
| No `from=` restriction | bare key, no source clause | key usable from anywhere, not just the deploy host it was minted for |
| No `restrict` on a single-purpose key | missing `restrict`/`no-*` opts | a backup/deploy key silently grants `ssh -L` into the interior and agent relay |
| `agent-forwarding` left enabled in options | absence of `no-agent-forwarding` | lands on a forwarding host → Phase 3 hijack surface |
| Shared key across users/hosts | identical `base64` body in many files | one stolen key = fleet-wide access; also a fingerprint-diff finding |
| Secret in the comment field | `comment` = `user@host`, a path, or a generation note | recon, and occasionally the matching private key's location |

The `command=`/`from=`/`restrict`/`cert-authority` options and their **escapes** (forced-command
argv injection, `from=` DNS-spoof adjacency, `AuthorizedKeysCommand` abuse) are detailed as
a decision tree on [[ssh-openssh]]. Here the method is: never skim an `authorized_keys` you
can read — enumerate every optioned line for the restriction it omits.

### 2.3 `authorized_keys` as a write target

The instant any other service gives file-write in a home directory (redis `CONFIG SET
dir ~/.ssh` + `dbfilename authorized_keys`, webshell, NFS `no_root_squash`, LFI upload,
backup-restore path), appending your own public key is the canonical persistence and often
the whole foothold:

```bash
echo "ssh-ed25519 AAAAC3...ATTACKER_KEY op" >> /home/deploy/.ssh/authorized_keys
ssh -i attacker_key deploy@198.51.100.10
```

Report the **write primitive** as the root cause (that is what gets fixed) with the SSH key
as the impact. `authorized_keys` read/write is, empirically, the single most common SSH
pivot.

### 2.4 Weak and predictable keys

- **Short / legacy keys.** `ssh-keygen -l -f key.pub` prints the bit length and type.
  RSA < 2048, any `ssh-dss` (DSA, 1024-bit and deprecated/removed in current OpenSSH), and
  1024-bit DH host keys are hardening findings; short RSA is a (expensive) factoring target
  only at the very bottom end.
- **Debian OpenSSL predictable PRNG (CVE-2008-0166).** Any key generated on a Debian-family
  host between 2006-09-17 and 2008-05-13 is one of a tiny precomputed set — recovery is a
  lookup, not a search. One line of history here; the full recover-from-public-key workflow
  (fingerprint → fetch the matching keyset → grep → the sibling file *is* the private key)
  is on [[ssh-openssh]].
- **Fleet-shared host keys.** Keys baked at image-build time instead of first boot mean
  every unit shares one key (endemic on embedded/Dropbear). Collect full host keys across
  the segment (Phase 0.1) and diff.

### 2.5 Hardcoded credentials in repos and configs

When source, CI config, container images, or infra-as-code are in scope, SSH secrets leak
in-band constantly. Scan deliberately:

```bash
# private keys and passphrases committed to a repo (whole history, not just HEAD)
trufflehog git file://./target-repo --only-verified
gitleaks detect --source ./target-repo --report-format json

# fast manual sweep of a checkout or extracted image
grep -rnE -- '-----BEGIN (OPENSSH|RSA|EC|DSA) PRIVATE KEY-----' .
grep -rnE 'IdentityFile|ProxyJump|sshpass -p|ssh .* -i ' .   # embedded key paths + inline passwords
```

High-signal shapes: `id_rsa`/`id_ed25519` committed under `deploy/` or `.ssh/`, a
`sshpass -p '...'` one-liner in a CI job, a Dockerfile `COPY` of a private key into an
image layer (recoverable even if a later layer deletes it), and `ssh_config`/`.ssh/config`
with `IdentityFile` pointing at a key stored beside it. Each is a direct-auth foothold;
report the commit/layer that carries it as root cause.

---

## Phase 3 — Agent-forwarding abuse on a host you hold

Goal: when you land (root, or same-UID) on a host that *other* users SSH **through** with
agent forwarding (`ssh -A`, or `ForwardAgent yes`), their live agent is a credential you
can borrow without any key material — the agent signs challenges for you.

Preconditions, all required:

1. A user authenticated *to* this host with agent forwarding on, and their session is live.
2. You can read their forwarded agent socket (you are root, or the same UID, or the socket
   permissions are loose — `ssh(1)` itself warns that anyone who can bypass file
   permissions on the socket host can use the forwarded agent).

```bash
# 1. find live forwarded agent sockets (per-connection dirs, classically under /tmp)
find /tmp -maxdepth 2 -name 'agent.*' -type s 2>/dev/null
ls -l /tmp/ssh-*/agent.*          # note owner (= the victim) and mtime (= freshest session)

# 2. point SSH at the newest socket and list the keys it holds
SSH_AUTH_SOCK=/tmp/ssh-XXXX/agent.1234 ssh-add -l
# 2048 SHA256:... deploy@jump (RSA)   <- the agent is holding this user's key

# 3. authenticate onward as the socket's owner — the agent signs, you never touch the key
SSH_AUTH_SOCK=/tmp/ssh-XXXX/agent.1234 ssh deploy@198.51.100.20
```

Decision point: this is **post-foothold and time-boxed** — it works only while the victim's
session is live and only toward hosts that trust that user's key. Harvest `ssh-add -l`
fingerprints and the victim's `known_hosts` (Phase 4) *immediately* to map where the
borrowed agent can reach before the window closes. Severity High: it is lateral movement
with no credential theft and little forensic trail on the victim's side.

---

## Phase 4 — Recon from client-side artifacts

Goal: a conquered host's SSH *client* state is a map of everywhere its owner connects —
pure pivot intel that multiplies Phases 3 and 7.

```bash
cat ~/.ssh/config /etc/ssh/ssh_config          # Host blocks: hostnames, Users, Ports, IdentityFile, ProxyJump
cat ~/.ssh/known_hosts                          # every host this user has SSHed to
grep -rhE 'Host |HostName|User |ProxyJump|IdentityFile' ~/.ssh/config
ls -l ~/.ssh/id_* ~/.ssh/*.pem                  # local private keys (feed Phase 2.1)
```

`ssh_config` is the richest single file: each `Host` block names a real hostname, the
login user, the key file, and — via `ProxyJump` — the bastion chain, i.e. the exact path
into a segment you cannot otherwise see.

**`known_hosts` when hashed.** Debian/Ubuntu ship `HashKnownHosts yes`, so entries look
like `|1|salt|hash` and plain `grep` misses them. Hashing is one-way, but it only hides
hostnames from a casual read — a candidate list defeats it by guess-and-verify:

```bash
# test whether a candidate host is in a hashed known_hosts (ssh-keygen understands the hash)
ssh-keygen -F 198.51.100.20 -f ~/.ssh/known_hosts
# feed it names from DNS, ssh_config, DHCP leases, /etc/hosts, recent `w`/`last` — each hit
# confirms the owner connects there and hands you an agent-hijack or spray target
```

This is lateral-movement intelligence, not a finding by itself — report it only as the
pivot path that led to the next finding.

---

## Phase 5 — Client-side attack surface and the 2025 CVE class

Goal: SSH is not only a server bug class. The *client* parses attacker-influenceable input
(hostnames, usernames, URIs, host keys) and 2025 produced a sharp cluster of client-side
RCE/MITM bugs. Test these when your target is a system that makes **outbound** SSH
connections to destinations an attacker can influence — CI runners, git-over-SSH automation,
provisioning tooling.

### 5.1 ProxyCommand username injection — CVE-2025-61984 / CVE-2025-61985

OpenSSH **before 10.1** does not reject control characters in a username taken from an
*untrusted* source (the command line, or `%`-sequence expansion inside a config file). When
a `ProxyCommand` references the username via an unquoted `%r`, a crafted username containing
control characters breaks out of the intended command and runs attacker-chosen code on the
*client* host. CVE-2025-61984 is the control-character variant; CVE-2025-61985 is the null
character in an `ssh://` URI, same sink. Both fixed in OpenSSH 10.1.

The practical delivery path is **git submodules**: a malicious `.gitmodules` carries a
hostile username, and a victim whose SSH config uses `ProxyCommand ... %r` triggers the bug
merely by cloning the repo recursively.

```text
# VULNERABLE client config (unquoted %r)
Host *
    ProxyCommand /usr/bin/corp-proxy %h %p %r

# attacker .gitmodules entry (username carries control chars)
[submodule "x"]
    url = ssh://<crafted-username>@198.51.100.10/repo.git

# robust: quote the expansion, and upgrade to >= 10.1 which rejects control chars outright
#     ProxyCommand /usr/bin/corp-proxy %h %p "%r"
```

Finding conditions to confirm before reporting: client OpenSSH < 10.1 **and** a
`ProxyCommand` using `%r` **and** a path by which an attacker supplies the username
(recursive clone, a URL the client is handed, a `%`-expanded config value). On an authorized
engagement, prove it with a benign marker command (write a timestamp to a scratch file), not
a destructive payload.

### 5.2 Host-key MITM — TOFU, `VerifyHostKeyDNS`, and CA deployments

SSH's default trust model is **trust-on-first-use**: the first connection writes the host
key to `known_hosts` and every later connection trusts it silently. Two client-side failure
modes turn TOFU into a MITM:

- **`StrictHostKeyChecking=no` / `accept-new` in automation.** CI jobs, cron, and
  provisioning scripts that disable host-key checking connect to a MITM'd endpoint without a
  murmur. Grep the target's scripts and `ssh_config` for it; its presence on a
  credential-bearing automated connection is the finding (an attacker on-path harvests the
  password or rides the session). This is endemic and almost always reportable.
- **`VerifyHostKeyDNS` MITM — CVE-2025-26465.** An OpenSSH **client** 6.8p1–9.9p1 with
  `VerifyHostKeyDNS` set to `yes` or `ask` can be induced to accept an attacker's host key
  via crafted SSHFP handling, enabling a machine-in-the-middle regardless of `known_hosts`
  (fixed 9.9p2). Check the client version and whether `VerifyHostKeyDNS` is enabled — the
  combination is the gate. (Its sibling **CVE-2025-26466** is a pre-auth DoS on client and
  server 9.5p1–9.9p1, fixed 9.9p2 — one line: a resource-exhaustion note, not a foothold.)

The robust deployment replaces TOFU with an **SSH certificate authority**: hosts present a
certificate signed by a CA the client pins once (`@cert-authority` in `known_hosts`), so no
first-use leap of faith and no per-host key distribution. That moves the risk to the CA key
— whose theft or misuse is organizational key compromise, detailed as a forgery workflow on
[[ssh-openssh]]. When you see a CA deployment, the question shifts from "can I MITM the
first connection" to "where is the CA key and what signs with it".

---

## Phase 6 — Pivoting and tunnelling (authorized engagements)

Goal: once a key or credential lands, SSH *is* your movement and reach layer. Not a finding
in itself — the standard way in and out — but you must know the flags cold, and you must
understand how the blue team sees each one so your report's remediation is honest.

### 6.1 The semantics, in one table

| Flag | Meaning | Reach it buys |
|---|---|---|
| `-L 8443:127.0.0.1:443` | local forward: your `:8443` → target's view of `127.0.0.1:443` | reach an interior service bound to loopback on the far side |
| `-R 9001:127.0.0.1:9001` | remote forward: target's `:9001` → back to your `127.0.0.1:9001` | egress / call-back channel from inside the network |
| `-D 1080` | dynamic SOCKS proxy on your `:1080` | route arbitrary tools through the session as a pivot |
| `-J jump1,jump2 user@target` | ProxyJump chain | hop bastion→bastion to a host you cannot route to directly |
| `-W host:port` | stdio forward (ProxyCommand-style) | single-hop pivot without a shell on the jump host |

The `ProxyJump`/`-J` chain is the clean way to reach a deep segment: each hop only needs
to reach the next, and the comma-separated list reads exactly as the path (`-J bastion,relay
target`). Prefer it over nested `ssh` invocations — one command, one audit trail, and it
composes with `-L`/`-D` on the final hop.

### 6.2 How the blue team sees it (frame remediation honestly)

Every tunnel leaves tells; naming them makes your report credible and your remediation
actionable rather than theatrical:

- **Host logs.** OpenSSH at `LogLevel VERBOSE` logs the accepted key fingerprint and the
  channel requests; `-R`/`-L`/`-D` create `direct-tcpip`/`forwarded-tcpip` channels that a
  tuned `sshd` and host EDR record. `Accepted publickey` with a fingerprint not in the
  expected inventory is the detection.
- **Network shape.** A single long-lived SSH session carrying bursty, multiplexed traffic
  (a tunnel) looks different from an interactive shell; Zeek's `ssh.log` and NetFlow flag
  the duration/volume anomaly and unexpected `-R` back-connections to external IPs.
- **Config drift.** `AllowTcpForwarding no` and `PermitOpen` on the server kill `-L`/`-D`;
  `GatewayPorts no` limits `-R` exposure. If your pivot worked, their absence *is* the
  hardening recommendation — cite the exact `sshd_config` line.

Report pivoting only as the path that reached a finding, never as a finding itself, and
state the detection surface you crossed so the defender can close it.

---

## Reporting and evidence standards per class

The report is the product. Quote the banner version with every version-gated claim, and
hold each finding to the evidence standard its class demands:

| Finding class | Minimum evidence | Severity anchor |
|---|---|---|
| Private-key exposure | the file-read primitive (root cause) + `ssh-keygen -l -f` fingerprint + one authenticated `id` | Critical |
| `authorized_keys` write | the write primitive + the appended line + one login as the target user | Critical |
| Weak/predictable key | public-key fingerprint + length/type (or keyset match for Debian PRNG) | High–Critical |
| Agent-forwarding hijack | socket path + owner + `ssh-add -l` + one onward `id` | High |
| Client-side CVE (ProxyCommand/MITM) | client version + the vulnerable config line + a benign-marker PoC | High–Critical |
| Weak credential / spray | the single landing guess + its source (breach/convention), not the wordlist | High if it lands |
| User enumeration | reproducible valid-vs-invalid paired transcripts | Low/Info (chain it) |
| Crypto posture / Terrapin | `ssh-audit`/scanner output + whether strict KEX negotiates | Low/Info |

Universal rules: one request per PoC step, expected-vs-actual captured, never exfiltrate
more than the proof needs, clean up any key you planted, and point remediation at the
root-cause primitive (the file read, the write path, the stale client) rather than "SSH".

---

## CTF notes — the SSH box as a genre

SSH boxes are a recognised CTF archetype, and the intended path is almost never "brute
`22`". The genre conventions:

- **The key is hidden in another service.** The web app, an exposed git repo, an SMB share,
  a backup tarball, or an LFI holds `id_rsa` — Phase 2.1 is the whole game. A found key with
  a rockyou-tier passphrase (`ssh2john` → `john`) is the single most common shape.
- **The username is seeded elsewhere.** A comment in `authorized_keys`, a key comment
  (`user@host`), a web-app profile, or an email on the site gives the login name — pair it
  with the recovered key.
- **`authorized_keys` write = the intended foothold** when a box hands you redis `CONFIG
  SET`, an NFS `no_root_squash` mount, or a writable home.
- **Agent hijack and `known_hosts` are the pivot** on multi-host networks: land on the
  jump box, borrow the live agent (Phase 3) or read `known_hosts`/`ssh_config` (Phase 4) to
  find the next target. ProxyJump the chain.
- **Restricted-shell / forced-command escapes** (`command=` keys, `rbash`) are a frequent
  post-SSH puzzle — the escape catalogue is on [[ssh-openssh]].

The CTF reflex that transfers to real engagements: **SSH is the exit, not the entrance** —
spend your time finding the credential in the adjacent service, not hammering the daemon.

---

## References

- OpenSSH man pages (`ssh(1)`, `sshd(8)`, `ssh_config(5)`, `ssh-keygen(1)`) — https://man.openbsd.org/ssh
- OpenSSH release notes (version/CVE gating) — https://www.openssh.com/releasenotes.html
- ssh-audit (jtesta) — https://github.com/jtesta/ssh-audit
- Terrapin attack (CVE-2023-48795) + scanner — https://terrapin-attack.com/
- NVD CVE-2023-48795 (Terrapin prefix truncation) — https://nvd.nist.gov/vuln/detail/CVE-2023-48795
- NVD CVE-2025-26465 (client host-key MITM via VerifyHostKeyDNS) — https://nvd.nist.gov/vuln/detail/CVE-2025-26465
- Qualys TRU, CVE-2025-26465 / CVE-2025-26466 — https://blog.qualys.com/vulnerabilities-threat-research/2025/02/18/qualys-tru-discovers-two-vulnerabilities-in-openssh-cve-2025-26465-cve-2025-26466
- Debian tracker, CVE-2025-61984 (control chars in username → ProxyCommand) — https://security-tracker.debian.org/tracker/CVE-2025-61984
- F5, CVE-2025-61984 / CVE-2025-61985 (ProxyCommand code execution, fixed 10.1) — https://my.f5.com/manage/s/article/K000159684
- NVD CVE-2018-15473 (user enumeration ≤7.7) — https://nvd.nist.gov/vuln/detail/CVE-2018-15473
- OpenSSH HashKnownHosts / de-hash via `ssh-keygen -F` — https://man.openbsd.org/ssh-keygen#F
- TruffleHog — https://github.com/trufflesecurity/trufflehog · Gitleaks — https://github.com/gitleaks/gitleaks

*Drafted 2026-11-19 (model claude-opus-4-8); upstream-verified against OpenSSH man pages and release notes, the ssh-audit project, terrapin-attack.com, and NVD/GHSA/Debian records for CVE-2023-48795, CVE-2018-15473, and the 2025 OpenSSH cluster (CVE-2025-26465/26466/61984/61985). Companions: [[ssh|SSH overview]], [[ssh-openssh|OpenSSH attack surface]].*