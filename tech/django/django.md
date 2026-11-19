---
technology: Django
kind: overview
audience: offensive-security
family: web-platforms
layer: Python web framework (settings.py contract, SECRET_KEY signing, ORM tables, admin site)
language: Python
first-released: 2005
license: BSD
homepage: "https://www.djangoproject.com"
github-repo: "django/django"
evidence: "9 writeups (command-context gate: manage.py, settings.py reads, SECRET_KEY with Django context, django.contrib.*/django.core.signing lines, /admin/login/?next=, wsgi.py + WSGIScriptAlias, django_session/auth_user/django_admin_log tables, hashcat Django modes 124/10000; the placeholder's 6 was under-counted — gate adds 6 files and drops one false positive)"
status: enriched
provenance: "corpus-mined from 9 writeups in the writeup corpus (see ../internal coverage ledger) + upstream-verified: docs.djangoproject.com settings reference (DEBUG masking keyword list, SECRET_KEY guidance), Django security release 2019-12-18 (CVE-2019-19844 ranges), Django deprecation timeline + 5.0 release notes (PickleSerializer), django/views/debug.py source (cleanse_setting), accessed 2026-11-12"
---

# Django

> The whole threat model is one file: `settings.py` names every secret, every backend, and the `SECRET_KEY` that signs sessions and password-reset tokens. Wherever Django source leaks, everything after it is deterministic — and on projects pinned to old session serializers, a leaked key is straight RCE.

## What it is

Django (2005, Adrian Holovaty and Simon Willison, BSD-licensed, Django Software Foundation) is the Python web framework behind a large share of corporate internal apps, dashboards, and APIs. An install is a package of conventions: `manage.py` at the project root, a `settings.py` holding configuration and secrets, ORM tables with fixed names (`auth_user`, `django_session`, `django_admin_log`), a session/CSRF cookie pair (`sessionid` + `csrftoken`), and an auto-generated admin site at `/admin/`.

The operator's mental model: **a settings contract plus a signing key**. Every deployment reads the same fixed names, so nothing about a target is a surprise once you can read its source or database. The framework's central guarantee — you cannot forge what the server signed — stops entirely at `SECRET_KEY` secrecy. Leak that key plus an old serializer setting and the guarantee inverts: you forge what the server will trust (observed: two independent chains did exactly this).

The takeaway: Django itself is mature and boring; the targets are mis-deployed instances — `DEBUG=True` shipped to prod, source trees readable through an adjacent service, databases with crackable `auth_user` hashes, admin sites extended with developer conveniences. The observed pattern is almost never a Django CVE; it is the same secret-management failure rendered five different ways.

## The anatomy that matters on an engagement

    recon (csrftoken/sessionid cookies, /admin/login/?next=/admin/, technical 404/400 pages, wsgi.py in webserver config)
      → source or config read (DEBUG page, LFI, leaked backup, adjacent service like Grafana/DB)
      → loot fork A: SECRET_KEY + SESSION_ENGINE=signed_cookies + PickleSerializer  → forge session cookie → RCE
      → loot fork B: database → auth_user hashes → hashcat 10000 (PBKDF2-SHA256) / 124 (legacy SHA-1) → is_staff
      → loot fork C: settings.py → DB creds, third-party app config, cache paths
      → admin site (stock ModelAdmin, or custom dev-tool extensions) → object-level access
      → app layer (OAuth toolkit flows, custom views)

Three structural properties drive most findings:

1. **One key signs everything the client sees.** `SECRET_KEY` backs signed-cookie sessions, password-reset tokens, and CSRF masking. Reading it is not "info disclosure" — it is the prerequisite for forgery attacks on every signed surface at once.
2. **Names never change.** `/admin/login/`, `sessionid`, `auth_user.is_staff`, `django_session` — identical on every Django site since the 1.x era. You can write the SQL and the forgery code before you ever see the target.
3. **Source disclosure is the whole game.** Observed Django starts all began with reading `settings.py`, project source, or the Django database — via a Grafana datasource config, an exposed backup, LFI, or a readable on-disk project. The framework has no exotic wire protocol: files and DB rows are the attack surface.

## Where you'll meet it

- **Internal corporate tools and dashboards** — the least-maintained software class on the target; frozen dependency sets mean decade-old serializer defaults survive.
- **Appliance and platform internals** — Django apps ship inside larger products (observed: a Django codebase sitting readable on a Grafana box; a malware-scanner appliance whose web UI is Django). You meet Django as loot inside another unit's compromise.
- **OAuth authorization servers** — django-oauth-toolkit deployments exposing `/o/token/` (observed). The attack is the OAuth flow, but the loot and the admin are Django's.
- **Source trees on shared hosts** — project directories with `manage.py` and `settings.py` readable by the service user you just became (observed: two separate chains).

## Reading the framework's mind

- `DEBUG=True` does **not** hand you `SECRET_KEY` on the technical 500 page: modern Django masks any setting whose name contains `API`, `KEY`, `PASS`, `SECRET`, `SIGNATURE`, or `TOKEN` (partial matches) as asterisks. What the page still leaks: full traceback with local variables, `INSTALLED_APPS`, middleware, filesystem paths, `INTERNAL_IPS`, the SQL query log — app inventory and file layout without the key.
- The inverse fingerprint: `DEBUG=False` with an empty `ALLOWED_HOSTS` makes Django answer **every** request with the plain-text page `Bad Request (400)` — a distinctive tell that separates a broken Django from a dead vhost.
- `django.contrib.sessions.serializers.PickleSerializer` was deprecated in 4.1 and removed in 5.0. On older pinned projects, `SESSION_ENGINE = 'django.contrib.sessions.backends.signed_cookies'` + `PickleSerializer` + a leaked `SECRET_KEY` converts `django.core.signing.dumps(payload, salt='django.contrib.sessions.backends.signed_cookies')` into arbitrary pickle execution (observed: executed twice, same salt string both times).
- Password hashes in `auth_user` are self-describing strings — `pbkdf2_sha256$<iterations>$<salt>$<hash>` — with hashcat modes 10000 (PBKDF2-SHA256) and 124 (legacy SHA-1). The `is_staff`/`is_superuser` flags in the same row tell you which crack actually matters.
- CVE-2019-19844 (upstream history, unobserved): before 1.11.27 / 2.2.9 / 3.0.1, a Unicode-case-trick email in the password-reset form got the reset token sent to the attacker's submitted address — account takeover on vulnerable versions. Version-gate before claiming it.

## Where to go next

[[django-bug-bounty|Django bug-bounty methodology]] — phased: fingerprint → source/config read paths → SECRET_KEY forgery chains (session, reset tokens) → database loot and hash cracking → admin surface → app layer.
