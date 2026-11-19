---
technology: Adobe Experience Manager (AEM)
kind: overview
audience: offensive-security
status: upstream-only
family: web-platforms
layer: Java web CMS / edge-cached content platform
language: Java (OSGi, Apache Sling, JCR/Oak)
homepage: https://business.adobe.com/products/experience-manager/aem.html
provenance: "upstream-only (HowToHunt + primary sources); zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — surface grounded in Adobe PSIRT bulletins APSB20-15 / APSB20-31 / APSB20-72 (AEM CVE details, affected versions), Adobe Experience League KBs ka-27832 / ka-32036 / ka-17497 (dispatcher filter bypass patterns, publish-side JSON exposure, dispatcher cache rules) and SLCyber's 2025 dispatcher-bypass research (CVE-2025-54246–54252 family, nocanon GraphQL bypass, path-parameter hybrid); rewritten from KathanP19/HowToHunt (CMS/AEM.md), GPL-3.0, accessed 2026-11-13"
---

# Adobe Experience Manager

> The enterprise CMS whose every URL is a database query — `/content/site/page.selector.json` renders any repository node as JSON — with a default `admin:admin` on the authoring instance, a repository browser at `/crx/de/index.jsp`, and nothing between you and all of that except a hand-written filter file the dispatcher matches against the request line alone.

## What it is

Adobe Experience Manager is Adobe's enterprise content platform: a Java application built from Apache Sling (HTTP request → repository resource → servlet resolution), a Java Content Repository (Jackrabbit/Oak) that stores every page, asset, user and config as a tree of nodes, an OSGi runtime (Apache Felix) where every feature is a hot-deployable bundle, and the Granite UI framework that renders the admin screens. It descends from Day Software's Communiqué/CRX product line (Adobe acquired Day in 2010), which is why the strings `crx`, `granite` and `sling` still name the most interesting endpoints in a 2020s deployment. A standard installation runs two roles: an **Author** instance (authenticated editors, all admin tooling — default quickstart port 4502) and a **Publish** instance (anonymous readers, port 4503), with content replicated author → publish.

The operator-critical detail is what sits in front: the **dispatcher**, an Apache httpd module that is simultaneously a cache and the only request filter. It serves cached pages for anonymous GETs and applies a hand-maintained `dispatcher.any` filter list to everything else. Adobe's own guidance states the security model plainly: dispatcher filtering "is not a reliable security boundary by itself" — repository ACLs and publish-tier endpoint restrictions are the primary control, with the dispatcher as defense in depth. In practice the filter file IS the boundary you attack, because it matches only the first line of the HTTP request (verb, path, extension, suffix, query string — never headers or body), and the backend's URL canonicalization does not have to agree with the dispatcher's glob matching.

The operator takeaway: AEM concentrates an unusual amount of pre-auth surface behind one hand-written config file. Repository-wide JSON exports, the QueryBuilder servlet, the OSGi web console, CRXDE Lite, and package installation all live at predictable paths under `/libs`, `/bin`, `/etc` and `/crx`, so the entire game is (1) fingerprint the AEM, (2) enumerate which of those endpoints the dispatcher accidentally lets through — via filter-rule gaps, encoding confusion, or default proxy rules that skip the dispatcher entirely — and (3) exploit what answers. Adobe's PSIRT history (SSRF and stored-XSS families in every 2020 bulletin; a 2025 dispatcher-bypass family) plus the perennial default `admin:admin` mean both unauthenticated and one-credential-away paths are realistic.

## The anatomy that matters on an engagement

    internet → dispatcher (Apache + mod_dispatcher)
      cache: anonymous GET/HEAD with file extension, no auth header/cookie
      filter: dispatcher.any globs on the request LINE only (no headers/body)
      └─ gaps: ProxyPassMatch holes (GraphQL persisted queries, Delivery API)
               encoded slashes (%2F), path parameters (;x=...), selector abuse
    → publish instance (Sling on Jetty/Oak, anonymous)
         /content/...  pages, assets — every node addressable, JSON-exportable
         /bin/...     servlets: querybuilder.json, stored POST handlers
         /libs/...    system content + granite servlets (login, core endpoints)
         /etc/...     configs, truststore — historically exposed
    author instance (4502, authenticated — often internet-reachable by accident)
         admin:admin default · /crx/de/index.jsp CRXDE Lite repository browser
         /crx/packmgr package install (code exec) · /system/console OSGi console
         WebDAV at /crx/repository · replication into publish

Three structural properties drive findings: (1) **every repository node is a URL** — Sling resolves `/content/site/page.<selector>.<extension>` to a renderer, so `.json` depth selectors (`.-1.json`, `.1.json`) turn any readable subtree into a structured dump; (2) **the filter file is hand-written per customer** while the backend URL grammar is uniform, so mismatch (encoding, trailing selectors, semicolon path params) is the recurring dispatcher-bypass bug class; (3) **author and publish share a repository model**, meaning an exposed author instance is one default password away from code execution via package manager, which then replicates into publish.

## Where you'll meet it

- **Enterprise/government marketing sites** — the classic exposure: public site on publish+dispatcher, author instance discovered on a subdomain (`author.`, `auth.`, management VLANs misexposed). Fingerprint-first recon pays: `/libs/granite/` clientlib paths and the Granite login screen identify AEM before you touch anything sensitive.
- **CDN-fronted AEM Cloud (as a Cloud Service)** — the modern shape; the dispatcher config is customer-managed and the 2025 bypass research (CVE-2025-54246–54252 family) targeted exactly the default Apache rules shipped for cloud instances.
- **Bug bounty scopes** — large programs love AEM because exposure is binary and loud: an open `/bin/querybuilder.json` or an exposed CRXDE is an instant report, and the exposure wordlist problem is solved (see methodology).
- **Legacy 6.x installs** — unmaintained since before the 2020 PSIRT wave (SSRF family, stored-XSS family), often still carrying demo content and default credentials.
- **Adjacent surface** — AEM Forms add-on (its own SSRF/XSS history), Assets/Scene7/dynamic media paths, GraphQL persisted-query endpoints; each has its own proxy/cache rules and therefore its own bypass seams.

## Reading the framework's mind

1. **URLs decompose mechanically**: `/content/path.sel1.sel2.ext/suffix` — the last extension picks the renderer, selectors pick variants. You can compute interesting URLs offline; no fuzzing intuition needed. (→ [[aem-bug-bounty]] selector phase)
2. **The login page is a fingerprint**: `/libs/granite/core/content/login.html` renders "AEM Sign In" and pulls `/libs/granite/css` stylesheets — harmless to request, definitive to identify, and itself worth denying per Adobe's KB (its exposure on publish signals sloppy filters).
3. **The dispatcher caches only 200-OK GET/HEAD responses with file extensions and no auth markers** — which is exactly why authorization-header-stripping, cache-rule, and `ignoreUrlParams` quirks have historically produced poisoned-cache and credential-leak classes.
4. **Fresh installs trust you**: `admin:admin` on author (4502) is the single highest-value default credential in the ecosystem; the OSGi web console and CRXDE Lite sit one authentication away from repository and code control.
5. **Adobe's CVE history is a families story**: SSRF → information disclosure (CVE-2020-3769, -9643, -9645, -2020-24444) and stored/reflected XSS (CVE-2020-9644/-9647/-9648, -24445) recur across the 2020 bulletins — when you find one JSON-servlet leak, assume siblings and grep Adobe PSIRT for the bulletin before writing the report.

## Where to go next

The full hunt method — fingerprinting, exposure wordlists, default credentials, CRXDE/package-manager abuse, dispatcher filter-bypass families (with the refuted-CVE trap note), selector-driven info-disc classes, and PSIRT-verified CVE history — is in [[aem-bug-bounty]].
