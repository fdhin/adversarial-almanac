---
technology: "Flask"
kind: overview
audience: offensive-security
family: web-platforms
layer: web-framework (WSGI)
language: python
first-released: 2010
license: BSD-3-Clause
homepage: "https://flask.palletsprojects.com"
github-repo: "pallets/flask"
provenance: "corpus-grounded (47 writeups gated by command-context markers — see internal coverage ledger; strongest families cover traceback/source chains, debug+SSTI, session-cookie forgery, traversal-to-app.py, Gunicorn smuggling, and uWSGI socket RCE) + upstream-verified (Werkzeug debug/__init__.py pin source and changes page, Flask quickstart/deployment docs, Jinja2 sandbox docs, NVD CVE-2024-34069). Enrichment pass 1, 2026-11-12."
---

# Flask

> The framework that introduces itself on every response — `Server: Werkzeug/2.2.2 Python/3.10.12` — and whose entire session-security model collapses to one line of `app.py`: the `SECRET_KEY`.

## What it is

Flask is Armin Ronacher's micro-framework (2010, BSD-3-Clause, now Pallets), assembled from two libraries that matter more to an attacker than Flask itself: **Werkzeug** (WSGI toolkit + development server + debugger) and **Jinja2** (template engine). "Micro" means Flask ships routing, request/response objects, sessions, and a dev server — and deliberately nothing else; every real deployment staples on extensions and a production WSGI server (gunicorn, uWSGI, Apache mod_wsgi).

The operator takeaway is that a Flask target leaks its stack by default. The development server puts `Werkzeug/<version> Python/<version>` in the `Server` header of every response — version disclosure for the framework AND the interpreter in one line (nine observed targets). Seeing it means the *dev server* answered — or nothing fronted it — and the two following surfaces come into play: the client-side **session cookie** (signed, not encrypted; forge it and you own the auth model) and, when debug mode was left on, the Werkzeug **debugger console** (a PIN-gated Python shell on the box).

## The anatomy that matters on an engagement

    client → [ nginx / HAProxy → gunicorn | uWSGI | mod_wsgi ] → Flask app
           → routing → handler → Jinja2 render → response
    dev-server deployments (no front end):
      error → interactive traceback → /console?__debugger__=yes (PIN-gated RCE)
    session: flask session cookie ← HMAC(SECRET_KEY) — lives client-side

Three structural properties drive most findings:

- **The session is a signed cookie, not a server-side store.** Integrity-only: anyone can *read* it, and anyone with the `SECRET_KEY` can *write* it. Crackable secrets (field-observed: `secret123`) or leaked keys (one observed chain via healthcheck endpoint, another via SSTI `{{ config }}`) convert directly into auth forgery — including SQL injection delivered *through* the re-signed cookie (field-observed).
- **Templates are Python with a thin template skin.** Jinja2 expressions reach the full object graph (`__class__`, `__mro__`, `__subclasses__`), so any user input that reaches `render_template_string` (or a template file the app renders) is remote code execution, not markup injection. `{{7*'7'}}` → `7777777` is the Jinja2 signature.
- **The dev server is one flag away from a debug shell.** `flask run --debug` / `app.run(debug=True)` enables tracebacks with source snippets AND the PIN-protected console. The PIN is derivable offline from host facts (username, `flask.app`, `app.py` path, MAC, machine-id) once you can read them.

## Where you'll meet it

- **Internal tools and one-off admin panels** on :5000/:8000 (four observed engagements): quickest path to production-exposed debuggers and weak `SECRET_KEY`s.
- **File-upload/image-processing and ML demo endpoints** (two observed engagements): user input flowing into templates or filenames — the SSTI and traversal sweet spot.
- **Larger apps fronted by nginx/HAProxy with gunicorn** (three observed engagements): no Werkzeug banner (you fingerprint from the 404 shape and cookie format instead), but front-desync bugs move in — Gunicorn <20.0.1 mishandled chunked transfer-encoding (field-observed request smuggling).
- **Source-code-adjacent chains** (three observed engagements): path traversal / `.git` / SMB leaks that land on `app.py` — the file that holds `SECRET_KEY`, hidden routes, and the developer's mistakes in one read.

## Reading the framework's mind

Five facts predict a Flask target's wire behavior — each expands into a method in [[flask-bug-bounty|the methodology]]:

1. **`Server: Werkzeug/x.y.z Python/a.b.c` is a complete stack fingerprint** (nmap `http-server-header` picks it up unauthenticated); its absence in front of a Python app means a real WSGI server (gunicorn/uwsgi headers) — different bug classes (smuggling, socket exposure).
2. **The `session` cookie is three dot-separated base64 parts** and decodes without any key — reading it tells you the app's auth fields; cracking/flipping it is Phase 1 of every Flask engagement.
3. **`{{7*'7'}}` → `7777777`** says Jinja2 (Twig answers `49`, Java/FreeMarker differs) — commit to the Python payload tree.
4. **A full HTML traceback page means debug mode**; the console behind it asks for a 9-digit PIN that is a hash of public-ish host facts, not a secret.
5. **`app.py` / `config.py` are load-bearing filenames** — the first files to pull through any read primitive (traversal, `.git`, backups), because they hold `SECRET_KEY`.

A 10-line illustration of where the framework's guarantee stops:

```python
app = Flask(__name__)
app.secret_key = "secret123"          # signed-cookie integrity == this string

@app.route("/greet")
def greet():
    name = request.args.get("name", "")
    # SAFE: name is DATA passed to a fixed template
    return render_template("greet.html", name=name)
    # RCE: name is CODE evaluated by Jinja2
    # return render_template_string("Hello " + name)
```

## Where to go next

[[flask-bug-bounty|Flask bug bounty methodology]] — fingerprinting, session forgery with flask-unsign, the SSTI payload tree and WAF bypasses, the Werkzeug console PIN recipe, source-disclosure chains, and deployment-edge bugs (gunicorn smuggling, uWSGI sockets, ProxyFix).
