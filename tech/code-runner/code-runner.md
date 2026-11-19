---
technology: "Online code runners / sandbox escapes"
kind: overview
audience: offensive-security
family: web-platforms
status: "thin-class (class-framed unit, owner-approved: apps and libraries whose product is executing untrusted code)"
layer: application feature class — browser code editors, playground APIs and expression-evaluation services backed by sandbox libraries (vm2, js2py, restricted-Python eval)
provenance: "corpus-grounded (re-gated 2026-11-12, command-context gate, see internal coverage ledger): sandbox-library and jail-payload command-context evidence across vm2, js2py and stripped-builtin eval services + upstream-verified: NVD/Snyk CVE-2023-30547 (vm2 <=3.9.16, patched 3.9.17), NVD/CVE-2024-28397 + Marven11 PoC repo (js2py <=0.74, disable_pyimport bypass, no upstream fix), huntr/Metasploit CVE-2023-0297 (pyLoad js2py pre-auth, flash/addcrypted2, patched 0.5.0b3.dev31), vm2 repository deprecation notice (2023-07, escape history incl. CVE-2023-37466/CVE-2023-37903 as upstream context), CPython eval/object-model semantics, 2026-11-12."
---

# Online code runners / sandbox escapes

> The product is remote code execution; the only thing between you and the host is a library whose entire version history is a list of escapes.

## What it is

This is a class unit (owner-approved framing) for every service whose core feature is executing code a visitor typed: browser "code editor" playgrounds, "test your snippet" utilities, chatbot-adjacent eval endpoints, expression-calculator APIs, and download-manager plugins that evaluate scripts from the outside world. What they share is a *promise* — the submitted code runs inside a sandbox — and that promise is the attack surface. The sandbox is one of three things: a JavaScript sandbox library in the Node.js process (vm2 being the canonical case), a JavaScript-to-Python translator in the Python process (js2py), or a plain `eval()` with Python's builtins stripped from the namespace. Each of the three has a documented, reusable escape.

The operator's mental model: RCE-as-a-service with a filter in front of it. The web process that renders the page is usually the same process that "safely" runs your code, so a sandbox escape is not a niche bug — it is code execution in the application's own context, with the app's environment variables, database handles and file system one payload away. The escape primitives are public and stable: vm2 escapes through unsanitized host exceptions raised inside its own `handleException()` path; js2py escapes because `disable_pyimport()` does not actually sever the JavaScript-to-Python object graph; a stripped-builtin `eval` falls to the Python object model itself, where any literal is a doorway into the class hierarchy and from there into `os` or `subprocess`.

The design's characteristic mistake is filtering words instead of capabilities. Keyword blocklists (`import`, `os`, `system`) are defeated by string concatenation (`'o'+'s'`), by attribute traversal that never spells the banned name, or by reaching the same functions through a module object found in someone else's `__globals__`. A runner that survives its first attacker is rare; a runner whose vendor believes the sandbox is a security boundary is the norm.

## The anatomy that matters on an engagement

    browser editor / API → POST to a run endpoint (e.g. /run_code)
      → server-side evaluation:
           vm2 VM.run(code)          ← JS sandbox in the Node process
           js2py.eval_js(code)       ← JS translated into the Python process
           eval(code, {'__builtins__': None})   ← "safe" Python calculator
      → keyword/AST filter (the only control that is actually theirs)
      → escape payload reaches host objects → process/module walk
      → command execution as the application user → app-context RCE

Three structural properties drive most findings:

- **The sandbox library is the boundary — and it is a weak one.** vm2 carried a years-long chain of escapes and was deprecated outright by its maintainer with the advice to stop using it; js2py's escape has no upstream fix at all. Version-identifying the library is usually the whole solution.
- **Filters ban names, not reachability.** Python's everything-is-an-object design means `(()).__class__.__base__.__subclasses__()` enumerates loaded modules' classes from a bare literal; `__init__.__globals__` on any class hands you that module's namespace, builtins or not.
- **The escape is blind-friendly.** Runners often swallow command output (the editor shows nothing, or the transport returns a JSON error); out-of-band callbacks are the standard confirmation, and their success is indistinguishable from a finding.

## Where you'll meet it

- **Teaching platforms and "try it" widgets** — editor UIs on Express (3000/tcp) or Gunicorn (5000/8000/tcp) with an About/limitations page that names the sandbox library and its restrictions; that page is free version disclosure.
- **Downloaders and scrapers embedding a JS interpreter** — a package like pyLoad used js2py to evaluate remote captcha scripts, turning a library escape into an unauthenticated, pre-auth RCE on the product (upstream: CVE-2023-0297).
- **Expression/evaluation APIs** — grading, scoring or calculator endpoints that `eval` input with builtins stripped and consider the namespace reduction sufficient.
- **Bug bounty nuance:** where "run code" is the product, execution alone is not a finding — the report is the *escape* (host reach from inside the sandbox, filter bypass onto host objects), so evidence must show the boundary crossing, not just command output.

## Reading the framework's mind

1. **An About/limitations page or a downloadable app bundle is a version oracle** — one engagement shipped the app as a zip whose `requirements.txt` pinned `js2py==0.74`, converting a guess into a CVE lookup before touching the editor.
2. **vm2 fingerprints:** the editor accepts Node-flavored JS and blocks `require`/`process`; the exception path (`new Error().stack` inside a Proxy trap) is the CVE-2023-30547 entry — patched only in 3.9.17.
3. **js2py fingerprints:** JS runs but with Python-shaped artifacts (`getOwnPropertyNames`, dunder attributes reachable from plain objects) — the CVE-2024-28397 walk goes object → `__getattribute__` → `__class__`/`__base__` → `__subclasses__()` → `subprocess.Popen`.
4. **Stripped-builtin Python fingerprints:** `os`, `import` and friends raise `NameError` while literals still work — the jail is `eval`-based, and `__subclasses__()` plus `__getitem__` indexing walks to the `os` module without a single import.
5. **Silent output means the confirmation is a callback** — a DNS/HTTP beacon or a reverse shell from the payload is the finding's proof, exactly as observed in every corpus chain here.

## Where to go next

[[code-runner-bug-bounty]] — the phased agent methodology: fingerprinting run endpoints, identifying which sandbox you are in, the three payload families (vm2 exception-smuggling, js2py object-graph walk, Python class-base walking and builtins reconstruction), filter-evasion rules, and post-escape lateral movement.
