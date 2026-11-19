---
name: "adaptix-serverless-c2"
categories:
  - backdoor
version: "git (2026-10-29)"
homepage: "https://github.com/stillbigjosh/adaptix-serverless-c2"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "stillbigjosh/adaptix-serverless-c2"
github-stars: 33
github-pushed: "2026-10-29"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# adaptix-serverless-c2

> Serverless C2 transport for AdaptixC2 — relays the Kharon agent's traffic through an AWS Lambda Function URL and DynamoDB so all C2 looks like outbound HTTPS to AWS, with no inbound ports on the teamserver.

## What it's for

A listener plugin (plus deployment tooling) that hides an AdaptixC2 v1.2 teamserver behind managed AWS infrastructure. The bundled Kharon implant checks in over HTTPS to a stateless Lambda relay, which stores agent data in a DynamoDB inbound table and polls an outbound table (up to 8 s) for the teamserver's replies; the plugin inside AdaptixC2 polls the same tables every 5 seconds, parses the LokyCrypt-encrypted Kharon wire protocol and queues operator commands. The OPSEC property is direction and destination: agent traffic is outbound-only to `*.lambda-url.*.on.aws`, the teamserver opens nothing publicly, DynamoDB records expire via TTL, and CloudWatch logs are KMS-encrypted. Costs and caveats: the Lambda poll loop adds 0–8 s latency per response, the raw AWS domain is conspicuous unless fronted with a custom domain via CloudFront, and it is a plugin — it needs an existing AdaptixC2 v1.2 install, Terraform ≥ 1.5 and an AWS account; it is not a standalone C2 framework.

## Typical use cases

### Stand up the serverless relay in AWS

Provision the Lambda and DynamoDB hop infrastructure.

```
./scripts/build_relay.sh && ./scripts/deploy_infra.sh
```

Outcome: the relay binary is cross-compiled for Amazon Linux, then Terraform creates the Lambda Function URL relay, the `adaptix-inbound`/`adaptix-outbound` DynamoDB tables (24 h TTL), IAM roles and KMS key; the output ends with the `lambda_function_url` to paste into the listener configuration. `relay_api_key` in `deploy/aws/terraform.tfvars` sets the shared key both sides must present.

### Register the listener with the teamserver

Install the plugin into an existing AdaptixC2 v1.2 installation.

```
./scripts/install.sh /path/to/AdaptixC2
```

Outcome: the script backs up `dist/`, patches the AdaptixC2 source for the new `BeaconServerless` connector, builds the listener `.so` with GOEXPERIMENT flags matching the server binary, rebuilds and restarts the service. Afterwards create a `BeaconServerless` listener in the web UI, point **Lambda URL** at the Terraform output, set **Relay API Key** to the Terraform value, upload the malleable profile `profiles/lambda_default.json`, and generate a Kharon payload against it — agents check in within seconds of execution.

### Tear the infrastructure down

Remove the plugin and destroy the AWS footprint.

```
./scripts/uninstall.sh /path/to/AdaptixC2 && cd deploy/aws && terraform destroy
```

Outcome: the plugin is removed from AdaptixC2 and the service restored, then Terraform deletes the Lambda function, DynamoDB tables, IAM roles and KMS key created at deploy time, leaving no billed AWS resources behind.

*No corpus evidence — derived from upstream documentation.*
