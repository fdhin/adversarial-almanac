---
name: "jiraffe"
categories:
  - webapp
homepage: "https://github.com/0x48piraj/jiraffe"
github-repo: "0x48piraj/jiraffe"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: MIT
github-stars: 205
github-pushed: "2026-04-02"
github-commits-year: 71
gh-checked: "2026-11-14"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# jiraffe

> Jira security reconnaissance and exploitation framework — unauthenticated disclosure modules plus CVE validation for Atlassian Jira deployments.

## What it's for

Jiraffe is a Python framework (`python3 -m jiraffe`) for assessing Atlassian Jira instances: a recon stage of non-exploit modules checks for unauthenticated access and information disclosure (user/project/group enumeration endpoints, exposed dashboards and gadget configuration, Service Desk and self-signup enabled, dev mode) and an exploit stage validates a catalogue of 16 known Jira CVEs — SSRF via gadget `makeRequest` and OAuth IconUriServlet (CVE-2019-8451, CVE-2017-9506), Velocity template injection RCE (CVE-2019-11581), Tinymce path traversal, XSS family, username and project enumeration. It follows a recon-first, exploit-second flow with interactive and `--auto` modes, gates exploits by target version and cloud/server awareness, ships AWS SSRF helpers (metadata, IAM) plus a custom-payload option for other clouds, and can emit JSON. Reach for it whenever a Jira URL shows up in scope: the unauthenticated recon modules alone routinely map users and projects on misconfigured instances. Low-noise by design — no automatic shell execution.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Interactive assessment of a Jira instance

```
python3 -m jiraffe -t https://jira.example.com
```

Outcome: detects the Jira version and deployment type (cloud vs Server/DC), runs the recon modules, and drops you into the menu to pick exploits for the findings.

### Automatic recon-plus-exploitation run

```
python3 -m jiraffe -t https://jira.example.com --auto
```

Outcome: runs the full module chain without prompting — discovery, then version-gated exploitation of what was found, printing per-finding results.

### Recon only, no exploitation

```
python3 -m jiraffe -t https://jira.example.com --check-only
```

Outcome: the same detection pass (`--dry-run` alias) but exploits are never executed — the safe mode for enumeration-only engagements.

### Execute a command through template injection

```
python3 -m jiraffe -t https://jira.example.com --cmd 'id'
```

Outcome: targets CVE-2019-11581 (Velocity SSTI in ContactAdministrators/SendBulkMail) and returns the command's output when the instance is vulnerable and the form/SMTP precondition holds.

### Point SSRF at internal targets

```
python3 -m jiraffe -t https://jira.example.com --ssrf http://169.254.169.254/latest/meta-data/
```

Outcome: uses the gadget SSRF CVEs to make the Jira server fetch the URL (AWS metadata, internal services) and shows what comes back.

### List and filter the exploit catalogue

```
python3 -m jiraffe --list-exploits
python3 -m jiraffe -t https://jira.example.com --auto --severity HIGH --json
```

Outcome: the first prints every module with its CVE and severity; the second runs only HIGH-severity exploits and emits results as JSON for pipeline consumption.
