---
technology: "Postfix"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: MTA (25/tcp smtpd; /etc/postfix/main.cf + master.cf)
status: confirmed
homepage: "https://www.postfix.org/"
provenance: "evidence-grounded (10 files under command-context gate — /etc/postfix/disclaimer alterMIME pipe via master.cf executed as a real user → reverse shell; postconf -e configuration of the local MTA; 25/tcp 'Postfix smtpd' banners + swaks phishing; 'by host (Postfix)' Received-header banners and mail-path tracing) + upstream-verified (postconf(5) — alias_maps, aliases(5), sender_dependent_relayhost_maps, smtpd_relay_restrictions defaults; Debian postconf documentation). Companion router: [[smtp|SMTP]]. Enrichment pass 1, 2026-11-12."
---

# Postfix — implementation methodology

Postfix is the default Linux MTA and the most common 25/tcp banner in the evidence base. Its
daemon is hardened; the findings are in **configuration**: what aliases pipe to, what
content filters run, what relay maps trust, and what the banner leaks. Target model:
`220 host ESMTP Postfix` on 25, config under `/etc/postfix/` (main.cf parameters,
master.cf services), delivery via local(8) with aliases.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Alias/content_filter pipe abuse | write access to alias/script invoked at delivery | file write | RCE as the pipe user — Critical |
| Sender-dependent relay misconfig | sender_dependent_relayhost_maps + SASL creds readable | file read | Mail relay hijack/spoof channel — Medium |
| Open relay | smtpd_relay_restrictions mis-set | yes | Spam/phish primitive — High |
| User enumeration | RCPT oracle (protocol-common) | yes | Valid account list — Medium |
| Banner/Received archaeology | any mail delivered through the host | yes | Internal topology, software versions — recon |
| SMTP smuggling / parser differential era | old unpatched versions | varies | Spoofed delivery — verify per advisory |

## Phase 0 — Fingerprint

```bash
nmap -sV -p 25 198.51.100.10          # "25/tcp open smtp Postfix smtpd"
swaks --to probe@example.com --server 198.51.100.10   # deliver yourself a test mail
grep '^Received:' received.mail       # "by host (Postfix, from userid 1001)" — each hop's
                                      # software AND executing uid, observed pattern
```

The Received header stamps the Postfix instance *and* the `userid` that submitted —
observed chains (`from userid 1001`) used that to identify the account
running mail-generating services.

## Phase 1 — Alias pipes and content filters (the RCE class)

Postfix runs code at delivery time by design. aliases(5)/postconf(5): an alias entry
`name: "|/path/to/command"` pipes the message into a command; `content_filter` /
`mailbox_command` / master.cf pipe services hand every message to an external program
(altermime, spamfilters, autoresponders). One observed chain is canonical:

```
/etc/postfix/master.cf:  filter service → disclaimer script (alterMIME), runs as user john
/etc/postfix/disclaimer_addresses: which senders trigger it
→ file write on the disclaimer script (or its config) → append reverse-shell line
→ send mail from a listed sender → script executes as john on delivery
```

Method: from any foothold, read `main.cf` (`postconf -n` shows non-defaults) → find
`alias_maps`, `content_filter`, `mailbox_command`, master.cf pipe entries → enumerate
what they execute and **as whom** → any write primitive on those files is RCE at that
user. Remember postalias/newaliases rebuild alias DBs — edited text may need the map
rebuilt or the service reloaded to take effect.

## Phase 2 — Relay maps and second-hop abuse

- `sender_dependent_relayhost_maps` + `smtp_sasl_password_maps`: per-sender outbound
  routing with stored credentials (postconf(5) documents the parameters and the
  hash:/etc/postfix/sender_relay form). Read access to those maps = external relay
  credentials + a spoofing channel that originates from your target's IP.
- `relayhost` / `transport_maps` global routing: any write flips all outbound mail to
  your host — silent exfiltration of password resets and 2FA mail.
- Open-relay check stays protocol-common (`smtpd_relay_restrictions` defaults to
  permit_mynetworks/permit_sasl_authenticated/defer_unauth_destination — deviations are
  the finding; observed Postfix boxes were relays only when admins broke this).

## Phase 3 — Mail as the attack path (observed chains)

With internal address lists (harvested from the org's site/leaks), swaks delivers the
phish from internal-looking senders through the target's own Postfix — content filters
(ClamAV etc.) and client mail handling are the engagement's initial-access phase, not a
Postfix bug. The MTA's role: verify acceptance of internal-only recipient domains and
craft envelope headers that survive its rewrites.

## Phase 4 — Local configuration context (observed)

One observed chain configured a local Postfix (`postconf -e "myhostname = ..."`) as attacker
infrastructure for a mail-redirection chain — Postfix on your own box is the standard
tool for testing mail-path behavior (aliasing, virtual maps, header rewrites) before
touching the target's.

## Command quick reference

```bash
postconf -n                       # non-default config (from any shell on the host)
cat /etc/aliases /etc/postfix/master.cf
swaks --to X --from Y --server HOST --header 'Subject: probe'
```

## Tool mapping (prose)

swaks for envelope-level probing and phish delivery (observed); postconf /
postalias / newaliases for config and alias management on-host; nmap smtp-commands NSE
for verb enumeration; telnet/openssl s_client for raw dialogue. Tools vault owns
per-tool pages.
