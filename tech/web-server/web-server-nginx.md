---
technology: nginx
kind: overview
audience: offensive-security
family: web
layer: HTTP server / reverse proxy (TCP/80, 443; config in /etc/nginx)
language: C
first-released: 2004
license: "BSD-2-Clause"
homepage: "https://nginx.org"
github-repo: "nginx/nginx"
provenance: "evidence-grounded (19 writeups gated at command-context: alias/off-by-slash traversal exploitation, autoindex, default-vhost exposure, .htpasswd-in-conf.d reads, command-context reads of /etc/nginx configs used for progress — see ../internal coverage ledger) + upstream-verified: nginx documentation (alias/root location semantics, merge_slashes, autoindex), the gixy alias-traversal check, Detectify/Orange Tsai parser-confusion research. Off-by-slash is a MISCONFIGURATION class with no CVE — stated to prevent miscitation. Pass 2 wave C, 2026-11-12."
---

# nginx

> The daemon that fronts half the internet, configured once and never audited — where a single missing trailing slash in a `location`/`alias` pair turns static file serving into arbitrary file disclosure, and where every file read on the box should end in `/etc/nginx/sites-enabled/`.

## What it is

nginx (engine-x, Igor Sysoev, 2004; BSD-2-Clause) is an event-driven HTTP server and reverse proxy. For an operator it does two jobs: a **static file server** whose URL-to-filesystem mapping is defined by `root`/`alias` directives inside `location` blocks, and a **router/proxy** whose `proxy_pass` rules decide which upstream sees which request — with its own path-rewriting semantics. Nearly every nginx finding falls in one of those two jobs: the mapping builds a filesystem path the administrator did not intend, or the proxy forwards a path the upstream was never supposed to receive.

The mental model for the mapping: a request URI is matched against `location` blocks (prefix, `=` exact, `~` regex), then the matched block's `root` (prepends the URI to the path) or `alias` (REPLACES the matched location part with the alias path) produces the filesystem path. All of the classic bugs are boundary errors in that substitution.

## The anatomy that matters on an engagement

    request URI
      → location match (prefix | = | ~ regex; first/best prefix wins)
      → mapping:  root /var/www  + URI        → /var/www + URI
                  alias /data/x/ + URI-after-location
      → static:  file | autoindex listing | .htpasswd-style sidecar leaks
      → proxy:   proxy_pass http://upstream[/path]  → URI rewrite semantics
      → auth:    auth_basic + auth_basic_user_file (.htpasswd format)

Three structural properties drive most findings:

- **The `location`/`alias` text substitution is literal.** nginx does not reason about path boundaries — it concatenates strings. A slash missing on one side of the pair means the attacker's URI is spliced into the filesystem path one character earlier than intended.
- **Normalization is selective.** `merge_slashes` (default **on**) collapses adjacent slashes before matching; `%2f` is not decoded by default. Every difference between what nginx normalizes and what the upstream normalizes is a potential access-control bypass.
- **The config directory is a credential store.** vhost files enumerate the internal service map; `.htpasswd` files (for `auth_basic`) are left world-readable inside `/etc/nginx` with distressing regularity. nginx also **ignores `.htaccess`** — see the router page for why that voids vendor protections.

## Off-by-slash: the alias traversal

The signature nginx misconfiguration (popularized by Orange Tsai's parser-logic research; no CVE exists — do not cite one):

```
location /static {          # <-- location WITHOUT trailing slash
    alias /opt/app/static/; # <-- alias WITH trailing slash
}
```

Everything after the literal prefix `/static` is appended to the alias — including when that "everything" starts with `..`. The probe and the kill:

```
GET /static../etc/passwd        # filesystem path becomes /opt/app/static/../etc/passwd
```

- **Non-destructive detection first**: `GET /staticfoo` and `GET /static/foo` returning the SAME resource proves the prefix boundary is not enforced (both splice onto the alias). A 301 redirect from `/static..` to `/static../` (nginx's directory-normalization courtesy) confirms the escaped directory exists.
- **Impact is whatever sits next to the alias**: application source, sibling vhost roots, the application's own config one directory up. A real deployment aliased a static path into an application tree and the one-directory-up walk exposed the application's source and secrets; another aliased a convenience path to a dump artifact and `..` reached the artifact itself.
- **The same slash bug exists in `proxy_pass`**: `location /api { proxy_pass http://apiserver/v1/; }` forwards `/api../` as `/v1/../` → the upstream root — revealing endpoints the front tier was supposed to hide (status pages, admin handlers).
- **Fixes** (grep a looted config for them): location and alias must BOTH end in `/`, or both not; a single-file mapping belongs in `location = /name` with an exact alias. The gixy scanner ships an `alias_traversal` check for exactly this shape.

Even without a traversal, a slashless `location` + slashless `alias` still splices: `/img_secret` can reach `/var/images_secret` when `/img` aliases `/var/images`. Sibling-name disclosure counts as a finding.

## merge_slashes and the normalization seam

`merge_slashes` is **on by default**: `//admin` and `/admin` match identically. Administrators who turn it OFF (usually to support legitimate double-slash paths) create a front/back mismatch: a rule written `location /admin` with `merge_slashes off` will not match `//admin`, but the upstream may normalize it back — ACL bypass by double slash. The same seam exists with `%2f`: nginx does not decode it by default, so `/%2fadmin` and `/admin` are different URIs to the front and (potentially) the same path to the back. When an nginx front guards anything, test `//` and `..%2f` variants of the denied path against BOTH tiers' idea of the path.

## Misconfigured root/alias and static-serving leaks

- **`autoindex on`** in a location serving files without an index produces full directory listings of backup dirs, artifact dirs, and upload dirs — grep looted configs for `autoindex` immediately.
- **root-inheritance mistakes**: a `location` that forgets its `root` inherits the server-level one, so `/uploads/` serves out of the site root; the inverse (a global `root` pointing at `/`-adjacent trees) turns the site into a file browser with the right URI.
- **`alias` to non-web directories**: configs written for convenience (`location /backup { alias /var/backups/site/; }`) hand the attacker the backup tier — database dumps, tarballs, `.git` directories — with no auth line in sight.
- **`internal` locations and `X-Accel-Redirect`**: a location marked `internal` is meant to be reachable only via upstream redirect headers; a second `location` (or a proxy that passes the URI directly) that also matches the file defeats the intent. Test protected download paths for direct-fetch.
- **Basic-auth sidecars**: `auth_basic_user_file` pointing at `.htpasswd` inside `/etc/nginx/conf.d/` — world-readable, `$apr1$` hashes, crackable offline (john/hashcat). A readable `.htpasswd` next to an `auth_basic` vhost is a finding pair.

## What to pull with any file read (config targets)

With an LFI/XXE/SQLi file read on an nginx box, in priority order:

```
/etc/nginx/nginx.conf                 # globals: include pattern, user, error paths
/etc/nginx/sites-enabled/*            # every vhost: server_name, root, alias, proxy_pass,
                                       #   auth_basic + user-file paths
/etc/nginx/conf.d/*.conf              # non-Debian layout equivalents
/etc/nginx/conf.d/.htpasswd           # Basic-auth hashes
+ the app roots / alias targets the configs reveal
```

Real chains of this shape: a blind SQLi dumping the default vhost file to discover an internal-only site; an LFI reading `sites-available` to find the disabled-but-still-referenced admin vhost; a file read pulling the reverse-proxy config that exposed the interior application-server port. The config is the map of the next two moves.

## Where you'll meet it

- **The default front door** of modern deployments — static sites, SPA hosts, and above all the reverse-proxy tier in front of application servers. When the app is a known product, nginx's OWN config mistakes are usually the untested surface.
- **Debian/Ubuntu hosts** where per-site files under `sites-enabled/` accumulate for years, each written by a different tutorial; the oldest file in that directory is your best target.
- **Internal-only default vhosts**: the distribution default (`sites-enabled/default`, listening on `*:80` or loopback) left enabled beside the real site — real chains have reached an internal application through it because it was never audited, only installed.
- **Kubernetes/ingress-adjacent boxes** where nginx config is generated — the generator's templates repeat the same alias bug across every tenant.

## Reading nginx's mind

1. **A slashless `location` with a slashed `alias` (or `proxy_pass`) is the finding** — test the sibling-name equivalence before the traversal.
2. **Normalization differences between front and upstream are bypasses** — probe `//`, `..%2f`, and `/.` variants of denied paths.
3. **nginx never reads `.htaccess`** — protections shipped as `.htaccess` do not exist behind it; test the "protected" directory directly.
4. **The banner rarely carries a version** (`server_tokens` off or a rewrite) — nginx bugs worth chasing are config-shaped, not version-shaped; loot the config instead.
5. **Every config read pays twice**: vhosts enumerate hidden hostnames, `proxy_pass` lines enumerate the interior.

## Where to go next

- [[web-server|Web servers router]] — dispatch table (which daemon is this?), the config-disclosure-through-any-file-read pattern, Caddy and the small daemons.
