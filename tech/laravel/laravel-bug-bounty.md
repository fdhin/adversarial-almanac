---
technology: Laravel
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: web-platforms
evidence: "14 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 14 writeups in the writeup corpus + upstream-verified: OSV CVE-2021-3129, cve.org/NVD CVE-2018-15133 (version ranges, X-XSRF-TOKEN, phpggc Laravel/RCE/3), ambionics/phpggc README, laravel.com/docs encryption + upgrade notes, laravel.com/docs Eloquent mass-assignment ($fillable/$guarded). Accessed 2026-11-12"
---

Companion page: [[laravel|Laravel overview]] — what the framework is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Laravel Bug Bounty Methodology

**Scope:** Laravel deployments — `.env`/`APP_KEY` chains, Ignition/debug-mode RCE and disclosure, session and queue deserialization, mass assignment, second-order SQL injection in Eloquent-backed apps. **Out of scope:** pure PHP language bugs with no framework dependency, Redis/SMTP as technologies (their own units), the PHP gadget chains of other frameworks (phpggc's other families).

**Authorization:** presume written permission. Cookie forgery and deserialization phases manufacture server-side code execution — stage benign markers (`id`, a proof file) before any interactive shell, and confirm rules cover account-session manipulation.

## Decision tree (one screen)

1. Laravel confirmed (cookies, routes, debug page, artisan in cron) → Phase 2.
2. Any `.env`/source read → Phase 3. `APP_KEY` found → Phase 4 (key exploitation; version decides RCE vs decryption-only).
3. `/_ignition/health-check` answers → Phase 5 (Ignition RCE, version-gated).
4. Redis/queue reachable and app stores Laravel payloads → Phase 6 (queue deserialization).
5. Authenticated app access → Phase 7 (mass assignment, second-order SQLi).

## Phase 1 — Fingerprinting

```bash
curl -si http://example.com/          # Set-Cookie: laravel_session=eyJpdi...; XSRF-TOKEN=eyJpdi...
curl -s http://example.com/_ignition/health-check     # Ignition present (debug component)
```

Tells: `laravel_session`/`XSRF-TOKEN` cookie pair; `X-XSRF-TOKEN` request header echo on SPAs; `Cache-Control: no-cache, private`; error pages titled with the framework's exception styling; `vendor/laravel/framework` paths in any disclosure; `php artisan` in cron/service definitions; `storage/framework/`, `app/Http/Controllers`, `routes/web.php` layout in listings or leaked source. Version: `composer.json`/`composer.lock` in leaked source, `vendor/laravel/framework/src/Illuminate/...` path fragments in stack traces, or the debug page's environment tab.

## Phase 2 — .env and source loot paths

| Path | What it yields |
|---|---|
| LFI / arbitrary read | `.env` at project root: `APP_KEY`, `APP_DEBUG`, `DB_*`, `MAIL_*`, service secrets |
| Exposed backups, dotfile listings | `.env.bak`, editor swaps, mis-served project root |
| Git/SCM exposure | full source incl. `.env` history; product forks keep editable env in repo |
| Adjacent service compromise | app source under `/var/www/<project>`, `app/Http/Controllers/*` |
| Cron/process listing | `php artisan schedule:run` reveals app path and scheduler-defined jobs |

Read order once source is available: `.env` (key, debug flag, credentials) → `composer.json` (framework + package versions → CVE gates) → `routes/web.php` (attack surface map) → `app/Http/Controllers` (auth logic, raw SQL, file handling) → `app/Console/Kernel.php` (scheduled jobs).

## Phase 3 — .env triage

`APP_KEY=base64:...` → Phase 4. `APP_DEBUG=true` → Phase 5 as well (disclosure at minimum). Database and mail credentials → direct pivots. Also record timezone/locale and package-specific keys (passport, scout, broadcasting) — each is a lateral secret.

## Phase 4 — APP_KEY exploitation

**4a. Cookie/session decryption and forgery.** Stripping `base64:` gives the raw key for AES-256-CBC. Decrypt captured `laravel_session` values (base64-decode → JSON `{iv, value, mac}`) to read session contents; forging valid new envelopes (correct MAC) gives session-data manipulation on any version. With `APP_PREVIOUS_KEYS` present, old keys may still open old ciphertexts.

**4b. X-XSRF-TOKEN unserialize RCE — CVE-2018-15133 (version-gated).** Laravel through 5.5.40 and 5.6.x through 5.6.29 decrypt the `X-XSRF-TOKEN` header and pass it to `unserialize()`. Chain: pick a phpggc `Laravel/RCE` gadget for the target's framework generation, generate `./phpggc Laravel/RCE<N> system '<cmd>'`, encrypt-and-MAC the payload with the leaked key, send as `X-XSRF-TOKEN`. Public exploit scripts take URL + APP_KEY and do the crypto. Version-gate before claiming: newer majors dropped the unserialize; on those, a leaked key is decryption/forgery only — report accordingly.

**4c. Ignition file-write RCE — CVE-2021-3129 (version-gated, unauthenticated).** Preconditions: debug mode ON, Laravel before 8.4.2, Ignition (facade/ignition) before 2.5.2. Probe `GET /_ignition/health-check`. The chain abuses `/_ignition/execute-solution` (the "make view variable optional" solution) to `file_put_contents`/`file_get_contents` with controlled arguments: poison `storage/logs/laravel.log` with a PHP-filter-crafted payload, generate a phpggc phar (`--phar` output), replay the solution requests to convert/clear the log into the payload file, trigger it. Ready-made CVE exploits automate the sequence given the base URL; verify version pin (composer) before reporting the RCE — patched Ignition (>=2.5.2, spatie/laravel-ignition lineage) makes the endpoint inert.

## Phase 5 — Debug-mode disclosure (no RCE needed)

With `APP_DEBUG=true`: exception pages leak full stack traces with vendor frames (file paths, app root), environment variables (masking of secret-named vars exists but app-specific vars often survive), and request dumps. Even on patched Ignition, mine traces for: application namespace and paths, package versions, DB name/engine, and internal hosts in the trace arguments. The disclosure itself is a reportable finding; enumerate systematically by triggering exceptions (bad IDs, malformed JSON, type confusion).

## Phase 6 — Queue/cache deserialization through infrastructure

Where the app's session or queue backend is reachable (redis on localhost pivoted out, exposed cache service):

1. Confirm Laravel conventions — key prefixes `laravel_session:` (redis sessions) and `laravel:` (queue payloads), values starting `O:` when serialized objects are stored.
2. Generate the gadget: `./phpggc Laravel/RCE<N> system '<cmd>'`; prefer null-byte-free chains when the transport or the value handler rejects binary (chain README flags which contain nulls; test `-a`/alternate encodings).
3. Write the payload at the expected key (or poison a queued job), then trigger consumption: page request for session values, `php artisan queue:work --once` / `schedule:run` for jobs.
4. Session values are encrypted with `APP_KEY` on many stacks — if reads show ciphertext envelopes, re-encrypt the gadget with the key first (same envelope format as Phase 4a).

## Phase 7 — Authenticated app-layer bugs

- **Mass assignment:** Eloquent models guard nothing unless `$fillable`/`$guarded` say so; test `POST`-able models for type-confused or privilege fields (`is_admin`, `role`, `user_id`) — create/update endpoints accepting unexpected columns escalate silently. Upstream documents the class explicitly.
- **Second-order SQL injection:** values stored via one endpoint (preferences, profile fields) that are later concatenated into raw query builders (`whereRaw`, `DB::select`, `orderByRaw`) in another code path. Test stored values with SQL metacharacters, then walk every consumer of the stored field (exports, searches, admin filters). Watch for sanitization-that-isn't: input filters that strip literal whitespace fall to comment separators (`/**/`) in the stored value, which the consumer's raw query still honors — feed endpoints rendering "different content per stored preference" are the classic boolean oracle for driving this blind. sqlmap's second-request mode (`--second-req`) automates the pair once both endpoints are captured.
- **Auth-logic bugs in controllers:** Laravel apps hand-roll PIN/2FA/reset flows; compare controller logic against framework auth contracts — type juggling, unprotected verification endpoints, and session fixation on custom flows are recurring.

## Disclosure notes

- Version-gate both RCE classes with evidence (composer pin or debug-page version): "APP_KEY leaked" is not RCE on modern majors — state decryption/forgery impact separately from unserialize impact.
- Ignition: name the exploited component and versions (Ignition <2.5.2, Laravel <8.4.2 debug mode) and whether `/_ignition/health-check` still answers post-fix.
- Mass assignment: report the exact extraneous parameter accepted and the privilege transition it caused.
- Second-order SQLi: document both endpoints (writer and consumer) — one alone reproduces nothing.

## Appendix A — Tool mapping (prose)

- **curl / Burp Suite** — fingerprinting, cookie capture, header replay, Ignition probes.
- **phpggc** — Laravel gadget-chain generation (`Laravel/RCE` families, `--phar` phar output for log-poisoning chains); the tool's chain list self-documents version applicability.
- **public CVE exploit scripts** — automate the APP_KEY crypto and the Ignition request sequence; audit before running, then stage markers.
- **redis-cli** — key-prefix recon and payload planting for Phase 6.
- **openssl / Python cryptography** — offline AES-256-CBC envelope decryption and MAC handling for Phase 4a.
- **gobuster/ffuf-style fuzzers** — `.env`/backup discovery and route enumeration.
