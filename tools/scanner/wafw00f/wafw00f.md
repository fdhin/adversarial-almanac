---
name: "wafw00f"
categories:
  - blackarch-scanner
version: "902.1fb92e6"
homepage: "https://github.com/EnableSecurity/wafw00f"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-01-21"
build-system: python
license: GPL
meta-checked: "2026-11-09"
github-repo: "EnableSecurity/wafw00f"
github-stars: 6592
github-pushed: "2026-04-19"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 40
gh-activity-checked: "2026-11-09"
---

# wafw00f

> The Web Application Firewall fingerprinting tool by EnableSecurity — the de facto standard answer to "what WAF is in front of this site?".

## What it's for

wafw00f identifies which Web Application Firewall or CDN protection product sits in front of a website. It works in three stages: a normal request whose response already identifies many WAFs, then deliberately malicious requests whose block pages and headers identify more, and finally response heuristics to guess an otherwise-unknown security layer. It knows roughly 200 products (Cloudflare, ModSecurity, F5 BIG-IP, Imperva, AWS ELB/Azure Front Door, Sucuri, … — `wafw00f -l` prints the full table with manufacturers). Knowing the WAF is the prerequisite for tuning bypasses, and recon reports usually cite its output verbatim. It only fingerprints the WAF — it does not attempt to bypass it, and it says nothing about the application behind it.

## Typical use cases

### Fingerprint the WAF protecting a site

```
wafw00f https://example.com
```

Outcome: prints a checked-host line and, on success, `The site https://example.com is behind <WAF> (<vendor>) WAF.` plus the number of requests sent; a miss prints that no WAF was identified.

### Aggressive mode — enumerate every matching WAF

```
wafw00f -a https://example.com
```

Outcome: `-a`/`--findall` does not stop at the first signature hit, reporting every WAF product that matches — the right mode when a CDN fronts a separate WAF and you want both named.

### Verbose run to watch the probes

```
wafw00f -vvv https://example.com
```

Outcome: each `-v` raises verbosity — request/response details and signature evaluation scroll past, showing exactly which probe triggered the identification.

### Test the redirect target's host instead of following it

```
wafw00f -r https://example.com
```

Outcome: `-r`/`--noredirect` disables following of 3xx responses, so the fingerprint applies to the host you named rather than wherever it forwards you — often the difference between seeing the WAF and seeing the origin.

### Enumerate the detectable WAFs

```
wafw00f -l
```

Outcome: prints the WAF-name/manufacturer table (one line per supported product) and exits without touching any target; use it to pick a `-t` value.

### Test for one specific WAF

```
wafw00f -t "AireeCDN (Airee)" https://example.com
```

Outcome: runs only the checks for the named product (quote names containing spaces, exact strings as listed by `-l`) and reports yes/no for that WAF only.

### Batch-scan many targets through a proxy and save JSON

```
wafw00f -i targets.txt -o results.json -p http://127.0.0.1:8080
```

Outcome: reads one URL per line from `targets.txt`, routes every probe through the given HTTP/SOCKS proxy (e.g. Burp on 8080), and writes a JSON results file (`-o -` streams to stdout); `-f csv|json|text` forces the output format regardless of the file extension.

## Bug bounty workflow with wafw00f

wafw00f answers one question — *what security product sits in front of this host?* — and that answer is an **input to every active tool you run afterward**, not a finding in itself. Treat it as decision-support: the WAF identity decides your sqlmap tamper chain, your nuclei rate caps, whether a `403` you see later is the app or the edge, and whether your traffic is about to get your source IP blocked for the rest of the engagement. The one thing to internalise before the first command: **wafw00f is not passive.** Its default run sends a benign baseline request *and* a combined attack request (XSS + SQLi + LFI strings stuffed into random parameter names); `-a` and the generic-detection fallback add more. Every one of those probes hits the target and is logged by the WAF you are trying to name. Scope and volume discipline therefore apply from request one.

### Phase 0 — Truly-passive groundwork first

Because wafw00f will fire attack strings, do the zero-traffic-to-origin recon before you run it, so you already know whether you are looking at a CDN edge, a reverse proxy, or the origin:

```bash
# CNAME / edge hints without attacking anything
dig +short app.example.com CNAME
dig +short app.example.com A
# Edge/vendor tells in ordinary response headers (one benign request)
curl -sI https://app.example.com/ | grep -iE 'server|via|x-cdn|cf-ray|x-sucuri|x-akamai|x-amz|x-cache'
```

A `cf-ray` header, a `*.edgekey.net` CNAME, or `Server: AkamaiGHost` often tells you the edge vendor before wafw00f sends a single attack probe. Knowing this lets you predict what wafw00f *should* say and spot the interesting case: the header says CDN-X but wafw00f fingerprints a *second*, separate WAF behind it.

### Phase 1 — Baseline fingerprint (lowest-volume run)

One default invocation, host pinned, conservative timeout:

```bash
wafw00f -r -T 10 https://app.example.com
```

`-r`/`--noredirect` stops wafw00f following 3xx responses, so the fingerprint applies to the host you named rather than wherever the edge forwards you — frequently the difference between fingerprinting the WAF and accidentally fingerprinting a login redirect or the apex. `-T`/`--timeout` (default 7 seconds) is raised slightly so a slow edge does not produce a false "no WAF." Expected shape on a hit:

```
[+] The site https://app.example.com is behind Cloudflare (Cloudflare Inc.) WAF.
[~] Number of requests: 2
```

The `Number of requests: 2` line is your volume receipt — one baseline, one attack probe. Record it; it is evidence that the scan was single-digit, not a flood.

### Phase 2 — Enumerate the full edge stack

A single signature hit stops the default scan. When a CDN fronts a distinct WAF (common: Cloudflare or Akamai edge in front of an on-box ModSecurity / F5 / Imperva), you want *both* names:

```bash
wafw00f -a -r https://app.example.com
```

`-a`/`--findall` keeps testing every signature instead of stopping at the first, and it also forces the generic-detection pass to run even when a named product already matched. Generic detection is wafw00f's third stage: it re-examines the responses it already has (status-code deltas between baseline and the XSS/SQLi/LFI probes, a `Server` header that changes under attack, a request that gets connection-blocked, a different status when `User-Agent` is stripped) and reports a *generic* "security product is actively responding" verdict when no named plugin fires. That verdict is weaker evidence than a named hit — note it as "generic/unidentified WAF behaviour," not as a product.

### Phase 3 — Translate the WAF identity into downstream config

This is the whole point of the tool. The named WAF reshapes how you run everything else:

- **sqlmap** — pick the tamper chain *before* you start instead of letting sqlmap flail. `sqlmap --list-tampers` prints the current set; WAF-specific picks (e.g. `modsecurityversioned`/`modsecurityzeroversioned` for ModSecurity, generic evasion like `space2comment`, `between`, `charencode`, `randomcase`) go in `--tamper=`. Add `--random-agent` and a `--delay`. sqlmap also runs its own WAF check by default (`--check-waf`, `--identify-waf`); wafw00f corroborates it up front so you are not guessing.
- **nuclei** — a WAF means your default throughput will get you blocked and will also produce false `403`/`429` "findings." Lower `-rl`/`-rate-limit` and `-c`/`-concurrency`, raise `-timeout`, and pass the program's required identifying header via `-H`. If the whole run comes back `403`, the WAF is eating your templates — not evidence the app is secure.
- **ffuf / feroxbuster** — content discovery behind a WAF needs `-rate` (ffuf) or `--rate-limit` (feroxbuster), fewer threads (`-t`), and a delay (`ffuf -p 0.1-1.0`). A WAF that blocks on volume turns a wordlist run into a self-inflicted IP ban.
- **Manual testing** — knowing it is, say, Imperva vs. AWS WAF tells you which payload encodings and which `Content-Type` tricks have a chance, and whether a `403` is the edge or the origin app.

### Phase 4 — The only path to a standalone finding: the bypass

wafw00f tells you a WAF *exists*; it never bypasses it and never finds the origin. A WAF fingerprint becomes report-worthy only when you chain it into a demonstrated weakness: an **origin IP reachable directly** (so the WAF is decorative — all your later payloads should target the origin, and the exposed origin is the actual finding), a WAF running in **detection-only/monitor mode** (attacks are logged, not blocked), or a **default/unconfigured ruleset**. Each of those requires a separate PoC against the origin or a bypassed request — wafw00f is the first step of that chain, documented as context, not the finding.

### Phase 5 — Scope-wide inventory (batch)

For a program with many in-scope hosts, inventory the edge posture in one pass and keep the machine-readable output for your recon notes:

```bash
wafw00f -i scope-hosts.txt -o waf-inventory.json -r -T 10
```

`-i`/`--input-file` reads one target per line (csv/json inputs need a `url` column/element); `-o` writes JSON/CSV/text by file extension. The inventory tells you which hosts are hardened (slow down, expect friction) and which are bare (likely the origin / a forgotten subdomain — often the softest targets). Keep batch runs to in-scope hosts only; see scope discipline below.

## How wafw00f decides, and how much to trust it

The result's confidence depends on *which* of the three stages produced it — and the tool does not shout the difference, so you have to read it from the run. Understanding the match machinery lets you grade your own evidence before you act on it.

### The three stages and the evidence each uses

1. **Normal-request signatures (strongest).** A single benign request whose response already carries a vendor fingerprint — a tell-tale header, a `Set-Cookie`, a status/reason string, or body content. Internally the per-vendor plugins call `matchHeader`, `matchCookie` (both read the *baseline* response by default), and `matchStatus`/`matchReason`/`matchContent` (which read the *attack* response by default). A hit here is a named product with high confidence and the fewest requests.
2. **Malicious-request signatures (strong).** The combined attack request (`centralAttack`: XSS + SQLi + LFI strings in randomly-named parameters) provokes a block page or a WAF-specific error whose content/status the `matchContent`/`matchStatus` helpers recognise. Still a named product, but it required an attack probe to surface — note that the WAF is in *blocking* mode, which is itself useful intelligence.
3. **Generic detection (weak — a guess, not a product).** Runs only with `-a` or when stages 1–2 named nothing. It infers "a security product is actively responding" from signal deltas, not signatures: a status change when `User-Agent` is stripped, baseline-vs-attack status differences on the XSS/SQLi/LFI probes, a `Server` header that changes under attack, or a connection-level block (`RequestBlocked`). Report this as "generic WAF behaviour (unidentified)" — never as a vendor.

### Anatomy of the attack probe (what you are actually sending)

The stage-2 request (`centralAttack`) is a single request that packs three payload classes into randomly-named parameters, so one round-trip exercises the WAF across common rule families:

| Probe class | What it trips | Why it matters for scope |
|---|---|---|
| XSS string | reflected-input / script-tag rules | A live `<script>`-class payload lands in the target's logs — it reads as an attack attempt |
| SQLi string | SQL-keyword / quote rules | A real injection pattern; a program that forbids automated injection testing is being violated |
| LFI string | path-traversal / file-include rules | A traversal sequence; same consideration — this is active attack traffic, not a health check |

This is why there is no "passive mode" and why running wafw00f against an out-of-scope host is unauthorized attacking, not harmless recon. It is also why a single run can be enough to trip an alert or a block — the probe is deliberately broad.

### Confidence tiers you should write into your notes

| Result | Stage | Confidence | How to act on it |
|---|---|---|---|
| `is behind <WAF> (<vendor>) WAF.` | 1 or 2 | High — named signature | Tune downstream tools to that vendor; cite verbatim |
| Multiple names joined by `and/or` (after `-a`) | 1/2 | High per name | Treat each layer separately (edge vs. backend) |
| Generic / "No WAF detected by the generic detection" after `-a` | 3 | Low | "Unidentified filtering" at most; do not name a vendor |
| No WAF reported at all | 1–3 miss | Inconclusive, *not* "no WAF" | Could be stale signatures, a redirect followed without `-r`, or a WAF that ignores these probes — corroborate manually |

A "no WAF" line is the most misread output in the tool: it means *these signatures and these probes did not match*, not that the host is unprotected. Re-run with `-r`, a current build (`wafw00f -V`), and your own header check from Phase 0 before you trust it.

### Vendor identity → downstream decision

Once a vendor is named, the identity changes your next move. The table below maps common `-l` products to what the result implies; wafw00f's own signatures (and the `-vvv` transcript) are authoritative over any remembered tell, which drift between releases:

| Named product | Usual role | What the identity implies for your next step |
|---|---|---|
| Cloudflare | Edge CDN + WAF | Edge, not origin — the highest-value follow-up is origin-IP discovery (historical DNS, TLS cert SANs); challenge/`403` pages are the edge, not the app |
| Akamai (Kona / Ghost) | Edge CDN + WAF | Same edge-vs-origin split; expect aggressive rate controls — cap nuclei/ffuf hard |
| Imperva (Incapsula) / Imperva SecureSphere | Edge WAF / on-box WAF | Strong signature-based blocking; favour generic encoding/obfuscation tampers in sqlmap and slow, low-concurrency fuzzing |
| F5 BIG-IP ASM | On-box / reverse-proxy WAF | Often in front of the real origin; a backend WAF behind a separate CDN — pull both with `-a` |
| AWS WAF / ELB | Cloud edge | Frequently a thin/default ruleset; worth probing whether rules are actually configured (monitor-mode candidate) |
| ModSecurity (OWASP CRS) | On-box WAF | CRS paranoia level drives blocking; `modsecurityversioned`/`modsecurityzeroversioned` tampers exist for exactly this target |
| Sucuri / Azure Front Door / Fastly / Barracuda | Edge CDN+WAF | Edge layer — confirm whether a distinct backend WAF exists (`-a`) and whether the origin is reachable directly |

## Chaining wafw00f into the recon pipeline

wafw00f sits *after* host discovery and *before* active scanning. A typical flow: `subfinder`/`amass` → `httpx` (live hosts) → **wafw00f** (edge posture) → tune `nuclei`/`sqlmap`/`ffuf`. The JSON output makes the hand-off scriptable:

```bash
# Extract just the hosts that came back WAF-less — your soft-target shortlist
jq -r '.[] | select(.detected == false) | .url' waf-inventory.json
# Group hosts by detected product for per-vendor tool tuning
jq -r '.[] | select(.detected == true) | "\(.firewall)\t\(.url)"' waf-inventory.json | sort
```

The JSON records carry (among others) the target `url`, a boolean `detected`, and the `firewall`/`manufacturer` names — confirm field names against your installed version with a one-off `wafw00f -f json -o - https://app.example.com`, since schema fields can shift between releases. The payoff of the split: WAF-less hosts get your normal-speed tooling and the earliest attention (they are often the origin or a forgotten asset), while WAF-fronted hosts get throttled, vendor-tuned runs so you neither waste requests on blocked payloads nor burn your source IP.

## Where the money is with wafw00f

wafw00f almost never pays a bounty by itself — it is a force-multiplier for the tools that do. Read this as "what the result is worth," with the rare directly-reportable rows called out:

| Signal from wafw00f | Reached with | What it's worth | Dupe / report risk |
|---|---|---|---|
| Named WAF identified | default run | Picks sqlmap tampers + rate caps for every later tool — indirect value on every subsequent finding | Recon only — never reported alone (always a dupe/N-A as a standalone) |
| CDN + separate backend WAF both named | `-a -r` | Two trust boundaries to probe; the backend WAF is often weaker than the edge | Recon only |
| Generic/unidentified WAF behaviour | `-a` (generic stage) | Weak "something is filtering" signal; prompts manual payload-shape testing | Recon only; do not assert a product |
| **No WAF on a host its siblings protect** | `-i` batch across scope | That host is likely the origin or a forgotten asset — the soft target; feeds origin-exposure hunt | The *origin exposure* is reportable (Medium–High), not the wafw00f line |
| WAF named but trivially **bypassed to origin** | wafw00f → origin-discovery → direct request | WAF is decorative; all payloads retarget the origin | **Origin IP exposure** reportable (Medium–High); needs a direct-to-origin PoC |
| WAF in **monitor/detection-only** mode | wafw00f names it → attack reaches app unblocked | Confirms the WAF is not actually mitigating | Reportable only with a working attack PoC through it (severity = the underlying bug) |

The honest framing for any report: *wafw00f established which protection is present; the finding is the bug that reaches the origin despite it.* A WAF name on its own is Informational at best and most programs will close it as not-applicable.

## Command recipes

Annotated, lowest-volume-first. There is **no fully passive mode** — even the default sends one attack probe — so "low volume" here means *few requests against in-scope hosts only*, not zero-attack.

```bash
# 1) Minimal fingerprint, host pinned, generous timeout (2 requests total)
wafw00f -r -T 10 https://app.example.com
#   -> [+] The site https://app.example.com is behind <WAF> (<vendor>) WAF.
#   -> [~] Number of requests: 2        # your volume receipt — keep it
```

```bash
# 2) Enumerate every matching product + force the generic pass (more requests)
wafw00f -a -r https://app.example.com
#   lists each matching WAF joined by "and/or"; generic stage adds probes
#   (User-Agent-stripped request, status/Server-header deltas)
```

```bash
# 3) Watch exactly which probe triggered the match (triage / evidence)
wafw00f -vvv -r https://app.example.com
#   each -v raises verbosity; request/response + signature evaluation scroll past
#   capture this when a reviewer may question the identification
```

```bash
# 4) List what wafw00f can detect, then test for exactly one product
wafw00f -l                                   # 170+ products (172 in the 2.4.2 sample),
                                             # name + manufacturer, then exits
wafw00f -t "Cloudflare (Cloudflare Inc.)" -r https://app.example.com
#   -t runs only that plugin and returns; quote names with spaces exactly as -l prints them
```

```bash
# 5) Route every probe through Burp (inspect/replay) and respect program identity
#    headers.txt must include a User-Agent AND the program's required marker header,
#    because -H OVERWRITES wafw00f's entire default header set.
printf 'User-Agent: bugbounty-<handle>\nX-Bug-Bounty: <handle>\n' > headers.txt
wafw00f -r -H headers.txt -p http://127.0.0.1:8080 https://app.example.com
#   -p accepts http://host:port, socks5://host:port, or http://user:pass@host:port
```

```bash
# 6) Scope-wide inventory to machine-readable output
wafw00f -i scope-hosts.txt -o waf-inventory.json -r -T 10
#   -i: one URL per line (csv/json need a `url` column/element)
#   -o: extension decides format; -f csv|json|text forces it; -o - streams to stdout
wafw00f -i scope-hosts.txt -f csv -o - -r | tee waf-inventory.csv
```

Low-volume settings that matter: run against **one host at a time** during active testing; prefer the default (non-`-a`) run unless you specifically need the full stack, since `-a` + generic detection multiplies requests; set `-T` high enough that a slow edge is not misread as "no WAF"; and never loop wafw00f in a tight shell `for` over a large host list without a delay — use `-i` with a reviewed, in-scope file instead.

### Downstream commands, tuned by the WAF result

These are the commands the fingerprint actually changes — the reason you ran wafw00f at all. Each is the *throttled, vendor-aware* variant you switch to once a WAF is confirmed; flags are verifiable in each tool's own `--help`.

```bash
# sqlmap — behind a confirmed WAF: pick tampers, random UA, add delay, keep it quiet.
# `--list-tampers` prints the current set; the picks below are generic evasion plus the
# ModSecurity-specific pair. Let sqlmap confirm with its own --identify-waf.
sqlmap -u 'https://app.example.com/item?id=1' \
  --tamper=space2comment,between,charencode,randomcase \
  --random-agent --delay 1 --time-sec 10 --level 2 --risk 1 --batch
# For a ModSecurity/CRS result, add the versioned-comment tampers instead:
sqlmap -u 'https://app.example.com/item?id=1' \
  --tamper=modsecurityversioned,modsecurityzeroversioned,space2comment --random-agent --delay 1
```

```bash
# nuclei — a WAF means default throughput gets you blocked and fabricates 403/429 "findings".
# Lower rate + concurrency, raise timeout, carry the program's identifying header.
nuclei -u https://app.example.com \
  -rl 20 -c 10 -timeout 15 \
  -H 'User-Agent: bugbounty-<handle>' -H 'X-Bug-Bounty: <handle>'
#   -rl/-rate-limit requests-per-second, -c/-concurrency parallel templates.
#   If every template returns 403, the WAF is eating them — not a secure app.
```

```bash
# ffuf / feroxbuster — content discovery behind a WAF needs rate caps + a delay + fewer threads.
ffuf -w wordlist.txt -u https://app.example.com/FUZZ \
  -rate 20 -t 10 -p 0.1-1.0 \
  -H 'User-Agent: bugbounty-<handle>' -H 'X-Bug-Bounty: <handle>' -mc all -fc 403
#   -p adds a randomised per-request delay; -fc 403 filters the WAF's block status so it
#   does not drown real results. feroxbuster's equivalent cap is --rate-limit.
```

The common thread: a WAF turns *volume* into *risk* (blocked payloads, a banned source IP, false `403` noise), so every downstream tool drops to low rate/concurrency with a delay, carries the program marker header, and filters the WAF's block status out of the results. Without the wafw00f result you would run these at defaults and either get blocked or mistake edge responses for app behaviour.

### Worked micro-walkthrough

```bash
# 1. Passive: edge tell before any attack probe
curl -sI https://app.example.com/ | grep -i cf-ray      # -> cf-ray: 8a1f...  (Cloudflare edge?)
# 2. Confirm + check for a second layer, host pinned
wafw00f -a -r https://app.example.com
#    -> [+] The site https://app.example.com is behind Cloudflare (Cloudflare Inc.) WAF.
#    -> [+] The site https://app.example.com is behind ModSecurity (Trustwave SpiderLabs) WAF.
#    -> [~] Number of requests: 5
# 3. Decision: edge = Cloudflare (hunt origin IP separately); backend = ModSecurity/CRS
#    -> sqlmap gets modsecurity* tampers; nuclei drops to -rl 20 -c 10; and the real
#       prize becomes "can I reach the origin directly and skip both layers?"
```

Two WAF names, five requests, and a fully-tuned downstream plan — none of which is itself a report, all of which shapes the testing that produces one.

## Pitfalls, noise and scope discipline

- **It sends real attack strings.** The stage-2 probe carries XSS, SQLi, and LFI payloads. Against an out-of-scope host that is unauthorized attack traffic; against an in-scope host under a program that forbids automated scanning it can still be a rules violation. Confirm the program permits automated/active recon before running it, and only ever point it at in-scope hosts.
- **You can get your source IP blocked.** The whole purpose of the target is to catch attack patterns. A WAF that trips on wafw00f's probes may block or rate-limit your IP for the rest of the session, poisoning every later test. Fingerprint early, once, from an IP you can rotate if the program allows — and if the baseline run already returns `403`/challenge, stop escalating.
- **CDN is not WAF, and the edge is not the origin.** wafw00f fingerprints whatever answers the request — usually the edge. Without `-r` it may follow a redirect and fingerprint a different host entirely. A "Cloudflare" result tells you nothing about the on-box WAF behind it; use `-a` to pull the full stack and verify which layer each `403` comes from.
- **Generic detection is a guess, not a product.** The stage-3 heuristic says "something is filtering," inferred from status/header deltas. Reporting it as a specific vendor is wrong; label it "generic WAF behaviour (unidentified)."
- **Version drift.** The BlackArch package metadata in this file's frontmatter is a build hash; the upstream stable line is **v2.4.2**. Signature coverage changes between releases, so a "no WAF" from an old package may be a stale signature set, not an absent WAF. Re-check with `wafw00f -V` and a current build before concluding.
- **`-H` overwrites all default headers.** If you pass a headers file to carry the program's required identifying header, include a `User-Agent` too, or you will send bare requests that behave differently from a normal browser and skew the fingerprint.
- **Don't over-claim in reports.** A WAF name is recon context. Submitting "Target uses Cloudflare" as a finding is a guaranteed not-applicable and wastes triager goodwill.

### False positives and false negatives to expect

Grade the result against these common failure modes before you build a plan on it:

- **False "no WAF" from a followed redirect.** Without `-r`, a 3xx to an unprotected apex or a login host is what gets fingerprinted. Always pin with `-r` when the host answers with a redirect.
- **False "no WAF" from stale signatures.** An old packaged build (frontmatter here is a BlackArch build hash, not the upstream version) lacks recent vendor signatures. Confirm with `wafw00f -V` against **v2.4.2+** before concluding a host is bare.
- **Edge mistaken for the whole defence.** A stage-1 header hit on the CDN stops the default scan and hides a distinct backend WAF. Use `-a` whenever the first result is a CDN vendor.
- **Generic-stage false positive.** Load balancers, rate limiters, bot managers, and even ordinary error pages can produce the status/header deltas that drive stage-3 generic detection. A generic verdict is "something filters," not "a WAF" — corroborate before acting.
- **Transient blocks read as a WAF.** If an earlier tool already got your IP throttled, wafw00f's probes may hit a block that looks like a dedicated WAF. Fingerprint *first*, from a clean IP, before heavier tooling muddies the signal.
- **`-t` scoping surprise.** `-t`/`--test` runs only the named plugin and returns after the first target, so a `-t` run over an `-i` list silently tests just one host — use `-t` for single-URL confirmation only.

## Reporting evidence

wafw00f output rarely stands alone, so capture it as *context that supports the real finding* (origin exposure, WAF bypass, a payload that reached the app). Capture, verbatim:

1. **The exact command**, including `-r`/`-a`/`-T` and any `-H` header file, so volume and scope are auditable.
2. **The tool version** (`wafw00f -V`, e.g. `v2.4.2`) and the package build if relevant — signature sets are version-dependent.
3. **The detection line** (`[+] The site … is behind <WAF> (<vendor>) WAF.`) and the **`[~] Number of requests: N`** line as proof the scan was low-volume.
4. **The `-vvv` transcript** of the probe that triggered the match, when an identification might be challenged.
5. **The structured output** (`-o result.json` or `-f json -o -`) for the recon-notes appendix and for diffing across re-tests.
6. For a *reportable* chain (origin exposure, bypass, monitor-mode), the **direct-to-origin request/response pair** or the bypassed request that proves the WAF did not mitigate — that pair, not the wafw00f line, is the PoC. Redact nothing you need for proof and exfiltrate nothing you do not.

State plainly in the report that wafw00f established the protection in place and that the finding is the weakness reaching the origin despite it.

### Recon-notes entry template

Keep one compact, reusable record per host so the WAF posture is greppable later and so every downstream choice is traceable to it:

```
host:        https://app.example.com
wafw00f:     v2.4.2 (pipx master)  |  run 2026-11-16T14:02Z
command:     wafw00f -a -r https://app.example.com
edge:        Cloudflare (Cloudflare Inc.)        [stage-1 header hit, high conf]
backend:     ModSecurity (Trustwave SpiderLabs)  [stage-2 block page, high conf]
requests:    5
decision:    sqlmap tampers=modsecurity*; nuclei -rl 20 -c 10; hunt origin IP (edge=CF)
reportable:  no (recon only) — revisit if origin reachable / monitor-mode confirmed
```

This is the artefact that justifies, months later, why you throttled a particular host or why a `403` in your logs was the edge rather than a vulnerability you missed.

## Full flag reference (v2.4.2)

Every option wafw00f defines, taken from its `main.py` (optparse; `-h`/`--help` is added automatically). This is the complete surface — there are no hidden flags beyond these:

| Short | Long | Effect | Default |
|---|---|---|---|
| `-v` | `--verbose` | Raise verbosity; repeatable (`-vvv`) for request/response + signature detail | off (count 0) |
| `-a` | `--findall` | Report every matching WAF instead of stopping at the first; also forces the generic pass | off |
| `-r` | `--noredirect` | Do **not** follow 3xx — fingerprint the host you named, not its redirect target | follows redirects |
| `-t` | `--test` | Test for one named WAF only (quote names with spaces); returns after the first target | — |
| `-o` | `--output` | Write results to a file; extension (`.json`/`.csv`) picks the format; `-` = stdout | — |
| `-f` | `--format` | Force output format: `json`, `csv`, or `text` | text |
| `-i` | `--input-file` | Read targets from a file (csv/json need a `url` column/element) | — |
| `-l` | `--list` | Print the detectable-WAF table (name + manufacturer) and exit | — |
| `-p` | `--proxy` | Send probes via a proxy: `http://host:port`, `socks5://host:port`, `http://user:pass@host:port` | — |
| `-V` | `--version` | Print the installed version and license, then exit | — |
| `-H` | `--headers` | Load custom headers from a text file — **overwrites the entire default header set** | — |
| `-T` | `--timeout` | Per-request timeout in seconds | `7` |
| | `--no-colors` | Disable ANSI colour in output (useful when piping/logging) | colour on |

Behavioural notes from the source worth knowing: the default run issues a benign baseline request *plus* one combined attack request (hence `Number of requests: 2` on a clean hit); generic detection runs only under `-a` or when nothing matched; and `-o -` suppresses the live scan printout, emitting only the final structured result (JSON is dumped with sorted keys, CSV with a header row).

### Keeping the signature set current

Because a "no WAF" can be a stale signature set rather than a bare host, pin to a current upstream build when the packaged version lags:

```bash
pipx install git+https://github.com/EnableSecurity/wafw00f.git   # isolated, latest master
# or, for a reproducible run without installing:
docker run --rm -it wafw00f -r https://app.example.com           # after: docker build . -t wafw00f
wafw00f -V                                                       # confirm you are on v2.4.2+
```

Re-pull before a big engagement; vendor signatures are the whole value of the tool, and they only move forward in newer releases.

## References

- wafw00f — project homepage / README (detection stages, install, `-l` sample, v2.4.2): https://github.com/EnableSecurity/wafw00f
- wafw00f — `main.py` CLI source (authoritative flag list, attack/generic-detection logic, output formats): https://raw.githubusercontent.com/EnableSecurity/wafw00f/master/wafw00f/main.py
- Downstream flags referenced above (`--tamper`/`--list-tampers`/`--check-waf` for sqlmap; `-rl`/`-c`/`-timeout` for nuclei; `-rate`/`-p`/`-t` for ffuf) are verifiable against each tool's own `--help`.

*Research supplement added 2026-11-16, grounded in the wafw00f GitHub repository and its `main.py` CLI source (upstream v2.4.2); downstream tool flags cross-checked against their own help output (model: claude-opus-4-8).*
