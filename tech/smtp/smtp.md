---
technology: "SMTP"
kind: overview
audience: offensive-security
family: network-services
role: area-router
layer: mail transfer (25/tcp submission/relay, 465/587 with TLS; HTTP surfaces 80/443 for Exchange)
standards: "RFC 5321 (SMTP), RFC 2045-2047 (MIME); product families: Exchange, Postfix, Exim, OpenSMTPD"
status: confirmed
provenance: "evidence-grounded (split probe with command-context gate, 2026-11-12: Postfix 10 files confirmed — banner/config/Received-header markers; Exchange 4 files thin — OWA/EWS/msexchange markers; Exim 0 files — gate FAILED, no variant page; generic VRFY/EXPN/RCPT/relay markers 10 files. The loose probe's Exchange=18 collapsed to 4: bare 'exchange' matched key-exchange prose and Open Web Analytics 'owa.js') + upstream-verified (RFC 5321 VRFY/EXPN semantics, NVD CVE-2021-26855 ProxyLogon, NVD CVE-2019-10149 Exim, postconf(5)/aliases(5)). Variant pages: [[smtp-exchange|Exchange]], [[smtp-postfix|Postfix]]. Enrichment pass 1, 2026-11-12. Pass 2 extension 2026-11-12: mail-spool LFI-to-RCE chain and OpenSMTPD CVE-2020-7247 sections added — CVE verified against the Qualys advisory (smtp_mailaddr/smtp_session.c, a8e222352f exploitable-since commit, fixed 6.6.2, 'uncommented' default config remote, 64-char local-part limit, $ and | escaping, Morris-worm body trick, valid-RCPT requirement) and openbsd.org errata; exploit-db 47984 is the public exploit shape."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# SMTP (area router)

> Mail servers leak their user list before auth (VRFY, EXPN, RCPT oracles), relay for strangers when misconfigured, and — in the Exchange case — put an entire AD attack surface behind one HTTPS port.

## What it is

SMTP (RFC 5321) is the envelope protocol: HELO/EHLO, MAIL FROM, RCPT TO, DATA, delivered over 25/tcp (or 465/587 with TLS). Everything an attacker cares about sorts by **implementation**: Microsoft **Exchange** couples the SMTP daemon to a vast HTTP estate (OWA, EWS, Autodiscover) and to AD — its history is pre-auth proxy/RCE chains; **Postfix** is the hardened default on Linux — its findings live in config (alias pipes, relay maps, content filters) rather than the daemon; **Exim** is the configurable-everything MTA whose string-expansion language produced years of RCE families (CVE-2019-10149, the 2019 return of RCE via `deliver_message()` recipient handling in 4.87–4.91, KEV-listed — no evidence-base hits, so no variant page; treat any Exim banner as a version-check trigger); **OpenSMTPD** appears in the evidence base as an RFC-strict daemon where user enumeration still works through RCPT (observed).

The operator takeaway: the banner names the product in the first line (`220 host ESMTP Postfix`, `220 host Microsoft ESMTP MAIL Service`), and that one line routes you to the right playbook — product CVE families for Exchange/Exim, config abuse for Postfix, protocol oracles everywhere.

## The anatomy that matters on an engagement

    client → 25/tcp → banner (product+version) → EHLO (capabilities: VRFY? STARTTLS? AUTH?)
         → MAIL FROM/RCPT TO (delivery oracle: enumerate users by response codes)
         → DATA (MIME body: client-side attack delivery)
         open relay test: third-party MAIL FROM + RCPT accepted+queued = spam primitive
    Exchange adds: 443 → /owa (forms auth+spray), /ews (SOAP API), /autodiscover (config+creds)

Three structural properties drive most findings:

- **Recipient validation is an oracle.** VRFY and EXPN answer directly when enabled; when disabled (RFC 5321 lets servers refuse), RCPT TO still discriminates 250 vs 550 — that is a user enumeration channel on any accept-then-bounce-free server (observed with smtp-user-enum's RCPT mode).
- **Mail is a credential-delivery system.** swaks-fired phishing with internal sender names (observed) turns the MTA into the initial-access tool; MIME headers and HTML bodies are the payload format.
- **Post-accept processing is code.** Alias entries piping to commands, content_filter/disclaimer scripts (observed: alterMIME disclaimer pipe running as a real user), autoresponders — the MDA layer executes operator config, and file-write on that config is RCE at the mailing user's privilege.

## Where you'll meet it

- **Corporate MX records** (external recon): the public mail exchanger names the product family before you touch it — Exchange on AD-joined estates, Postfix on Linux, security appliances in front.
- **Internal relays and gateways** (observed): banners, Received-header archaeology (each hop stamps `by host (Postfix) with ESMTP id ...` — observed chains traced mail paths through configs this way), open relay checks.
- **Exchange estates** (observed): OWA password spraying (observed with atomizer spray + OWA recon), EWS endpoints, Exchange-group membership feeding AD ACL abuse (observed).
- **Mail-delivered malware chains**: MIME-encoded payloads (observed chains decoding base64 attachments) and macro documents ride SMTP as transport.

## Protocol-common attacks (any MTA)

**Banner/MIME fingerprint** — the 220 line and EHLO extensions (STARTTLS, AUTH mechanisms, SIZE, VRFY) identify product and posture; Received headers from any delivered mail (your own probe message) enumerate internal hops and their software versions.

**User enumeration** —

```bash
smtp-user-enum -M RCPT -U users.txt -t 198.51.100.10    # RCPT oracle (works when VRFY is off)
telnet 198.51.100.10 25
VRFY user@example.com        # 252/250 = maybe/yes, 550 = no such user
EXPN listname@example.com    # expands to membership — mailing lists leak org charts
```

Observed: OpenSMTPD disabled VRFY but the RCPT path still enumerated; note its
RFC-strict `<user@host>` angle-bracket requirement broke naive tooling until quoted
exactly.

**Open relay test** — `MAIL FROM:<outsider@example.net>` + `RCPT TO:<other@elsewhere.example>`; acceptance (250) plus queueing = relay. Swaks automates: `swaks --to test@elsewhere.example --from probe@example.net --server 198.51.100.10`. Open relays are the phishing/spam primitive and often the pivot into internal-only recipient domains.

**Exim version-gate (protocol-common because no evidence-base variant)** — an Exim banner (any 4.x) means: check the version against the RCE families before exotic work. CVE-2019-10149 (4.87–4.91, NVD: improper recipient-address validation in deliver_message() → command execution; CISA KEV) is the archetype of a class of string-expansion/format RCEs; verify each candidate CVE against NVD/exim.org advisories — never from memory.

**Mail spool as an LFI payload store (deliver mail → include `/var/mail/<user>` → RCE)** — any MTA doing local delivery appends messages to the mbox spool `/var/mail/<user>`, which makes port 25 a *file-write primitive with full content control* on hosts that also have an include-capable LFI. Deliver the payload by speaking SMTP directly (netcat or swaks): `MAIL FROM:` anything, `RCPT TO:` a local user, `DATA` with the webshell in the body (`<?php system($_GET['cmd']); ?>`), terminate with a lone `.`. Then aim the LFI at the spool — traversal-filter bypasses ride along: a naive `str_replace("../","")` filter falls to `....//` chains because each pass strips the inner `../` and leaves a fresh one behind:

```text
GET /index.php?page=....//....//....//....//....//....//var/mail/<user>&cmd=<command>
```

Gates: local delivery must actually spool (an internal relay-only MX forwards or bounces instead of writing the mbox), you need a valid local username (`/etc/passwd` through the same LFI, or the RCPT oracle above), and the LFI must include, not just read. Execution lands as the web server user — chain it the way you would any webshell.

**OpenSMTPD version-gated RCE — CVE-2020-7247 (pre-auth, as root)** — the Qualys-advisory bug in `smtp_mailaddr()` (smtp_session.c): shell metacharacters in the `MAIL FROM` local part are not neutralized, and a crafted SMTP session executes arbitrary shell commands **as root**. Exploitable from the May 2018 grammar rewrite (commit a8e222352f) through 6.6.1, fixed in **6.6.2** (OpenBSD 6.5/6.6 errata); remote in the "uncommented" default configuration — listening on all interfaces, accepting external mail — while the loopback-only default config exposes it locally. Constraints the public exploits already route around: local part limited to 64 characters, `$` and `|` escaped (the mail-body-as-shell-script Morris-worm technique carries bigger payloads), and one valid `RCPT TO:` required — harvest a real recipient (the RCPT oracle) before firing. Any OpenSMTPD banner in the 6.6 era is a version-check trigger; the exposure is remote pre-auth root command execution, the headline finding of the engagement.

## Reading the protocol's mind

1. **The banner and EHLO answer before any auth** — product, version, capabilities, and whether VRFY survives.
2. **RCPT distinguishes users** — response-code deltas are the oracle when every direct verb is disabled.
3. **Received headers are a network map** in every phishing reply you can induce.
4. **Postfix pipe config executes at mail time** — alias/content_filter/disclaimer writes are code-exec tickets at the mailing user's privilege.
5. **Exchange on 443 outweighs Exchange on 25** — the HTTP estate (OWA/EWS/Autodiscover) is where its history lives; see the variant page.

## Ports cheat-sheet additions (2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); the three CVE gates re-verified against NVD records, NSE and module names against the nmap and metasploit repos. VRFY/RCPT enumeration, the manual open-relay dialogue and unauthenticated send above already cover the source's core commands — skipped.

- **One-sweep NSE battery** — `nmap -p25 --script smtp-commands,smtp-enum-users,smtp-vuln-cve2010-4344,smtp-vuln-cve2011-1720,smtp-vuln-cve2011-1764` fingerprints the verb set, runs the user-enumeration oracle natively, and version-gates three named bugs in one pass.
- **What the three vuln scripts actually gate** (each verified against its NVD record — two different MTAs hide behind the script names):
  - **CVE-2010-4344 — Exim before 4.70**: heap overflow in `string_vformat()`, reached pre-auth by a session with *two MAIL commands plus a large message carrying crafted headers* (the bug is in the improper-rejection logging of that sequence).
  - **CVE-2011-1720 — Postfix before 2.5.13 / 2.6.10 / 2.7.4 / 2.8.3**: only when certain Cyrus SASL methods are enabled, a failed AUTH leaves the server handle stale — heap memory corruption, denial of service to possible code execution via an invalid AUTH command.
  - **CVE-2011-1764 — Exim before 4.76**: format-string in `dkim_exim_verify_finish()` — DKIM verification log lines interpolate attacker-controlled identity fields, so a `%` in the identity field is the trigger.
  Never report "smtp-vuln hit" without pinning the product and the version gate — the scripts only ever meant that.
- **Framework-side relay sweep** — metasploit's auxiliary/scanner/smtp/smtp_relay runs the open-relay probe (MAILFROM/MAILTO parameters) across an address range; the manual envelope dialogue above remains the proof artifact, the module is the sweep.

## Where to go next

[[smtp-exchange|Exchange variant]] — OWA/EWS/Autodiscover recon, spraying, proxy-bug era context. [[smtp-postfix|Postfix variant]] — alias pipes, sender-dependent relay, content-filter abuse.
