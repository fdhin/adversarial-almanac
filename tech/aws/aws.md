---
technology: "AWS-flavored attack surface (S3 / Lambda / LocalStack)"
kind: overview
audience: offensive-security
family: cloud
status: "confirmed (6 writeup files pass the AWS command-context gate — the unit is the CLI-and-endpoint surface, not the console)"
layer: service class — S3-API object storage (real AWS, MinIO, or LocalStack emulation), Lambda function enumeration, emulated AWS services on the LocalStack edge port 4566, and credential loot (~/.aws/credentials, env, git history)
provenance: "corpus-grounded (re-gated 2026-11-12, AWS command-context gate, see internal coverage ledger; owner-approved as 'aws (localstack/S3/Lambda)') + upstream-verified: LocalStack documentation (edge port 4566, awslocal wrapper, no enforced auth by default), GHSA/NVD CVE-2021-32090 (LocalStack dashboard command injection, <0.12.10, fixed 0.12.10, Sonar write-up), AWS CLI docs (--endpoint-url, credential chain, AKIA/ASIA key prefixes), MinIO docs (S3-compatible API), 2026-11-12."
---

# AWS-flavored attack surface (S3 / Lambda / LocalStack)

> Half the "AWS" you will meet on a box is not Amazon at all — it is an emulator that answers the same API with no authentication, on one port, next to the real credentials in a dotfile.

## What it is

This unit covers the AWS attack surface you reach with a CLI, not a console: object storage speaking the S3 API (whether backed by Amazon, by MinIO, or by an emulator), Lambda functions whose code can be enumerated and pulled, and LocalStack — the emulator teams run to develop "in the cloud" without the cloud. The unifying observation from live chains: the client tooling is identical everywhere (`aws` CLI / `awslocal` wrapper / boto3), the protocol is signature-shaped but the *enforcement* varies wildly. LocalStack by default enforces no security policy at all — any dummy access key and secret are accepted — and deployments still leak into production networks on its single edge port, 4566.

The operator's mental model: wherever an engagement box talks S3-or-Lambda, three questions decide the chain. *What endpoint is the client really hitting* (`--endpoint-url` and config files redirect the CLI to emulators and self-hosted MinIOs)? *What does the endpoint accept as auth* (real SigV4 keys, or anything)? *Where do the credentials live* (`~/.aws/credentials`, process environment, application source, git history)? Answer those and the rest is mechanical — list buckets, sync objects, pull Lambda function code, query emulated services for secrets other tooling forgot about.

The design's characteristic mistake is believing "it's not real AWS, so it's not real exposure." An S3 bucket that fronts a website accepts uploads that land in the web root; an emulated CloudWatch holds the same secrets as the real one because the same code wrote them; an emulated Lambda executor on old versions ran shell commands built from a URL parameter (upstream: CVE-2021-32090, fixed in 0.12.10). The cloud-shaped corner of a network is often its softest.

## The anatomy that matters on an engagement

    find the AWS-flavored surface:
      nmap: 4566/tcp (LocalStack edge), 9000-class or odd high ports answering XML + x-amz headers (S3 API)
      subdomains like s3.<target> answering JSON status / x-amz-* response headers
      on-host loot: ~/.aws/credentials, ~/.aws/config, AWS_* in env, boto3 Session(...) in app source
    → aim the CLI at it:  aws --endpoint-url=http://<host>[:port] <service> <verb>
       (emulators: aws configure with any dummy keypair; MinIO: creds from the app that owns it)
    → enumerate: s3 ls / s3api list-buckets / lambda list-functions + get-function
    → act: sync bucket objects (keys, DB dumps) · drop a webshell into a web-served bucket ·
           pull the function zip (Code.Location) and read its secrets ·
           query emulated CloudWatch/SecretsManager for other tools' leftovers

Three structural properties drive most findings:

- **The endpoint is only as real as its auth.** Real AWS rejects bad keys instantly; emulators accept anything, so `aws configure` with garbage is itself a fingerprint — and a free pass.
- **S3-as-web-root turns uploads into RCE.** When a site's files ARE a bucket (images loading from `s3.<target>`, an index.html in the listing), a dropped PHP/webshell lands in the document root — object storage permissions become webshell permissions.
- **Lambda code is exfiltratable source.** `lambda get-function` returns a `Code.Location` URL for the function's zip; the handler source routinely holds secrets (JWT signing keys, DB passwords) reused elsewhere.

## Where you'll meet it

- **LocalStack in dev/staging networks** — docker-compose files expose it with mapped sockets (including the host docker socket); the dashboard-era versions carry command injection (CVE-2021-32090), and the container often mounts more than it should.
- **S3-backed websites and internal MinIO deployments** — a subdomain serving `x-amz-version-id` headers or a `Server: MinIO` banner on an odd port; creds come from the owning app (mass-assignment bugs, config files) and the prize is object loot or webroot upload.
- **Cloud-mimicking engagement hosts** — a box whose hostname says `aws`, whose `~/.aws/credentials` and environment hold real-looking keys, and whose 4566 port is the same emulator; the keys enumerate the buckets that serve the site.
- **Credentialed post-exploitation** — with a shell, the credential file and the git history are the fastest paths to cloud-shaped loot (one chain recovered long-lived keys from an old commit and pulled the Lambda source straight after).

## Reading the framework's mind

1. **4566/tcp open is a LocalStack tell** — the modern single edge port (older releases used per-service ports); `awslocal` on the host confirms it, and `aws --endpoint-url=http://127.0.0.1:4566` with dummy keys proves the no-auth posture.
2. **x-amz-* response headers (version-id, request-id) mean S3 API regardless of vendor** — MinIO answers with them too, on whatever port it was given.
3. **Key prefixes classify loot:** `AKIA…` long-lived access keys, `ASIA…` temporary (session) credentials — the prefix tells you whether you stole a permanent door or a timed one.
4. **`get-function`'s Code.Location hands over the source zip** — enumerate first (`list-functions`), then read handler code for secrets; the emulators serve the same shape.
5. **Emulated services hold real secrets** — CloudWatch log groups and SecretsManager entries behind 4566 were written by real automation (rotate/restore-style events leak values into logs) — enumerate them like any secret store.

## Where to go next

[[aws-bug-bounty]] — the phased agent methodology: endpoint discovery and fingerprinting, CLI aiming with `--endpoint-url`, S3 enumeration and webroot-upload chains, Lambda code pulls, LocalStack abuse (logs/secrets/lambda exec, CVE-2021-32090), and credential-file loot.
