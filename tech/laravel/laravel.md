---
technology: Laravel
kind: overview
audience: offensive-security
family: web-platforms
layer: PHP web framework (.env config, APP_KEY encryption, Eloquent ORM, artisan CLI, Ignition debug pages)
language: PHP
first-released: 2011
license: MIT
homepage: "https://laravel.com"
github-repo: "laravel/framework"
evidence: "14 writeups (command-context gate: APP_KEY=base64 loot, laravel_session/XSRF-TOKEN/X-XSRF-TOKEN wire artifacts, php artisan invocations, _ignition routes and facade/ignition + spatie/laravel-ignition, phpggc Laravel chains, laravel/framework composer/vendor paths, storage/framework + app/Http/Controllers file-layout markers; parent estimate was 10-12 with 9 named seeds — all 9 seeds confirmed, gate adds 5)"
status: enriched
provenance: "corpus-mined from 14 writeups in the writeup corpus (see ../internal coverage ledger) + upstream-verified: OSV CVE-2021-3129 (Ignition <2.5.2, Laravel debug mode <8.4.2, file_get_contents/file_put_contents misuse), CVE-2018-15133 via cve.org/NVD (X-XSRF-TOKEN unserialize, Laravel through 5.5.40 and 5.6.x through 5.6.29, phpggc Laravel/RCE/3 PendingBroadcast), ambionics/phpggc README (Laravel gadget chains), laravel.com/docs encryption (AES-256-CBC + MAC, APP_KEY/APP_PREVIOUS_KEYS, php artisan key:generate). Accessed 2026-11-12"
---

# Laravel

> One `.env` file holds the whole threat model: `APP_KEY` encrypts and signs every cookie and session, `APP_DEBUG` decides whether strangers get stack traces, and when either leaks, a stock framework turns into an unauthenticated remote-code-execution chain.

## What it is

Laravel (2011, Taylor Otwell, MIT) is the dominant PHP web framework — MVC routing, the Eloquent ORM, Blade templates, and the `php artisan` CLI. Deployments are instantly recognizable by convention: a `public/` web root, configuration in a `.env` file (`APP_KEY`, `APP_DEBUG`, database credentials), `laravel_session` and `XSRF-TOKEN` cookies, and controller code under `app/Http/Controllers` with routes in `routes/web.php`. Beyond greenfield apps, a family of self-hosted products (CRMs, e-commerce platforms) is Laravel under the hood, so you meet the framework inside product boxes as often as in bespoke code.

The operator's mental model: **one key plus one environment file**. The framework's central guarantee — clients cannot read or forge what the server encrypted — is exactly as strong as `APP_KEY` secrecy. The key encrypts session cookies (AES-256-CBC with a MAC, base64 JSON `{iv, value, mac}` — the tell-tale `eyJpdi...` cookie prefix), and on framework versions with an `unserialize` in the cookie/token path, key knowledge converts directly into object-injection RCE via public PHP gadget chains.

The takeaway: Laravel's own bones are modern, but its deployment failures are catastrophic and standardized. A leaked `.env` is a master key; `APP_DEBUG=true` in production mounts the Ignition error pages, whose historical file-handling bug is unauthenticated RCE; and any infrastructure that stores serialized Laravel objects the app will later unserialize (queue workers, cache backends) is a gadget-chain detonator waiting for a key or a write primitive.

## The anatomy that matters on an engagement

    request → public/index.php → routes/web.php → middleware → controller → Eloquent → database
                                  ↘ .env (APP_KEY, APP_DEBUG, DB_*, MAIL_*, service secrets)

    loot chain A: .env read (LFI, exposed backup, source disclosure, SCM, adjacent service)
      → APP_KEY → decrypt/forge cookies & sessions, decrypt stored secrets
      → unserialize gadgets (phpggc Laravel/RCE*) → RCE on vulnerable versions
    loot chain B: APP_DEBUG=true + old Ignition → /_ignition/execute-solution → file write → RCE
    loot chain C: app writes serialized objects to redis/queue (laravel: / laravel_session: prefixes)
      → attacker writes phpggc payload at that key → worker unserializes → RCE

Three structural properties drive most findings:

1. **Everything client-visible is one encrypted blob format.** Cookies, sessions, cached values share the `{iv, value, mac}` envelope; with `APP_KEY` you decrypt them all offline, and on old versions forge them into deserialization payloads.
2. **The debug page is a disclosure and an RCE at once.** Ignition stack traces name vendor files, paths, and environment values; the same component's historical `/_ignition/execute-solution` endpoint wrote files from attacker-controlled arguments on unpatched versions.
3. **Serialized PHP objects flow through infrastructure.** Queue payloads and some cache/session backends store `serialize()`d objects; the framework expects only itself to write them, so any adjacent write primitive (redis access, SSRF, weak admin) becomes code execution through the same public gadget chains.

## Where you'll meet it

- **Product installs** — self-hosted CRMs and shops running a known Laravel core with product-specific controllers; composer pins in leaked `composer.json`/`composer.lock` give exact framework and dependency versions.
- **Internal tools and localhost-only admin panels** — Laravel apps bound to 127.0.0.1 or private vhosts, reached through tunnels, SSRF, or reverse proxies; the debug mode is often still on because "nobody can reach it".
- **SPA backends** — JSON APIs where the frontend sends the `XSRF-TOKEN` cookie back as the `X-XSRF-TOKEN` request header; that header pair alone fingerprints Laravel-style flows even when the framework is never named.
- **Cron and queue infrastructure** — `php artisan schedule:run` entries and queue workers running as service accounts; scheduled-job definitions and their helper classes are both recon and privesc material.

## Reading the framework's mind

- `laravel_session`/`XSRF-TOKEN` cookies (and API reuses of them) decrypt to base64 JSON `{"iv":..., "value":..., "mac":...}` — AES-256-CBC keyed by `APP_KEY` (strip the `base64:` prefix). A captured cookie plus a candidate key is an offline decryption test; session payloads reveal the user id and session attributes the app trusts.
- `APP_KEY=base64:<44 chars>` in any readable `.env` is the single highest-value line in a Laravel loot pass. Newer versions list `APP_PREVIOUS_KEYS` — old keys may still decrypt older stored ciphertexts.
- CVE-2018-15133 (upstream history): Laravel through 5.5.40 and 5.6.x through 5.6.29 `unserialize()` the decrypted `X-XSRF-TOKEN` — with a leaked `APP_KEY`, encrypting a phpggc `Laravel/RCE` payload into that header is unauthenticated RCE. Public exploit scripts take URL + key as arguments.
- CVE-2021-3129 (upstream history): Ignition before 2.5.2 on sites running debug mode with Laravel before 8.4.2 — `/_ignition/execute-solution` reaches `file_get_contents`/`file_put_contents` with attacker arguments; the working chain clears/poisons `storage/logs/laravel.log` through a PHP filter and detonates a phpggc phar payload. Check `/_ignition/health-check` first to confirm the component exists.
- Serialization targets are named by convention: redis-backed sessions and queues under `laravel_session:` / `laravel:` key prefixes, payload bodies starting `O:` (object) — chainable with phpggc's Laravel gadget families; prefer chains without null bytes when the transport rejects them.

## Where to go next

[[laravel-bug-bounty|Laravel bug-bounty methodology]] — phased: fingerprint → .env and source loot paths → APP_KEY exploitation (cookie forgery, X-XSRF-TOKEN and Ignition RCE chains with version gates) → queue/cache deserialization → debug-mode disclosure → mass assignment and second-order injection patterns.
