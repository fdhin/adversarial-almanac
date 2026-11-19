---
technology: Adobe Experience Manager (AEM)
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: web-platforms
version: "1.1"
last-refresh: "2026-11-19"
provenance: "upstream-only (HowToHunt + primary sources); zero corpus evidence by design — CVE details, affected versions and bypass patterns transcribed/verified against Adobe PSIRT bulletins APSB20-15 / APSB20-31 / APSB20-72 (helpx.adobe.com), Adobe Experience League KBs ka-27832 / ka-32036 / ka-17497, and SLCyber's CVE-2025-54246–54252 dispatcher-bypass research; the hinted 'CVE-2020-9402/9409 AEM JSON servlet family' was verified REFUTED against the CVE Program API (2026-11-13: 9402 = Django SQLi, 9409 = TIBCO JasperReports) and is not cited; rewritten from KathanP19/HowToHunt (CMS/AEM.md), GPL-3.0, accessed 2026-11-13. v1.1 supplement 2026-11-19 (model gpt-6.1-sol via M365 Copilot senior-review pass, operator framing; claims dual-verified against helpx.adobe.com/Adobe Trust Center bulletins, experienceleague.adobe.com, NVD, and live GitHub repos): APSB25-90 de-grouped into its seven individual CVEs with Adobe severities; 2026 bulletin timeline added (APSB26-24/-56/-57/-74/-98/-189 with fixed versions; current lines 6.5 SP25 / 6.5 LTS SP3 / Cloud 2026.10.29251); baseline+differential validation harnesses replace status-code conclusions; capability ladders for CRXDE/packmgr/OSGi/WebDAV; dispatcher chain model corrected (structured filters, nocanon nuance, Jetty-dating claim removed); missed-attack-surface table (GraphQL, cache correctness, custom servlets, CORS); aem-hacker corrected to a Python CLI toolset; silent-failure-mode table."
---

# AEM bug-bounty methodology

Companion overview: [[aem]].

Scope: an internet-facing AEM publish tier behind a dispatcher, an accidentally exposed author instance, and the JSON/servlet surface both leak. Classify findings by **demonstrated capability**, never by status code or recognizable path — a 200 and a login page can look identical. Every `aem.example.com` below = the in-scope host.

Currency gate (read first; Adobe's newest bulletins live on the Trust Center, not HelpX):

| Line | Latest / support state (2026-11) | Meaning for targeting |
|---|---|---|
| AEM 6.5 classic | SP25 = 6.5.25.0 (2026-05-21); classic 6.5 supported until 2027-02-28 | Huge install base; SP level ≠ patched (Oct/Nov 2026 bulletins need separate hotfixes) |
| AEM 6.5 LTS | SP3 (2026-08-20); SP2 (2026-02-19) | LTS adopters lag; check SP + hotfixes |
| AEM Cloud Service | release 2026.10.29251 (2026-11-03) is the APSB26-189 security-fixed version | Cloud auto-updates, but customer proxy/dispatcher config stays theirs — the bypass classes below persist |

2026 bulletins (verified against Adobe bulletins): **APSB26-24** (Mar 10; CVE-2026-27223–27266; fixed Cloud 2026.02 / LTS SP2 / classic SP24) · **APSB26-56/57** (Jun 9; large CVE batch incl. Forms JEE 34691/34693/34694; fixed Cloud 2026.05 / LTS SP2 / classic SP25) · **APSB26-74** (Jul 14; fixed Cloud 2026.6.0 / LTS SP2+NPR-43972 / classic SP25+NPR-43971) · **APSB26-98** (Sep 8; incl. CVE-2026-19232 privesc) · **APSB26-189** (Nov 10; critical+medium, arbitrary code execution / privesc / feature bypass). Anything below the applicable fixed version is a dated, citable target.

## Phase 0 — Fingerprint with a baseline

The Granite login is a **strong fingerprint, not an identification**: it does not establish author-vs-publish, 6.5-vs-LTS-vs-Cloud, service-pack level, or even that a live AEM origin answered (caches and custom shells imitate it). Capture a baseline first:

```bash
base='https://aem.example.com'; out="$(mktemp -d)"
for p in '' '__aem_probe_nonexistent_7d82c4' 'libs/granite/core/content/login.html'; do
  curl --silent --show-error --connect-timeout 10 --max-time 30 \
    --dump-header "$out/$(basename ${p:-root}).headers" --output "$out/$(basename ${p:-root}).body" \
    --write-out 'status=%{http_code} type=%{content_type} bytes=%{size_download} redirect=%{redirect_url}\n' \
    "$base/$p"
done
grep -Ei '^(HTTP/|location:|content-type:|server:|via:|age:|cache-control:|www-authenticate:)' "$out/"*.headers
sha256sum "$out/"*.body
```

Decision points: identical bodies on login path and the nonexistent control = generic responder, not a fingerprint. `Age:`/`Via:` hint at cache involvement. Old demo namespaces (`/content/geometrixx`, `/etc/designs/`) are a **historical clue**, not a version oracle — they survive upgrades.

Silent failures: `curl -s` swallows transport errors (use `--show-error`); `curl -I` sends HEAD which proxies/WAFs may treat differently than GET; exact-match on "AEM Sign In" dies on localized/customized pages.

## Phase 1 — Exposure enumeration (capability classification)

Wordlist-driven sweep (AEM-List `paths` file is the practical list — it already carries the useful mutation families: `;%0a` variants, `///` triple-slash forms, `.1.json`/`.infinity.json` selectors, sessionInfo paths, SSRF-styled analytics endpoints). Classify every non-404 by **capability**, not status:

| Observed | Establishes |
|---|---|
| Public HTML/asset | a public route exists |
| Admin login page | admin surface reachable (exposure finding, severity per program) |
| Admin shell, no data | UI assets reachable; backend authz unknown |
| Anonymous repository data | a specific anonymous read capability |
| State-changing response | a specific write/admin operation permitted |

Soft 404s, auth redirects, SPA catch-alls, WAF 403s before dispatcher, CDN negative caching, and rate limiting all fake "hits" and "misses" — compare bodies against the nonexistent-path control. SPA shells return 200 for everything; a scanner that follows redirects reports the login page as the "exposed endpoint".

## Phase 2 — Credentials and administrative surfaces

Port 4502 and `admin:admin` are **historical install defaults worth testing where permitted**, not a description of a deployed author service (SSO/gateways are common). A 200/302 with `-u admin:admin` proves nothing alone — diff anonymous vs authenticated responses on a known resource and establish the identity actually accepted.

Capability ladders (each rung is a separate finding; reachability ≠ the top rung):

- **CRXDE Lite** (`/crx/de/index.jsp`): exposed UI → anonymous read → sensitive read → write. The principal's effective JCR permissions decide what the UI can actually touch.
- **Package Manager** (`/crx/packmgr/index.jsp`): reachable → upload permitted → install permitted → execution supported. Install = code execution *if* those hold; author→publish propagation is NOT automatic (replication permissions, topology, distribution config are separate gates). On bounty: demonstrate reachability + permission, report "potential capability", stop.
- **OSGi console** (`/system/console*`): login page → read-only → config read → config write → bundle install (execution). Config views leak classpaths/versions; "secrets" require an actual recoverable secret (masked values don't count).
- **WebDAV** (`/crx/repository`): route existence ≠ WebDAV enabled ≠ anonymous access ≠ write permitted. Version-qualify before citing.

## Phase 3 — Dispatcher and proxy bypass

Model the real chain — `client → CDN/WAF → Apache routing/rewrite/proxy → Dispatcher → Sling resolution` — bypasses are disagreements between adjacent grammars. Dispatcher filters match on request-line components (structured filter fields in modern farms, legacy `/glob` in old ones); headers matter to the overall chain even though dispatcher itself doesn't inspect bodies.

**Verified product fixes (APSB25-90, 2025-09-09)** — seven distinct CVEs, not one "bypass family":

| CVE | Adobe classification | Severity |
|---|---|---|
| CVE-2025-54246 | Incorrect Authorization (low-priv write bypass) | Important 6.5 |
| CVE-2025-54247 | Improper Input Validation | Important 6.5 |
| CVE-2025-54248 | Improper Input Validation | **Critical** 7.7 |
| CVE-2025-54249 | SSRF | Important 6.5 |
| CVE-2025-54250 | Improper Input Validation | Important 4.9 |
| CVE-2025-54251 | XML injection (blind XPath) | Important 4.3 |
| CVE-2025-54252 | Stored XSS | Important 5.4 |

Affected: 6.5.23 and earlier, 6.5 LTS SP1 and earlier, Cloud Service. Fixed: Cloud 2025.9, or GRANITE-61551 hotfix on 6.5.23 / LTS SP1 (6.5.23 alone is *affected*, not fixed). The SLCyber proxy-configuration bypasses (`/graphql/execute.json/..%2f...`, path-parameter smuggling into unanchored `ProxyPassMatch ... nocanon`) are the *routing* class that exposes these — a matching proxy route may skip dispatcher evaluation; whether it exposes anything depends on every hop's URL handling and backend authz. `nocanon` forwards the path raw; it is not a universal guarantee.

Differential validation — every mutation is a hypothesis until diffed:

```bash
out="$(mktemp -d)"
curl --path-as-is --globoff -s --max-time 20 -d "$out/b" -o "$out/b.body" \
  "$base/bin/querybuilder.json?path=/content/example&p.limit=1"
curl --path-as-is --globoff -s --max-time 20 -d "$out/e" -o "$out/e.body" \
  "$base/%2fbin%2fquerybuilder.json?path=/content/example&p.limit=1"
jq -e 'type == "object" and has("hits")' "$out/b.body" "$out/e.body"
```

Read it as: baseline denied + mutation returns recognizable QueryBuilder JSON = candidate bypass (then prove what it reached); both same = nothing; mutation returns HTML/400 = rejection somewhere in the chain, not a version fingerprint. `--path-as-is` stops *curl* normalizing dot segments (intermediaries still can); `--globoff` only disables curl globbing. Drop the "..;/ success dates the Jetty stack" claim — a hit can come from normalization at any hop.

## Phase 4 — Selector-driven info-disc and XSS

JSON rendering exposes what the effective principal can read and the selected renderer serializes — it does not bypass ACLs, and `.-1.json` is not an unlimited dump (renderer limits apply). Depth selectors are renderer-specific; custom servlets/exporters can take precedence. Compare depths on an unambiguous resource and report the *specific* properties whose anonymous availability is unintended — absent-from-HTML ≠ confidential.

QueryBuilder — bound it (`p.limit=5`, `p.guessTotal=true`; a guess is not a count). Empty hits ≠ blocked; a working servlet ≠ unauthorized disclosure; results are permission-sensitive. QueryBuilder and JSON-export findings may share one root cause.

Terminology: `/content/page.print.html` — `print` is a **selector**, `html` the extension (suffix = post-extension path). Alternate renderers are an audit surface; stored XSS needs attacker data reaching an executable context — lead with a unique inert marker and record its HTML/attribute/JS/URL context before claiming XSS. CSP, content-type, and author-vs-publish side all gate exploitability.

Sling POST servlet: anonymous write requires servlet resolution, principal write permission, and no CSRF/filter block. Evidence ladder: agreed disposable location → record absence → create uniquely-named inert node → read back independently → remove via approved mechanism → confirm removal.

## Phase 5 — Missed attack surface (add to every engagement)

| Surface | Assess | Common false conclusion |
|---|---|---|
| GraphQL persisted queries | exposure, variable handling, authorization, cache separation | "persisted" = safe |
| GraphQL authoring/schema surfaces | non-public dev/management functionality exposed | any GraphQL response = vuln |
| Content Fragment / model exporters | structured output beyond intended public content | absent from HTML = secret |
| Assets / delivery APIs | asset authz, renditions, unpublished material | one protected URL = all renditions protected |
| **Custom `/bin` + resource-type servlets** | authn, object-level authz, method handling | "core AEM hardening covers custom code" |
| Forms components/integrations | component versions, submission authz, downstream trust | base patch level covers add-ons |
| Clientlibs / source maps | internal routes, env identifiers, accidental secrets | every internal string = sensitive |
| Content lifecycle | drafts/previews/archives access vs published | path name = publication state |
| **Cache boundaries** | anonymous vs authenticated representation separation | backend ACLs prevent cached disclosure |
| CORS | actual cross-origin readability with credentials | permissive header = exploitable |
| Replication/distribution | exposed management, permissions, trust | author access = publish access |
| Admin APIs behind UIs | backing requests enforce authz independently | blocked UI = blocked capability |

Two promotions to first-class phases: **cache correctness** (a mis-cached authenticated response defeats perfect backend ACLs) and **custom application authorization** (hardened standard endpoints + leaky custom servlets is the common real-world shape).

## Phase 6 — CVE history (per-CVE verified)

| CVE | Class | Bulletin | Affected (fix) |
| --- | --- | --- | --- |
| CVE-2020-3769 | SSRF → info-disc | APSB20-15 (2020-03-17) | AEM 6.1–6.5 (6.3.3.8 / 6.4.8.0 / 6.5.4.0) |
| CVE-2020-9643 | SSRF → info-disc | APSB20-31 (2020-06-09) | **AEM 6.1–6.4 only** (6.3.3.8 / 6.4.8.1) — not 6.5 |
| CVE-2020-9645 | blind SSRF → info-disc | APSB20-31 | AEM 6.1–6.5 (6.4.8.1 / 6.5.5.0) |
| CVE-2020-9644 / 9647 / 9648 | stored / DOM / reflected XSS | APSB20-31 | AEM 6.1–6.5 (6.4.8.1 / 6.5.5.0) |
| CVE-2020-24444 | blind SSRF (Forms add-on) | APSB20-72 (2020-12-08) | Forms add-on ≤6.5.6.0 / 6.4.8.2 |
| CVE-2020-24445 | stored XSS (Critical) | APSB20-72 | AEM CS + AEM ≤6.5.6.0 |
| CVE-2025-54246–54252 | seven distinct classes | APSB25-90 (2025-09-09) | see Phase 3 table; Cloud 2025.9 / GRANITE-61551 |

The recurring 2020 pattern (SSRF→info-disc and XSS families) is a template for unpatched 6.x, but a historical class predicts nothing about a specific endpoint today — test, don't assume.

**Trap note (verified refutation, re-confirmed 2026-11-19):** "CVE-2020-9402/9409 AEM JSON servlet family" circulating in writeups is wrong — 9402 = Django SQLi (tolerance parameter on Oracle; Django fixes 1.11.29/2.2.11/3.0.4), 9409 = TIBCO JasperReports admin-UI superuser acquisition. Citing either as AEM kills a report's credibility.

## Phase 7 — Reporting

Severity follows demonstrated capability: exposed admin login = exposure/hardening (program-weighted); accepted default credentials = authentication failure, scope = what the principal can do; anonymous sensitive read = info-disc scoped to the data shown; package/bundle install permission = potentially critical, documented as capability not executed payload; dispatcher bypass = access-path weakness weighted by what it reached; anonymous write = integrity impact by writable scope. Remediation by failing layer (product fix / proxy route / dispatcher policy / repo authz / cache eligibility / app code) in one compact block per report.

## Silent failure modes

| Failure mode | Consequence |
|---|---|
| Status code read as exposure/deployment | Auth-before-routing and soft-404s misread |
| 200/302 with creds read as admin | Login page or proxy answer misread as access |
| Mutation hit read as version fingerprint | Wrong stack diagnosis; wrong report |
| curl normalizing the path client-side | Bypass never sent; false negative |
| SPA catch-all / followed redirect | Login page enumerated as 50 exposed endpoints |
| `.-1.json` trusted as full dump | Renderer limits + ACLs ignored; overstated finding |
| Author foothold read as publish compromise | Replication gates never checked |
| Cached response read as origin behavior | Cache-poisoned conclusion both ways |
| Scanner POSTs/packages during "enum" | State changes in a bounty target |

## Appendix — tools (verified)

- **aem-hacker** (0ang3el, GitHub) — **Python CLI toolset** (aem_hacker.py main scanner, aem_discoverer.py, SSRF→RCE helpers, Sling JSP-upload script), built around dispatcher bypass checks. A separately named "Burp AEM Security Scanner" extension also exists — don't conflate them.
- **aemscan** (Raz0r/aemscan) — standalone Python scanner: default-credential brute force, error-page info leak, WebDAV check (CVE-2015-1833 XXE), version detection, path sweep. Its cred brute force mutates state — gate it.
- **AEM-List** (clarkvoss/AEM-List) — `paths` wordlist with mutation families baked in (`;%0a`, `///`, selector variants, SSRF-styled endpoints). Historical strings don't guarantee current-release effect — treat entries as hypotheses.
- Scanner acceptance bar before use: does it normalize paths, follow redirects, reuse cookies, send POST/PUT/DELETE, classify from status codes alone, and is every finding reproducible with one bounded request?

## References (v1.1 verification trail)

- helpx.adobe.com / Adobe Trust Center: APSB25-90 (individual CVE table + GRANITE-61551), APSB26-24, APSB26-56/57, APSB26-74, APSB26-98 (CVE-2026-19232; Experience League ka-44835), APSB26-189 (Cloud fix 2026.10.29251). Newer bulletins are Trust Center-first — HelpX-only checks miss October/November.
- Adobe release notes: 6.5 SP25 (6.5.25.0, 2026-05-21), 6.5 LTS SP3 (2026-08-20), LTS FAQ (6.5 classic until 2027-02-28), Cloud maintenance release 29251 (2026-11-03).
- APSB20-31/72 per-CVE rows (9643 = 6.1–6.4 only; 9645 = 6.1–6.5); Django 9402 upstream advisory; GHSA-j2v4-xwh5-47fc (TIBCO 9409).
- github.com/0ang3el/aem-hacker, github.com/Raz0r/aemscan (setup.py entry point), github.com/clarkvoss/AEM-List (paths file).
- SLCyber dispatcher-bypass research (proxy-configuration class behind the APSB25-90 fixes); Adobe KBs ka-27832 (encoded slashes) and ka-32036 (path parameters/selectors).
