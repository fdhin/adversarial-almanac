---
technology: Adobe Experience Manager (AEM)
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: web-platforms
version: "1.0"
provenance: "upstream-only (HowToHunt + primary sources); zero corpus evidence by design — CVE details, affected versions and bypass patterns transcribed/verified against Adobe PSIRT bulletins APSB20-15 / APSB20-31 / APSB20-72 (helpx.adobe.com), Adobe Experience League KBs ka-27832 / ka-32036 / ka-17497, and SLCyber's CVE-2025-54246–54252 dispatcher-bypass research; the hinted 'CVE-2020-9402/9409 AEM JSON servlet family' was verified REFUTED against the CVE Program API (2026-11-13: 9402 = Django SQLi, 9409 = TIBCO JasperReports) and is not cited; rewritten from KathanP19/HowToHunt (CMS/AEM.md), GPL-3.0, accessed 2026-11-13"
---

# AEM bug-bounty methodology

Companion overview: [[aem]].

Scope: an internet-facing AEM publish tier behind a dispatcher, an accidentally exposed author instance, and the JSON/servlet surface both of them leak. Every command below uses `https://aem.example.com` for the target — substitute the in-scope host.

## Phase 0 — Fingerprint

Confirm AEM before touching anything loud:

```bash
curl -s https://aem.example.com/libs/granite/core/content/login.html \
  | grep -o 'AEM Sign In\|granite[^"]*' | sort -u
curl -sI https://aem.example.com/ | grep -i 'dispatcher\|server'
```

- Granite login screen ("AEM Sign In", `/libs/granite/css` clientlib links) = confirmed AEM.
- The dispatcher sometimes advertises itself in response headers; a bare Apache `Server:` header with an aggressive 404-for-everything posture suggests a default-deny filter farm (good hygiene — expect a harder fight).
- Old installs leak demo namespaces (`/content/geometrixx`, `/etc/designs/`) — instant version-oracle.
- Tooling in prose: the Burp-oriented **aem-hacker** extension (0ang3el) wraps this whole methodology — fingerprinting probes, an AEM-sensitive-path wordlist, and dispatcher-bypass mutations; the standalone Python scanner **aemscan** (Raz0r) checks known exposed endpoints in one pass. Both complement, not replace, the manual phases below.

## Phase 1 — Exposure enumeration (the wordlist phase)

AEM exposure hunting is wordlist-driven in the spirit of can-i-take-over-xyz: a curated list of sensitive AEM paths, thrown at the dispatcher, and every non-404 adjudicated. In prose rather than link form: the community **AEM-List** wordlist (clarkvoss/AEM-List on GitHub) is the canonical paths list; aem-hacker bundles an equivalent. Core entries to try manually:

```text
/content                          /etc/truststore
/bin/querybuilder.json            /crx/de/index.jsp
/crx/packmgr/index.jsp            /system/console
/system/console/configMgr         /libs/granite/core/content/login.html
/etc/clientlibs                   /content/dam
/bin/receive?sling:authRequestLogin=1
```

Decision point: for each hit, classify **cached public content** (fine), **anonymous JSON/servlet response** (info-disc candidate → Phase 4), or **admin UI that should never answer here** (CRXDE/package manager/OSGi console on the internet → Phase 2, critical).

## Phase 2 — Default credentials and the author-instance jackpot

Author instances default to port 4502 with `admin:admin`; the same credential habitually guards the OSGi web console.

```bash
curl -s -o /dev/null -w '%{http_code}\n' \
  -u admin:admin https://author.example.com/crx/de/index.jsp
# 200/302 past login = repository browser as administrator
```

What each authenticated foothold buys:

- **CRXDE Lite** (`/crx/de/index.jsp`) — browse and edit the whole JCR repository, view users/nodes, read configs. On publish it is usually disabled; when it answers unauthenticated-that-is-misconfigured, that alone is a critical report.
- **Package Manager** (`/crx/packmgr/index.jsp`) — install a crafted package (zip) containing JSPs or OSGi bundles → code execution, then replication into publish. This is the endgame; do not install anything on a bounty target — demonstrate reachability and stop.
- **OSGi web console** (`/system/console`, `/system/console/configMgr`) — live bundle management; installing a bundle is code execution, and the console's own configuration page leaks classpaths, versions and secrets (report as info-disc).
- **WebDAV** (`/crx/repository`) — repository as a filesystem; historically enabled by default.

## Phase 3 — Dispatcher filter bypass

The dispatcher filters on the request line only (verb/path/extension/suffix/query string — never headers or body) using customer-written glob rules, while the backend (Sling on Jetty) canonicalizes URLs its own way. Bypass = make the two grammars disagree. Verified families, newest first:

1. **Default-proxy holes (2025, CVE-2025-54246–54252 family — SLCyber research).** The sample Apache config shipped for AEM Cloud `ProxyPassMatch`es some paths straight to the backend *without dispatcher filtering* — notably GraphQL persisted queries (`/graphql/execute.json/...`, with `nocanon`) and Delivery API paths. Because `nocanon` suppresses URL canonicalization and the regex is unanchored:

   ```text
   /graphql/execute.json/..%2f../bin/querybuilder.json
   /bin/querybuilder.json;x='x/graphql/execute.json/x'
   ```

   The first sends the raw `..%2f` path to Jetty, which normalizes it to `/bin/querybuilder.json`. The second smuggles the unanchored match inside a **path parameter** — also the evasion when a WAF blocks `..%2f` sequences. Report: any config with these proxy rules is bypassable regardless of `dispatcher.any`.

2. **Encoded slashes (Adobe KB ka-27832).** Filters matching `/bin/querybuilder.json` literally are bypassed with `%2F`:

   ```bash
   curl -s 'https://aem.example.com/%2fbin%2fquerybuilder.json?path=/etc'
   # non-404 response = filter bypass + unauthenticated QueryBuilder access
   ```

3. **Path parameters / selectors vs glob rules (Adobe KB ka-32036).** A semicolon path-parameter makes a URL skip glob matches designed for clean paths, and a trailing selector+extension exposes JSON renderers:

   ```text
   /etc/truststore.-1.json;x='.ico/x'      → truststore info disclosure
   /.children.-1.json;x='.ico/x'           → internal node names/structure
   ```

4. **Jetty `..;/` traversal.** `/adobe/dynamicmedia/deliver/..;/..;/..;/bin/querybuilder.json` — the classic trick; modern Jetty rejects `..;/` outright (400-family), so a *successful* response here dates the stack, not the dispatcher. Historical newline/encoding glob-confusion variants (`%0a`, `%0b` inside the path) belong to the same family — always re-test the same sensitive path under several encodings rather than trusting one miss.

Mitigation you should expect to recommend (from the same Adobe KBs): Apache-level `LocationMatch` with `ProxyPass "!"` for sensitive endpoints, deny rules for `*;*` and `/etc/truststore*`, `/libs/dam/merge*`, `/libs/granite/core/content/login.html*` — and the standing reminder that dispatcher rules are defense in depth, not the boundary; publish ACLs are.

## Phase 4 — Selector-driven info-disc and XSS classes

Once any `/content`-rooted GET survives the dispatcher, Sling's URL grammar does the work:

- **JSON export at depth**: append `.1.json`, `.2.json`, `.-1.json` (depth) to any node path — `/content/example.en.1.json`. A readable anonymous subtree becomes a structured dump (users' hidden fields, unreleased content, DAM metadata). Compare several depths; report what differs from the rendered page.
- **QueryBuilder servlet**: `/bin/querybuilder.json?path=/content&1_property=jcr:primaryType&1_property.value=cq:Page` enumerates repository structure through a query API; hits here are separate findings from raw JSON export (see ka-27832/ka-32036 — it is the endpoint the recent bypasses were built to reach).
- **Selector XSS**: alternate renderers selected by suffix (`/content/page.print.html`, mobile/overlay selectors) historically rendered content into less-audited templates — stored-XSS sink when authoring input lands in them (Adobe's own stored-XSS CVEs below are this class).
- **Sling POST servlet**: `POST /content/some/path` creates/modifies nodes when anonymous write is misconfigured — try a harmless node name, capture, and report as defacement-class write primitive. Never leave the marker in place on a bounty target.

## Phase 5 — CVE history (PSIRT-verified)

State as upstream history, each verified against the Adobe bulletin named:

| CVE | Class | Bulletin / source | Affected (fix) |
| --- | --- | --- | --- |
| CVE-2020-3769 | SSRF → info-disc | APSB20-15 (2020-03-17) | AEM 6.1–6.5 (6.3.3.8 / 6.4.8.0 / 6.5.4.0) |
| CVE-2020-9643 | SSRF → info-disc | APSB20-31 (2020-06-09) | AEM 6.1–6.4 (6.3.3.8 / 6.4.8.1) |
| CVE-2020-9645 | blind SSRF → info-disc | APSB20-31 | AEM 6.1–6.5 (6.4.8.1 / 6.5.5.0) |
| CVE-2020-9644 | stored XSS | APSB20-31 | AEM 6.1–6.5 (6.4.8.1 / 6.5.5.0) |
| CVE-2020-9647 | DOM-based XSS | APSB20-31 | AEM 6.1–6.5 (6.4.8.1 / 6.5.5.0) |
| CVE-2020-9648 | reflected XSS | APSB20-31 | AEM 6.1–6.5 (6.4.8.1 / 6.5.5.0) |
| CVE-2020-24444 | blind SSRF (AEM Forms add-on) | APSB20-72 (2020-12-08) | Forms add-on ≤ 6.5.6.0 / 6.4.8.2 |
| CVE-2020-24445 | stored XSS (Critical) | APSB20-72 | AEM CS + AEM ≤ 6.5.6.0 |
| CVE-2025-54246–54252 | dispatcher/proxy bypass family | SLCyber research + Adobe PSIRT (2025) | default cloud Apache proxy rules |

The 2020 pattern is two families — **SSRF→info-disc** and **stored/reflected XSS** — recurring every bulletin; use them as templates for what an unpatched 6.x will still carry.

**Trap note (verified refutation, 2026-11-13):** writeups circulate "CVE-2020-9402 / CVE-2020-9409 — AEM JSON servlet information-disclosure family". Both IDs are real CVEs but belong to *other products*: CVE-2020-9402 is Django SQL injection (GIS tolerance parameter on Oracle; MITRE CNA record), CVE-2020-9409 is TIBCO JasperReports Server admin-UI access control (TIBCO CNA record) — verified against the CVE Program API. Citing either as AEM in a report is an instant credibility loss; the AEM JSON-exposure story is the selector/QueryBuilder class in Phase 4 plus the bulletins above.

## Phase 6 — Disclosure

Report per finding class: exposed admin UI (CRXDE/packmgr/OSGi console) and default credentials as critical auth-boundary failures; dispatcher bypass as a filter-evasion chain to whatever it reached (info-disc weight depends on what the JSON/servlet returned); anonymous write via Sling POST as defacement-class. Recommend: rotate `admin`, disable CRXDE/WebDAV on production, apply the Adobe KB deny rules (`*;*`, truststore, login page), pin proxy rules to canonical paths (no `nocanon` on unanchored regexes), and treat publish ACLs — not the dispatcher — as the boundary. Credit Adobe PSIRT bulletin IDs when recommending version fixes.

## Appendix — tools in prose

- **aem-hacker** (Burp extension, 0ang3el) — AEM fingerprinting, sensitive-path scanning with a bundled wordlist, and dispatcher-bypass mutations; the workflow closest to this page.
- **aemscan** (Raz0r) — standalone Python scanner enumerating known AEM exposure endpoints.
- **AEM-List** (clarkvoss/AEM-List) — the canonical AEM paths wordlist for the Phase 1 sweep; pair it with generic content-discovery (ffuf) when the dispatcher 404s everything.
- Adobe's own surfaces double as tooling: the login page and `/libs/granite/` clientlibs fingerprint (Phase 0), and `system/console` component listings when reachable.
