---
name: "trivy"
categories:
  - scanner
homepage: "https://trivy.dev"
origin: "pentest-book"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: go
github-repo: "aquasecurity/trivy"
github-stars: 39286
github-match: "exact"
gh-checked: "2026-11-13"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
last-refresh: "2026-11-19"
license: "Apache-2.0"
---

# trivy

> One binary that scans containers, filesystems, and clusters for CVEs, misconfigurations, and leaked secrets.

## What it's for

Trivy is a comprehensive security scanner built around two axes: scanners (vulnerabilities/CVEs, misconfigurations, exposed secrets, software licenses, SBOM) and targets (container images, local filesystems, remote Git repositories, virtual machine images, Kubernetes clusters). Most popular languages and operating systems are covered, so one tool replaces a stack of per-ecosystem scanners. For offensive work the value is fast attack-surface triage — what an image or checkout exposes before you touch it — and secret sweeps that show what a foothold would yield. It is a static-analysis-minded tool: it does not exploit anything, and it is not a web application scanner (no crawling or active testing of a running service).

## Typical use cases

### Triage a container image for known CVEs

Quick read on how outdated and vulnerable a public or locally built image is.

```
trivy image python:3.4-alpine
```

Outcome: the image is pulled (or reused locally), its OS and language packages are enumerated, and a table of known CVEs per package is printed with severity and fixed-version columns plus a summary count.

### Sweep a project tree for secrets and misconfigurations

Combine scanners on a checkout instead of running separate tools.

```
trivy fs --scanners vuln,secret,misconfig myproject/
```

Outcome: the directory is walked and three result sets come back in one pass — vulnerable dependencies, hardcoded credentials and keys, and configuration/IaC misconfigurations.

### Audit a Kubernetes cluster's security posture

Summarize what a whole cluster exposes.

```
trivy k8s --report summary cluster
```

Outcome: Trivy connects to the current cluster context and prints a per-resource summary of vulnerabilities and misconfigurations across the cluster's workloads.

No corpus evidence — derived from upstream documentation.

---

## Bug bounty workflow with Trivy

**Scope first.** Trivy is a static artifact scanner, not a web scanner — it never touches the target's running service, so it carries almost no DoS risk. The scope risk is different: Trivy will happily scan anything, and a report built on an **out-of-scope artifact** or an **unreachable CVE** is a fast rejection. Before running anything, confirm the artifact is in scope. The assets Trivy is good for in a bug-bounty context are: the program's **published container images** (Docker Hub, GHCR, ECR — only if the registry/namespace is listed in scope), **public source repositories** the program owns, and **SBOMs** the program publishes (some vendors ship a CycloneDX/SPDX SBOM per release). A third-party base image's CVE is the upstream project's bug, not the program's, unless the program has explicitly shipped and exposed it.

The spine of this workflow: **a Trivy CVE line is a lead, not a finding.** Phases 0–2 build the lead list; Phase 3 is the entire difference between a payout and a `N/A — informational` close.

Verified against Trivy **v0.76.2** (released 2026-11-05); flags below exist in that line's CLI reference.

### Phase 0 — Safe setup and tool integrity

```
# Pin a known-good release; Trivy pulls its DB from an OCI registry on first run
trivy --version
trivy image --download-db-only          # warm the vuln DB once, offline-friendly after
```

- Nothing here hits the target. The only network traffic is to the registry the image lives in (an image pull) and to the Trivy DB mirror (`mirror.gcr.io/aquasec/trivy-db:2`). Neither is the program's infrastructure, so there is no rate-limit or volume concern against the target.
- Tool integrity matters in a supply-chain scope: pin the binary version and prefer the GitHub release assets with checksums over a floating `latest` tag. A malicious Trivy release was reported in 2026 (a `v0.69.4` tag published through compromised credentials, along with the `trivy-action`/`setup-trivy` CI actions) — verify what you run; the primary source of truth for releases is the GitHub releases page.
- `--skip-db-update` reuses a cached DB (useful when you want reproducible results across a session); `--download-db-only` refreshes it without scanning.

### Phase 1 — Inventory the artifact

Enumerate the full package list *before* filtering to vulnerabilities — the inventory itself is recon (what language runtimes, what base OS, what internal/private packages leak in an image you were handed).

```
# Container image you were given, or pulled from an in-scope registry
trivy image --format json -o inv.json ghcr.io/example-org/api:1.4.2

# Public repo in scope (vuln+secret run by default on repo target)
trivy repo https://github.com/example-org/api

# A published SBOM — input format auto-detected (CycloneDX / SPDX / SPDX-JSON)
trivy sbom example-org-api-1.4.2.cdx.json
```

- `--list-all-pkgs` (on by default in JSON) dumps every package regardless of vulnerability — the raw bill of materials.
- `--dependency-tree` (EXPERIMENTAL) shows the origin tree of a vulnerable package, which tells you whether a CVE lands in a **direct** dependency you could influence or a deep transitive one.
- `--pkg-relationships root,direct` narrows to the packages the project actually declares, cutting transitive noise early. (It conflicts with `--dependency-tree`, `--vex`, and SBOM generation — pick one.)

### Phase 2 — Vulnerability triage

Cut the firehose to the set worth investigating. The order is: severity floor → drop the unfixable → prefer precision.

```
trivy image \
  --severity HIGH,CRITICAL \
  --ignore-unfixed \
  --detection-priority precise \
  --format json -o triage.json \
  ghcr.io/example-org/api:1.4.2
```

- `--severity` accepts `UNKNOWN,LOW,MEDIUM,HIGH,CRITICAL`. Trivy's severity is **vendor-sourced when available** and only falls back to NVD — the JSON carries `SeveritySource` and a `VendorSeverity` map, so a CVE NVD calls HIGH may be `Low` per Red Hat/Debian because the distro backported or doesn't ship the vulnerable path. Read `SeveritySource` before you trust the severity column.
- `--ignore-unfixed` is a shorthand for `--ignore-status affected,will_not_fix,fix_deferred,end_of_life` — it shows only vulnerabilities with an available fix. For a bounty lead this is usually the right default: an unfixed/won't-fix CVE is rarely a reportable finding on its own.
- `--detection-priority precise` (the default) minimizes false positives; `comprehensive` casts wider and knowingly adds false positives — only reach for it when you suspect Trivy is under-reporting and you will verify each hit by hand anyway.
- `--pkg-types os` vs `library` splits distro-package CVEs from application-dependency CVEs. Application dependencies are usually where a bounty-relevant, reachable bug lives.

### Phase 3 — Reachability and exploitability (the money phase)

This is where most Trivy-derived reports die or survive. Triage gives you `package X version Y has CVE-Z`. A payout needs: **the vulnerable code path is reachable in the running target, and here is the request that proves it.** Trivy does not do reachability analysis — you do.

For each candidate CVE from Phase 2:

1. **Read the advisory, not the row.** Open `PrimaryURL`/`References` from the JSON. Establish the actual trigger: which function, which input, which configuration. Many CVEs require a feature the target doesn't use (a parser, a codec, a CLI subcommand) — those are unreachable and not findings.
2. **Confirm the version is really deployed.** The image tag may differ from what's live; a vendor may have backported the fix without bumping the version string (exactly what `SeveritySource` hints at). If you can observe the running service, fingerprint the real version.
3. **Build a PoC against the live, in-scope target** — the normal bug-bounty discipline (single request, low volume, `example.com`/TEST-NET only, no destructive payloads). The Trivy hit is the *hypothesis*; the HTTP request/response pair that triggers the CVE on the target is the *finding*.
4. **If you cannot demonstrate reachability, say so.** A report that reads "Trivy reports CVE-Z in X; I could not confirm exploitability" is honest and occasionally accepted as informational on a hardening-minded program — but don't dress it as critical.

Trivy's role in this phase is narrowing and evidence, not proof. Use `--dependency-tree` to argue the dependency is reachable from application code, and keep the JSON as the provenance trail.

**Worked example — from row to PoC.** Suppose triage emits:

```
HIGH   CVE-2024-XXXXX   libexample 2.3.1 -> 2.3.4   ghsa
```

The discipline:

1. Open `PrimaryURL` → the GHSA says the bug is a deserialization flaw in `libexample.loads()` reachable only when the caller passes untrusted input to that function.
2. `trivy image --dependency-tree` shows `libexample` is pulled directly by the app (not a build-only transitive), so application code plausibly calls it.
3. Map it to the target: which in-scope endpoint feeds user input into a `libexample.loads()` path? If the app exposes an import/upload route that round-trips through that library, you have a candidate request.
4. Build the single-request PoC against `api.example.com`, capture the request/response that triggers the documented behavior. *That* pair is the finding; the Trivy row is its provenance.
5. If no endpoint reaches `loads()`, the CVE is present-but-unreachable — note it, don't grade it High.

### Phase 4 — Secrets sweep

On a repo, filesystem, or image, the secret scanner is on by default for `fs`/`repo` and included via `--scanners`. A live credential in an in-scope artifact is one of the few Trivy findings that is *directly* reportable — but handle it carefully.

```
trivy fs --scanners secret ./checkout
trivy image --scanners secret ghcr.io/example-org/api:1.4.2
```

- Treat any hit as **sensitive**: do not paste the secret value into a report, do not test a found cloud key against live infrastructure beyond the minimal proof the program's rules allow, and redact in evidence.
- Image secret scanning catches credentials baked into layers (a key `rm`'d in a later layer still sits in an earlier one) — a classic supply-chain leak.

### Phase 5 — Config / IaC misconfiguration (only where in scope)

If the program's scope includes Infrastructure-as-Code (a public Terraform/Helm/Kubernetes repo, Dockerfiles), misconfiguration scanning surfaces issues scanners of running services can't see.

```
trivy config ./infra                       # standalone IaC scan
trivy fs --scanners misconfig ./repo       # misconfig as part of an fs scan
```

- Misconfig is **not** enabled by default in `image`/`fs`/`repo` — you must add `--scanners misconfig` (the standalone `config` subcommand enables it implicitly).
- Supported types auto-detected: Dockerfile, Kubernetes, Terraform, CloudFormation, Azure ARM, Helm. The `--misconfig-scanners` list controls which engines run.
- Built-in checks map to AVD IDs (e.g. `AVD-DS-0002` "last USER should not be root", `AVD-KSV-*` Kubernetes checks). The checks bundle is fetched from an OCI registry, so this step needs internet.
- Bounty reality check: most IaC misconfigs are **informational** unless you can tie them to a concrete exposure. "No resource limits on a pod" is hardening; "a `0.0.0.0/0` security-group rule on the admin port that I can reach" is a finding. Verify the misconfig is live before reporting.

### Phase 6 — Dedupe, suppress, and report

Programs publish known-issue lists and accepted-risk statements; a duplicate is wasted effort and can hurt your reputation score. Trivy's filtering files are how you encode "already known" so your working output only shows the fresh deltas.

```
# Re-scan showing only what survives your ignore list
trivy image --ignorefile ./.trivyignore.yaml --show-suppressed \
  ghcr.io/example-org/api:1.4.2
```

- `.trivyignore` (plain IDs) and `.trivyignore.yaml` (EXPERIMENTAL, richer) suppress by CVE/AVD/secret ID. The YAML form supports `id`, `paths`, `purls`, `expired_at` (auto-expiring an acceptance), and a non-filtering `statement`.
- `--show-suppressed` keeps suppressed items visible but flagged (exported as `ExperimentalModifiedFindings` in JSON) — so you can diff "known" vs "new" in one run.
- If the program publishes a **VEX** document (Vulnerability Exploitability eXchange), feed it: `--vex /path/to/vex.json` applies the vendor's own "not affected / not reachable" assertions, which is exactly the dedupe you want. See the recipes below.

---

## Where the money is with Trivy

Trivy rarely *is* the finding — it is the lead generator. This table ranks where leads most often convert, with the honest dupe risk for each.

| Priority | Finding class | Trivy command / feature that reaches it | Typical severity | Dupe / rejection risk |
|---|---|---|---|---|
| 1 | Live secret in a published image/repo layer | `--scanners secret` on `image`/`repo`/`fs` | High–Critical | Low if the credential is valid and in-scope; verify it's live |
| 2 | Reachable CVE in a *direct* app dependency | `--pkg-types library` + `--dependency-tree`, then manual PoC | High–Critical | **High** unless you demonstrate reachability (Phase 3) |
| 3 | Exploitable IaC misconfig on an exposed asset | `trivy config` / `--scanners misconfig` | Medium–High | High — most are informational without a live exposure |
| 4 | Vulnerable OS package in a service you can reach | `--pkg-types os --ignore-unfixed`, then PoC | Medium–High | High — distro backports (`SeveritySource`) kill many |
| 5 | Supply-chain drift in a published SBOM | `trivy sbom <file>` vs the live image | Medium | Medium — needs a concrete impact story |
| 6 | Leaked internal package names / private registry refs | `--list-all-pkgs` inventory | Low–Info | Low value alone; good as recon for other bugs |
| 7 | End-of-life base OS still serving traffic | `--exit-on-eol` / EOL status | Low–Medium | Often known/accepted; check the program's lists |
| 8 | Raw "outdated dependency" with no PoC | any vuln scan | Info / rejected | **Very high** — do not file these as graded findings |

The pattern: everything above the line (1–2) can convert to real bounties; everything below needs a reachability or exposure story, and row 8 is the trap that gets hunters a reputation for noise.

---

## Command recipes

Annotated command → expected output. Trivy's load falls on registries and the DB mirror, not the target, so there are no rate caps to tune against the program — the discipline here is **output hygiene and scope**, not request volume.

### Triage JSON you can track and diff

```
trivy image --severity HIGH,CRITICAL --ignore-unfixed \
  --format json -o api-1.4.2.json ghcr.io/example-org/api:1.4.2
```

Expected: a JSON document with `.Results[]`, each carrying `Target`, `Class`, `Type`, and `Vulnerabilities[]` whose entries include `VulnerabilityID`, `PkgName`, `InstalledVersion`, `FixedVersion`, `Severity`, `SeveritySource`, and `PrimaryURL`. Keep these files per tag — diffing two tags' JSON shows exactly which CVEs a release introduced or fixed.

### Reduce to a reviewable lead list

```
trivy image --format json ghcr.io/example-org/api:1.4.2 \
| jq -r '.Results[].Vulnerabilities[]?
    | select(.FixedVersion != "")
    | "\(.Severity)\t\(.VulnerabilityID)\t\(.PkgName) \(.InstalledVersion) -> \(.FixedVersion)\t\(.SeveritySource)"'
```

Expected: one tab-separated line per fixable CVE with the severity source attached, so you immediately see which "HIGH" rows are really distro-downgraded. This is your Phase-3 queue.

### Apply vendor VEX to drop unreachable CVEs

```
# Local VEX file published by the vendor
trivy image --vex ./example-org.openvex.json --show-suppressed \
  ghcr.io/example-org/api:1.4.2

# Or the community VEX Hub / a configured VEX repository
trivy image --vex repo ghcr.io/example-org/api:1.4.2
```

Expected: CVEs the vendor marks `not_affected` move to suppressed (visible with `--show-suppressed`, tagged in `ExperimentalModifiedFindings`). `--vex` is EXPERIMENTAL and accepts a **file path**, `repo`, `oci`, or `sbom-ref`; multiple `--vex` flags compose and earlier ones win on priority. Manage repositories with `trivy vex repo init|list|download`.

### Dependency-origin tree for a reachability argument

```
trivy image --dependency-tree --severity HIGH,CRITICAL \
  ghcr.io/example-org/api:1.4.2
```

Expected: vulnerable packages printed with the chain that pulled them in — evidence for whether the CVE sits in a direct dependency (influenceable) or a deep transitive one.

### Repo scan pinned to a branch/tag/commit

```
trivy repo --branch main https://github.com/example-org/api
trivy repo --tag v1.4.2 https://github.com/example-org/api
trivy repo --commit 0a1b2c3 https://github.com/example-org/api
```

Expected: vuln + secret scan (defaults for the `repo` target) of exactly that ref. Private in-scope repos: export `GITHUB_TOKEN` (or `GITLAB_TOKEN`) — Trivy reads it from the environment, never put it on the command line.

### IaC misconfig, high-signal only

```
trivy config --severity HIGH,CRITICAL --format json -o iac.json ./infra
```

Expected: `.Results[].Misconfigurations[]` with `ID` (AVD), `Title`, `Severity`, `Resolution`, and `CauseMetadata` (file + line). Pair with `--include-non-failures` only when you want to prove a control *is* present.

### SARIF for your own tracking board

```
trivy image --format sarif -o api.sarif ghcr.io/example-org/api:1.4.2
```

Expected: SARIF 2.1.0 you can load into a code-scanning viewer to triage visually. Other `--format` values: `table` (default), `json`, `template` (with `-t`), `cyclonedx`, `spdx`, `spdx-json`, `github`, `cosign-vuln`.

### Scan a saved image tarball offline

```
docker save ghcr.io/example-org/api:1.4.2 -o api.tar
trivy image --input api.tar --offline-scan
```

Expected: scan with no registry pull and `--offline-scan` suppressing external dependency-resolution API calls — useful when you were handed an artifact and want zero outbound traffic beyond the DB.

### Detect drift between a published SBOM and the live image

```
# What the vendor claims ships
trivy sbom --format json -o sbom-claim.json example-org-api-1.4.2.cdx.json
# What the image actually contains
trivy image --format json -o image-actual.json ghcr.io/example-org/api:1.4.2
# Diff the package sets
diff <(jq -r '.Results[].Packages[]?.Name' sbom-claim.json | sort -u) \
     <(jq -r '.Results[].Packages[]?.Name' image-actual.json | sort -u)
```

Expected: packages present in the image but absent from the published SBOM (or vice-versa). Drift is a supply-chain integrity story — the SBOM understating what ships means its consumers' vuln scans miss real packages. Note Trivy warns that SBOMs generated by other tools may scan less accurately, since it relies on its own custom properties.

### CI-style gating / proving a non-empty result set

```
trivy image --severity CRITICAL --ignore-unfixed --exit-code 1 \
  ghcr.io/example-org/api:1.4.2
echo "exit=$?"
```

Expected: exit code `1` when any CRITICAL fixable vuln is found, `0` otherwise — a clean machine-readable "is there anything here" signal for your own scripting. `--exit-on-eol <code>` does the same for an end-of-life base OS (a reportable hardening issue when that OS is still serving in-scope traffic).

### Compliance report (CIS-style posture)

```
trivy image --compliance docker-cis-1.6.0 --report summary \
  ghcr.io/example-org/api:1.4.2
```

Expected: a pass/fail summary against the named built-in compliance spec. `--report` accepts `summary` or `all`. Useful context for a report, but a failed CIS control is hardening unless you can tie it to a live exposure.

### Custom template output for your tracker

```
trivy image --format template \
  -t '{{ range .Results }}{{ range .Vulnerabilities }}{{ .VulnerabilityID }},{{ .PkgName }},{{ .Severity }}
{{ end }}{{ end }}' \
  ghcr.io/example-org/api:1.4.2
```

Expected: a CSV-ish line per vuln driven by Go templating — handy when you want a column layout the built-in formats don't give. `--format template` requires `-t`/`--template`.

### Kubernetes / VM targets (only if the cluster or VM image is in scope)

```
trivy k8s --report summary cluster          # needs a working kube-context
trivy vm --scanners vuln ./disk.vmdk        # scan a VM disk image / snapshot
```

Expected: `k8s` enumerates workloads and summarizes vuln+misconfig per resource; `vm` scans a VM image file (VMDK/qcow2/raw, or a cloud snapshot). Both are in-scope only when the program explicitly lists the cluster or the VM artifact — a cluster you merely have credentials to is not automatically fair game.

### JSON fields worth pulling

The report schema under `.Results[]` carries, per vulnerability: `VulnerabilityID`, `PkgName`, `InstalledVersion`, `FixedVersion`, `Severity`, `SeveritySource`, `VendorSeverity` (map of source→numeric 0–4), `PrimaryURL`, `References`, `CVSS`, and `Status`. Misconfigurations carry `ID` (AVD), `Title`, `Severity`, `Resolution`, and `CauseMetadata` with the file and line. Pull `SeveritySource`/`VendorSeverity` early — they are what expose a nominally-CRITICAL CVE as a vendor-downgraded Low.

### One-pass multi-scanner sweep on a checkout

```
trivy fs --scanners vuln,secret,misconfig --severity HIGH,CRITICAL \
  --format json -o sweep.json ./checkout
```

Expected: vulnerable dependencies, hardcoded secrets, and IaC misconfigs in a single JSON, split across `.Results[]` by `Class` (`os-pkgs`/`lang-pkgs`, `secret`, `config`). This is the efficient first look at an in-scope repo checkout before you decide which thread to pull.

### Scan the image config/history, not just the filesystem

```
trivy image --image-config-scanners misconfig,secret \
  ghcr.io/example-org/api:1.4.2
```

Expected: findings against the image **configuration and build history** (`ENV`/`ARG` lines, metadata) rather than the unpacked filesystem — a distinct supply-chain surface where credentials get baked into build args. `--image-config-scanners` accepts `misconfig` and/or `secret`.

---

## Pitfalls, noise and scope discipline

**Reporting raw scanner output.** The single fastest way to get rejected (and dinged on signal) is filing "Trivy found CVE-XXXX in package Y" with no reachability, no PoC, no impact. Programs see these constantly. A CVE row is a hypothesis; Phase 3 turns it into a finding. If you can't reach it, don't grade it.

**Scanning out-of-scope artifacts.** Pulling and reporting on a third-party base image, a dependency the program merely consumes, or a registry namespace not listed in scope wastes everyone's time. The CVE in `debian:bookworm` is Debian's; the program owns only what it built and exposed. Confirm the artifact's ownership and scope status first.

**Trusting the severity column blindly.** Trivy prefers vendor severity over NVD, and distros downgrade or mark `will_not_fix` when the vulnerable path isn't shipped. A row that says CRITICAL may have `SeveritySource: redhat` and a `VendorSeverity` of Low. Read `SeveritySource`; `--ignore-unfixed` removes most of the won't-fix noise automatically.

**Comprehensive mode without follow-through.** `--detection-priority comprehensive` deliberately adds false positives. If you flip it on and then report everything it emits, you are shipping Trivy's acknowledged false positives as findings. Leave it at `precise` unless you will hand-verify each extra hit.

**Secrets handling.** A real credential in an image layer is sensitive data. Do not exfiltrate it, do not test a cloud key beyond the minimum the program's rules permit, and redact the value in your report. Mishandling a found secret can itself breach the program's rules.

**IaC misconfig as "critical."** Most misconfigs are hardening notes. Severity should reflect *live, reachable* exposure, not the check's nominal rating. Tie it to a concrete attack path or file it as informational.

**Noise from transitive dependencies.** An image can carry hundreds of CVEs in deep transitive packages that are never loaded. `--pkg-relationships root,direct` and `--dependency-tree` keep you focused on what the application actually pulls in. (Note `--pkg-relationships` can't combine with `--dependency-tree`/`--vex`/SBOM generation — run them as separate passes.)

**Dupe risk.** Published vendor VEX documents and the program's known-issue lists exist precisely so you don't re-file accepted risk. Feed them with `--vex` and `--ignorefile` and work only the delta. Not doing this is how a hunter files ten dupes in an afternoon.

**Tool-side volume.** Trivy itself is safe against the target — it reads artifacts, it doesn't probe the service. The only volume to be mindful of is image pulls against a registry (bandwidth) and DB updates; `--skip-db-update` and `--input <tar>` keep repeated runs cheap and quiet.

---

## Reporting evidence

Capture the chain from scanner lead to proven impact — the Trivy output is provenance, the PoC is the finding.

- **The exact command and version.** Record `trivy --version` (e.g. v0.76.2), the full command line including `--severity`/`--ignore-unfixed`/`--detection-priority`, and the DB timestamp. Reproducibility is part of credibility.
- **The scoped artifact identifier.** The precise image digest (`ghcr.io/example-org/api@sha256:…`), repo URL + commit, or SBOM filename — not just a mutable tag. Prove you scanned an in-scope artifact.
- **The JSON record for the specific CVE.** Excerpt the `Vulnerabilities[]` entry: `VulnerabilityID`, `PkgName`, `InstalledVersion`, `FixedVersion`, `Severity`, `SeveritySource`, `PrimaryURL`. This shows you read the source, not just the colored table.
- **The reachability argument.** `--dependency-tree` output or a code pointer showing the vulnerable package is actually reached from application code.
- **The live PoC.** The request/response pair (against `example.com`/TEST-NET, single request, non-destructive) that triggers the CVE on the running target — this is what separates a finding from a scanner dump. Quote expected vs. actual.
- **For secrets:** the file/layer and line (redacted value), proof it's a live credential only to the minimal extent the program allows, and the exposure path.
- **For misconfig:** the AVD `ID`, `CauseMetadata` (file + line), and evidence the misconfigured resource is actually reachable/exposed.
- **Remediation.** The `FixedVersion` (or the misconfig's `Resolution`), framed as the concrete upgrade/config change.

Keep the raw JSON attached as an appendix; triagers can re-run your exact command and land on the same row.

---

## References

- Trivy documentation home — https://trivy.dev/latest/docs/
- `trivy image` CLI reference (flags, defaults, allowed values) — https://trivy.dev/latest/docs/references/configuration/cli/trivy_image/
- Vulnerability scanning (severity sourcing, `SeveritySource`/`VendorSeverity`, detection priority, pkg-types/relationships) — https://trivy.dev/latest/docs/scanner/vulnerability/
- Misconfiguration scanning (IaC types, `trivy config`, `--scanners misconfig`, AVD checks) — https://trivy.dev/latest/docs/scanner/misconfiguration/
- Filtering (`--severity`, `--ignore-unfixed`, `--ignore-status`, `.trivyignore`/`.trivyignore.yaml`, `--ignore-policy`, `--show-suppressed`) — https://trivy.dev/latest/docs/configuration/filtering/
- SBOM scanning (CycloneDX/SPDX/SPDX-JSON input, `trivy sbom`) — https://trivy.dev/latest/docs/target/sbom/
- Repository scanning (`trivy repo`, `--branch`/`--tag`/`--commit`, `GITHUB_TOKEN`) — https://trivy.dev/latest/docs/target/repository/
- VEX support (`--vex` file/repo/oci/sbom-ref, VEX Hub, `trivy vex repo`) — https://trivy.dev/latest/docs/supply-chain/vex/
- VEX Repository specification — https://github.com/aquasecurity/vex-repo-spec
- Trivy releases (authoritative latest version + checksums) — https://github.com/aquasecurity/trivy/releases

*Research supplement added 2026-11-15, grounded in the official Trivy documentation (trivy.dev/latest/docs) and the aquasecurity/trivy GitHub releases page, verified against Trivy v0.76.2 (model: claude-opus-4-8).*
