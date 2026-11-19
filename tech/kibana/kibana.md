---
technology: "Kibana"
kind: overview
audience: offensive-security
family: web-platforms
status: "thin (1 evidence file passes the re-gated command-context gate — the structure-pass 3 was digit-collision noise; upstream carries nearly the whole surface)"
layer: visualization/UI web app over Elasticsearch, 5601/tcp (Node.js server)
language: TypeScript / Node.js
homepage: "https://www.elastic.co/kibana"
github-repo: "elastic/kibana"
provenance: "field-grounded THIN (re-gated 2026-11-12: 1 evidence file — localhost 5601 found via ss, ssh -L port-forward, /api/status version 6.4.2, CVE-2018-17246 Console LFI → node reverse shell as kibana user; structure-pass count 3 was bare-digit noise — an ephemeral port 56010, a SID suffix -5601, a hex substring; see internal coverage ledger) + upstream-verified: NVD CVE-2018-17246 (Kibana <6.4.3/<5.6.13, Console plugin file inclusion, found by CyberArk; Elastic blog), NVD CVE-2019-7609 (Kibana <5.6.15/<6.6.1, Timelion arbitrary code execution; Rapid7 module; mpgn PoC), CVE-2020-7013 via Elastic 6.8.9/7.7.0 advisory + Red Hat (TSVB prototype pollution, authenticated write to Kibana index → code execution), 2026-11-12. Parent-hinted CVE-2019-16660 verified as JoyPlus CMS CSRF (not Kibana) and discarded; hinted CVE-2019-7614 verified as an Elasticsearch race condition (not Kibana) and discarded."
---

# Kibana

> For most of its life Kibana had no authentication of its own — a visualization server that evaluates saved expressions as code, one port away from every document the company indexes.

## What it is

Kibana is the Elastic Stack's front end: a Node.js web app on **5601/tcp** that queries [[elasticsearch]] and renders dashboards. Two design facts decide everything on an engagement. First, **Kibana historically had no native login** — authentication was delegated to Elasticsearch's X-Pack security, which was pay-walled until 6.8/7.1 (2019), so the enormous installed base of pre-2019 (and never-hardened) Kibanas answers anonymous HTTP, and "authenticated" in their CVEs means nothing more than "the request reached the app". Second, Kibana's own features are **expression evaluators**: Timelion expressions, TSVB visualizations, Console — saved objects whose content the server processes, repeatedly, as code-ish input. Saved objects live in the `.kibana` index inside Elasticsearch itself, so write access to ES (`/_search` see everything, index-write = saved-object write) becomes execution on the Kibana host under the right version.

That combination produced a three-generation RCE family: the Console plugin's **CVE-2018-17246** (arbitrary file inclusion, Kibana <6.4.3/<5.6.13 — any local `.js` file gets loaded and executed by the Node worker), Timelion's **CVE-2019-7609** (arbitrary code execution via prototype pollution in the expression evaluator, <5.6.15/<6.6.1), and TSVB's **CVE-2020-7013** (prototype pollution, <6.8.9/<7.7.0, for an attacker who can write to the Kibana index). The evidence pool holds the canonical 2018 chain end-to-end (loopback 5601 → port-forward → `/api/status` version read → LFI → reverse shell as the `kibana` user); everything past 6.4.2 here is upstream-carried.

## The anatomy that matters on an engagement

    port scan / ss on foothold → 5601/tcp (loopback or exposed)
      → GET /            → Kibana landing (app state, version cues)
      → GET /api/status  → version JSON, anonymous             ← the era fork
      → auth? pre-6.8/7.1 usually none (X-Pack paywalled)
      → version <6.4.3  → Console LFI (CVE-2018-17246):
            /api/console/api_server?apis=../../../../tmp/shell.js → node runs it
      → version <5.6.15/6.6.1 → Timelion RCE (CVE-2019-7609)
      → ES write access + <6.8.9/7.7.0 → TSVB saved-object RCE (CVE-2020-7013)
      → shell as the kibana/elasticsearch service user

Three structural properties drive most findings:

- **Version equals vulnerability, and the version is free.** `/api/status` answers anonymously with the `version.number` — one GET moves the target into the LFI/Timelion/TSVB era or out of all of them.
- **Kibana's objects live in Elasticsearch.** The `.kibana` index in ES enumeration implies the whole stack; ES write access is Kibana code execution on the vulnerable generations — crosslink [[elasticsearch]] before treating 5601 as a separate target.
- **It deploys on loopback next to its data.** The canonical pattern is localhost-only 5601 on the ES host, reached after a shell via `ssh -L` (observed) — its exposure is an internal-network property, not an edge property.

## Where you'll meet it

- **Loopback on an Elasticsearch host** — `ss`-driven discovery after a foothold; port-forward and own the stack (observed). Budget time for it whenever the ES host yields a shell.
- **Internal-facing ELK estates** — 5601 open on monitoring VLANs; often unauthenticated and unpatched because "it's internal", running exactly the dashboard queries that name internal systems and credentials-shaped fields.
- **As the ES story's second act** — after ES data exfil ([[elasticsearch]]), Kibana turns read access into code execution on the ES host itself: same estate, new primitive.

## Reading the app's mind

1. **`GET /api/status` returns `version.number` anonymously** — the fingerprint and CVE fork in one request, no auth on default installs.
2. **A bare landing page with no login form** means no X-Pack security — the app trusts whoever the network lets in; every "authenticated" CVE gate dissolves.
3. **The `.kibana` index in `_cat/indices`** ([[elasticsearch]] Phase 3) proves Kibana is in the stack even when 5601 is filtered — the saved objects in it are the TSVB-era payload carriers.
4. **CVE-2018-17246 needs a local file you can create** — the victim must be able to reach content you control (observed: /tmp/shell.js plus an outbound callback); a read-only mount or no-writable-path blunts it.
5. **Read-only Kibana mode** (`kibana.yml` `readonly`/feature privileges) restricts editing, not the vulnerable evaluation endpoints of the affected eras — never accept "it's read-only" as patch posture for <7.7.

## Where to go next

[[kibana-bug-bounty]] — the phased agent methodology: loopback discovery and port-forwarding, the version fork, the three-generation RCE table with working payload shapes, and the ES crosslink chains.
