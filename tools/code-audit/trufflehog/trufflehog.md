---
name: "trufflehog"
categories:
  - blackarch-code-audit
version: "v3.99.7.r0.g31ce31ee1"
homepage: "https://github.com/dxa4481/truffleHog"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: go
license: GPL2
meta-checked: "2026-11-09"
github-repo: "dxa4481/truffleHog"
github-stars: 28928
github-pushed: "2026-11-05"
github-archived: false
github-issues: 599
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 466
gh-activity-checked: "2026-11-09"
---

# trufflehog

> Secret-scanning engine that digs through git history (and filesystems, images, object stores, SaaS apps), classifies 800+ credential types and verifies live ones against the issuing service before reporting.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding credentials that were accidentally committed — including ones deleted later, buried in old branches, or hiding in a source you would not think to grep. A single Go binary with one subcommand per source (`git`, `github`, `filesystem`, `docker`, `s3`, `gcs`, `jenkins`, `elasticsearch`, `postman`, `stdin`, ...). Findings are classified by detector; `--results=verified` restricts output to secrets TruffleHog has actually logged in with to confirm they are live, which is what makes scans actionable instead of noisy. The current binary replaces the legacy Python `truffleHog` interface (`trufflehog --regex --entropy=False <url>`) — old recipes with those top-level flags no longer apply.

## Typical use cases

### Scan a remote git repository for verified secrets only

```
trufflehog git https://github.com/example/example-repo --results=verified
```

Outcome: the repository is cloned and its full commit history and branches walked; each verified finding prints detector type, decoder, the raw secret, file, line, commit hash, author email and timestamp. Add `--json` for machine-readable output or `--sarif` for GitHub code scanning.

### Scan an already-cloned local repository

```
trufflehog git file://. --results=verified,unknown
```

Outcome: scans the repo in the current directory, including objects unreachable from current branches. Local repos are first cloned to a temporary directory to neutralize malicious git configs (`--clone-path` chooses where; `--trust-local-git-config` skips the copy for trusted repos). `unknown` adds findings that could not be verified.

### Scan plain files and directories without git

```
trufflehog filesystem /srv/app
```

Outcome: each listed file and directory is scanned for known secret formats; output and filters are identical to the git scans, so this is the right subcommand for config bundles, backups and exported trees.

### Sweep an entire GitHub organization

```
trufflehog github --org=example-org --results=verified --exclude-archived
```

Outcome: enumerates the organization's repositories and scans each for live credentials, skipping archived repos; add `--issue-comments --pr-comments` to include issue and pull-request discussion where keys often get pasted.

### Gate a pipeline on new findings

```
trufflehog git file://. --since-commit main --branch feature-1 --results=verified,unknown --fail
```

Outcome: only the commits on `feature-1` that are not reachable from `main` are scanned, and the process exits with code 183 when valid credentials are found — wire it into CI so a leaked key fails the build instead of shipping.

---

## Bug bounty workflow with trufflehog

Secret scanning is the one recon activity where a successful finding can walk you straight out of scope. Every other tool in this vault touches *the target's* surface; TruffleHog's verification step touches **the issuing service of whatever key it found** — which may be a third-party cloud account, a customer's tenant, or a vendor SaaS the program does not own. Treat discovery and verification as two different authorizations, and never let the tool's convenience collapse them.

### Phase 0 — Authorization and the scope gate (before any scan)

Answer three questions in writing before the first command:

1. **Is the GitHub org/repo in scope?** Only scan orgs, repos, images, and buckets the program explicitly lists. A repo being *public* is not consent to scan it under a program's rules — scope is defined by the program, not by GitHub's visibility setting.
2. **Does the ROE permit verification?** Discovery (reading commits, matching patterns) is passive and never contacts a credential's provider. Verification *logs in* — it sends an authenticated request to the live service behind the key. If the key unlocks a system the program does not own, verifying it can be unauthorized access to a third party, regardless of your bounty scope. When in doubt, run discovery only (`--no-verification`) and report the candidate unverified, stating that you did not exercise the credential.
3. **Who owns the key you expect to find?** A leaked AWS key in the program's repo may belong to the program, a customer, or a partner. You cannot know until you look at the finding — so plan to triage ownership *before* deciding whether verification is in-bounds.

The default posture for a cautious first pass:

```
trufflehog github --org=example-org --no-verification --results=verified,unknown,unverified
```

`--no-verification` guarantees nothing is sent to any provider; you get an inventory of candidate secrets labelled `unverified`. Nothing here leaves your machine except the GitHub API reads needed to enumerate the org.

### Phase 1 — Map the in-scope surface to the right source

TruffleHog has one subcommand per source; pick the one that matches what the program authorized, not the broadest one available.

| Program scope | Subcommand | Notes |
|---|---|---|
| A whole GitHub org | `github --org=example-org` | enumerates + scans every repo; add `--exclude-archived` to skip dead code |
| Named repos only | `github --repo=https://github.com/example-org/app` (repeatable) or `git https://...` | stay inside the listed repos — do not let `--org` pull siblings out of scope |
| A repo you cloned | `git file://./app` | local clone, offline, full history |
| Delivered artifacts (configs, backups, exported trees) | `filesystem ./artifact` | no git required |
| In-scope container images | `docker --image example-org/app:latest` | walks image layers where build-time secrets hide |
| In-scope object storage | `s3 --bucket=example-bucket` | narrow with `--include-prefix`/`--exclude-extension` |
| CI/SaaS the program owns | `circleci`, `travisci`, `jenkins --url=...`, `postman`, `elasticsearch` | each needs its own credential; scanning a shared SaaS tenant can spill outside scope |

Authenticate GitHub with a **dedicated, minimal-scope personal access token** passed via `--token` — unauthenticated scans are heavily rate-limited, and reusing your primary token risks it ending up in your own logs. A read-only token is enough for public repos.

### Phase 2 — Discovery pass across history (no provider contact)

By default the `git`/`github` sources walk the **full commit history and all branches**, including objects unreachable from current tips — which is the entire point, since the highest-value keys are the ones deleted in a later commit and assumed gone. Nothing in this phase contacts a provider.

```
# Whole org, history + PR/issue discussion, no verification
trufflehog github --org=example-org --token="$PAT" \
  --issue-comments --pr-comments \
  --no-verification --results=verified,unknown,unverified --json > candidates.json
```

`--issue-comments` and `--pr-comments` reach the discussion threads where developers paste keys "just to debug" and never scrub them — a surface most hunters skip, which lowers dupe risk. The output is a candidate list, every entry `unverified`.

For a local repo, scan the copy TruffleHog makes rather than the repo in place:

```
trufflehog git file://./app --no-verification --results=verified,unknown,unverified
```

Local `file://` repos are first cloned to a temp directory to neutralize a malicious `core.fsmonitor`/hooks config in an untrusted repo (the fix for **CVE-2025-41390**); `--clone-path` chooses where. Only add `--trust-local-git-config` for a repo you trust — it skips the protective copy.

### Phase 3 — Verification pass (only when the ROE allows touching the provider)

If and only if Phase 0 cleared it, re-run for verification:

```
trufflehog github --org=example-org --token="$PAT" --results=verified
```

`--results=verified` restricts output to secrets TruffleHog confirmed are **live** by making a request to the issuing service. This is what turns a wall of maybe-secrets into a short list of real, exploitable credentials — the single most valuable thing the tool does for a report.

Understand exactly what "verify" does per detector before you run it (see *Pitfalls* below for the safe-vs-state-changing split). For the common cloud detectors the verification request is a read-only identity call — the AWS detector, for example, calls `GetCallerIdentity`, which returns the key's own account/principal and changes nothing. But TruffleHog ships 800+ detectors and publishes no per-detector guarantee that every verifier is non-mutating, so for anything exotic, confirm by reading that detector's source under `pkg/detectors/` before you let it fire. When you only trust a subset, scope verification with `--include-detectors` / `--exclude-detectors` (IDs or protobuf names; excludes win over includes) and keep the rest discovery-only.

### Phase 4 — Triage: live, owned, and in-scope

A verified key is not automatically a valid report. For each one:

- **Confirm it is live, not just well-formed.** The `verified` label already means this; a `unverified` result with `--no-verification` off means TruffleHog tried and the key failed (dead/rotated/expired) — do not report those as live.
- **Confirm the owner is the program.** Read the principal/account returned by verification (ARN, token owner, project ID). A key that resolves to a *customer's* account or an unrelated third party is a disclosure to make carefully, often through the program's defined process, and not something to probe further.
- **Establish scope/impact without "using" the key.** A single identity-level verification is your evidence that the key is live. Resist escalating to data access. TruffleHog's `analyze` subcommand enumerates a credential's fine-grained permissions and the resources it can reach — that is far more invasive than verification and effectively *exercises* the key; run it only with explicit written permission for that account, never as a reflex.

### Phase 5 — Minimal PoC and report

Capture the finding line, the commit that introduced it, and the verification outcome — then stop. The proof that a key is live is the verified status plus the identity the provider echoed back (account ID / token owner), **not** a dump of the data it unlocks. Over-collection here is the fastest way to turn a clean High into a scope violation. See *Reporting evidence*.

---

## Where the money is with trufflehog

Priority is a product of severity × how likely the key is still live × how likely you are the first to report it. Public-repo leaks are heavily deduped by GitHub's own push-protection and other hunters; the freshest value is in less-trodden corners (comments, image layers, buckets, recently pushed branches).

| Priority | Finding class | Feature/flag that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Verified cloud provider key (AWS/GCP/Azure) in repo or history | `github --org` / `git` + `--results=verified` | Critical | High if public; low in private/internal repos |
| 2 | Verified DB / cache connection string (Postgres, Mongo, Redis) with a routable host | default detectors + `--results=verified` | High–Critical | Medium |
| 3 | Verified SaaS token with blast radius (Slack, Stripe, SendGrid, Twilio, GitHub PAT) | `--results=verified` | High | Medium |
| 4 | Verified secret in **issue/PR comments** | `--issue-comments --pr-comments` | High | **Low** — most hunters never enable these |
| 5 | Verified private SSH / TLS / signing key | default private-key detectors | High | Medium |
| 6 | Build-time secret baked into a container image layer | `docker --image` | High | Low |
| 7 | Secret in an exposed object-store bucket | `s3 --bucket` | High | Low |
| 8 | `.env` / credentials file in a delivered artifact or backup | `filesystem` | Medium–High | Low |
| 9 | Fresh key on a recently pushed branch (diff only) | `git --since-commit <base> --branch <feat>` | High | **Low** — newest, least-reviewed code |
| 10 | High-entropy unverified candidate in a private/internal repo | `--no-verification` + manual triage | Low–Medium | High (noisy) |

The two rows that consistently beat the field are **#4** (comments) and **#9** (diff of new branches): both reach code the automated push-protection scanners and other hunters tend to miss, and both keep your request volume tiny.

---

## Command recipes

Annotated commands. Every target uses `example-org` / `example.com`. "Low volume" for a secret scanner mostly means *how many live provider calls you emit during verification* and *how hard you hammer the GitHub API* — not request floods at the target web app.

**Passive org inventory (zero provider contact):**

```
trufflehog github --org=example-org --token="$PAT" \
  --no-verification --results=verified,unknown,unverified --json > candidates.json
# -> JSON lines; every result SourceMetadata carries repo, file, line, commit,
#    author email + timestamp. Verification is OFF, so all are "unverified".
```

Use this first on any new program. It never logs into a provider, so it cannot overstep scope.

**Verified-only org sweep (after ROE clears verification):**

```
trufflehog github --org=example-org --token="$PAT" --results=verified
# -> only live credentials print: detector type, decoder, raw secret,
#    file:line, commit hash, author. Each line = one confirmed-live key.
```

**Diff a feature branch — newest code, smallest footprint:**

```
trufflehog git https://github.com/example-org/app \
  --since-commit main --branch feature-1 --results=verified,unknown
# -> scans only commits on feature-1 not reachable from main.
```

**Gate CI on leaks (exit 183 on findings):**

```
trufflehog git file://. --since-commit main --branch HEAD \
  --results=verified --fail --no-update
# -> exit 0 = clean, 183 = verified secret found, 1 = scan error.
#    --branch HEAD suits a CI checkout; --no-update skips the self-update check.
```

**Scan an in-scope container image:**

```
trufflehog docker --image example-org/app:latest --results=verified
# docker://image:tag for the local daemon, file://image.tar for an exported layer.
```

**Scan a delivered artifact / config bundle:**

```
trufflehog filesystem ./delivered-bundle --results=verified,unknown
```

**Pipe arbitrary data through stdin:**

```
aws s3 cp s3://example-bucket/dump.gz - | gunzip -c | trufflehog stdin --no-verification
```

**Cut false-positive noise on an unverified pass:**

```
trufflehog filesystem ./app --no-verification \
  --filter-entropy 3.0 --filter-unverified --exclude-detectors=generic
# --filter-entropy drops low-entropy unverified matches (start at 3.0);
# --filter-unverified keeps only the first unverified hit per chunk per detector.
```

**SARIF for GitHub code scanning (verification off for a clean artifact):**

```
trufflehog filesystem . --sarif --no-verification > results.sarif
# SARIF is buffered in memory and written at the end.
```

**Custom regex detector with a verification webhook** — passed via `--config`. The YAML lives under a top-level `detectors:` list; `keywords` gate the regex search, each named `regex` must match (first capture group is the reported value), and an optional `verify` block POSTs the matches to your endpoint:

```yaml
detectors:
  - name: AcmeInternalToken
    keywords:
      - acme
    regex:
      token: 'acme_[A-Za-z0-9]{32}'
    entropy: 3
    exclude_words:
      - example
    verify:
      - endpoint: https://verifier.example.com/check
        unsafe: false            # must be true if endpoint is plain HTTP
        headers:
          - "Authorization: Bearer REDACTED"
        successRanges:
          - "200-202"            # these statuses mean "live"
```

```
trufflehog filesystem ./app --config=acme-detectors.yaml --results=verified
```

A `200 OK` (or a status in `successRanges`) from the webhook marks the match verified; a network/API error yields `unknown`; omit the whole `verify` block and matches stay `unverified`. Use `--custom-verifiers-only` to run *only* your endpoints, or `--verifier` to override a built-in detector's verification URL — useful when you want verification traffic to hit a lab endpoint you control instead of a live provider.

---

## Tuning: result statuses, detectors, archives, and git depth

**The four result statuses.** `--results` takes any comma-separated subset of: `verified` (confirmed valid by an API call), `unverified` (detected but not verified), `unknown` (verification was attempted but failed on a network/API error — *not* the same as dead), and `filtered_unverified` (an unverified result that a filter would otherwise have dropped, surfaced so you can audit your filters). The default is `verified,unverified,unknown`. For a clean actionable report, `--results=verified`; for an inventory, add `unverified,unknown`; add `filtered_unverified` only when you suspect a real key is being filtered out.

Treat `unknown` with care: a key that errors on verification (rate-limited provider, network hiccup, an endpoint that moved) is reported `unknown`, and re-running later may flip it to `verified`. Do not report an `unknown` as dead.

**Scoping detectors.** `--include-detectors` / `--exclude-detectors` accept comma-separated detector type names, protobuf names, numeric IDs, and ID ranges; `--exclude-detectors` wins when an ID appears in both. Narrow to the providers that matter on a given target to cut both noise and verification traffic:

```
# Only look for (and verify) AWS + GitHub tokens
trufflehog github --org=example-org --token="$PAT" \
  --include-detectors="aws,github" --results=verified

# Everything except the noisy generic detector
trufflehog filesystem ./app --exclude-detectors="generic" --no-verification
```

Two verification knobs matter when you do verify: `--no-verification-cache` forces a fresh check instead of reusing a cached verification result (useful when a key may have just been rotated), and `--allow-verification-overlap` lets similar-looking credentials be verified across multiple detectors rather than being claimed by the first match — turn it on when a value could be valid for more than one provider and you need to know which.

**Archives and binaries.** TruffleHog unpacks archives and reads some binaries by default. On a large artifact this is where scans blow up in time and memory, and where a hostile archive (a zip bomb) can bite:

```
trufflehog filesystem ./bundle \
  --archive-max-size=250MB --archive-max-depth=5 --archive-timeout=60s
```

`--archive-max-size` (units like `512B`, `2KB`, `4MB`), `--archive-max-depth`, and `--archive-timeout` bound the work. `--force-skip-archives` and `--force-skip-binaries` skip those inputs entirely — the right call when the program only cares about source text, and a cheap mitigation against a malicious nested archive in an untrusted bundle. `--detector-timeout` caps how long any single detector may run (e.g. `30s`), which also defuses a pathological-input stall.

**Git history depth — what actually gets walked.** For the `git` and `github` sources the default is the *entire* reachable history across all branches, and TruffleHog deliberately reaches objects unreachable from current tips — which is the whole value, because the keys worth finding are the ones a later commit "removed." Control it with:

- `--since-commit <ref>` — scan only commits not reachable from `<ref>`; the diff-mode primitive for "what did this branch/PR add."
- `--branch <name>` — restrict to one branch (`--branch HEAD` for an already-checked-out CI workspace).
- `--skip-additional-refs` — skip refs beyond normal branches (e.g. GitHub's `refs/pull/*` PR refs), which trims a huge amount of work on busy repos when you only care about mainline history.

Pairing `--since-commit` with `--branch` is the low-footprint pattern from Phase 2: you scan a few new commits instead of re-walking years of history, which is both faster and far less likely to re-surface already-reported public leaks.

**github-experimental — the deleted-object hunter.** For a single repo, `github-experimental --repo=<url> --object-discovery` performs an alpha-stage deeper enumeration that can recover objects ordinary history-walking misses (force-pushed-over and dangling commits); `--delete-cached-data` cleans its cache afterward. It is explicitly alpha and slower — reserve it for a high-value in-scope repo where you suspect a secret was force-pushed away, not for a broad org sweep.

**One config for several assets — `multi-scan`.** When a program lists repos *and* buckets *and* a Postman workspace, the `multi-scan` source reads a config file whose `sources:` list defines each one (entries carry a `'@type'`/`type` such as `SOURCE_TYPE_GITHUB` and a per-source `verify: true`; `debug: true` turns on logs for one source) and scans them in a single run. It keeps the exact in-scope asset list in a reviewable file rather than scattered across shell history — useful for reproducibility and for proving to a triager precisely what you scanned. The same `--config` file can also carry your custom `detectors:`.

**Non-git in-scope assets.** Programs increasingly list cloud buckets, container registries, and SaaS workspaces alongside repos. Each has a dedicated source and its own scoping flags — use them to stay inside exactly what was authorized:

- `s3 --bucket=example-bucket` — scan a named bucket; `--role-arn` (repeatable) assumes a role for cross-account access, and `--include-prefix` / `--exclude-prefix` / `--include-extension` / `--exclude-extension` narrow the key space. The include/exclude extension pair cannot be combined with each other, and `--bucket` cannot be combined with `--ignore-bucket`. Prefix filters are how you keep a scan inside the one path the program scoped rather than the whole bucket.
- `gcs --project-id=<id>` — Google Cloud Storage; `--cloud-environment` selects how it authenticates.
- `elasticsearch --nodes=... ` — authenticate with `--username`/`--password`, `--api-key`, `--service-token`, or `--cloud-id`; scans index contents for secrets a program's logging stack may have captured.
- `huggingface --model=<id>` / `--dataset=<id>` / `--space=<id>` (or `--org` / `--user`) — ML artifacts; `--include-discussions` and `--include-prs` reach the comment surfaces, the HF analogue of `--issue-comments`. `--skip-all-models`/`--skip-all-datasets`/`--skip-all-spaces` and the `--ignore-*` flags trim what gets walked.
- `postman --workspace-id=<id>` / `--collection-id=<id>` — Postman collections and environments are a notorious hiding place for live API keys; needs `--token`.
- `jenkins --url=<url>` (with `--username`/`--password`), `circleci --token=...`, `travisci --token=...` — CI systems the program owns, where build secrets and job logs live.

For every one of these, the scope rule from Phase 0 still governs: a shared SaaS tenant or a bucket with mixed ownership can hold other customers' secrets, so narrow with the prefix/collection/index flags and verify only keys you have confirmed belong to the program.

---

## Pitfalls, noise and scope discipline

**Verification is an authenticated request to someone else's system.** This is the pitfall that gets secret hunters into real trouble, not just rejected. When TruffleHog verifies an AWS key it calls `GetCallerIdentity`; when it verifies a SaaS token it calls that SaaS's API. If the key belongs to a third party, you have just made an authenticated request to a system you were never authorized to touch — a scope violation at best, a CFAA-style problem at worst. **Run `--no-verification` whenever you are unsure who owns the key**, and only verify once you know the issuing account is the program's.

**Safe vs. state-changing verifiers.** TruffleHog's verifiers are *designed* to be minimal, read-only identity/metadata checks (`GetCallerIdentity`, `GET /user`, token-introspection calls), and for mainstream cloud detectors they are. But there is no published, per-detector guarantee across all 800+ detectors, and a verifier that has to *attempt an action* to prove a key works is conceivable. Before verifying an unusual credential type, read its verifier in `pkg/detectors/<name>/` and confirm it only reads. For your own custom detectors, the verify webhook you point at is entirely your responsibility — `unsafe: true` (plain-HTTP endpoints) sends the matched secret over cleartext, so keep it `false` and HTTPS unless the endpoint is on localhost.

**`analyze` is not verification.** `trufflehog analyze` enumerates a key's permissions and the resources it can reach — it exercises the credential far beyond a liveness check. Treat it as active exploitation: explicit written permission for that specific account only.

**Verification shows up in the owner's logs.** Any authenticated API call lands in the provider's audit trail (CloudTrail, GitHub audit log, etc.). A burst of verification calls can trip the owner's anomaly detection and burn your element of discretion — another reason to inventory with `--no-verification` first and verify a small, deliberate set second.

**Scope creep via `--org`.** `github --org` scans *every* repo the org exposes, which may include repos the program did not list. If scope is a named-repo subset, use `--repo` (repeatable) and stay inside it. Archived repos are often explicitly out of scope — `--exclude-archived` keeps you clean and cuts noise.

**Public-repo leaks are heavily deduped.** GitHub push-protection, the provider's own secret scanning, and every other hunter have usually seen a public leak already. Expect "duplicate" or "informative" on obvious public finds; spend your effort on comments (`--issue-comments`/`--pr-comments`), image layers, buckets, and fresh branch diffs where you are more likely first.

**Reporting an unverified string as if it were live.** An `unverified` result means TruffleHog could *not* confirm the key works — it may be a rotated, fake, or example value. Never claim impact you have not verified. If the ROE forbids verification, say so and report the candidate honestly as "detected, not verified," with the reasoning for why it is likely live (location, format, surrounding code).

**Never scrub someone else's leak by exercising it.** Do not rotate, delete, or "test the blast radius" of a key you found. Report it; let the owner rotate.

**Legacy-flag confusion.** The current Go binary is not the old Python `truffleHog` (`--regex --entropy=False <url>`); those top-level flags no longer exist. Likewise `--results=verified` replaced the older `--only-verified` phrasing — use `--results`. And `trufflehog:ignore` on a secret's line suppresses it (handy for intentional test keys in your own PoC repos); `--no-ignore-tag` forces those back into the output.

---

## Reporting evidence

The report line for a secret finding is: **a verified-live key, proof of liveness from a safe verification, and the minimal context that lets the triager reproduce — nothing exfiltrated.**

Capture, per finding:

1. **The TruffleHog result block**, redacting the secret down to a non-usable prefix in the written report (keep the full value only in the secure channel the program designates). The block already carries detector type, decoder, `file:line`, commit hash, and author email/timestamp — all the provenance a triager needs.
2. **The introducing commit.** `git log -1 <hash>` and, where it clarifies impact, which branches/tags still contain it and whether it was "removed" in a later commit (a deleted-but-reachable secret is the classic high-severity case — it is still in history).
3. **Verification evidence that is safe to show.** The `verified` status plus the identity the provider echoed back — e.g. the account ID / ARN from `GetCallerIdentity`, or the token-owner handle — proves the key is live *without* demonstrating any data access. This is the single most persuasive line and the safest. State the exact verification method ("TruffleHog's built-in detector performed a read-only `GetCallerIdentity` call; it returned account `1234...`, confirming the key is active").
4. **Ownership determination.** State explicitly that the key resolves to the program's own account (and how you know), so the triager need not worry you touched a third party.
5. **Scope/impact, reasoned not demonstrated.** Describe what the key *could* access from its type and (if permitted and determined) its principal — do not paste exfiltrated records.
6. **Scan reproduction.** The exact command (minus your token), so the triager can re-run it: e.g. `trufflehog github --repo=https://github.com/example-org/app --results=verified`.
7. **Remediation.** Rotate the key immediately, purge it from history (`git filter-repo`/BFG) understanding that history rewriting does not un-leak it, and add push-protection/pre-commit scanning. Note that rotation is urgent *because* you confirmed it live.

Severity framing: verified cloud or database credential with broad access — Critical; verified scoped SaaS token or private key — High; unverified high-entropy candidate in a sensitive location — Low/Medium pending the owner's verification. Always lead the title with impact and the location: "Live AWS access key for the production account committed to `example-org/app` history (`config/old.env`, commit `a1b2c3d`)."

### Worked example — a verified AWS key, start to report

A `--results=verified` sweep prints a block like this (secret redacted here; the real scan shows it in full):

```
Found verified result 🐷🔑
Detector Type: AWS
Decoder Type: PLAIN
Raw result: AKIAEXAMPLE...REDACTED
Account: 123456789012
Repository: https://github.com/example-org/app
File: config/old.env
Line: 14
Commit: a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0
Timestamp: 2026-03-02 11:40:18 +0000
Email: dev@example.com
```

The `Account: 123456789012` line is the safe verification evidence: it is what `GetCallerIdentity` returned, proving the key is live and naming the account it belongs to — without reading one byte of the account's data. The triage steps:

1. **Confirm ownership** — `123456789012` is the program's published production account, so verification stayed in scope; note that in the report.
2. **Confirm it is still in history** — `git log --all --oneline | grep a1b2c3d` (and whether a later commit "removed" the file; it is still recoverable, which is the point).
3. **Stop.** Do not call any other AWS API with the key. The verified status plus the account ID is a complete, safe PoC.

The resulting report line: *"TruffleHog's AWS detector performed a read-only `GetCallerIdentity` call against the key committed at `config/old.env:14` (commit `a1b2c3d`) and received account `123456789012` — the program's production account — confirming the key is active. No further API calls were made. Recommend immediate rotation and history purge."* That single sentence carries impact, reproducibility, safety, and remediation — which is the whole report.

---

## References

- TruffleHog README (sources, flags, exit codes, result statuses, verification, custom-detector overview) — https://github.com/trufflesecurity/trufflehog
- TruffleHog README (raw) — https://raw.githubusercontent.com/trufflesecurity/trufflehog/main/README.md
- Custom detectors spec (YAML schema: `keywords`, `regex`, `verify`/`endpoint`/`unsafe`/`headers`/`successRanges`/`rotatedRanges`, `entropy`, `exclude_words`, `exclude_regexes_match`, `validations`) — https://github.com/trufflesecurity/trufflehog/blob/main/pkg/custom_detectors/CUSTOM_DETECTORS.md
- Example custom-detector config with filters — https://github.com/trufflesecurity/trufflehog/blob/main/examples/generic_with_filters.yml
- Introducing TruffleHog v3 (verification model, three result statuses) — https://trufflesecurity.com/blog/introducing-trufflehog-v3
- CVE-2025-41390 (local `file://` repo scanned without the protective temp clone when `--trust-local-git-config` is set) — https://github.com/trufflesecurity/trufflehog/security/advisories

*Research supplement added 2026-11-15, grounded in the TruffleHog README and CUSTOM_DETECTORS.md on github.com/trufflesecurity/trufflehog (fetched 2026-11-15) and the Truffle Security v3 announcement; flags verified against the binary's `--help` output as reproduced in the README (model: claude-opus-4-8).*
