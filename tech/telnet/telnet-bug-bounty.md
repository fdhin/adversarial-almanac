---
technology: "Telnet"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "evidence-grounded (FTP-leaked creds → telnet logon; JetDirect password via SNMP; CVE-2026-24061 reproduction) + upstream-verified (NVD CVE-2026-24061 record and CISA KEV listing, GNU inetutils 2.8 release note). Thin evidence (5 files) — upstream supplies daemon behavior and credential tables. Companion overview: [[telnet|Telnet]]. Phase 5 additions: rewritten from HackTricks (hacktricks/src/network-services-pentesting/pentesting-telnet.md), CC BY-NC 4.0, accessed 2026-11-13 — NSE script names verified against the nmap repo; metasploit module names verified against the metasploit repo (HT's 'brute_telnet' does not exist — the real module is telnet_login; HT's netgear_telnetenable module likewise absent); D-Link CVEs verified against D-Link SAP10412/TWCERT, CVE-2023-40478 against ZDI-23-1163 + NETGEAR PSV-2023-0002, CVE-2022-39028 against the Debian security tracker."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Telnet bug bounty methodology

Target model: 23/tcp (or a device-specific telnet stub), plaintext login, daemon
families: GNU inetutils telnetd, BSD-derived telnetd, BusyBox telnetd, vendor stubs
(HP JetDirect), Windows telnet service. Authorization assumed; the CVE-2026-24061 check
below has a non-destructive verify.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| inetutils telnetd auth bypass (CVE-2026-24061) | GNU inetutils ≤ 2.7 | yes | Root shell, zero credentials — Critical |
| Default/vendor credentials | device stubs, printers, IoT | credentialed | Admin shell — Critical |
| Credential reuse from adjacent leaks | any telnet with local users | credentialed | Interactive shell — High |
| Plaintext capture | any position on-path | n/a (capture) | Credentials + session data — High |
| JetDirect password via SNMP | HP printers, default community | yes (SNMP) | Printer admin → internal data — Medium |

## Phase 0 — Fingerprint the daemon

```bash
nmap -p 23 -sV 198.51.100.10
# 23/tcp open  telnet  → greet and read: "HP JetDirect", "Welcome to Microsoft Telnet Service",
#   a login: prompt, or a BusyBox shell — each routes to a different row of the triage table
telnet 198.51.100.10        # interact: banner, prompt shapes, login banner text
```

Version oracles: the pre-auth banner names the product on most stubs; GNU inetutils
telnetd exposes its version through the greeting or package evidence (one observed chain read
`telnet --version` → GNU inetutils 2.7 from a foothold before using the CVE). If you
hold any shell on the host, `telnetd --version` / package manager pins the family and
build — do this before claiming CVE-2026-24061.

## Phase 1 — CVE-2026-24061 (inetutils telnetd, ≤ 2.7)

Mechanism (NVD, CWE-88 argument injection): telnetd passes the client's USER
environment value to `login(1)`; a value of `-f root` makes login execute
`login -f root`, which **skips authentication** for that account. Pre-auth, any
network-reachable victim, root access without a password. CISA listed it in the KEV
catalog 2026-01-26; GNU inetutils 2.8 (2026-04) is the fixed release.

Observed reproduction (from a local shell on the victim host):

```bash
USER="-f root" telnet -a 127.0.0.1
# Connected → no password prompt → root@target shell
```

Remote variant: the USER variable rides the TELNET ENVIRON option, so a client that
sends environment variables (or a crafted raw session) delivers it pre-auth. Verify
non-destructively by connecting with `USER="-f id-probe"`: if the session lands at a
shell without any password exchange, capture the transcript and stop — that is the
finding. Patched 2.8+ ignores/scrubs the option.

## Phase 2 — Default and vendor credentials

No lockout on device stubs; run the vendor table before any brute:

```bash
hydra -l admin -P vendor-defaults.txt telnet://198.51.100.10
# also: root/blank, admin/admin, admin/password, vendor names (cisco, hp, mikrotik)
```

Observed pattern that beats brute force: **harvest the credential from an adjacent
protocol**. HP JetDirect: the printer's telnet password is stored in SNMP —

```bash
snmpwalk -v 2c -c public 198.51.100.10 .1.3.6.1.4.1.11.2.3.9.1.1.13.0
# hex blob → decode (e.g. python binascii.unhexlify) → telnet password
telnet 198.51.100.10
```

One Windows target: credentials extracted from files on the anonymous FTP share (an
access-control zip / mdb) replayed against the telnet logon — the telnet service was
fine; the credential reuse was the finding. Note for Windows targets: a telnet logon
carries the **Interactive** SID in the token, which changes what the session may access
(quoting that engagement's own analysis).

## Phase 3 — Plaintext capture

Any on-path position (shared segment, ARP/DNS spoofing, traffic-redirect foothold,
passive taps during internal testing) records telnet credentials and sessions verbatim:

```bash
# with a capture position: credentials appear as plaintext in the stream
tcpdump -i tap0 -A port 23
```

No verify needed — the capture is the evidence. Report alongside the segment diagram;
the fix (retire telnet) is organizational, so frame severity by what the sessions touch
(admin interfaces, PLC commands).

## Phase 4 — Post-shell hygiene

- Telnet sessions are terminal-limited: upgrade via a web-delivered stage or a second
  channel rather than fighting line-editing (one observed chain spawned a separate channel
  precisely because the telnet session locked up on wide output).
- On BusyBox telnetd roots, persistence is config (inittab/rcS re-spawn) — document
  before modifying anything.

## Phase 5 — Enumeration add-ons and recent CVE history (upstream additions, 2026-11-13)

**NSE battery beyond `-sV`**: `nmap -n -sV -Pn --script "*telnet* and safe" -p 23 <target>`
runs the safe subset in one pass. The scripts that matter: telnet-ntlm-info (against a
Windows telnet service with NTLM enabled it discloses NetBIOS/DNS domain and OS-build
metadata pre-auth — free fingerprinting for the Phase 2 credential table), telnet-brute
(`userdb=`/`passdb=` NSE alternative to hydra when you want results as nmap output), and
telnet-encryption (reports whether the ENCRYPT option is supported — historically
bug-rich parsing surface; support alone is not a finding). Metasploit complements with
`auxiliary/scanner/telnet/telnet_version` (banner/options), `telnet_login` (the actual
brute module), plus the family scanners brocade_enable_login, telnet_ruggedcom, and
telnet_encrypt_overflow (the Solaris 9/10 ENCRYPT-overflow check).

**Capture filters for Phase 3**: Wireshark's `tcp.port == 23 && (telnet.data ||
telnet.option)` splits session bytes from option negotiation — credentials land in
telnet.data; the IAC option stream is daemon-behavior evidence.

**Post-foothold config locations**: /etc/inetd.conf, /etc/xinetd.d/telnet and
/etc/xinetd.d/stelnet name the daemon, its flags, and whether a wrapper (TCP
wrappers/hosts.allow) gates it.

**Recent CVE history (each verified against the vendor/CNA advisory):**

- **D-Link DIR-X4860 cluster (D-Link SAP10412, TWCERT, fixed 1.04B05)** — CVE-2024-45696:
  an unauthenticated web-service packet forcibly ENABLES the telnet service with
  hard-coded credentials (LAN-only); CVE-2024-45697: telnet comes on by itself when the
  WAN port is plugged in, hard-coded credentials, remotely reachable; CVE-2024-45698:
  improper input validation in the telnet service — hard-coded-credential login plus OS
  command injection. The "telnet you did not see in the port scan" pattern: on this
  family, probe for a telnet daemon that the web service can switch on.
- **CVE-2023-40478 — NETGEAR RAX30 (ZDI-23-1163, fixed 1.0.10.94)**: stack overflow in
  the telnet CLI `passwd` command (TCP 23); network-adjacent, authentication required but
  bypassable, code execution as root. On a credentialed LAN foothold this is a direct
  device takeover.
- **CVE-2022-39028 — GNU inetutils telnetd (≤2.3) and MIT krb5-appl (≤1.0.3)**: a
  two-byte `0xff 0xf7` / `0xff 0xf8` sequence null-derefs telnetd; single hits crash and
  recover via inetd, but rapid repeats push inetd into "server failing (looping), service
  terminated" — a cheap, noisy DoS that also proves byte-level option-parser control.

Mirai lineage context: internet-wide port-23 scanning with tiny default-credential
dictionaries is still the volume channel for IoT botnets — expect weak-credential telnet
on anything consumer-grade to have been probed before you got there.

## Phase 6 — Multi-prompt brute force with patator (ports cheat-sheet addition, 2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution);
the `telnet_login` module's options verified against patator's own source. Hydra (Phase 2)
assumes a protocol-level grammar; telnet credentials ride an interactive terminal dialogue,
and patator is built for that shape — it feeds a sequence of inputs and matches each prompt
by regex instead of expecting a fixed exchange:

```bash
patator telnet_login host=198.51.100.10 inputs='FILE0\nFILE1' 0=logins.txt \
  1=passwords.txt persistent=0 prompt_re='Username: |Password: '
```

Two option semantics decide success on real daemons: `prompt_re` routes each input to its
prompt — read the daemon's actual strings off one manual session first (Windows-flavored
telnetds ask `Username:`, Unix ones `login:`) and mirror them exactly — and `persistent=0`
opens a fresh connection per attempt, because a half-authenticated session desyncs the
input sequence. Once you know the rejection text, add the usual patator action form
(`-x ignore:fgrep='Login incorrect'` style) so only hits surface.

## Reporting notes

CVE-2026-24061: quote the banner/version evidence, the USER value, and the no-password
transcript; severity Critical (KEV-listed, pre-auth root). Default credentials: name the
vendor default used and the admin surface gained. Capture findings: cite the position
and the exact credential strings observed, and note protocol-level remediation is
telnet removal (SSH), not a patch.

## Appendix — Tool map

- **nmap** (`-sV`, telnet-encryption NSE where present) — Phase 0.
- **telnet / nc** — raw interaction — Phases 0-1.
- **hydra / medusa** — telnet module for credentialed checks — Phase 2.
- **snmpwalk** — JetDirect password OID — Phase 2 (companion: the snmp unit's page).
- **tcpdump** — plaintext capture — Phase 3.
- Tools vault companions by name: nmap, hydra, netcat, tcpdump, snmpwalk (net-snmp).

CVE-2026-24061 (GNU inetutils telnetd ≤2.7, USER `-f root` auth bypass via
login(1), CWE-88, KEV 2026-01-26, fixed 2.8) · historical: BSD-based telnetd
option-negotiation buffer overflow (CVE-2001-0554, CERT VU#745371) justifies treating any
1990s-stamped telnetd as unpatched until proven otherwise.
