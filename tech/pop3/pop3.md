---
technology: "POP3"
kind: overview
audience: offensive-security
family: network-services
layer: "mail retrieval protocol (TCP/110 plaintext, TCP/995 POP3S/TLS)"
standards: "RFC 1939 (POP3, STD 53, 1996); CAPA per RFC 2449; STLS per RFC 2595; common servers: Dovecot (Linux), hMailServer (Windows)"
homepage: https://www.rfc-editor.org/rfc/rfc1939
status: thin
provenance: "corpus: 2 writeups under the command-context gate (2026-11-12; markers 110/tcp-open-pop3 / 995/tcp-open-ssl/pop3 / pop3-capabilities / pop3d banners / openssl s_client to 995) — one file (Dovecot 110+995, CAPA dumps, s_client mailbox walk) and another (hMailServer pop3d fingerprint anchoring an ini-read -> admin-hash-crack -> mail-platform chain). The structure-pass count of 3 was inflated: one rejected candidate had zero pop3 markers (AD ESC8 chain; '110' matches only UID/SID digits); another's mailbox access was webmail/IMAP, adjacent but not POP3 verbs; a third showed a commented-out nginx mail-module pop3 block (config sighting, not an attack). Upstream-verified: RFC 1939 (states, USER/PASS/APOP, TOP/UIDL), nmap pop3-capabilities NSE docs, Dovecot 2.3 core settings (auth_mechanisms default plain incl. apop; auth_failure_delay 2secs), THC-hydra README (POP3 module). Enrichment pass 1, wave 4."
---

# POP3

> A mail-retrieval protocol where the entire authentication story is two cleartext verbs — `USER` then `PASS` — and the prize behind them is a mailbox full of password resets, credentials and attachments that nobody rotates.

## What it is

POP3 (Post Office Protocol version 3, RFC 1939, STD 53) is the older and simpler of the two standard mailbox-access protocols: a client connects (TCP/110 plaintext, TCP/995 TLS), authenticates, downloads messages, and deletes them from the server. The session is a strict state machine. In the **AUTHORIZATION state** the client sends `USER <name>` and `PASS <secret>` (or the challenge-response `APOP`), and the server answers `+OK` or `-ERR`. In the **TRANSACTION state** the client works the single implicit inbox: `STAT` (count/size), `LIST` (message list), `RETR n` (fetch), `TOP n 0` (headers + first lines only), `UIDL` (unique ids for re-sync), `DELE n` (mark for deletion). On `QUIT` the **UPDATE state** commits the deletions. That's the whole protocol — no folders, no server-side search; anything more is IMAP territory.

The capabilities negotiation (`CAPA`, RFC 2449) is the free recon step: it lists supported commands and SASL mechanisms and may include an `IMPLEMENTATION` string naming the product and version — nmap's pop3-capabilities NSE does exactly this and its output doubles as a server fingerprint (`USER CAPA RESP-CODES UIDL PIPELINING STLS TOP SASL(PLAIN)` reads as Dovecot in the field; add the `Dovecot pop3d` service banner and you have the exact build). The observed record shows the two household implementations: **Dovecot** on Linux (one daemon serving imap 143/993 and pop3 110/995 from one config; default auth mechanisms are `plain` with `apop` and friends available but off; a 2-second `auth_failure_delay` throttles guessing) and **hMailServer** on Windows (ports 25/110/143/465/587/993 together; its `hMailServer.ini` stores an Administrator password hash that, once read by any file-disclosure bug, cracks offline and hands over the whole mail platform).

The operator takeaway: POP3 is almost never the initial bug — it is the **credential destination**. Mailboxes accumulate password-reset mails, plaintext credentials between colleagues, PGP/encrypted attachments whose keys live elsewhere, and the mail-server admin's own config. A single reused or guessed password becomes mailbox read, and mailbox read becomes domain credentials. Two structural facts keep it dangerous: `USER`/`PASS` on 110 are cleartext (a passive position harvests creds; `STLS` exists but is opt-in per client), and POP3 semantics encourage clients to *download and delete* — meaning the juiciest mail may sit on an employee's workstation rather than the server, while the server keeps the account itself as a credential target for spraying.

## The anatomy that matters on an engagement

    +OK Dovecot POP3 ready                                    ← banner = product fingerprint
    CAPA
    +OK  USER PASS STLS TOP UIDL RESP-CODES PIPELINING SASL(PLAIN) CAPA
    [if STLS/995: STARTTLS wrap, then cleartext inside]
    USER ayush          → +OK
    PASS <secret>       → +OK  (or -ERR [AUTH] authentication failed)
    STAT / LIST / UIDL  → inbox inventory
    TOP n 0             → headers only: sender, subjects, dates (cheap triage)
    RETR n              → full message: resets, credentials, base64 attachments
    QUIT                → UPDATE state commits DELEs

Three structural properties drive most findings:

- **The auth surface is two lines and a username list.** No lockout is mandated by the protocol; whatever throttling exists is server config (Dovecot's default 2s failure delay is modest). Combined with `SASL(PLAIN)` advertisements, POP3 is a natural credential-guessing target with hydra-class tooling, and a natural password-spray target because mailbox credentials are so often the AD/domain password.
- **CAPA + banner is a pre-auth implementation fingerprint** — `IMPLEMENTATION` strings and mechanism lists steer you straight to the product's CVE and config history (Dovecot vs hMailServer paths diverge completely from here).
- **Mailbox compromise is lateral fuel.** Mail is where every other system sends its secrets; the observed chain reads web-app credentials → mailbox → PGP-encrypted attachment + encryption script in Drafts → SSH key (one chain), and mail-admin password hash → mail-platform admin → authenticated phishing as the admin (the other).

## Where you'll meet it

- **Corporate Linux mail stacks** — Dovecot fronting Postfix; 110/995 alongside 143/993. Credential reuse between the mailbox and SSH/FTP/admin panels is the recurring finding.
- **Windows SMB mail servers** — hMailServer with its classic port cluster (25/110/143/465/587/993); the `hMailServer.ini` admin-hash pattern turns any path traversal into mail-admin.
- **ISP/hosting legacy** — long-lived mailbox accounts with aged passwords, perfect for spraying; POP3's download-and-delete model means old boxes may hold nothing while still accepting the account password everywhere else.
- **Adjacent, not target** — pure-webmail compromises (the pure-webmail class: phish → webmail → credential mail) exercise the same mailbox-loot pattern through IMAP/HTTP rather than POP3 verbs.

## Reading the protocol's mind

1. **Every command answers `+OK` or `-ERR` on one line** — a trivially scriptable oracle; authentication failures on Dovecot carry the ` [AUTH]` prefix in the error text.
2. **`TOP n 0` is the cheap triage**: full headers (subjects alone map the org's business) without transferring bodies; use it before bulk `RETR`.
3. **`UIDL` exists so clients can re-sync** — unique ids per message let you enumerate and re-download selectively across sessions.
4. **Deletions commit only at QUIT** — a `RSET` before QUIT is your safety catch when you must not destroy evidence; treat `DELE` as destructive on any live mailbox.
5. **995 vs 110 changes only the transport**, not the verbs: `openssl s_client -connect host:995` then type the same POP3 lines — the field Dovecot chain runs exactly this way. See the [[pop3-bug-bounty|methodology]] for session scripts and the credential/exfil phases.

## Where to go next

[[pop3-bug-bounty|POP3 bug bounty methodology]] — CAPA fingerprinting, manual and scripted sessions, credential attacks in hydra context, mailbox exfil after compromise, and the Dovecot/hMailServer specifics.
