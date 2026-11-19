---
name: "csrft-git"
categories:
  - webapp
version: "r32.17146bb"
homepage: "https://github.com/PaulSec/CSRFT"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: node
license: GPL3
meta-checked: "2026-11-09"
github-repo: "PaulSec/CSRFT"
github-stars: 172
github-pushed: "2014-06-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# csrft-git

> Node.js toolkit that exploits confirmed CSRF vulnerabilities by serving malicious proof-of-concept pages to victims.

## What it's for

CSRFT (Cross Site Request Forgeries Toolkit) turns a known CSRF flaw into a working proof of concept. It runs a small Node.js HTTP server hosting attack scenarios described in JSON; when a victim visits the served page, JavaScript fires the configured GET or POST requests from their authenticated session. Scenarios support ordered steps (for example: log the victim in, then perform a privileged action), fixed values, and dictionary substitution via a `<%value%>` placeholder. It exploits CSRF, it does not find it — for detection and single-request PoCs see the OWASP tool in [[csrftester]].

## Typical use cases

### Serve an attack scenario

```
node server.js conf/attack.json 8080
```

Outcome: starts the CSRFT server on port 8080 (the default when omitted) with the scenarios from attack.json; send the victim the server's URL and each scenario step executes in their browser session. Run from the CSRFT source directory.

### Describe a multi-step attack in JSON

```
{
  "audit": {
    "name": "Login, elevate, log out",
    "scenario": [
      { "attack": [ { "method": "POST", "type_attack": "dico",
          "file": "dicos/passwords.txt", "form": "exploits/form.html" } ] },
      { "attack": [ { "method": "GET", "type_attack": "special_value",
          "url": "https://www.vulnerable.example/index.php/welcome/upgrade/27" } ] }
    ]
  }
}
```

Outcome: the server executes the attacks in order — first dictionary-login the victim using dicos/passwords.txt through the form in exploits/form.html, then fire the fixed GET — modelling a chained session exploit.

### Automate a dictionary attack from the command line

```
python utils/csrft_utils.py --url="https://www.vulnerable.example/changePassword.php?newPassword=fuzz" --param=newPassword --dico_file="dicos/passwords.txt"
```

Outcome: builds the scenario JSON for a GET dictionary attack against one parameter and launches the server itself, skipping hand-written configuration.

_No corpus evidence — derived from upstream documentation (project README)._
