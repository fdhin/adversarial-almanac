---
name: "redteamsimmer"
categories:
  - scanner
version: "git (2026-10-23)"
homepage: "https://github.com/breachsimrange/redteamsimmer"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "breachsimrange/redteamsimmer"
github-stars: 16
github-pushed: "2026-10-23"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 1
---

# redteamsimmer

> Web-based adversary emulation platform that puts a UI on Atomic Red Team test execution across Windows endpoints.

## What it's for

RedTeamSimmer replaces manual `Invoke-AtomicTest` PowerShell work with a web
application: a Flask server plus lightweight Go agents that run on target
Windows systems. From one dashboard you deploy agents, browse the full MITRE
ATT&CK catalog mapped by tactic, execute atomic tests with automatic
prerequisite handling, and watch live color-coded output; results land in an
operations history with an ATT&CK heatmap. It also ships pre-built emulation
plans for real threat actors (APT28, APT3, APT41, FIN7, Lazarus Group, Wizard
Spider) and correlates executed techniques against Sigma, Splunk and Elastic
detection rules, which makes it equally usable for purple-team exercises and
EDR coverage validation. Agents are Windows-only at the time of writing. The
original project is by @abhijithbr; it is now maintained by the BreachSimRange
team.

## Typical use cases

### Start the orchestration server

Launch the web UI that agents and operators connect to.

```
cd server && source venv/bin/activate && python app.py
```

Outcome: the Flask server starts on `http://localhost:5000`; log in with the
documented default credentials (`redteamsimmer` / `redteamsimmer` — change the
password immediately) and the technique catalog loads, provided the Atomic Red
Team library has been downloaded into `server/atomic/` first (without it the
catalog comes up empty).

### Enroll a Windows target with the agent

Deploy the compiled agent on an endpoint so it can receive test executions.

```
.\agent.exe
```

Outcome: run from an elevated PowerShell on the Windows target, the agent
registers with the server, reports hostname, user, OS and architecture plus a
detection report of 60+ AV/EDR products, downloads the PowerShell modules it
needs, and begins polling for tasks; it then appears in the web UI's Agents
view, ready to be selected as the target when executing techniques or actor
emulation plans.

*No corpus evidence — derived from upstream documentation (BreachSimRange
redteamsimmer README plus docs/QUICKSTART.md and the packaged source tree).*
