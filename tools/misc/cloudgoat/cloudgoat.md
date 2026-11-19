---
name: "cloudgoat"
categories:
  - misc
homepage: "https://github.com/RhinoSecurityLabs/cloudgoat"
origin: "cloudpentestcheatsheets"
recipe: "upstream"
packaged-by: []
build-system: python
license: BSD-3-Clause
meta-checked: "2026-11-13"
github-repo: "RhinoSecurityLabs/cloudgoat"
github-match: "exact"
github-stars: 3782
gh-checked: "2026-11-13"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# cloudgoat

> Rhino Security Labs' "vulnerable by design" cloud deployment tool — spins up CTF-style AWS and Azure scenarios in your own account for practicing cloud penetration testing.

## What it's for

CloudGoat is a training tool, not an attack tool: it uses Terraform to deploy deliberately vulnerable collections of cloud resources ("scenarios") into an AWS or Azure account you control, each with a defined goal — usually capturing a secret in Secrets Manager or S3. Scenarios are grouped easy/medium/hard (from `iam_enum_basics` and `lambda_privesc` through `cloud_breach_s3`, `detection_evasion` and `ecs_efs_attack`) and many offer multiple paths to victory; the README's scenario pages document the intended skills. It is a lab, exactly in the spirit of a vulnerable web app for cloud: deploy it only in a throwaway account — never alongside production or sensitive resources. CloudGoat manages only the resources it creates: anything you create while playing must be removed by hand before destroying, or it will leak. Requires Linux/macOS, Python 3.9+, Terraform >= 1.5.0, the AWS and Azure CLIs, and `jq`.

## Typical use cases

### Configure once after installing

Save repeating flags on every command.

```
cloudgoat config aws
```

Outcome: prompts for and stores the AWS profile to use in `config.yml` — CloudGoat deliberately refuses the system "default" profile so nobody accidentally deploys scenarios to production; `cloudgoat config azure` stores the Azure subscription, and `cloudgoat config whitelist --auto` creates `whitelist.txt` with your current public IP (via ifconfig.co) so scenario security groups only expose resources to you.

### See what you can practice

Browse the scenario catalog.

```
cloudgoat list all
```

Outcome: every scenario with deployed/undeployed state — `list aws` or `list azure` filters by cloud, and `list <scenario-name>` prints detail about a deployed instance.

### Deploy a scenario

Stand up a lab.

```
cloudgoat create iam_privesc_by_rollback
```

Outcome: Terraform builds the scenario in the configured account and prints the starting resources; a folder named `<scenario>-<unique-id>` is created containing `start.txt` (your starting credentials/URLs — the same data printed to console) and, for some scenarios, an SSH keypair `cloudgoat`/`cloudgoat.pub`. Do not delete or edit that folder — it holds the state CloudGoat needs to tear the scenario down later.

### Tear everything down

End of session hygiene.

```
cloudgoat destroy all
```

Outcome: deletes every scenario's cloud resources and moves the scenario instance folders to `./trash` (recoverable Terraform state); run `destroy <scenario-name>` for one — after removing anything you created yourself, which CloudGoat cannot manage, and glance at the web console afterwards to catch leftovers.

### Run it from Docker

No local Python/Terraform setup.

```
docker run -it rhinosecuritylabs/cloudgoat:latest
```

Outcome: an interactive container with cloudgoat ready; to use host cloud credentials mount them explicitly with `-v ~/.aws:/root/.aws/` — which hands the container full access to those credentials, so treat the image as disposable.

No corpus evidence — derived from upstream documentation.
