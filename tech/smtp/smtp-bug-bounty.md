---
technology: "SMTP"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "upstream-verified — swaks option semantics (jetmore.org ref.txt/Changes, Kali tool page) for --tls/--tlso/--auth/--quit-after; nmap smtp-open-relay.nse source (svn.nmap.org) read for the 16-test envelope matrix, collapsed here to the 6 relay-authorization pattern families; smtp-user-enum -M VRFY/EXPN/RCPT semantics (pentestmonkey/Kali); NetExec protocol list (netexec.wiki llms index — NO smtp protocol, corrected inline); NO STARTTLS research (Poddebniak/Ising/Böck/Schinzel 2021, nostarttls.secvuln.info) for STARTTLS command-injection + stripping; MailKit CVE-2026-41319 (STARTTLS response injection, 2026) as the current-year technique anchor; RFC 6409/8314/8997 submission-port semantics; SPF/DKIM/DMARC spoofability logic (IPVoid/InventiveHQ verdict rules) with espoofer + Spoofy named; smtp-ntlm-info NSE for internal-hostname leakage. Companion overview [[smtp|SMTP]]. Research pass 2026-11-19, model claude-opus-4-8."
source: "verified against primary tool/RFC/advisory documentation 2026-11-19; no evidence-base engagement data — upstream-only methodology article"
---

# SMTP bug bounty methodology

Companion page: [[smtp|SMTP overview]] — the implementation router (Exchange/Postfix/Exim/OpenSMTPD) and the protocol-common attacks. This file is the **email-infrastructure methodology**: how to find, prove, and safely report MTA weaknesses under an authorized program.

Target model: `mail.example.com` / `*.example.com` and `198.51.100.0/24`, 25/tcp relay plus 587/465 submission. Every probe here is single-request and low-volume. **The one rule that keeps this deployable: a relay or spoof probe lands only in a mailbox you personally control — never a third-party recipient, never volume.** A stranger's inbox is not your proof artifact; your own is.

## Where the money is (triage priority)

| Priority | Class | Shape | Typical severity |
|---|---|---|---|
| 1 | Open relay | third-party envelope accepted + queued (proven to *your* mailbox) | High–Critical |
| 2 | Auth on cleartext / STARTTLS strippable | AUTH LOGIN/PLAIN offered before/without TLS, or STARTTLS downgradable | High |
| 3 | Domain spoofable | SPF `~all`/`+all`/missing, DMARC `p=none` or absent, no `sp=` | Medium–High (program-dependent) |
| 4 | User enumeration | VRFY/EXPN enabled, or RCPT 250/550 differential | Medium |
| 5 | Internal hostname/IP leak | banner FQDN, Received chain, Exchange NTLM challenge | Low–Medium |
| 6 | STARTTLS command injection | pre-TLS buffer carried across the TLS boundary | High (needs MitM) |
| 7 | Missing TLS / weak ciphers | plaintext submission, SSLv3/TLS1.0, expired cert | Low–Medium |

Phase 0 fingerprints the MTA; Phases 1–3 are the payout phases; 4–6 round out transport and disclosure. Work them in order on a fresh target.

---

## Phase 0 — Fingerprint the MTA and map capabilities

Goal: name the product and read the capability set *before* any active test. The banner and `EHLO` answer everything pre-auth, and the product name routes you to the right enumeration shape (Phase 1).

```bash
# Banner + capability map in one dialogue
nc 198.51.100.10 25
# <- 220 mail.example.com ESMTP Postfix (Ubuntu)      banner names product + often OS
EHLO probe.example                                   # NOT your real identity; a label
# <- 250-mail.example.com
# <- 250-PIPELINING
# <- 250-SIZE 10240000
# <- 250-VRFY            <- user-enum verb live (Phase 1)
# <- 250-STARTTLS        <- opportunistic TLS present (Phase 3)
# <- 250-AUTH PLAIN LOGIN <- auth mechanisms (Phase 4)
# <- 250 8BITMIME
```

```bash
# Scripted equivalent — nmap enumerates the verb set and version-gates known bugs
nmap -p25,465,587 -sV --script smtp-commands,smtp-ntlm-info,smtp-open-relay,smtp-enum-users mail.example.com
```

Read four things off the output:

- **Product + version** (`Postfix`, `Microsoft ESMTP MAIL Service`, `Exim 4.x`, `OpenSMTPD`). This routes the enumeration shape below. See [[smtp]] for the per-product CVE families — this file does not repeat them.
- **VRFY / EXPN present?** → Phase 1 has a direct oracle. Absent → RCPT differential.
- **STARTTLS present?** → Phase 3. Advertised on 25 but the server also offers `AUTH` *before* `STARTTLS` → a stripping target.
- **AUTH mechanisms before TLS?** → Phase 4 credential-exposure finding.

**Tooling reality check:** NetExec (`nxc`) has **no SMTP protocol** (its protocols are smb/ldap/winrm/mssql/ssh/ftp/rdp/wmi/nfs/vnc only — verified against the NetExec docs index, 2026-11-19). Do not reach for `nxc smtp`; it does not exist. The SMTP toolchain is **swaks** (the transaction engine), **smtp-user-enum** (enumeration), **nmap `smtp-*` NSE** (fingerprint/enum/relay/ntlm), and **metasploit `auxiliary/scanner/smtp/{smtp_enum,smtp_relay}`** (range sweeps). Any guide that says "netexec smtp" is wrong.

---

## Phase 1 — User enumeration (VRFY / EXPN / RCPT differential)

Goal: turn the recipient-validation path into a username oracle. Three verbs, decreasing noise, and the response *shape* differs by server family — read the family first.

### 1.1 The three channels

```bash
# VRFY — the direct verb (quietest when it works)
VRFY jsmith                     # 250 ok / 252 maybe / 550 no such user

# EXPN — expands a mailing list to its members (org-chart leak)
EXPN all-staff                  # 250-member1 / 250 member2 ...   or 550

# RCPT differential — works when VRFY/EXPN are disabled
MAIL FROM:<probe@example.net>
RCPT TO:<jsmith@example.com>     # 250 = deliverable (user exists)
RCPT TO:<nouser9x@example.com>   # 550 5.1.1 = unknown recipient
```

Tooling (the Kali default is the pentestmonkey Perl build):

```bash
smtp-user-enum -M RCPT -U users.txt -D example.com -t 198.51.100.10   # RCPT mode, append domain
smtp-user-enum -M VRFY -u root -t 198.51.100.10                       # single-user VRFY check
```

swaks can drive RCPT enumeration without ever sending a body — stop the dialogue before DATA:

```bash
swaks --server 198.51.100.10 --from probe@example.net \
      --to jsmith@example.com --quit-after RCPT     # reads the RCPT code, sends no mail
```

### 1.2 Server-family response shapes (read before you trust the oracle)

The same verb means different things per product — the oracle you *think* you have may be noise:

- **Microsoft Exchange** — `VRFY` typically answered with a flat `252` ("I'll try", no signal). The reliable oracle is **RCPT**: a valid internal mailbox returns `250`, an unknown one `550 5.1.1 User unknown` (RecipientNotFound). Strong, clean differential on internal connectors.
- **Postfix** — `VRFY` is commonly turned off (`disable_vrfy_command=yes` → `502`/`252`). RCPT is the oracle, **but** watch for accept-then-bounce: a Postfix in front of a backend it cannot address-verify accepts *every* RCPT with `250` and bounces asynchronously — the synchronous oracle is dead, and you must not generate the bounces at volume. Confirm with one known-good and one known-bad address before trusting a run.
- **Sendmail** — historically the enumeration goldmine (`VRFY`/`EXPN` on by default, `250` valid / `550` unknown). Hardened installs set the privacy options `novrfy,noexpn` → both verbs return `252`, killing the direct channel; fall back to RCPT.

**The `252` trap:** `252` means "cannot verify, will try delivery" — *inconclusive*. Many servers return `252` for **every** address specifically to defeat enumeration. If both a valid and a garbage address return `252`, the channel is closed; do not report it as enumeration. Record a valid/invalid pair as your control on every run.

**Evidence standard:** a username-enumeration report is a response-code table — the exact `250` vs `550` lines for a known-valid and a known-invalid address, same server, same session. No user list dumped; two or three proof rows plus the method.

---

## Phase 2 — Open-relay testing (STRICT safe discipline)

Goal: prove the server relays mail for strangers. This is the highest-value and the **most dangerous to test** — a careless relay probe actually sends spam. The discipline below is non-negotiable.

### 2.1 The safe-relay contract

An open relay accepts mail where **neither** the sender **nor** the recipient is local to the server. To prove that without harming anyone:

1. **The recipient is always a mailbox you personally control** on a domain you own — `you@your-mailbox.example`. It is "foreign" to the target (that is what proves relay), but it is *you*. Never a third-party address, ever.
2. **One probe per relay-authorization pattern** (the six below). Not a loop, not a wordlist — six envelopes, maximum.
3. **Proof = the message arriving in your mailbox**, with the target's `Received:` header showing it passed through `mail.example.com`. A `250` acceptance alone is weaker (the server may still drop it); delivery to your box is the artifact.
4. If the server answers `530 5.7.0 Authentication required`, it is **not** an open relay — stop and record that.

### 2.2 The six relay-authorization patterns

The classic test (nmap `smtp-open-relay.nse` runs 16 envelope variants) collapses to six *pattern families* — each a different way to disguise a foreign recipient so a naive relay-authorization check passes it. Run each once, recipient always pointing at your mailbox:

| # | Pattern | Envelope `RCPT TO:` form | Bypass it tests |
|---|---|---|---|
| 1 | Plain foreign | `<you@your-mailbox.example>` | no relay restriction at all |
| 2 | Percent hack | `<you%your-mailbox.example@mail.example.com>` | "local domain" check seeing only the target domain |
| 3 | Double-at | `<you@your-mailbox.example@mail.example.com>` | parser taking the last `@` as authoritative |
| 4 | Quoted local-part | `<"you@your-mailbox.example">` | quoting that hides the `@` from the filter |
| 5 | Source route | `<@mail.example.com:you@your-mailbox.example>` | legacy route-through syntax (RFC 821 relic) |
| 6 | Bang path | `<your-mailbox.example!you>` | UUCP-style path the filter does not parse |

```bash
# Pattern 1, the plain case — swaks is the proof engine
swaks --server 198.51.100.10 \
      --from outsider@example.net \
      --to you@your-mailbox.example \
      --header 'Subject: relay-canary-p1'
# <- look for: "250 2.0.0 Ok: queued as ..." THEN the mail in your inbox
```

For the exotic recipient forms (patterns 2–6) swaks may normalise the address, so send them with raw envelope control or a manual `nc` dialogue:

```bash
nc 198.51.100.10 25
EHLO probe.example
MAIL FROM:<outsider@example.net>
RCPT TO:<you%your-mailbox.example@mail.example.com>    # pattern 2
DATA
Subject: relay-canary-p2
.
QUIT
```

Acceptance of **any** pattern, confirmed by arrival in your mailbox, is the finding. Report which pattern(s) passed — pattern 1 passing is a wide-open relay; only patterns 2–6 passing is a filter-bypass relay (still reportable, often higher-signal because it shows the restriction exists but is defeatable).

For a *range* sweep across `198.51.100.0/24`, `metasploit auxiliary/scanner/smtp/smtp_relay` runs the probe across hosts — but set `MAILFROM`/`MAILTO` to your controlled addresses and keep `RHOSTS` to the authorized range. The manual envelope above stays the proof artifact; the module is only the sweep.

---

## Phase 3 — STARTTLS and transport exposure

Goal: is the confidentiality of the session actually enforced, or can it be stripped? Opportunistic TLS (STARTTLS on 25) is a *downgrade* surface by design.

### 3.1 TLS posture

```bash
# Does STARTTLS negotiate, and to what?
openssl s_client -connect mail.example.com:25 -starttls smtp -brief
testssl.sh --starttls smtp mail.example.com:25        # full cipher/protocol/cert report
```

Findings: STARTTLS advertised but failing to negotiate; SSLv3/TLS1.0 accepted; expired or hostname-mismatched cert on submission ports; `AUTH` offered on a cleartext channel (see Phase 4).

### 3.2 STARTTLS stripping — opportunistic TLS is downgradable

STARTTLS on port 25 is *opportunistic*: a meddler-in-the-middle who can edit the TCP stream removes the `250-STARTTLS` line from the `EHLO` response, and a client configured to "use TLS if offered" falls back to plaintext — exposing everything including `AUTH` credentials. The standard operator trick is to replace `STARTTLS` with garbage bytes rather than delete it, preserving packet length. This is the **client/deployment** finding: a submission service that advertises STARTTLS but does not *require* it, with no MTA-STS policy, is strippable.

```bash
# Is there an MTA-STS policy forcing TLS? (its absence is half the finding)
curl -s https://mta-sts.example.com/.well-known/mta-sts.txt
dig +short TXT _mta-sts.example.com
# a 'mode: enforce' policy defends against stripping; none / 'mode: testing' does not
```

### 3.3 STARTTLS command injection (the NO STARTTLS class)

The 2021 *NO STARTTLS* research (Poddebniak, Ising, Böck, Schinzel) found ~320,000 MTAs (≈2% of the internet) vulnerable to **command injection across the TLS boundary**: the server buffers bytes sent *after* `STARTTLS` in the same TCP segment and replays them *inside* the encrypted session as if the authenticated client typed them. With a login on the same server, a MitM injects a plaintext `RCPT`/`MAIL`/`AUTH` that executes post-handshake — a credential-theft and mail-injection primitive. The current-year echo is **MailKit CVE-2026-41319** (STARTTLS *response* injection: a MitM injects server responses across the plaintext→TLS boundary to downgrade the SASL mechanism). Testing the injection class requires an authorized MitM position; absent that, report the *precondition* — STARTTLS offered on a path where plaintext buffering is observable — and cite the research. The clean remediation to recommend: discard any buffered data at the STARTTLS transition, and enforce TLS (MTA-STS `enforce`).

---

## Phase 4 — Authentication surface

Goal: where do credentials cross the wire, and are they protected?

### 4.1 Port semantics (RFC 6409 / 8314)

- **25/tcp** — server-to-server *relay*. STARTTLS optional. `AUTH` here is unusual and, if offered in cleartext, a finding.
- **587/tcp** — *submission* (RFC 6409). STARTTLS expected before `AUTH`. The correct posture: `AUTH` mechanisms are **not** advertised until STARTTLS has run.
- **465/tcp** — *submissions*, implicit TLS (RFC 8314): the handshake begins on connect, no plaintext window. The older plaintext "smtps" use is deprecated. RFC 8997 sets TLS 1.2 as the floor.

### 4.2 AUTH exposure checks

```bash
# Are LOGIN/PLAIN (base64, not encryption) offered BEFORE STARTTLS?
nc mail.example.com 587
EHLO probe.example
# <- 250-AUTH LOGIN PLAIN      <- if seen WITHOUT a prior STARTTLS: cleartext cred exposure
STARTTLS
# <- then re-EHLO: AUTH should ONLY appear here
```

The finding is `AUTH PLAIN`/`AUTH LOGIN` advertised on a channel where TLS is not yet active — those mechanisms are base64, i.e. plaintext. swaks proves the exposure path (use a *test* account you own; never spray real users):

```bash
swaks --server mail.example.com:587 --auth LOGIN \
      --auth-user you@example.com --tls      # --tls = STARTTLS mandatory; drop it to test cleartext accept
```

If `--auth` succeeds **without** `--tls` (no STARTTLS), credentials went in cleartext — report it. Do **not** run credential lists against the AUTH endpoint; a single authorized test account demonstrates the exposure. No rate-limit on AUTH is worth a one-line amplifier note (a handful of attempts, never a flood).

---

## Phase 5 — SPF / DKIM / DMARC gaps and spoofability

Goal: can an attacker send mail *as* an in-scope domain? This is often the most program-relevant finding, and the **primary evidence is DNS records — no mail sent**.

### 5.1 Read the records (the finding is here)

```bash
dig +short TXT example.com               | grep -i spf     # v=spf1 ... all
dig +short TXT _dmarc.example.com                          # v=DMARC1; p=...
dig +short TXT selector._domainkey.example.com             # DKIM (needs the selector name)
```

Verdict logic (this is what the report argues):

| Record | Weak / spoofable | Strong |
|---|---|---|
| **SPF** `all` qualifier | `+all` (any sender), `?all` (neutral), **missing SPF** | `-all` (hard fail), `~all` (soft, with DMARC enforcing) |
| **DMARC** `p=` | `p=none` (monitor only — **no enforcement**), **absent** | `p=quarantine` / `p=reject` |
| **DMARC** `sp=` | absent or `sp=none` while `p=reject` → **subdomains spoofable** | `sp=reject` |
| **SPF** lookup count | >10 DNS lookups → `permerror` → SPF ignored | ≤10 |

The headline combinations: **SPF present but `~all` with DMARC `p=none`** = effectively spoofable (nothing enforces). **No DMARC at all** = spoofable regardless of SPF. **`p=reject` but no `sp=`** = the apex is safe but `*.example.com` subdomains are wide open — a common high-value miss. DKIM alone proves nothing about spoofability (it is per-selector and cannot be enumerated without the selector name); SPF+DMARC are the discoverable, decisive half.

Tooling: `checkdmarc` (parses and grades all three), **Spoofy** (`MattKeeley/Spoofy` — reads the records and outputs a spoofable/not verdict with the policy logic above). These are **read-only DNS tools** — no mail leaves your host.

### 5.2 The spoof proof — done safely

The DNS analysis *is* the finding; a live-send proof is a bonus the triager sometimes wants. Keep it safe:

- **Default (no mail):** report the record analysis. It is complete and verifiable by the triager from public DNS.
- **If a live proof is wanted:** send the spoofed message **to a mailbox you control** (`you@your-mailbox.example`) with `From: anyone@example.com`, and screenshot it landing in the inbox with SPF/DMARC results in the Authentication-Results header. The spoofed domain is the in-scope target; the recipient is *you*. Never send a spoofed message to a real third party, and never to the target's own users.
- **espoofer** (`chenjj/espoofer`) automates SPF/DKIM/DMARC-bypass construction — it sends real spoofed mail, so point it only at your own mailbox and only on authorized scope.

```bash
# Safe spoof demonstration: target's domain in From, YOUR mailbox as recipient
swaks --server your-smtp.example --from ceo@example.com \
      --to you@your-mailbox.example \
      --header 'Subject: dmarc-spoof-proof' --body 'authz test'
# then read Authentication-Results in the received copy: spf=fail/softfail dkim=none dmarc=none
```

---

## Phase 6 — Internal hostname / IP leakage

Goal: harvest internal network intelligence the MTA gives away pre-auth. Low severity alone, but it feeds every other phase and is clean recon evidence.

- **Banner FQDN** — `220 mx01.internal.corp.example ESMTP` leaks the internal hostname and naming scheme.
- **`Received:` header archaeology** — induce one bounce or autoreply (your own probe), read the `Received:` chain: each hop stamps `by host (Postfix) with ESMTP id ...` plus internal IPs and software versions — a network map and a software inventory in one.
- **Exchange NTLM challenge** — an Exchange connector that offers `AUTH NTLM` leaks the NetBIOS domain, computer name, DNS domain/FQDN, and forest in the NTLM type-2 challenge. `smtp-ntlm-info` extracts it with no credentials:

```bash
nmap -p25,587 --script smtp-ntlm-info mail.example.com
# <- NetBIOS_Domain_Name: CORP / DNS_Computer_Name: mx01.corp.example / DNS_Tree_Name: corp.example
```

That AD-identity disclosure is a legitimate standalone finding on an internet-facing Exchange and a pivot into AD recon. Report the exact leaked fields.

---

## Reporting and evidence standards

The report is the product. Structure every finding the same way:

1. **Title, impact first** — "mail.example.com is an open relay (filter-bypass, percent-hack pattern)" beats "SMTP misconfig".
2. **Preconditions** — unauthenticated, external, one request.
3. **PoC** — copy-pasteable `swaks`/`nc` with expected vs actual, one request per step.
4. **Evidence, scoped to the proof:**
   - **Relay** = the probe message received in **your** mailbox, with the target's `Received:` header. Not a `250` alone, never a third-party delivery.
   - **Spoof** = the record analysis (primary) and/or a spoofed message in **your own** mailbox with the Authentication-Results header.
   - **Enumeration** = two or three response-code rows (valid vs invalid), not a dumped user list.
   - **Leak** = the exact fields disclosed (FQDN, NTLM domain, Received chain).
5. **Remediation** — relay: lock `smtpd_relay_restrictions` / require auth; spoof: SPF `-all` + DMARC `p=reject` + `sp=reject`; STARTTLS: MTA-STS `enforce` and discard pre-TLS buffer; enum: disable VRFY/EXPN; leak: generic banner, strip internal `Received` at the perimeter.

**Mass-mail discipline, absolute:** never send to third-party recipients; never loop a relay or spoof test; six relay envelopes maximum; one test account for AUTH, not a list; one bounce for Received-header analysis. Volume is how this methodology stops being authorized.

### Severity rules of thumb

Open relay (plain) — High–Critical; relay via filter-bypass — High; domain fully spoofable (no DMARC enforcement) — Medium–High and very program-dependent (many programs rate email-auth as informational, some as Medium — read the policy); cleartext AUTH exposure — High; STARTTLS strippable — Medium–High; user enumeration — Low–Medium; internal hostname/NTLM leak — Low–Medium.

---

## CTF notes

- **Banner and `EHLO` first** — the product name and verb set are free, and CTF SMTP boxes almost always hinge on one advertised capability (VRFY on, open relay, or an AUTH you can brute a weak test cred against).
- **VRFY/RCPT enumeration** is the classic first move to harvest a valid local username, which then feeds the next service (a mail-spool LFI-to-RCE chain, a reused password, an SSH login). See [[smtp]] for the `/var/mail/<user>` spool-write → LFI include chain — SMTP's RCPT oracle hands you the username that chain needs.
- **Open relay in CTF** is usually the plain pattern (#1) and the flag is in the delivered mail or the ability to phish an internal "user" bot — the two-account/own-mailbox discipline relaxes in a lab, but the envelope syntax (percent/double-at/source-route) is exactly what a hardened-but-bypassable box tests.
- **EXPN a mailing list** (`all`, `staff`, `admin`) for a free user list where VRFY is off.
- **Read every `Received:` header** on any mail you can retrieve — CTF authors hide hostnames, internal IPs, and occasionally credentials there.
- **swaks is the universal tool** — it scripts the full transaction (auth, TLS, custom headers, raw body) so you rarely need a raw `nc` dialogue except for the exotic relay envelopes.

---

## References

- swaks reference (options, `--tls`/`--tlso`, `--auth`, `--quit-after`) — https://www.jetmore.org/john/code/swaks/files/swaks-20240103.0/doc/ref.txt
- swaks (Kali tool page) — https://www.kali.org/tools/swaks
- smtp-user-enum (pentestmonkey, `-M VRFY/EXPN/RCPT`) — https://pentestmonkey.net/tools/user-enumeration/smtp-user-enum
- nmap `smtp-open-relay` NSE (the 16-test envelope matrix) — https://nmap.org/nsedoc/scripts/smtp-open-relay.html
- nmap `smtp-open-relay.nse` source — https://svn.nmap.org/nmap/scripts/smtp-open-relay.nse
- nmap `smtp-enum-users` NSE — https://nmap.org/nsedoc/scripts/smtp-enum-users.html
- NetExec documentation (protocol list — no SMTP) — https://www.netexec.wiki/
- NO STARTTLS research (command injection + stripping, 2021) — https://nostarttls.secvuln.info
- MailKit STARTTLS response injection, CVE-2026-41319 (SASL downgrade) — https://corgea.com/advisories/vulnerabilities/CVE-2026-41319
- RFC 8314 (submission over implicit TLS, port 465/587) — https://www.rfc-editor.org/rfc/rfc8314
- IPVoid email spoof check (spoofability verdict logic) — https://www.ipvoid.com/email-spoof-check/
- Spoofy (SPF/DMARC spoofability verdict tool) — https://github.com/MattKeeley/Spoofy
- espoofer (SPF/DKIM/DMARC bypass sender — own infra only) — https://github.com/chenjj/espoofer

*Researched and written 2026-11-19 against primary tool, RFC, and advisory documentation (swaks ref.txt, nmap smtp-open-relay.nse source, pentestmonkey smtp-user-enum, NetExec docs index, NO STARTTLS research + MailKit CVE-2026-41319, RFC 6409/8314/8997, IPVoid/Spoofy/espoofer); model claude-opus-4-8.*
