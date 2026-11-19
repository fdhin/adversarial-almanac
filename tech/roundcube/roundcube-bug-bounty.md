---
technology: "Roundcube"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
version: "1.0"
status: thin
provenance: "field-grounded (real deployments: CVE-2020-12640 against a 1.4.2 install versioned by release-date inference, payload staged in an upload-writable shared path, included via a Burp-modified /installer request; CVE-2024-42009 delivered through a front-end contact form whose recipient and content the attacker controls (IDOR), the XSS payload POSTing victim mailbox contents to an attacker listener; /etc/shadow cracks from the mail host replayed into the webmail; docker-socket recon of a loopback-published roundcubemail container) + upstream-verified (NVD/Debian CVE-2020-12640: <1.2.10, <1.3.11, <1.4.4, fix commit 814eadb; cve.org + SonarSource blog + 1.5.8/1.6.8 release notes for CVE-2024-42009/-42008/-42010; defaults.inc.php at tag 1.4.2 and master for db_dsnw, des_key, enable_installer, display_product_info, smtp_user/smtp_pass, useragent). Thin unit — CVE ranges and config facts are upstream-verified, chains are field-shown. Companion overview: [[roundcube|Roundcube]]. Pass 2 new-unit wave B, 2026-11-12."
---

# Roundcube bug bounty methodology

Target model: Roundcube 1.x–1.6.x on Apache/Nginx + PHP, often the
`webmail.example.com` vhost of an org mail stack (Postfix/Dovecot-adjacent),
SQL backend named by `config/config.inc.php`. Authorization for testing
assumed.

Scope discipline: Roundcube-product surface. The IMAP/POP3/SMTP servers behind
it are separate units ([[pop3|pop3]], [[smtp|smtp]]); generic PHP bugs belong
elsewhere. Mail-credential reuse *between* those layers is in scope here.

## Triage table (upstream-verified ranges)

| Family | Affected | Pre-auth? | Payoff |
|---|---|---|---|
| CVE-2020-12640 plugin-name file inclusion | 1.2.0–<1.2.10, 1.3.0–<1.3.11, 1.4.0–<1.4.4 (fixed 1.4.4/1.3.11/1.2.10) | yes (installer or any path feeding the plugin list) | Local file include → web shell |
| CVE-2024-42009 message-body desanitization XSS | through 1.5.7, 1.6.0–1.6.7 (fixed 1.5.8/1.6.8) | crafted email to victim | Steal and send victim's mail |
| CVE-2024-42008 attachment-serving XSS | same research window | crafted email | Session/mailbox access |
| CVE-2024-42010 CSS remote-content leak | same research window | crafted email | Read remote content as victim |

## Phase 0 — Fingerprint and version hunt

The login page hides the version by default (`display_product_info: 1` =
name only). Version sources, cheapest first:

```bash
curl -s https://webmail.example.com/ | grep -i 'title\|generator'
#   <title>Example Webmail :: Welcome to Example Webmail</title>   ← product_name, rebranded
curl -si https://webmail.example.com/installer/index.php | head
curl -s https://webmail.example.com/CHANGELOG | head
curl -s https://webmail.example.com/README.md | head
```

- `installer/` present at all is itself a finding: upstream defaults
  `enable_installer` to false with a "CAN EXPOSE SENSITIVE CONFIG DATA"
  warning — an install that still answers did not clean up the tree.
- Sent-mail `User-Agent`/`X-Mailer`: the 1.x line stamped
  `Roundcube Webmail/<version>` on outgoing mail by default (verified at the
  1.4.2 tag; current releases ship no default) — any message written by the
  org's users versions the install.
- **Release-date inference** (field-proven): when nothing leaks, correlate
  deployment evidence — file timestamps on an exposed file-management system,
  "the webmail is new" chatter in leaked mail — against the Roundcube release
  timeline to bracket the version, then test the CVEs that fit.
- With any account: Settings → About prints the version.

## Phase 1 — Credential context (the mail-cred reuse web)

Roundcube authenticates against IMAP and sends via SMTP reusing the same
password (`smtp_user '%u'`, `smtp_pass '%p'` defaults). Consequences:

- A webmail password is valid on IMAP submission paths, [[pop3|pop3]] and
  [[smtp|smtp]] services, and — as field practice keeps showing — on the
  mailbox owner's shell account. Test everywhere at once.
- The reverse holds: cracks from the mail host (`/etc/shadow` after a local
  root) or from an adjacent app replay straight into the webmail login.
- `login_rate_limit` defaults to three failed attempts per minute — spray
  slowly or not at all; prefer one good credential from elsewhere.

## Phase 2 — CVE-2020-12640: plugin-name file inclusion (pre-1.4.4)

Root cause (upstream advisory): the plugin API builds an include path from the
plugin name without traversal checks — a name containing `../` includes an
arbitrary local PHP file, code execution if you control any file content on
disk. Delivery on a stock install runs through the installer's settings form,
which submits plugin toggles as `_plugin_<name>` parameters:

```
POST /installer/index.php?_step=2   (intercept and edit the plugin fields)
_plugin_../../../../../../dev/shm/payload=
```

Field-proven mechanics (match these or the include fails):

- The include resolves under `plugins/` with the payload name completing a
  path — an observed resolution:
  `/var/www/html/roundcube/plugins/../../../../../../dev/shm/payload/../../../../../../dev/shm/payload.php`
  → `/dev/shm/payload.php`.
- **Every intermediate directory in the traversal must exist**: stage a
  directory and a same-basename `.php` file (e.g. `payload/` and
  `payload.php`) in a path the web user can write — the field chain used a
  shared memory path another app uploaded into.
- Requirement chain: (1) version < 1.4.4, (2) `installer/` reachable or another
  vector that feeds the plugin list, (3) any file-write/upload primitive into
  an includable location. Missing (3)? Hunt one on sibling vhosts first.
- Read the error log after a failed attempt (`logs/` under the install root if
  reachable) — it prints the resolved include path, which turns guesswork into
  arithmetic.

## Phase 3 — CVE-2024-42009: mailbox-stealing XSS (≤1.5.7 / ≤1.6.7)

The bug (upstream advisory + researcher write-up): `message_body()` in
`program/actions/mail/show.php` post-processes already-sanitized HTML and can
be broken out of — a crafted message executes script in the victim's webmail
origin when they view it. Exploitation is a delivery problem, not a bug
problem:

- **Direct**: send the crafted HTML mail to the victim from any mailbox the
  org will render (external sender allowed unless filtered).
- **Front-end relay** (field chain): an internal application whose contact
  form controls both `recipient` and body content-type — an IDOR that lets you
  inject mail *into* the org as a trusted internal sender. Recipient plus
  HTML body is exactly what the XSS needs; the same relay then receives the
  payload's POST-back.
- **Payload shape** (field-proven): JavaScript that reads the victim's message
  list/thread, POSTs the HTML to an attacker HTTP listener, and parses sender
  addresses out of the response markup (the `rcmContactAddress` classes);
  keep it self-contained — one view, one fetch, one POST.
- **What the mailbox pays**: password-reset links for every internal system
  (field chain: a reset link read mid-mail unlocked a protected dashboard),
  credentials in threads, internal hostnames — mail access escalates better
  than shell on the webmail box itself.
- Cover siblings in the same sweep: CVE-2024-42008 (non-HTML/SVG attachment
  serving) and CVE-2024-42010 (CSS-driven remote-content access) share the
  version window and the delivery machinery.

## Phase 4 — Post-auth and host-side loot

- Any account → enumerate identities, search the mailbox for creds, check
  Settings → About for the exact version, and read `identities` for the org's
  address map.
- On the host, read `config/config.inc.php` first:
  `$config['db_dsnw'] = 'mysql://user:pass@host/db';` (default
  `mysql://roundcube:@localhost/roundcubemail`) and `$config['des_key']`.
  An unchanged 1.x default key (`rcmail-!24ByteDESkey*Str`) plus the session
  table decrypts parked IMAP passwords.
- `$config['plugins']` names the attack surface extensions actually load
  (password changers, ZIP downloaders, attachments plugins) — plugin CVEs are
  per-name, so this line gates them.

## Phase 5 — Containerized deployments

Field shape: `roundcube/roundcubemail` published `127.0.0.1:8000->80/tcp`
beside a mailserver container holding 25/110/143/587/993/995 on loopback —
found via the Docker socket from a compromised neighbor (see [[docker|Docker]]).

- Such a webmail is invisible externally: enumerate from inside (docker exec,
  a proxy through the compromised container, or the panel that reads docker
  state). Add `:8000`-style loopback ports to internal scan lists.
- The image is a stock upstream build — assume current Roundcube with default
  config; the bugs live in what fronts it (mailserver auth, the exposed
  socket) rather than the app version.

## Decision points

- Version ≤ pre-1.4.4 window + installer present + any upload path → Phase 2
  (fastest shell).
- Modern version but org uses webmail heavily → Phase 3 delivery engineering;
  hunt IDOR/mail-injection front-ends that make it one-step.
- Have creds from anywhere else → log in first (Phase 1 says they likely
  work), then decide between mailbox mining and host-side Phase 4.
- Loopback binding found → Phase 5 before anything; external-surface
  conclusions are wrong until you've enumerated from inside.

## Tool mapping (in prose)

curl and jq for the fingerprint endpoints; Burp Suite to intercept and edit
the installer request (the `_plugin_` fields) and to inspect contact-form
POSTs; a plain HTTP listener (python http.server or a small Flask app) to
receive the XSS POST-back; hashcat with rockyou for shadow/webmail credential
round-trips; the mysql client against `db_dsnw`; docker CLI against a
reachable socket for container recon.
