---
technology: "Ruby on Rails"
kind: overview
audience: offensive-security
family: web-platforms
layer: full-stack web framework (MVC over Rack; Puma/Passenger serving, ActiveRecord ORM, Marshal-backed sessions and caches)
language: Ruby
first-released: 2004 (David Heinemeier Hansson, extracted from Basecamp)
license: MIT
homepage: "https://rubyonrails.org"
github-repo: "rails/rails"
provenance: "field-grounded (6 contributing writeups gated by command-context markers — Rails/Passenger/WEBrick/ERB/cache-store command lines, ActiveRecord schema artifacts, gem/bundler paths; exclusions: GitLab-internal Rails usage stays in the gitlab unit, and 'Kerberos'-substring ERB noise) + upstream-verified (GitHub Advisory GHSA-2p68-f74v-9wc6 and the rubyonrails-security posts for CVE-2020-8165 and CVE-2019-5418, Rails release announcements for version gates, Snyk/CVE record for CVE-2022-25765, Camaleon CMS advisory and PoC for CVE-2025-2304). Enrichment pass 2, 2026-11-12. Scope note: the unit covers the Rails stack proper and the immediately adjacent Ruby web surfaces the same chains travel (WEBrick, ERB rendering, Psych YAML, bundler) — where the bug class is framework-independent the page says so."
---

# Ruby on Rails

> A framework that auto-signs everything — sessions with Marshal, cache entries with Marshal, forms with an authenticity token — so the entire attack surface reduces to one question: can you make Rails deserialize a string you chose?

## What it is

Rails (2004, DHH; MIT) is the full-stack Ruby web framework that normalized convention over configuration: ActiveRecord models over a database, ERB templates, a router, and Rack underneath. On the wire it is a Ruby app server — Puma in development (port 3000), or nginx fronting Phusion Passenger in production — and every one of those layers leaks a recognizable signature. For an operator the defining property is **invisible serialization**: cookie sessions, cache entries, and flash messages are Marshal-serialized blobs, and a single leaked `secret_key_base` (or a cache store that unmarshals what you hand it) converts directly into code execution through public gadget chains in ActiveSupport itself.

The bug profile follows the architecture: deserialization at the session/cache edge (the `ActiveSupport::Deprecation::DeprecatedInstanceVariableProxy` → ERB gadget), template injection wherever ERB renders user input, file-content disclosure through render/download paths, mass assignment wherever strong parameters were opted out with `permit!`, and a deployment neighborhood (gitweb siblings, `Gemfile.lock`s, bundler configs, SQLite dev databases) that gives up credentials with unusual generosity.

## The anatomy that matters on an engagement

    client → nginx → Passenger/Puma → Rails (router → controller → ActiveRecord → ERB)
                        ↘ cookies: Marshal + AES-HMAC (secret_key_base from config/secrets.yml or ENV)
                        ↘ cache: Redis/Memcached via ActiveSupport cache stores (Marshal entries)
                        ↘ disk: development.sqlite3 (dev), log/production.log, tmp/restart.txt

Three structural properties drive most findings:

- **Everything trusted is a serialized blob.** `Rails.cache.fetch` and the session cookie both round-trip Marshal data; when a user-controlled value lands inside either, the gadget chain is one paste away.
- **The framework's own artifacts fingerprint and betray it.** `X-Runtime`/`X-Request-Id` headers, `<app>_rails_session` cookies, `authenticity_token` fields, `/packs/js/application-<digest>.js` Webpacker assets, the Phusion Passenger server header, and stack traces that print the exact Rails version.
- **The repo is the recon.** Sibling services (gitweb) or file-read bugs that expose `Gemfile`, `config.ru`, and `.ruby-version` give you the exact framework and gem versions, which decide every version-bound bug below.

## Where you'll meet it

- **Marketing sites, internal tools, APIs behind a React frontend** — the Rails API flavor (`/api` subdomain, JSON errors). Modern deployments pin exact gem versions; the API-frontend split means file-read and download endpoints (`/download?filename=...`) are the soft edge, and the Rails version only surfaces in verbose errors.
- **Self-hosted platforms written in Rails** — the largest one is its own unit ([[gitlab]]); anything from CMSes (Camaleon) to helpdesks carries the same mass-assignment and session machinery plus the platform's own admin edge.
- **Ruby-web neighbors** — Sinatra-class apps on WEBrick and Passenger-hosted scripts share ERB rendering, Psych YAML loading, and bundler configuration with Rails deployments; the gadget chains and loot paths below apply to the whole neighborhood, not just full Rails.
- **DevOps boxes where Ruby is the local scripting language** — root-run Ruby scripts and system tools (`gem`, `facter`) turn every deserialization primitive into root.

## Reading the framework's mind

- **Cache stores with `raw: true` trust the bytes.** A controller calling `Rails.cache.fetch(key, raw: true)` around a user value is the textbook CVE-2020-8165 setup — the store will Marshal-load the entry on read.
- **ERB executes `<%= %>`.** Any input that survives into a template render is code; naive filters fall to URL-encoding (`%3C%25%3D` for `<%=`) and newline-prefixed payloads.
- **Strong parameters are opt-out, not opt-in, in legacy code.** `params.permit!` whitelists everything, and a model with a `role` column plus a password-change form is a self-service privilege escalation.
- **Errors are verbose by default.** Unhandled exceptions print the Rails and Ruby versions — a version oracle before you send a single payload.
- **The database schema names itself.** `schema_migrations` and `ar_internal_metadata` tables in a leaked SQLite/PostgreSQL dump are ActiveRecord fingerprints, and the `password_digest` column is bcrypt (`$2a$12$...`) by convention — crackable, and reused for SSH.

Example of what Rails generates for you (and where the guarantee stops):

```ruby
# app/controllers/users_controller.rb — Rails keeps this session cookie signed+encrypted...
session[:user_id] = user.id
# ...but the cache is only as safe as the store:
@name = Rails.cache.fetch("username_#{session[:user_id]}", raw: true) do
  params[:user][:username]   # <- user bytes, stored raw, unmarshalled on read
end
```

## Where to go next

The full hunt method — fingerprinting, the deserialization gadget, ERB injection with filter bypasses, file-read patterns, mass assignment, and the deployment loot paths — is in [[ruby-on-rails-bug-bounty]].
