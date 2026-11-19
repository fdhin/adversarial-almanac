---
technology: "IRC"
kind: overview
audience: offensive-security
family: network-services
layer: text conferencing protocol (TCP; 6667 conventional, 6697 TLS, 194 assigned; daemons pick custom ports)
standards: "RFC 1459 (1993) / RFC 2812 (2000, client protocol); daemon families: UnrealIRCd, InspIRCd, ngircd, Ergo/Oragono, solanum/charybdis"
status: thin
provenance: "structure-pass count of 31 was inflated — command-context re-gate (2026-11-12) found exactly 1 attacking chain (irssi version grab -> UnrealIRCd 3.2.8.1 backdoor, msf unreal_ircd_3281_backdoor, manual 'AB;' netcat trigger, shell as ircd user). Area probe: unrealircd is the ONLY ircd variant in the evidence pool (no inspircd/ergo/oragono/ngircd/charybdis command-context hits) -> no variant page per gate; 7 more files carry only the Debian 'irc:x:39:39:ircd' /etc/passwd line (enumeration noise). Upstream-verified: NVD CVE-2010-2075 + the official UnrealIRCd advisory (md5s, grep check, scope); RFC 2812 registration/commands. Enrichment pass 1, wave 2."
---

# IRC

> A 1993 chat protocol whose daemons still run as dedicated users on forgotten boxes — and whose most famous incident is a trojanized official tarball that shipped a pre-auth RCE for eight months.

## What it is

IRC (Internet Relay Chat, RFC 1459/2812) is plaintext text-conferencing over TCP: a client registers with `NICK` + `USER` (optionally `PASS` first), then joins channels (`#name`) and exchanges `PRIVMSG`s. RFC 2812's own abstract notes the simplest client is "any socket program capable of connecting to the server" — no handshake crypto, no mandatory auth, everything readable on the wire. The server side is a **daemon family** question, not a product question: UnrealIRCd, InspIRCd, ngircd, Ergo (formerly Oragono), solanum/charybdis, each with its own config, port conventions, and CVE history; the conventional port is 6667 (clear) / 6697 (TLS), but operators constantly pick custom ports — the one real observed chain found the IRCd on 8067 with the backdoor listener surfacing on 65534.

The operator-relevant history: **UnrealIRCd 3.2.8.1 was trojanized at the distribution mirrors** — the official .tar.gz served from November 2009 to June 2010 carried a backdoor in the `DEBUG3_DOLOG_SYSTEM` macro (CVE-2010-2075) that executes any command sent with an `AB;` prefix, regardless of user restrictions, as the ircd user. Nobody noticed for eight months. The official advisory publishes the bad/good MD5s and the `grep DEBUG3_DOLOG_SYSTEM include/struct.h` two-line check; precompiled Windows binaries and CVS checkouts were never affected — so version string alone doesn't prove the target is backdoored, but it is the trigger to try.

The modern engagement reality is thinner: IRC surfaces mostly appear as **leftover daemons** (community servers, bot hosts, old Linux boxes — note Debian-family images carry a non-login `irc` user at uid 39 whose appearance in /etc/passwd output is a hint an ircd was intended, not proof one is running) and as **bot infrastructure** — eggdrop/IRC-bot config files with nick passwords and server credentials are a classic post-exploit credential-reuse jackpot.

## The anatomy that matters on an engagement

    connect TCP (6667/custom) -> banner/version on connect
    register: NICK <nick> / USER <user> 0 * :<realname>   <- pre-auth oracle starts here
    server numerics: 001 welcome, 433 nick in use, 251 lusers (user/service counts)
    enum: LIST (channels+topics) · WHOIS <nick> (idle, host, channels) · LUSERS · ADMIN · MOTD
    ops: /oper + password -> server operator (daemon-family config: opers, links, services)
    backdoor lane (Unreal 3.2.8.1 only): 'AB;<command>' on any listening ircd port -> RCE as ircd user

Three structural properties drive most findings:

- **Registration is a free pre-auth probe surface** — the daemon answers before you authenticate anything: numerics leak user counts (251), nick collisions leak live nicknames (433), `LIST` dumps channels and topics (often internal project/ops names), `WHOIS` leaks idle time and hostmasks of connected users.
- **The daemon runs as a dedicated low-priv user** (`ircd`, `irc`) — so the backdoor/RCE lands as that user, and the follow-up is the same as any web-shell: hunt config files, bot credentials, and service passwords in the daemon's home/config dirs, then move laterally with reused creds.
- **Credential reuse is the yield** — ircd.conf/links.conf hold server-to-server passwords, oper blocks hold operator hashes, bot configs hold NickServ passwords; every one of those is a spray candidate on SSH/elsewhere (the observed chain cracked a steg password from the ircd user's filesystem and rode it to SSH).

## Where you'll meet it

- **Forgotten public/community IRC servers** — version-banner (CTCP `VERSION`, connect numerics) fingerprinting then CVE-matching against the daemon family; Unreal 3.2.8.1-era boxes still exist in legacy estates.
- **CTF/lab and legacy-corporate boxes** — an ircd as the intended foothold service; expect a custom port and the daemon user as your first shell context.
- **Internal networks with bot culture** — build/dev notification bots (old-school) whose configs live in home dirs with plaintext nickserv/server passwords.
- **As a pivot indicator** — the `irc` uid-39 passwd entry or an `ircd` homedir signals the surface even when the port scan missed the (custom-port) daemon.

## Reading the protocol's mind

1. **The version string is the whole triage** — connect and read the banner (or send CTCP VERSION); "Unreal 3.2.8.1" triggers the CVE-2010-2075 check, other daemon/version strings route you to that family's CVE history.
2. **Numerics enumerate for free** — 001/002/005 caps leak daemon capabilities, 251 (LUSERS) leaks network size, 322 (LIST) leaks every non-secret channel with topics; none require any registration beyond NICK/USER.
3. **Nick collision = live-user oracle** — 433 on a guessed nick means that identity is connected right now (or registered with services); loop a name list to map who is online.
4. **`AB;` is only a trigger on trojanized builds** — CVE-2010-2075 scope is the mirror-served .tar.gz between Nov 2009 and Jun 2010; Windows binaries/CVS were clean. Test cheaply (a ping command), never assume from version alone.
5. **Everything is plaintext on 6667** — on-path you read credentials and sessions wholesale; on 6697 TLS you lose that but keep the metadata (who talks to the server, when).

## Where to go next

[[irc-bug-bounty|IRC bug bounty methodology]] — registration-oracle enumeration, the UnrealIRCd 3.2.8.1 backdoor chain (verified), bot-config credential mining.
