---
technology: "Google Cloud attack surface (IAM / service accounts / metadata server)"
kind: overview
audience: offensive-security
status: upstream-only
family: cloud
layer: public-cloud control plane — org/project/folder hierarchy under Resource Manager, IAM service-account identities, the Compute metadata server, and Cloud Storage / SQL / KMS / Cloud Functions data planes
homepage: https://cloud.google.com
source: "six2dez/pentest-book (enumeration/cloud/gcp.md + enumeration/cloud/serverless.md, no license — rewritten with attribution), accessed 2026-11-13"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — key techniques verified against Google Cloud docs (VM metadata overview: metadata server reachable without additional authorization, any process on the host reads all values, metadata.google.internal / 169.254.169.254 endpoints; create-and-delete service account keys: JSON key layout, iam.disableServiceAccountKeyCreation enforced by default for orgs created on/after 2024-05-03; service account impersonation: --impersonate-service-account, short-lived tokens, dual-identity audit logs; Cloud Run/Functions security: default service account, run.routes.invoke, allow-public-access IAM bypass) + rewritten from six2dez/pentest-book (enumeration/cloud/gcp.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Google Cloud attack surface (IAM / service accounts / metadata server)

> In GCP nothing has a password — everything is a service account; own one key file or one metadata-server response and you own every project it can touch.

## What it is

This unit covers Google Cloud as an attacker meets it: the `gcloud` CLI against an org → projects → resources hierarchy, IAM as the single authorization fabric, service accounts as the identity primitive, and the metadata server as the credential dispenser inside every Compute-shaped workload. The sibling framing to [[aws]] holds: the surface is the CLI and its credential stores, not consoles. Where AWS chains run through IAM users, roles, and the instance-metadata credential chain, GCP chains run through service-account JSON keys, impersonation permissions, and a metadata server that Google explicitly documents as accessible "without additional authorization" by any process that can query it.

The identity model concentrates risk. A service account is simultaneously a principal (it can be granted roles) and a resource (roles can be granted ON it — `roles/iam.serviceAccountTokenCreator` lets a principal mint its tokens, `iam.serviceAccountKeys.create` mints its long-lived keys). That duality is why GCP privilege escalation is permission-shaped: a single extra IAM binding anywhere on a service account is often a full-takeover primitive. Google's own guidance now treats user-managed JSON keys as a liability — organizations created on or after May 3, 2024 get the `iam.disableServiceAccountKeyCreation` org policy enforced by default — which tells you exactly what the attack path is.

The characteristic mistake is the compute metadata server. It answers at `metadata.google.internal` / `169.254.169.254` from inside any instance, requires only the `Metadata-Flavor: Google` header, and — per Google's security notes — any process that can query it sees *all* values on it: custom metadata, startup scripts, and the default service account's OAuth tokens. An SSRF in any app hosted on GCP, a shell on any VM, or code exec in a Cloud Function is therefore one HTTP request away from the workload's entire credential set.

## The anatomy that matters on an engagement

    get a service-account identity:
      loot → user-managed JSON key files (gcloud auth activation) · ~/.config/gcloud/credentials.db on hosts ·
             ADC files (gcloud auth application-default login) · keys in CI variables and source
      spray/phish → Workspace user (web SSO) · or take a user with impersonation rights
      SSRF/RCE on GCP compute → metadata server token (Metadata-Flavor header)
    → authenticate:  gcloud auth activate-service-account --key-file=key.json
                     gcloud config configurations activate <stolen> · or --impersonate-service-account <sa>
    → enumerate:     gcloud organizations list · projects list / get-iam-policy · iam service-accounts list
                     (keys, describe) · compute instances list (describe → scopes, SA, ssh-keys) ·
                     storage buckets (gsutil) · sql/spanner instances · functions list · kms keyrings
    → escalate (permission → primitive):
                     iam.serviceAccountKeys.create → key for a privileged SA
                     iam.serviceAccountTokenCreator → impersonate a privileged SA (short-lived, dual-identity audit trail)
                     resourcemanager.projects.setIamPolicy → grant yourself roles/owner
                     compute.instances.setMetadata → inject SSH keys · compute.instances.setServiceAccount → re-bind a VM
                     cloudfunctions create/update → deploy code under a privileged SA
    → persist:       fresh SA + editor binding + key · project-level ssh-keys metadata (applies to every instance)
                     redeployed "monitoring" Cloud Function

Three structural properties drive most findings:

- **Service accounts are both principal and resource.** The escalation primitive list is short and enumerable; a `get-iam-policy` read on every SA tells you which one you can already take over.
- **The metadata server is an unauthenticated credential oracle — inside.** `169.254.169.254` with `Metadata-Flavor: Google` returns the default SA's token, its email, its scopes, startup scripts, and SSH keys; defense is network/logical placement, not auth.
- **SSH access is metadata, not a keyfile on disk.** Instance and project metadata blocks (`ssh-keys`) are the authorized-key store; whoever can write metadata — or run `gcloud compute ssh`, which adds a key to project metadata — has a login path to Linux guests. A stolen private key alone does nothing; the matching username must appear in metadata.

## Where you'll meet it

- **Compromised developer boxes and CI runners.** `~/.config/gcloud/` (including other users' home directories — `credentials.db`, named configurations, ADC JSON) is the on-host loot; copying the directory and `gcloud auth list` replays the identity.
- **SSRF in web apps.** Any GCP-hosted app with a fetch primitive: token endpoint via `metadata.google.internal` (header-capable SSRF) — the AWS counterpart at the same IP is covered in [[aws]].
- **Post-exploitation with any IAM read.** `gcloud projects get-iam-policy` and `asset search-all-iam-policies` answer who holds what; `allUsers`/`allAuthenticatedUsers` members mark public surfaces (buckets, functions, services).
- **GKE and data estates.** Cluster credentials via `gcloud container clusters get-credentials` land a kubeconfig for [[kubernetes]]; SQL/Spanner export-to-bucket is the exfil route for database contents; Cloud KMS decrypt unseals what the estate encrypted.
- **Serverless (section below).** Cloud Functions bundle code, environment variables, a service account, and a public-invocation switch into one resource.

## Reading the framework's mind

1. **Scopes cap the default SA, IAM caps everything else.** A VM's access scopes (readable from metadata at `.../instance/service-accounts/default/scopes`) limit the *default* credential's reach regardless of IAM — but any other credential path (keys, impersonation) ignores VM scopes. Enumerate scopes first; they tell you what the free token can do.
2. **`--impersonate-service-account` works on any gcloud verb.** With token-creator rights on a privileged SA, every command can run as it; short-lived tokens (hours, non-refreshing) are the clean path, and audit logs record both your identity and the impersonated one — opsec is a planning input, not an afterthought.
3. **Basic roles are legacy-wide.** `roles/editor` and `roles/owner` (and `roles/iam.serviceAccountKeyAdmin`-adjacent basics) still appear on projects; editor can't change IAM policy in modern GCP, but it can create keys and run compute — treat it as near-owner.
4. **Buckets have two namespaces to find them by.** `gs://<name>` via authenticated listing, and the public `https://storage.googleapis.com/<name>/` HTTP face; uniform bucket-level access modernizes IAM but `allUsers:objectViewer`-style public grants remain the classic leak. Name-guessing by company/permutation is the recon (GCPBucketBrute-class, cloud_enum covers all three clouds).
5. **Snapshots and images are data-plane copies.** `compute snapshots list` → create a new instance from a disk snapshot (`--source-snapshot`) mounts another workload's filesystem without ever authenticating to it — the quietest disk exfil in the platform.

## Serverless: Cloud Functions as a section

A Cloud Function is a service account with code attached. Enumerate with `gcloud functions list` / `describe` — the description carries the runtime, the trigger, the `serviceAccount` email, environment variables (`environmentVariables` in the describe JSON), and the `sourceArchiveUrl` GCS bucket where the code sits (`gsutil cp` it down once you have bucket access). Invocation rights are IAM: `gcloud functions get-iam-policy` shows whether `allUsers` can call; `--allow-unauthenticated` at deploy time is the same public switch (second-generation functions run on Cloud Run, where "allow public access" disables the invoker IAM check). Escalation from any function create/update right: deploy your own function bound to a privileged service account (`--service-account=`), or update an existing one's code — the SA does the rest. From inside a function, the metadata server is the same `metadata.google.internal` token endpoint as any Compute workload. Event-driven variants add an injection face: a Pub/Sub-backed function processes whatever message lands on its topic — publish rights on the topic are input control. AWS Lambda's counterpart enum (`list-functions`, `get-function` code pulls, env-var extraction) is documented in [[aws]].

## Where to go next

The phased methodology — key-file loot, gcloud enumeration, the permission-to-primitive escalation map, metadata SSRF, bucket and function abuse, persistence — is in [[gcp-bug-bounty]].
